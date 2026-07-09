---
title: Exigences Fonctionnelles
description: 
published: true
date: 2026-07-09T21:54:42.571Z
tags: exigences
editor: markdown
dateCreated: 2026-06-29T14:19:46.146Z
---

# Exigences Fonctionnelles – Smart SIEM CTU

Chaque exigence est formatée selon la syntaxe suivante : **Le système doit [action] [condition] [résultat attendu]**.

## FR-01 : Collecte et Ingestion (CTU S1)

| ID      | Exigence                                                                                                                     | Critère d’acceptation |
| ------- | ---------------------------------------------------------------------------------------------------------------------------- | --------------------- |
| FR-01.1 | Recevoir des logs via Syslog UDP/TCP et API REST/HTTPS.                                                                      | Envoi d’un log avec `netcat` sur le port 514 ou `curl` vers `/api/v1/ingest` accepté. |
| FR-01.2 | Posséder un agent de collecte déployable sur poste Linux simulé.                                                             | L’agent lit un fichier `*.log` local et le transmet au serveur avec accusé de réception. |
| FR-01.3 | Normaliser tout log en JSON unifié (Golden Schema) contenant `timestamp, source_ip, user, event_type, severity`.             | Log brut transformé ; logs invalides rejetés dans une file « dead-letter ». |
| FR-01.4 | Tagger automatiquement la criticité (INFO, WARNING, HIGH, CRITICAL) selon motifs.                                             | Un log contenant `root` + `fail` reçoit le tag `bruteforce` et la sévérité `HIGH`. |

## FR-02 : Stockage et Indexation (CTU S2)

| ID      | Exigence                                                                                                                     | Critère d’acceptation |
| ------- | ---------------------------------------------------------------------------------------------------------------------------- | --------------------- |
| FR-02.1 | Indexer les logs normalisés par `timestamp` et `source_ip`.                                                                  | Recherche filtrée sur `source_ip` retourne les résultats en < 1 s. |
| FR-02.2 | Implémenter une politique de rétention (30 jours par défaut).                                                                | Script de nettoyage automatique purge les logs > 30 jours (ou les archive). |
| FR-02.3 | Générer un hash SHA-256 par lot de logs et le stocker séparément.                                                            | Endpoint `/api/v1/audit/verify` confirme l’intégrité du lot. |

## FR-03 : Corrélation d’événements (CTU S3, S6, S7)

| ID      | Exigence                                                                                                                     | Critère d’acceptation |
| ------- | ---------------------------------------------------------------------------------------------------------------------------- | --------------------- |
| FR-03.1 | Exécuter des règles de corrélation définies en JSON (pas codées en dur).                                                     | Ajout/modification d’une règle sans redémarrage complet (reload à chaud). |
| FR-03.2 | Fournir 5 règles couvrant au moins 3 tactiques MITRE ATT&CK (Initial Access, Lateral Movement, Exfiltration).                | Voir Annexe A pour les définitions JSON exactes. |
| FR-03.3 | Gérer des fenêtres temporelles glissantes (ex. 5 échecs en 60 s).                                                            | Utilisation d’un cache Redis avec TTL pour compter les occurrences par IP source. |
| FR-03.4 | Calculer un « Confidence Score » (0-100) par alerte.                                                                         | Une alerte avec score > 80 est automatiquement promue en CRITICAL. |

## FR-04 : Alertes et SOAR (CTU S4)

| ID      | Exigence                                                                                                                     | Critère d’acceptation |
| ------- | ---------------------------------------------------------------------------------------------------------------------------- | --------------------- |
| FR-04.1 | Envoyer des notifications Email (SMTP) et Webhook (Slack/Teams).                                                             | Alerte CRITICAL déclenche un post Slack en < 5 s. |
| FR-04.2 | Exécuter des Playbooks SOAR en mode AUTO (sans humain) et CONFIRM (attente 60 s).                                            | AUTO : blocage IP via iptables simulé. CONFIRM : désactivation compte avec bouton d’annulation sur le dashboard. |
| FR-04.3 | Journaliser toutes les actions SOAR (qui, quoi, quand).                                                                      | Historique complet consultable dans l’interface « Incidents ». |

## FR-05 : Visualisation et Recherche (CTU S5, S6)

| ID      | Exigence                                                                                                                     | Critère d’acceptation |
| ------- | ---------------------------------------------------------------------------------------------------------------------------- | --------------------- |
| FR-05.1 | Dashboard « Crisis Room » plein écran rafraîchi toutes les 5 s.                                                              | Affiche : nb alertes CRITICAL/HIGH, top 5 IP suspectes, volume logs/h. |
| FR-05.2 | Moteur de recherche multi-critères (IP, User, Date, Type).                                                                   | Une requête `source_ip:192.168.1.1 AND severity:HIGH` retourne les logs correspondants. |
| FR-05.3 | Timeline interactive pour le forensique.                                                                                     | Clic sur un événement affiche les 5 min avant/après dans une vue chronologique. |
| FR-05.4 | Fonctionnalité « Pivoter » : clic sur IP/User relance une recherche filtrée.                                                 | Le clic recharge la page avec le filtre appliqué. |

## FR-06 : Sécurité et RBAC (CTU S8)

| ID      | Exigence                                                                                                                     | Critère d’acceptation |
| ------- | ---------------------------------------------------------------------------------------------------------------------------- | --------------------- |
| FR-06.1 | Authentification par JWT.                                                                                                    | L’API renvoie un token valide 1 h après login. |
| FR-06.2 | MFA (TOTP) obligatoire pour Analyste et Admin.                                                                               | Conforme RFC 6238, code 6 chiffres exigé après login. |
| FR-06.3 | RBAC strict : Lecteur (lecture seule), Analyste (lecture + action SOAR), Admin (lecture + action + configuration).           | Un Lecteur ne voit pas le bouton « Bloquer l’IP ». |