# Détection AD Kerberos et MITM (playbooks ARNO_SIEM)

Ce document décrit la mise en œuvre des playbooks **AD Kerberos** (R030–R032,
C012) et **MITM/ARP/DHCP/LLMNR** (R026–R029, C011) de SMART SIEM : prérequis
de collecte, configuration des allowlists, et règles de containment.

## 1. Prérequis — playbook AD (R030–R032, C012)

### Audit Active Directory

Le collecteur consomme le journal **Windows Security** du contrôleur de
domaine. Les événements requis doivent être audités :

| Événement | Rôle | Détection |
|---|---|---|
| 4768 / 4769 | Tickets Kerberos (TGT / TGS), type de chiffrement | R030 Kerberoasting (RC4), R031 AS-REP Roasting |
| 4662 | Accès à la réplique AD (DCSync) | R032 DCSync |
| 4624 / 4625 | Ouvertures de session réussies/échouées | R008 (utilisé par C012) |

Activer l'audit avec le script du playbook `activer-audit-ad.ps1` (section
« Activation de l'audit » du playbook AD) sur chaque DC, puis vérifier que
le collecteur Windows embarque bien le canal **Security**.

### Règles et corrélation

- **R030** Kerberoasting : demandes TGS (4769) en RC4/HMAC-MD5 vers des
  comptes de service, hors allowlist.
- **R031** AS-REP Roasting : comptes sans pré-authentification (4771/4768).
- **R032** DCSync : 4662 avec GUIDs de réplique `{1131f6aa-...}` sur un
  compte non-DC.
- **C012** AD Kerberos Attack Chain : (R030|R031) → R008 dans les 24 h,
  ou R032 seul → **CRITICAL** + `isolate_endpoint` + `disable_account`
  (CONFIRM). Dédup par preuve (événements sources), jamais par utilisateur
  seul : un second attaquant sur le même compte déclenche sa propre C012.

## 2. Prérequis — playbook MITM (R026–R029, C011)

| Source | Configuration | Rôle |
|---|---|---|
| pfSense → syslog distant | *Status → System Logs → Remote Logging* vers le collecteur | Motifs ARP (`arp: ... is using my IP address ...`, `arp: ... moved from ... to ...`) et offres DHCP |
| pfSense → filterlog | Règle WAN/LAN avec **log** activé, format CSV | R028 (UDP 5355/137), R003 (FLOW_BYTES) |
| arpwatch (hôtes Linux) | Paquet `arpwatch`, logs syslog `arpwatch: changed ethernet address ...` | Oscillation ARP (R026 HIGH) |
| dhclient (clients Linux) | Logs syslog `DHCPOFFER of ... from ...` | R027 rogue DHCP |
| Windows (GPO) | Désactiver **LLMNR** et **NBT-NS** (Computer Config → Admin Templates → Network → DNS Client) | Réduit la surface ; R028 détecte les contournements |
| DHCP snooping (switch) | Activer sur les ports accès, liste d'options 82 | Prévention rogue DHCP côté infrastructure |
| auditd (Linux) | Règles sur `/etc/resolv.conf`, `/etc/hosts`, routes | R029 DNS/gateway tampering |

### Règles

- **R026** ARP spoofing : conflit d'IP (motif B) → CRITICAL ; changement
  de table sur IP protégée ou ≥3 changements/5 min (fenêtre glissante)
  → HIGH. `isolate_endpoint` CONFIRM.
- **R027** Rogue DHCP : serveur hors `DHCP_SERVERS_ALLOWED` → HIGH.
  `isolate_endpoint` CONFIRM.
- **R028** LLMNR/NBT-NS poisoning : ≥3 destinations distinctes en 10 min
  depuis un même responder (port source 5355/137, port destination
  éphémère — les requêtes unicast ne sont PAS des réponses) → HIGH.
  `block_ip` CONFIRM.
- **R029** DNS/gateway tampering : écriture `resolv.conf`, modification de
  route par défaut, login `/login` → WARNING ; HIGH uniquement si
  indicateur fort (non-nginx) + preuve ARP **sur le même hôte**.
- **C011** MITM → compromission : (R026|R027|R028) → R008 dans les 30 min
  → CRITICAL + `isolate_endpoint` + `disable_account` (CONFIRM).

## 3. Guide de configuration — allowlists

Variables d'environnement du service `app` (voir `docker-compose.yml`) :

| Variable | Défaut compose | Rôle |
|---|---|---|
| `ARP_PROTECTED_IPS` | `192.168.189.2,192.168.189.10` | IPs jamais ciblées par R026 (passerelles, DC) — exclues des entités d'isolation |
| `DHCP_SERVERS_ALLOWED` | `192.168.189.2` | Serveurs DHCP légitimes ; toute offre depuis une autre IP → R027 |
| `NAME_SERVERS_ALLOWED` | `192.168.189.2,192.168.189.10` | Résolveurs LLMNR/NBT-NS légitimes ; tout autre responder → R028 |
| `AD_HONEY_ACCOUNTS` | — | Comptes leurres AD (détection immédiate à l'usage) |
| `AD_RC4_ALLOWED_SERVICES` | — | Comptes de service autorisés en RC4 (évite le bruit Kerberoasting) |
| `SOAR_FIREWALL_PROTECTED_IPS` | — | IPs que les playbooks firewall refusent de bloquer (voir §4) |
| `SOAR_FIREWALL_PROTECTED_AD_DC_IP` | — | IP du DC protégé pour `disable_account`/isolation |

En production, remplacer les valeurs par les IPs réelles du site. Une
allowlist vide = tout est suspect (bruyant) ; une allowlist trop large =
angles morts. Revoir après chaque changement d'infrastructure DHCP/DNS.

## 4. Runbook — ne JAMAIS bloquer l'IP spoofée

Règle d'or du playbook MITM : **l'IP usurpée est souvent la passerelle ou
le DC — la bloquer coupe l'accès de tout le réseau**. Le containment
targete l'IP RÉELLE de l'attaquant, jamais l'IP spoofée.

Garantie côté code (à ne pas contourner) :

1. **R026** : l'IP usurpée (`arp_usurped_ip`) est exclue des
   `related_entities.ips` ; les IPs protégées (`ARP_PROTECTED_IPS`) sont
   exclues aussi ; `hosts` est vide (isolate_endpoint préfère les hosts).
2. **R027** : seul le serveur DHCP rogue est listé ; le client DHCP
   (victime) n'apparaît ni en IP ni en host.
3. **R028** : seul le responder est listé (`block_ip` bloque TOUTES les
   IPs listées — les destinations interrogées ne doivent jamais y figurer).
4. **C011/C012** : `hosts` est vidé (`dropHosts`) car R008 porte le nom
   d'hôte du DC/victime ; l'isolation vise l'IP attaquante.
5. **Playbooks SOAR** : `SOAR_FIREWALL_PROTECTED_IPS` et
   `SOAR_FIREWALL_PROTECTED_AD_DC_IP` font REFUSER l'exécution si une
   cible protégée apparaît — vérifier ce refus dans l'UI avant tout
   approbation manuelle (mode CONFIRM).

Avant d'approuver un playbook CONFIRM, vérifier dans l'incident :

- `related_entities.ips` ne contient **aucune** IP de passerelle/DC
  (ex. `192.168.189.2`, `192.168.189.10`) ;
- l'IP listée est bien celle de la source attaquante (Kali/bettercap),
  pas l'IP spoofée ;
- en cas de doute : **ne pas approuver**, escalader à l'équipe réseau.

## 5. Validation

Chaque détection est prouvée par un scénario de simulation
(`simulation/scenarios/r026-*.json` … `c011-*.json`) exécuté via le
collecteur réel (`replay`) → Elasticsearch → corrélation. En lab
(VMware), les scénarios A32–A35 (bettercap/Ettercap) et A36–A39
(Impacket) restent à exécuter manuellement selon les playbooks ; les
critères de succès : incident < 2 min, bonne sévérité/entités, **aucune**
action sur `192.168.189.2`/`192.168.189.10`, pas de faux positif résiduel
15 min après le scénario.
