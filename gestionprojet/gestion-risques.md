---
title: Gestion des risques
description: 
published: true
date: 2026-07-09T21:54:37.251Z
tags: gestion_projet
editor: markdown
dateCreated: 2026-06-29T16:01:32.898Z
---

# Gestion des Risques – Smart SIEM

## Méthodologie d’Analyse des Risques

L’analyse des risques est conduite en six étapes, révisée à chaque jalon clé.

### 1. Identification des Risques
L’équipe réalise un brainstorming initial, puis affine les risques tout au long du projet. Chaque membre signale les obstacles potentiels dans son domaine. Les risques sont classés par catégories (techniques, organisationnels, cahier des charges, sécurité).

### 2. Évaluation de la Probabilité
Échelle de 1 à 5 :
- **1 – Très faible** (<10 %)
- **2 – Faible** (10–30 %)
- **3 – Moyenne** (30–60 %)
- **4 – Élevée** (60–90 %)
- **5 – Très élevée** (>90 %)

### 3. Évaluation de l’Impact
Échelle de 1 à 5 sur les délais, la qualité, le périmètre et le budget :
- **1 – Négligeable** : tâche secondaire
- **2 – Mineur** : composant non critique, rattrapage rapide
- **3 – Modéré** : composant important, réallocation nécessaire
- **4 – Majeur** : plusieurs composants menacés, délais en danger
- **5 – Critique** : échec global du projet possible

### 4. Calcul du Niveau de Risque
**Niveau = Probabilité × Impact**
- **Élevé (16–25)** : action immédiate requise
- **Moyen (8–15)** : planification d’atténuation nécessaire
- **Faible (1–7)** : surveillance simple

### 5. Définition des Plans de Réponse
Quatre types de réponse possibles :
- **Évitement** : modification du plan projet pour supprimer le risque
- **Transfert** : délégation à un tiers (ex. bibliothèques open source éprouvées)
- **Atténuation** : réduction proactive de la probabilité ou de l’impact
- **Acceptation** : reconnaissance du risque et préparation d’un plan de contingence

### 6. Suivi et Contrôle
Réévaluation lors des points hebdomadaires. Mise à jour de l’état, activation des réponses si le risque se matérialise, ajout des nouveaux risques identifiés.

---

## Identification et Analyse des Risques

### 4.1 Risques Techniques

| ID     | Description                                                                                     | Prob. | Impact | Niveau | Plan de réponse |
|--------|-------------------------------------------------------------------------------------------------|-------|--------|--------|-----------------|
| R-T-01 | **Intégration des composants** – Difficultés à faire communiquer collecte, stockage, corrélation, SOAR, dashboard. | 4 (Élevée) | 4 (Majeur) | 16 (Élevé) | Tests d’intégration continus dès la semaine 1, API documentées (Swagger/OpenAPI), démonstrations d’intégration hebdomadaires. Isoler le composant défaillant avant réintégration. |
| R-T-02 | **Performance de la base de données** – Recherches > 3 s sur 6 mois de logs avec Elasticsearch. | 3 (Moyenne) | 4 (Majeur) | 12 (Moyen) | Optimisation précoce (sharding, refresh_interval), validation avec 1000 logs en S1 puis 100 000 en S2. En dernier recours, réduction de la rétention ou exports CSV. |
| R-T-03 | **Sécurité des communications** – Mauvaise configuration TLS ou vulnérabilités dans les échanges. | 3 (Moyenne) | 5 (Critique) | 15 (Moyen) | Certificats auto-signés en développement, Let's Encrypt en production. Scripts de vérification automatique, formation aux bonnes pratiques. |
| R-T-04 | **Qualité du code** – Code non structuré, non documenté, vulnérabilités. | 3 (Moyenne) | 3 (Modéré) | 9 (Moyen) | Revues de code avant merge, analyseurs statiques, documentation maintenue à jour. |

### 4.2 Risques Organisationnels

| ID     | Description                                                                                     | Prob. | Impact | Niveau | Plan de réponse |
|--------|-------------------------------------------------------------------------------------------------|-------|--------|--------|-----------------|
| R-O-01 | **Disponibilité des membres** – Absence d’un membre clé (maladie, imprévu académique). | 3 (Moyenne) | 4 (Majeur) | 12 (Moyen) | Partage des connaissances, documentation systématique, tâches critiques en binôme, répartition flexible. |
| R-O-02 | **Manque de communication** – Mauvaise coordination, conflits d’intégration, malentendus. | 3 (Moyenne) | 3 (Modéré) | 9 (Moyen) | Stand-up quotidien 10 min, outil de suivi partagé (Trello/Notion), canal Slack/Teams dédié. |
| R-O-03 | **Gestion des versions** – Conflits Git, pertes de modifications. | 2 (Faible) | 2 (Mineur) | 4 (Faible) | Stratégie de branches claire (feature/develop/main), relecture obligatoire, formation Git. |

### 4.3 Risques Liés au Cahier des Charges

| ID     | Description                                                                                     | Prob. | Impact | Niveau | Plan de réponse |
|--------|-------------------------------------------------------------------------------------------------|-------|--------|--------|-----------------|
| R-C-01 | **Incompréhension des exigences** – Mauvaise interprétation des spécifications. | 3 (Moyenne) | 4 (Majeur) | 12 (Moyen) | Lecture collective du cahier des charges, clarification avec les concepteurs, démos hebdomadaires de validation. |
| R-C-02 | **Évolution des exigences** – Ajouts ou modifications en cours de projet. | 2 (Faible) | 3 (Modéré) | 6 (Faible) | Documentation des modifications et impacts, négociation des priorités, ajustement éventuel du périmètre. |

### 4.4 Risques de Sécurité

| ID     | Description                                                                                     | Prob. | Impact | Niveau | Plan de réponse |
|--------|-------------------------------------------------------------------------------------------------|-------|--------|--------|-----------------|
| R-S-01 | **Vulnérabilités dans les bibliothèques** – Dépendances non sécurisées ou obsolètes. | 3 (Moyenne) | 5 (Critique) | 15 (Moyen) | Versions à jour, scan de dépendances (Snyk, Dependabot), privilégier bibliothèques reconnues. |
| R-S-02 | **Compromission des données de test** – Données sensibles accidentellement incluses. | 2 (Faible) | 4 (Majeur) | 8 (Moyen) | Utilisation exclusive de données synthétiques, sensibilisation de l’équipe, jamais de données réelles en test. |

---

## Plan de Réponse aux Risques Majeurs

### R-T-01 : Intégration des Composants (Niveau 16)
- **Tests d’intégration continus** : exécutés à chaque modification du code, dès la semaine 1, pour valider API ↔ BDD ↔ agents.
- **Interfaces standardisées** : API REST documentées (Swagger), format JSON conforme ECS.
- **Démonstrations intégrées hebdomadaires** : validation bout en bout ; identification et correction rapide des composants défaillants.

### R-T-02 : Performance de la Base de Données (Niveau 12)
- **Validation précoce** : tests de performance avec 1 000 logs en S1, montée à 100 000 logs en S2.
- **Optimisation Elasticsearch** : paramétrage des shards, refresh_interval, mappings.
- **Indexation par période** : index quotidiens ou hebdomadaires pour faciliter les purges et la recherche.

### R-O-01 : Disponibilité des Membres de l’Équipe (Niveau 12)
- **Partage des connaissances** : documentation de toutes les décisions techniques, binômage sur les tâches critiques.
- **Supports accessibles** : ressources de cours et documentation en ligne pour l’autonomie.
- **Réaffectation rapide** : redistribution des tâches en fonction des compétences, alerte des encadrants si indisponibilité prolongée.

---

## Plan de Contingence

| Scénario critique                                   | Action de repli                                                                                                           |
| --------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| Échec d’intégration en Semaine 2                    | Réduction du périmètre : report UEBA et SOAR en S3, concentration sur collecte / normalisation / stockage. Fonctionnalités manquantes présentées comme améliorations futures. |
| Performances de recherche insuffisantes             | Limitation de la rétention à 1 mois, mise à disposition d’exports CSV pour les recherches longues. |
| Indisponibilité prolongée d’un membre               | Réaffectation des tâches, ajustement du périmètre, demande de délai supplémentaire aux encadrants. |
| Vulnérabilités de sécurité critiques détectées      | Correction prioritaire, adoption de solutions alternatives si nécessaire (ex. authentification via fournisseur tiers). |

---

## Surveillance et Suivi des Risques

- **Réévaluation hebdomadaire** : mise à jour des probabilités et impacts en fonction de l’avancement et des nouveaux éléments.
- **Identification continue** : tout nouveau risque est immédiatement ajouté au registre et analysé.
- **Déclenchement immédiat** : activation du plan de réponse dès qu’un risque se matérialise, suivi de l’efficacité des actions.