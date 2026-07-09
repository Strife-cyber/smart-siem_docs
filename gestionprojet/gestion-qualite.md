---
title: gestion de la qualite
description: 
published: true
date: 2026-07-09T21:54:34.448Z
tags: gestion_projet
editor: markdown
dateCreated: 2026-06-30T08:55:29.487Z
---

# Gestion de la Qualité – Smart SIEM CTU

## 1. Objectifs Qualité

Cinq objectifs principaux guident l’ensemble des actions qualité tout au long des trois semaines du projet.

| Objectif                       | Description                                                                                                                                                                                                 |
| ------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Conformité aux spécifications** | Chaque fonctionnalité (collecte, normalisation, corrélation, alertes, SOAR, UEBA, dashboards, recherche, RBAC, intégrité) doit être implémentée avec précision et vérifiée via la matrice de validation.     |
| **Cohérence entre modules**      | Garantir une intégration fluide entre tous les composants : la collecte alimente la normalisation, qui permet la corrélation, laquelle déclenche les alertes et les playbooks SOAR.                         |
| **Complétude des livrables**     | Chaque livrable hebdomadaire et final doit être complet et autonome. La robustesse de la S1 supporte les développements de la S2 ; la couche d’intelligence de S2 s’appuie sur des données correctement ingérées. |
| **Fiabilité opérationnelle**     | Capacité à traiter 50 000 evt/h, temps de recherche < 3 s, résilience aux pannes réseau grâce au cache des agents.                                                                                         |
| **Sécurité de la plateforme**    | Chiffrement TLS, RBAC strict, journalisation exhaustive, intégrité des logs par SHA-256.                                                                                                                   |

## 2. Rôles et Responsabilités en Matière de Qualité

| Rôle                       | Responsabilités qualité                                                                                                                                                                                     |
| -------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Chef de Projet**         | Responsable global de la qualité. Valide les livrables, organise les revues techniques hebdomadaires, s’assure du traitement des écarts, communique les objectifs qualité à l’équipe.                       |
| **Développeur Backend**    | Qualité du code de l’API REST, des pipelines de normalisation et des playbooks SOAR. Code structuré, documenté, testé.                                                                                     |
| **Développeur Frontend**   | Qualité de l’interface utilisateur, des tableaux de bord et de la recherche. Expérience utilisateur, fluidité, pertinence des visualisations par profil.                                                   |
| **Ingénieur Infrastructure** | Qualité du déploiement des agents, configuration Syslog, stabilité des environnements de démo.                                                                                                             |
| **Ingénieur Data**         | Qualité du modèle de stockage, indexation (< 3 s par requête), politiques de rétention, archivage et intégrité (SHA-256).                                                                                   |
| **Ingénieur DevOps**       | Qualité des processus CI/CD, reproductibilité des déploiements, documentation technique.                                                                                                                   |

## 3. Plan Qualité : Critères et Méthodes de Contrôle

### 3.1 Critères de Qualité Fonctionnelle

| Module                | Critères d’acceptation                                                                                                                                                                                                                               |
| --------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Collecte**          | Réception logs via Syslog UDP/TCP, REST/HTTPS, agents Linux/Windows. Minimum 1 000 logs de test ingérés en Semaine 1.                                                                                                                               |
| **Normalisation**     | JSON enrichi avec les champs obligatoires : `timestamp, source_ip, host, log_type, severity, raw_message`. Tagging automatique (criticité et type).                                                                                                  |
| **Corrélation**       | 5 règles documentées et testées, dont 2 multi-sources. Couvre au moins 2 tactiques MITRE ATT&CK.                                                                                                                                                     |
| **Alertes**           | 4 niveaux (INFO, WARNING, HIGH, CRITICAL), notification via au moins un canal (email ou webhook).                                                                                                                                                    |
| **SOAR**              | 3 playbooks documentés : blocage IP, désactivation compte, notification d’escalade.                                                                                                                                                                  |
| **Recherche**         | Requêtes par IP, utilisateur, type, criticité, plage horaire. Résultats horodatés.                                                                                                                                                                   |
| **Dashboards**        | Minimum 4 vues (Analyste, RSSI, temps réel, investigation). Génération automatique de rapports PDF.                                                                                                                                                  |

### 3.2 Critères de Qualité Technique

| Dimension        | Critère / Seuil                                                                                 |
| ---------------- | ----------------------------------------------------------------------------------------------- |
| **Performance**  | Recherche < 3 s sur 6 mois de logs. Déclenchement playbook SOAR < 30 s.                        |
| **Robustesse**   | Support de pics à 50 000 evt/h sans dégradation. Résilience réseau via cache agents.           |
| **Maintenabilité** | Code structuré, commenté, conventions respectées. Documentation claire, architecture modulaire. |

### 3.3 Critères de Qualité Sécurité

- **TLS** activé sur toutes les interfaces exposées.
- **RBAC** fonctionnel avec 3 rôles (Lecteur, Analyste, Administrateur) et ségrégation par périmètre.
- **Journalisation** exhaustive des actions utilisateurs (connexion, création/modification de comptes, consultation/traitement alertes).
- **Intégrité** : hachage SHA-256 par lot, stockage séparé des empreintes.

### 3.4 Méthodes de Contrôle

| Méthode                | Description                                                                                                                    |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| **Tests unitaires**    | Réalisés par les développeurs sur chaque fonctionnalité, avant intégration. Détection des régressions.                        |
| **Tests d’intégration** | Vérification hebdomadaire de la cohérence collecte → stockage → corrélation → alertes → playbooks.                             |
| **Audits de sécurité** | Chaque fin de semaine : chiffrement, RBAC, traçabilité. Audit final S3 pour conformité RGPD/ISO 27001.                         |
| **Validations pédagogiques** | Revues par les encadrants académiques pour adéquation au cahier des charges et aux objectifs pédagogiques.               |

## 4. Assurance Qualité : Processus de Vérification Continue

### 4.1 Revue Technique Hebdomadaire

Organisée par le Chef de Projet à la fin de chaque semaine :

- **Qualité du code** : structuration, commentaires, couverture des tests unitaires.
- **Conformité architecturale** : respect des orientations du kickoff, justification des écarts.
- **Performances** : tests de charge, benchmarks (temps de réponse, débit, ressources).
- **Problèmes techniques** : recensement, analyse des causes racines, actions correctives.

### 4.2 Revue Fonctionnelle Bimensuelle

Toutes les deux semaines, démonstration en conditions simulées :

- Exécution des scénarios MITRE ATT&CK : brute-force SSH (S3), Pass-the-Hash (S6), exfiltration lente (S7), évasion par suppression de logs (T1070).
- Test des playbooks SOAR : blocage IP, désactivation compte, isolation machine.
- Évaluation des dashboards par profils simulés (Bill Buchanan, Chloe O'Brian, auditeur).

### 4.3 Audit de Sécurité Final (Semaine 3)

- Vérification configuration TLS (certificats, protocoles sécurisés).
- Test du RBAC : tentatives d’accès non autorisées, ségrégation par périmètre.
- Inspection des logs d’audit : exhaustivité, protection contre les altérations.
- Évaluation conformité RGPD (données personnelles, droit à l’oubli) et ISO 27001 (politique de sécurité, gestion des accès, intégrité).

## 5. Contrôle Qualité : Outils et Matrices de Validation

### 5.1 Check-list des Livrables

**Livrable Semaine 1**
- [ ] Infrastructure déployée : ≥ 1 agent Linux/Windows connecté au serveur central
- [ ] API REST opérationnelle : endpoint logs, endpoint santé, documentation Swagger
- [ ] Base de données initialisée : schéma documenté, 1 000 logs simulés ingérés
- [ ] Sécurité de base : TLS actif, RBAC en place
- [ ] README technique avec guide d’installation complet

**Livrable Semaine 2**
- [ ] Moteur de corrélation : 5 règles actives dont 2 multi-sources
- [ ] Système d’alertes fonctionnel : notifications via ≥ 1 canal
- [ ] 3 playbooks SOAR documentés et testés
- [ ] Interface de recherche multi-critères opérationnelle
- [ ] Rapport de tests documentant les scénarios d’attaque simulés

**Livrable Final Semaine 3**
- [ ] Application déployée : tous les composants communiquent
- [ ] Dashboard interactif : ≥ 4 vues (Analyste, RSSI, temps réel, investigation)
- [ ] Rapport PDF auto-généré sur 7 jours simulés
- [ ] Documentation technique complète (architecture, schéma BDD, API, guide de déploiement)
- [ ] Rapport d’audit sécurité (traçabilité, conformité, tests d’intrusion)
- [ ] Code source versionné, dépôt Git propre, historique lisible
- [ ] Soutenance orale : 15 min de présentation + démo live

### 5.2 Matrice de Validation des Exigences Fonctionnelles

| Exigence fonctionnelle                                       | Test associé                                                                 | Statut      |
| ------------------------------------------------------------ | ---------------------------------------------------------------------------- | ----------- |
| Récepteur multi-protocoles (Syslog UDP/TCP, REST/HTTPS)      | Envoi de logs depuis Filebeat, cURL et client REST                           | À valider   |
| Pipeline de normalisation JSON enrichi                       | Vérification des champs sur 1 000 logs ingérés                               | À valider   |
| Tagging automatique (criticité, type)                        | Vérification du tagging sur logs simulés                                     | À valider   |
| Indexation sur timestamp, source, type, criticité            | Requête de recherche sur 30 jours                                            | À valider   |
| Chiffrement TLS sur toutes les communications                | Inspection des certificats et des flux                                       | À valider   |
| Politique de rétention configurable                          | Configuration et validation des purges                                       | À valider   |
| Module d’archivage avec hash SHA-256                         | Vérification de l’intégrité des archives                                     | À valider   |
| Moteur de corrélation threshold + pattern                    | Exécution des scénarios MITRE ATT&CK                                         | À valider   |
| Corrélation inter-sources                                    | Test de corrélation pare-feu ↔ Active Directory                              | À valider   |
| Fenêtres temporelles configurables                           | Test de 5 événements en < 60 s                                               | À valider   |
| Confidence score + suppression des doublons                  | Analyse des alertes générées                                                 | À valider   |
| Système d’alertes 4 niveaux                                  | Génération d’alertes de chaque niveau                                        | À valider   |
| Notification multicanal (email, webhook)                     | Vérification de la réception des notifications                               | À valider   |
| Playbooks SOAR (blocage IP, désactivation compte, isolation) | Test en conditions simulées                                                  | À valider   |
| Tableau de suivi des incidents                               | Simulation de cycle de vie d’incident                                        | À valider   |
| Dashboard Analyste, RSSI, temps réel, investigation          | Vérification des vues par profil                                             | À valider   |
| Vue Crisis Room (plein écran, refresh 5 s)                   | Test en situation de crise simulée                                           | À valider   |
| Rapport PDF quotidien/hebdomadaire                           | Génération et vérification du contenu                                        | À valider   |
| Recherche multi-critères                                     | Tests de recherche sur base de données                                       | À valider   |
| Timeline interactive                                         | Visualisation chronologique d’une attaque simulée                            | À valider   |
| Bouton « Pivoter sur cet indicateur »                        | Test de pivotement sur une IP suspecte                                       | À valider   |
| Authentification MFA (TOTP RFC 6238)                         | Test d’authentification avec TOTP                                            | À valider   |
| RBAC (Lecteur, Analyste, Administrateur)                     | Test d’accès avec différents rôles                                           | À valider   |
| Journalisation des actions utilisateurs                      | Inspection des logs d’audit                                                  | À valider   |
| Ségrégation des accès par périmètre                          | Test d’accès inter-équipes                                                   | À valider   |
| UEBA : Profilage comportemental par entité                   | Validation du profilage sur dataset 30 j                                     | À valider   |
| UEBA : Détection d’anomalies                                 | Détection du scénario Nina Myers                                             | À valider   |
| UEBA : Scoring de risque dynamique (0-100)                   | Vérification de l’évolution du score                                         | À valider   |

### 5.3 Rapports de Tests

Chaque phase de test produit un rapport contenant :
- Périmètre testé et conditions (configuration, données, environnement)
- Résultats (succès, échecs, performances mesurées)
- Anomalies constatées avec niveau de criticité
- Actions correctives entreprises
- Conclusion sur la conformité du composant

## 6. Documentation Qualité

| Document                       | Contenu                                                                                                                                                               |
| ------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Manuel Utilisateur**         | Connexion, navigation dans les dashboards, consultation des alertes, utilisation de la recherche, interaction avec les playbooks. Rédigé en langage accessible, illustré. |
| **Guide Technique**            | Architecture, procédures de déploiement, configuration des agents, politiques de rétention, sauvegarde/restauration, résolution des problèmes courants.                |
| **Rapport Qualité Hebdomadaire** | Avancement, résultats des tests, anomalies et résolutions, risques identifiés, écarts au planning et actions correctives. Produit par le Chef de Projet chaque semaine. |
| **Rapport d’Audit Final**      | Bilan complet : conformité fonctionnelle, performances, sécurité, qualité du code, complétude de la documentation.                                                    |

## 7. Validation Finale
Scénario reproduit devant le jury :
1. Attaque coordonnée : tentative d’accès Pentagone depuis IP de Douala, exfiltration de données, activation d’un compte dormant.
2. Le SIEM corrèle les 3 événements en < 4 s, génère une alerte CRITICAL.
3. Le playbook SOAR s’exécute automatiquement (< 30 s) : isolation du compte, blocage IP, notification email + webhook.
4. La vue Crisis Room affiche en temps réel : 1 alerte CRITICAL, 3 systèmes compromis, 2 playbooks en cours, carte des sources d’attaque.