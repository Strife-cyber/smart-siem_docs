---
title: Analyse de l'existant
description: 
published: true
date: 2026-07-09T21:54:08.708Z
tags: 
editor: markdown
dateCreated: 2026-06-20T08:19:44.300Z
---

# Analyse de l'Existant - Smart SIEM CTU


|----|----|
| **Version** | v1.0 — Analyse de l'existant |
| **Date** | 25/03/2026 |
| **Contexte** | Projet Smart SIEM — UCAC-ICAM |

## 0. Résumé Exécutif

Le marché des SIEM (Security Information and Event Management) a connu une évolution radicale depuis l'introduction du concept par Gartner en 2005. Les solutions traditionnelles, centrées sur la collecte passive de logs et la gestion de conformité, cèdent progressivement la place à des architectures **cloud-native** intégrant des capacités de **corrélation avancée**, d'**automatisation SOAR**, d'**analyse comportementale UEBA** et de **détection étendue XDR**.

Cette analyse de l'existant examine en détail les architectures des principales solutions SIEM propriétaires et open-source, leurs composants fondamentaux, leurs forces et faiblesses, afin d'éclairer les choix de conception du projet Smart SIEM de la CTU.

---

## 1. Définition et Périmètre du SIEM

### 1.1 Qu'est-ce qu'un SIEM ?

Un SIEM (Security Information and Event Management) est un système qui **collecte, corrèle et analyse** les données de sécurité provenant de l'ensemble de l'infrastructure informatique d'une organisation. Il combine deux fonctions historiquement distinctes :

| **Composant** | **Fonction** |
|---|---|
| **SIM** (Security Information Management) | Collecte et stockage à long terme des logs pour conformité et forensique |
| **SEM** (Security Event Management) | Analyse en temps réel, corrélation et alerte sur les événements de sécurité |

### 1.2 Objectifs Opérationnels d'un SIEM

Un SIEM moderne poursuit cinq objectifs fondamentaux :

1. **Collecte et agrégation** des données de sécurité multi-sources
2. **Normalisation** des logs dans un format unifié
3. **Analyse et corrélation** pour détecter des patterns d'attaque
4. **Alerting** en temps réel avec priorisation des risques
5. **Reporting et visualisation** pour les équipes SOC et la direction

---

## 2. Architecture Générique d'un SIEM

L'architecture d'un SIEM suit un pipeline de traitement de données en quatre étapes principales :

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         PIPELINE SIEM — VUE LOGIQUE                        │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌──────────┐    ┌──────────────┐    ┌─────────────┐    ┌──────────────┐   │
│  │ COLLECTE │───▶│ NORMALISATION│───▶│ CORRÉLATION │───▶│  ALERTING /  │   │
│  │          │    │              │    │             │    │   RÉPONSE    │   │
│  └──────────┘    └──────────────┘    └─────────────┘    └──────────────┘   │
│       │                │                  │                  │              │
│       ▼                ▼                  ▼                  ▼              │
│  ┌──────────┐    ┌──────────────┐    ┌─────────────┐    ┌──────────────┐   │
│  │ Agents   │    │ Parsing      │    │ Règles      │    │ Notifications│   │
│  │ Syslog   │    │ Enrichissement│   │ Statistiques│    │ Tableaux de  │   │
│  │ API      │    │ Tagging      │    │ Séquences   │    │ bord         │   │
│  │ Cloud    │    │              │    │ UEBA/ML     │    │ Playbooks    │   │
│  └──────────┘    └──────────────┘    └─────────────┘    └──────────────┘   │
│                                                                             │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│  │                     STOCKAGE & INDEXATION                            │   │
│  │              (Elasticsearch / Data Lake / S3)                       │   │
│  └──────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 2.1 Couche de Collecte

La collecte est le point d'entrée du SIEM. Elle doit supporter :

- **Protocoles multiples** : Syslog UDP/TCP, REST/HTTPS, agents déployables
- **Sources variées** : équipements réseau, serveurs, applications, cloud
- **Agents légers** : déployés sur les endpoints pour la collecte locale

### 2.2 Couche de Normalisation

Les logs arrivent dans des formats hétérogènes. La normalisation consiste à :

- **Parser** les logs bruts pour en extraire les champs clés
- **Mapper** les champs vers un schéma unifié (timestamp, source_ip, user, event_type, severity)
- **Enrichir** avec du contexte (géolocalisation, asset criticality, threat intelligence)

### 2.3 Couche de Corrélation

C'est le **cœur intelligent** du SIEM. La corrélation permet de :

- **Agréger** des événements similaires (ex: 5 échecs de connexion en 60s)
- **Détecter des séquences** d'attaques (kill-chain)
- **Calculer des scores de risque** dynamiques
- **Appliquer des règles** basées sur des frameworks comme MITRE ATT&CK

### 2.4 Couche de Stockage et Indexation

Le stockage doit permettre :

- **Recherche rapide** sur des volumes massifs (indexation temporelle)
- **Rétention configurable** (30j, 6 mois, 1 an)
- **Intégrité des données** (hachage, chaîne de custody)

### 2.5 Couche d'Alerte et Réponse

La couche finale comprend :

- **Moteur d'alertes** multi-niveaux (INFO, WARNING, HIGH, CRITICAL)
- **Notifications** multicanal (email, SMS, webhook)
- **Playbooks SOAR** pour l'automatisation de la réponse
- **Tableaux de bord** pour la visualisation et le pilotage

---

## 3. Analyse des Solutions Propriétaires

### 3.1 Splunk Enterprise Security

#### 3.1.1 Présentation

Splunk Enterprise Security (ES) est une **application** construite sur la plateforme Splunk Enterprise. Elle combine SIEM, SOAR et gestion du renseignement sur les menaces.

#### 3.1.2 Architecture

Splunk ES repose sur une architecture en trois types d'add-ons :

| **Type d'add-on** | **Rôle** |
|---|---|
| **Domain Add-ons (DA)** | Tableaux de bord et vues par domaine de sécurité |
| **Supporting Add-ons (SA)** | Couche de normalisation et mappage vers le Common Information Model (CIM) |
| **Technology Add-ons (TA)** | Collecte et mise en forme des données sources |

#### 3.1.3 Composants Techniques

- **Forwarders** : collectent les données et les envoient aux Indexers
- **Indexers** : stockent et indexent les données
- **Search Heads** : interface utilisateur pour les recherches et les dashboards

#### 3.1.4 Forces et Faiblesses

| **Forces** | **Faiblesses** |
|---|---|
| Écosystème riche d'add-ons | Coût élevé (licences basées sur le volume) |
| Modèle de données unifié (CIM) | Courbe d'apprentissage steep |
| Capacités avancées de corrélation | Complexité d'administration |
| Intégration native avec Splunk SOAR | Architecture historique non cloud-native |

---

### 3.2 IBM QRadar

#### 3.2.1 Présentation

IBM QRadar est une solution SIEM enterprise qui collecte et analyse en temps réel les logs et les flux réseau. Elle fait partie de la IBM QRadar Security Intelligence Platform.

#### 3.2.2 Architecture

L'architecture QRadar est modulaire :

| **Composant** | **Fonction** |
|---|---|
| **Event Collectors** | Capturent et transmettent les données |
| **Event Processors** | Collectent, stockent et analysent les événements |
| **Flow Processors** | Collectent les flux réseau (Layer 4) |
| **QFlow Processors** | Inspection deep packet (Layer 7) |
| **Console** | Interface centralisée pour les analystes SOC |

#### 3.2.3 Modèles de Déploiement

QRadar propose différents modèles d'appliance :

- **Intégré (all-in-one)** : jusqu'à 15 000 événements/seconde
- **Console dédiée** : pour la gestion
- **Event/Flow Processor** : pour la scalabilité horizontale
- **Flow Processor uniquement** : jusqu'à 1,2 million de flux/minute

#### 3.2.4 Évolution Récente

IBM a annoncé une version **cloud-native** de QRadar, conteneurisée sur Red Hat OpenShift, intégrant XDR, SOAR et IA.

#### 3.2.5 Forces et Faiblesses

| **Forces** | **Faiblesses** |
|---|---|
| Analyse des flux réseau native | Complexité de déploiement |
| Threat Intelligence X-Force intégrée | Modèle de licence coûteux |
| Modules complémentaires (risk, vuln, forensics) | Interface legacy |
| Scalabilité horizontale | Courbe d'apprentissage importante |

---

### 3.3 Microsoft Sentinel

#### 3.3.1 Présentation

Microsoft Sentinel est un SIEM **cloud-native**, conçu pour les environnements multi-cloud et multi-plateformes.

#### 3.3.2 Architecture

Sentinel est une plateforme intégrée dont les composants clés sont :

| **Composant** | **Fonction** |
|---|---|
| **Data Lake** | Stockage centralisé des données de sécurité |
| **Connecteurs (>300)** | Ingestion depuis multiples sources |
| **Parsers (KQL)** | Normalisation vers le schéma ASIM |
| **Analytics Rules** | Détections et corrélations |
| **Playbooks** | Automatisation SOAR |
| **Workbooks** | Visualisation et dashboards |
| **Jupyter Notebooks** | Investigations avancées et hunting |

#### 3.3.3 Modèle de Données : ASIM

Sentinel utilise le **Advanced Security Information Model (ASIM)** pour normaliser les données, permettant aux analystes de créer des règles sur un schéma unifié.

#### 3.3.4 Forces et Faiblesses

| **Forces** | **Faiblesses** |
|---|---|
| Cloud-native, sans infrastructure à gérer | Dépendance à l'écosystème Azure |
| Intégration native avec Microsoft 365 et Azure | Modèle de coût à l'ingestion (peu prévisible) |
| >300 connecteurs prêts à l'emploi | Complexité de la tarification |
| IA intégrée avec Security Copilot | Moins mature pour les environnements on-premise |
| SOAR intégré | |

---

## 4. Analyse des Solutions Open-Source

### 4.1 Wazuh

#### 4.1.1 Présentation

Wazuh est une plateforme de sécurité open-source qui unifie les capacités SIEM et XDR. Elle est utilisée comme base pour de nombreuses implémentations SOC open-source.

#### 4.1.2 Architecture

Wazuh repose sur trois composants centraux :

```
┌─────────────────────────────────────────────────────────────────┐
│                        ARCHITECTURE WAZUH                      │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │                    WAZUH DASHBOARD                       │   │
│  │         (Visualisation & Administration)                │   │
│  └────────────────────────┬────────────────────────────────┘   │
│                           │                                     │
│  ┌────────────────────────▼────────────────────────────────┐   │
│  │                    WAZUH INDEXER                        │   │
│  │         (Stockage, Indexation, Recherche)              │   │
│  └────────────────────────┬────────────────────────────────┘   │
│                           │                                     │
│  ┌────────────────────────▼────────────────────────────────┐   │
│  │                    WAZUH SERVER                         │   │
│  │    (Analyse, Corrélation, Règles, Alertes)             │   │
│  └────────────┬───────────────────────────┬────────────────┘   │
│               │                           │                     │
│  ┌────────────▼──────────┐  ┌────────────▼──────────┐          │
│  │    WAZUH AGENT        │  │   SOURCES EXTERNES     │          │
│  │  (Endpoints Linux/    │  │  (Syslog, API, Cloud)  │          │
│  │   Windows/macOS)      │  │                         │          │
│  └───────────────────────┘  └────────────────────────┘          │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

#### 4.1.3 Composants Détaillés

| **Composant** | **Fonction** | **Détails** |
|---|---|---|
| **Wazuh Agent** | Collecte sur endpoint | Multi-plateforme (Linux, Windows, macOS), collecte logs OS, applications, appels système |
| **Wazuh Server** | Analyse et corrélation | Décodeurs et règles intégrés, analyse des logs, déclenchement d'alertes |
| **Wazuh Indexer** | Stockage et indexation | Basé sur OpenSearch/Elasticsearch, recherche rapide |
| **Wazuh Dashboard** | Visualisation | Interface web pour dashboards et administration |

#### 4.1.4 Stack Open-Source Complémentaire

Wazuh est souvent intégré avec :

- **Graylog** : pour l'ingestion et la normalisation avancée des logs
- **Fluent Bit / Filebeat** : pour le forwarding léger des logs
- **ElastAlert / Praeco** : pour la corrélation avancée et les alertes
- **Grafana** : pour des visualisations supplémentaires

#### 4.1.5 Forces et Faiblesses

| **Forces** | **Faiblesses** |
|---|---|
| Open-source et gratuit | Nécessite une expertise pour le déploiement |
| Architecture modulaire et scalable | Support communautaire uniquement (sauf offre payante) |
| Intégration native avec MITRE ATT&CK | UI moins polishée que les solutions propriétaires |
| Communauté active | Courbe d'apprentissage pour la configuration des règles |
| Flexibilité totale de personnalisation | Scalabilité limitée sans architecture distribuée |

---

### 4.2 ELK Stack (Elasticsearch, Logstash, Kibana)

#### 4.2.1 Présentation

La stack ELK (aujourd'hui Elastic Stack) est l'une des architectures open-source les plus utilisées pour le logging et la sécurité.

#### 4.2.2 Architecture

| **Composant** | **Fonction** |
|---|---|
| **Elasticsearch** | Moteur de recherche et d'indexation distribué |
| **Logstash** | Pipeline de collecte, parsing et normalisation |
| **Kibana** | Interface de visualisation et dashboards |
| **Beats (Filebeat)** | Agents légers de collecte |

#### 4.2.3 Utilisation en SIEM

La stack ELK est souvent utilisée avec :
- **ElastAlert** : pour la corrélation et les alertes
- **MITRE ATT&CK mappings** : pour la détection
- **Dashboards personnalisés** : pour les équipes SOC

#### 4.2.4 Forces et Faiblesses

| **Forces** | **Faiblesses** |
|---|---|
| Extrêmement flexible et personnalisable | Pas de fonction SIEM "out-of-the-box" |
| Scalabilité horizontale | Configuration complexe |
| Communauté massive | Pas de corrélation native |
| Recherche ultra-rapide | Maintenance lourde |

---

### 4.3 Graylog

#### 4.3.1 Présentation

Graylog est une solution open-source de gestion centralisée des logs, souvent utilisée comme composant d'ingestion dans les architectures SIEM open-source.

#### 4.3.2 Rôle dans une Architecture SIEM

Graylog assure :
- **Ingestion** multi-protocole (Syslog, GELF, API)
- **Parsing et normalisation** des logs
- **Visualisation** et recherche
- **Alerting** de base

#### 4.3.3 Intégration Typique

Dans une stack SIEM open-source :

```
Sources → Fluent Bit → Graylog → Wazuh Indexer → Wazuh Dashboard
                              ↘
                           Alerting (ElastAlert)
```

---

## 5. Tendances et Évolutions du Marché

### 5.1 Du SIEM Traditionnel au Next-Gen SIEM

L'évolution du SIEM s'est faite en plusieurs phases :

| **Génération** | **Caractéristiques** | **Période** |
|---|---|---|
| **1ère génération** | Gestion de logs, conformité | Début 2000 |
| **2ème génération** | Corrélation basique, recherche améliorée | 2010-2018 |
| **Next-Gen SIEM** | Cloud-native, IA/ML, UEBA, SOAR intégré | 2018-présent |

### 5.2 Les Défis des SIEM Traditionnels

Les SIEM legacy souffrent de limitations structurelles :

- **Volume d'alertes** : 93% des entreprises se déclarent dépassées par le volume d'alertes
- **Faux positifs** : épuisement des équipes SOC
- **Scalabilité** : incapables de gérer les volumes de données cloud
- **Manque de contexte** : détections sans priorisation
- **Architecture on-premise** : inadaptée aux environnements hybrides

### 5.3 L'Intégration SOAR

Le SOAR (Security Orchestration, Automation and Response) est désormais un composant essentiel du SIEM moderne :

- **Orchestration** : coordination des outils de sécurité
- **Automation** : exécution automatique des playbooks
- **Response** : actions de remédiation automatisées

### 5.4 L'UEBA comme Capacité Différenciante

L'UEBA (User and Entity Behavior Analytics) permet :

- **Établir des baselines** comportementales par utilisateur
- **Détecter des anomalies** (horaires, volumes, accès)
- **Calculer des scores de risque** dynamiques

### 5.5 XDR : Convergence ou Complément ?

Le XDR (Extended Detection and Response) est souvent présenté comme l'évolution du SIEM :

| **SIEM** | **XDR** |
|---|---|
| Collecte tous les logs | Collecte télémetry native |
| Corrélation post-hoc | Détection en temps réel |
| Réponse manuelle ou scriptée | Réponse automatisée intégrée |
| Vue agrégée | Vue unifiée et contextualisée |

La tendance actuelle est à la **convergence** : les Next-Gen SIEM intègrent des capacités XDR.

---

## 6. Synthèse Comparative

### 6.1 Tableau Comparatif des Solutions

| **Critère** | **Splunk ES** | **IBM QRadar** | **Microsoft Sentinel** | **Wazuh (Open-Source)** |
|---|---|---|---|---|
| **Type** | Propriétaire | Propriétaire | Propriétaire (SaaS) | Open-Source |
| **Architecture** | On-premise / Cloud | On-premise / Cloud | Cloud-native | On-premise / Cloud |
| **Coût** | Élevé (volume) | Élevé | À l'ingestion | Gratuit |
| **Corrélation** | Avancée (CIM) | Avancée | Avancée (KQL) | Modérée (règles) |
| **SOAR** | Intégré | Intégré | Intégré (Playbooks) | Via intégration |
| **UEBA** | Oui | Oui | Oui | Limitée |
| **Courbe d'apprentissage** | Élevée | Élevée | Modérée | Élevée |
| **Communauté** | Large | Large | Large | Active |

### 6.2 Architecture de Référence pour un Projet Académique

Pour un projet comme le Smart SIEM de la CTU, l'architecture open-source est la plus pertinente :

```
┌─────────────────────────────────────────────────────────────────────────────┐
│              ARCHITECTURE DE RÉFÉRENCE — SIEM OPEN-SOURCE                 │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                         INTERFACE UTILISATEUR                       │   │
│  │              ┌──────────────┐    ┌──────────────┐                   │   │
│  │              │   Wazuh      │    │   Grafana    │                   │   │
│  │              │  Dashboard   │    │   (Vues      │                   │   │
│  │              │              │    │   avancées)  │                   │   │
│  │              └──────────────┘    └──────────────┘                   │   │
│  └────────────────────────────────┬────────────────────────────────────┘   │
│                                   │                                         │
│  ┌────────────────────────────────▼────────────────────────────────────┐   │
│  │                    WAZUH INDEXER (OpenSearch)                       │   │
│  │                 Stockage & Indexation des logs                       │   │
│  └────────────────────────────────┬────────────────────────────────────┘   │
│                                   │                                         │
│  ┌────────────────────────────────▼────────────────────────────────────┐   │
│  │                         WAZUH SERVER                                │   │
│  │              Analyse, Corrélation, Règles, Alertes                  │   │
│  └────────────┬───────────────────────────────────────┬────────────────┘   │
│               │                                       │                     │
│  ┌────────────▼──────────┐            ┌──────────────▼──────────────┐      │
│  │     WAZUH AGENT       │            │        GRAYLOG              │      │
│  │  (Collecte endpoint)  │            │   (Ingestion & Normalisation)│      │
│  └───────────────────────┘            └──────────────┬──────────────┘      │
│                                                      │                     │
│                                          ┌───────────▼───────────┐         │
│                                          │    FLUENT BIT /        │         │
│                                          │     FILEBEAT           │         │
│                                          │  (Collecte légère)     │         │
│                                          └───────────────────────┘         │
│                                                                             │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                  MODULES COMPLÉMENTAIRES                            │   │
│  │  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────────┐ │   │
│  │  │  ElastAlert  │  │   TheHive    │  │  MISP (Threat Intel)     │ │   │
│  │  │(Corrélation) │  │(Case Mgmt)   │  │                          │ │   │
│  │  └──────────────┘  └──────────────┘  └──────────────────────────┘ │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 7. Enseignements pour le Projet Smart SIEM CTU

### 7.1 Ce que les Solutions Existantes Nous Apprennent

| **Leçon** | **Application au Projet Smart SIEM** |
|---|---|
| **La normalisation est cruciale** | Définir un schéma unifié dès le début (comme le CIM de Splunk ou l'ASIM de Sentinel) |
| **La corrélation est le cœur du SIEM** | Implémenter un moteur de règles flexible (JSON) et des fenêtres temporelles |
| **L'automatisation SOAR est un différenciateur** | Concevoir des playbooks modulaires (blocage IP, désactivation compte) |
| **L'UEBA apporte une valeur ajoutée** | Modéliser des profils comportementaux et des scores de risque |
| **Le stockage doit être indexé et partitionné** | Utiliser Elasticsearch/OpenSearch avec indices temporels |
| **La visualisation doit être adaptée au public** | Vues Analyste (technique) / RSSI (synthétique) / Crisis Room (temps réel) |

### 7.2 Choix Stratégiques pour le Projet

1. **Stack technique** : S'inspirer de l'architecture Wazuh + Graylog + Elasticsearch
2. **Modèle de données** : Définir un schéma JSON unifié dès le Day 1
3. **Moteur de corrélation** : Règles basées sur MITRE ATT&CK (5 règles minimum)
4. **SOAR** : Playbooks paramétrables avec modes AUTO et CONFIRM
5. **UEBA** : Scoring dynamique avec baseline sur 30 jours
6. **Interface** : Dashboard "Crisis Room" avec rafraîchissement 5s

### 7.3 Ce qu'il ne Faut PAS Faire

- Ne pas sous-estimer l'importance de la normalisation
- Ne pas négliger la documentation technique
- Ne pas partir dans le développement sans conception préalable
- Ne pas oublier la sécurité du SIEM lui-même (RBAC, MFA, logs d'accès)
- Ne pas ignorer les cas de test (démo CTU)

---

## 8. Conclusion

L'analyse des solutions SIEM existantes révèle une **évolution profonde** du marché : des systèmes de gestion de logs passifs vers des **plateformes intelligentes** intégrant corrélation avancée, automatisation SOAR, analyse comportementale UEBA et capacités XDR.

Les solutions propriétaires (Splunk, QRadar, Sentinel) offrent des fonctionnalités complètes mais à des coûts élevés et avec des architectures parfois legacy. Les solutions open-source (Wazuh, ELK, Graylog) offrent une **flexibilité et un coût maîtrisé**, au prix d'une complexité de déploiement et d'une courbe d'apprentissage plus importante.

Pour le projet Smart SIEM de la CTU, l'architecture open-source constitue la **base de référence la plus pertinente**, permettant de couvrir l'ensemble des fonctionnalités exigées (collecte multi-sources, normalisation, corrélation, alertes, SOAR, UEBA, visualisation) tout en restant dans un cadre pédagogique et budgétaire maîtrisé.

**L'enjeu du projet n'est pas de réinventer le SIEM, mais de démontrer la maîtrise de ses composants fondamentaux et de leur intégration** — exactement ce que Chloe O'Brian attend de son outil pour protéger la CTU.

---

*Document préparé dans le cadre du projet Smart SIEM — UCAC/ICAM*
*Sources : Documentation officielle Splunk, IBM QRadar, Microsoft Sentinel, Wazuh, analyses sectorielles*