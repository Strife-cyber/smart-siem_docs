---
title: Gestion des parties prenantes
description: 
published: true
date: 2026-07-09T21:54:39.771Z
tags: gestion_projet
editor: markdown
dateCreated: 2026-06-29T15:29:29.971Z
---

# Parties Prenantes & Gouvernance – Smart SIEM

## 1. Identification des parties prenantes

| Partie prenante                | Catégorie              | Rôle dans le projet                                                                 | Attentes principales                                                                 |
| ------------------------------ | ---------------------- | ----------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| Enseignant / Concepteur (DN & DP) | Donneur d’ordre       | Définit le cahier des charges, valide les spécifications      | Respect des spécifications, qualité technique, autonomie des équipes                |
| Équipe de 5 étudiants       | Main d’œuvre         | Analyse, conçoit, développe et livre le Smart SIEM                                  | Monter en compétences, réussir la soutenance, livrer un produit fonctionnel          |
| Jury de soutenance             | Évaluateur final       | Évalue la démonstration finale (Jour 16), pose les questions techniques             | Démo fonctionnelle, maîtrise individuelle, clarté de la présentation                 |

## 2. Équipe projet – Rôles et responsabilités

L’équipe est composée de 4 à 6 étudiants. Un membre peut cumuler deux rôles.

| Rôle                    | Responsabilités principales                                                                 | Compétences clés attendues                          |
| ----------------------- | ------------------------------------------------------------------------------------------- | --------------------------------------------------- |
| Chef de projet          | Coordination, validation des livrables, définition des règles de corrélation, supervision sécurité | Gestion de projet, vision transverse, communication |
| Développeur Backend     | API REST, logique de traitement des logs, playbooks SOAR                                    | Python/FastAPI ou Node.js, sécurité, bases de données |
| Développeur Frontend    | Tableaux de bord, interfaces de recherche, rapports visuels                                 | React/Vue.js, D3.js/Chart.js, UX                    |
| Ingénieur Infrastructure| Agents de collecte, flux Syslog, environnements de déploiement                              | Linux, Docker, réseaux, protocoles                  |
| Ingénieur Data          | Modèle de stockage, pipelines de normalisation, politiques de rétention, exports            | Elasticsearch/PostgreSQL, ETL, modélisation          |
| Ingénieur DevOps        | CI/CD, administration des environnements, supervision                                       | Git, Docker, scripts, intégration continue          |

## 3. Niveaux d’implication dans le temps

| Partie prenante              | Jour 0 (Kickoff) | Semaine 1 (S1) | Semaine 2 (S2) | Semaine 3 (S3)     |
| ---------------------------- | ---------------- | -------------- | -------------- | ------------------ |
| Enseignant / Concepteur      | Consulté         | Informé        | Informé        | Informé + validation |
| Équipe étudiante             | Acteur principal | Acteur principal| Acteur principal| Acteur principal    |
| Jury de soutenance           | –                | –              | –              | Évaluateur (Jour 16)|

## 4. Canaux de communication

| Canal                   | Objectif                                                                 | Fréquence / Moment          | Participants concernés                     |
| ----------------------- | ------------------------------------------------------------------------ | --------------------------- | ------------------------------------------ |
| Standup quotidien       | Synchronisation : fait, à faire, blocages                               | 10 min chaque jour          | Équipe complète                            |
| Dépôt Git partagé       | Versionnement du code, branches par fonctionnalité | Continu                     | Tous les développeurs                      |
| Outil de backlog        | Suivi des tâches, bugs             | Mis à jour quotidiennement  | Chef de projet + équipe                    |
| Communication enseignant| Validation des orientations, points de blocage                          | Via canal convenu en Jour 0 | Chef de projet / équipe si nécessaire      |

## 5. Matrice RACI simplifiée (phases clés)

| Activité / Phase                  | Chef de projet | Dev Backend | Dev Frontend | Ing. Infra | Ing. Data | Ing. DevOps | Enseignant | Pairs acad. |
| --------------------------------- | -------------- | ----------- | ------------ | ---------- | --------- | ----------- | ---------- | ----------- |
| Spécification et architecture     | R,A            | R           | R            | R          | R         | R           | C,I        | I           |
| Développement S1 (collecte, API)  | A              | R           | –            | R          | R         | R           | I          | I           |
| Développement S2 (corrélation)    | A              | R           | –            | –          | R         | R           | I          | C           |
| Développement S3 (IHM, rapports)  | A              | R           | R            | –          | R         | R           | I          | –           |
| Tests et validation               | A              | R           | R            | R          | R         | R           | I          | C           |
| Démo et soutenance                | R,A            | R           | R            | R          | R         | R           | I          | I           |

*Légende : R = Responsable, A = Approbateur, C = Consulté, I = Informé*