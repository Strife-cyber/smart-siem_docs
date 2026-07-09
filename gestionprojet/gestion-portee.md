---
title: Gestion de la portée
description: 
published: true
date: 2026-07-09T21:54:31.568Z
tags: gestion_projet
editor: markdown
dateCreated: 2026-06-29T14:49:55.354Z
---

# Portée du Projet – Smart SIEM CTU

## 1. Périmètre fonctionnel

| Domaine                    | Détail des fonctionnalités incluses                                                                                             | Livrables / Preuves attendues                       |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------- |
| **Collecte de logs**       | Journaux Linux réels, générateurs Faker, datasets SANS/Splunk BOTS via Syslog, JSON, CSV. Agents déployables sur Linux/Windows. | Au moins 1 agent fonctionnel, ingestion multi-protocoles |
| **Normalisation**          | Pipeline de transformation en JSON enrichi : `timestamp, source_ip, host, log_type, severity, raw_message`.                    | Schéma JSON documenté, logs normalisés consultables |
| **Stockage sécurisé**      | Base de données indexée, chiffrement TLS au repos et en transit, hachage SHA-256, rétention configurable (30j par défaut).     | Base interrogeable, hash vérifiable via API         |
| **Corrélation et détection** | Moteur de règles (seuil + pattern). Scénarios MITRE ATT&CK : reconnaissance, mouvement latéral, exfiltration.                 | 5 règles JSON actives, alertes générées lors de simulations |
| **Alertes et SOAR**        | 4 niveaux (INFO, WARNING, HIGH, CRITICAL). 3 playbooks (blocage IP, désactivation compte, isolation machine) – Semaine 3.     | Notifications email/webhook en <5s, playbooks exécutés automatiquement et tracés |
| **UEBA** (comportemental)  | Profilage horaires/volumes, détection d’anomalies, scoring de risque dynamique – Semaine 3.                                   | Scénario « connexion hors horaires » détecté et scoré |
| **Visualisation & reporting** | 3 vues dashboard (analyste, auditeur, admin). Rapport PDF auto-généré, export CSV/Excel.                      | Dashboard interactif, PDF produit par le système sur 7 jours simulés |
| **Gestion des accès**      | Authentification locale, RBAC (Lecteur, Analyste, Administrateur), journalisation de toutes les actions utilisateur.          | Audit trail complet, interfaces restreintes selon rôle |
| **Environnement de déploiement** | Local ou conteneurisé (Docker), certificats auto-signés ou Let's Encrypt.                                          | Fichier `docker-compose.yml`, procédure de déploiement documentée |
| **Tests de sécurité**      | Simulations d’attaques via Caldera ou scripts Bash, tests d’intrusion sur l’environnement de développement.                  | Rapport de tests, scénarios documentés              |

## 2. Hors périmètre (exclusions)

| Élément exclu                           | Justification                                                                                                 |
| --------------------------------------- | ------------------------------------------------------------------------------------------------------------- |
| Connexion à un SI d’entreprise réel     | Contexte académique, pas d’accès aux systèmes de production, données sensibles protégées.                    |
| Déploiement cloud public (AWS, Azure…)  | Hors budget étudiant, inutile pour la démonstration des compétences visées.                                  |
| Conformité réglementaire certifiée      | RGPD/ISO 27001 simulés à titre pédagogique uniquement, sans valeur juridique.                                |
| Outils tiers payants ou sous licence    | Aucune licence commerciale autorisée (ex. Splunk, QRadar). Seules les solutions open source sont admises.    |
| Authentification fédérée (LDAP, SSO…)  | Non exigée par les spécifications ; l’authentification locale suffit.                                        |
| Haute disponibilité / clustering        | L’infrastructure mono-nœud répond aux besoins de développement et de démonstration.                         |

## 3. Conditions de succès (critères d’acceptation finaux)

| Condition                                                                                   | Critère de vérification mesurable                                       | Priorité  |
| ------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------- | --------- |
| Le système reçoit, normalise et stocke des logs de manière sécurisée                        | 1 000 logs simulés ingérés et interrogeables, TLS actif, hachage vérifié | Critique  |
| 5 règles de corrélation actives détectent des scénarios MITRE ATT&CK                        | Alertes générées lors des tests d’intrusion simulés, documentées        | Critique  |
| 3 playbooks SOAR exécutent une action automatique vérifiable                                | Blocage IP, désactivation compte, isolation machine tracés et démontrés | Critique  |
| Dashboard opérationnel avec 3 vues profil                                                   | Démonstration en direct devant le jury, sans explications préalables    | Critique  |
| Premier modèle UEBA fonctionnel (détection d’anomalie)                                      | Scénario « connexion hors horaires » détecté et scoré                   | Important |
| Documentation technique complète livrée                                                     | Architecture, API, schéma BDD, guide de déploiement, README             | Important |
| Rapport PDF auto-généré sur 7 jours simulés                                                 | Fichier PDF produit par le système lui-même, contenu cohérent           | Important |
| Code source propre, branché, historique lisible                                             | Dépôt Git avec README complet, branches fonctionnelles, commits clairs  | Standard  |

## 4. Traçabilité avec les fonctionnalités principales

| Condition de succès                              | Fonctionnalité associée (section cahier des charges) |
| ------------------------------------------------ | ---------------------------------------------------- |
| Ingestion & stockage sécurisés                   | 3.1 Collecte et Normalisation, 3.2 Stockage          |
| Règles de corrélation actives                    | 3.3 Corrélation d’événements                         |
| Playbooks SOAR                                   | 3.4 Alertes et Réponse aux incidents                 |
| Dashboard 3 vues                                 | 3.5 Visualisation et Reporting                       |
| Modèle UEBA                                      | 3.8 Analyse Comportementale (UEBA)                   |
| Documentation & rapport PDF                      | 3.5 Reporting, 5.3 Livrable final                    |
| Propreté du code                                 | 6. Critères d’évaluation, qualité technique          |