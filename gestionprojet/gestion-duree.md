---
title: Gestion de la duree
description: 
published: true
date: 2026-07-09T21:54:28.626Z
tags: gestion_projet
editor: markdown
dateCreated: 2026-07-01T11:03:02.174Z
---

# Planning et Gestion des Durées – Smart SIEM CTU

## 1. Fiche synthétique du projet

| Paramètre          | Valeur                                                   |
| ------------------ | -------------------------------------------------------- |
| Projet             | Smart SIEM – Système de Détection et Réponse Intelligent |
| Établissement      | UCAC-ICAM, Douala, Cameroun                              |
| Date de début      | 22 juin 2026 (lundi)                                     |
| Date de fin        | 3 juillet 2026 (vendredi)                                |
| Durée totale       | 10 jours ouvrés (2 semaines)                             |
| Nombre de tâches   | 38 tâches opérationnelles + 2 livrables                  |
| Effectif           | 5 membres                                                |

## 2. Périmètre fonctionnel

Le Smart SIEM est décomposé en 6 domaines fonctionnels, répartis sur les deux semaines :

1. **Infra & DevOps** : installation de l’environnement, déploiement des agents Syslog, CI/CD, audit final.
2. **Développement** : API REST, pipeline de normalisation, moteur de corrélation, alertes SOAR.
3. **Data** : modèle de base de données, indexation, ingestion, rétention, recherche.
4. **Sécurité** : TLS, RBAC, SHA-256, scénarios MITRE ATT&CK, playbooks SOAR (AUTO et CONFIRM).
5. **Frontend / UEBA** : dashboards Analyste, RSSI, Crisis Room, vue Auditeur, profilage comportemental, scoring dynamique.
6. **Intégration & Livrables** : tests de bout en bout, documentation, soutenance.

## 3. Vue d’ensemble du calendrier

| Semaine   | Début         | Fin           | Jours ouvrés | Thème                                      |
| --------- | ------------- | ------------- | ------------ | ------------------------------------------ |
| Semaine 1 | 22 juin 2026  | 26 juin 2026  | 5            | Fondations & Intelligence                  |
| Semaine 2 | 29 juin 2026  | 3 juillet 2026| 5            | Automatisation, Visualisation & Soutenance |
| **Total** | **22/06/2026**| **03/07/2026**| **10**       |                                            |

## 4. Liste des tâches, durées et dépendances

| ID   | Tâche                                                         | Durée (j) | Début au plus tôt* | Prédécesseurs          |
| ---- | ------------------------------------------------------------- | --------- | ------------------ | ---------------------- |
| T01  | Installation de l’environnement de développement               | 2         | 22/06              | –                      |
| T02  | Déploiement des agents de collecte Syslog                     | 2         | 22/06              | T01                    |
| T03  | Création de l’API REST réceptrice des logs                    | 3         | 22/06              | T01                    |
| T04  | Conception du modèle de BDD orienté logs                      | 2         | 22/06              | –                      |
| T05  | Configuration Syslog UDP/TCP + test de réception              | 2         | 23/06              | T02                    |
| T06  | Pipeline de normalisation JSON                                | 2         | 24/06              | T03                    |
| T07  | Tagging automatique : criticité et type                       | 2         | 25/06              | T06                    |
| T08  | Indexation sur timestamp, source, type, criticité             | 2         | 23/06              | T04                    |
| T09  | Activation TLS sur toutes les communications                  | 2         | 23/06              | T01                    |
| T10  | Endpoint de santé + documentation Swagger/OpenAPI              | 2         | 25/06              | T03                    |
| T11  | Moteur de corrélation — règles threshold                      | 2         | 25/06              | T06, T07, T08          |
| T12  | Pipeline d’ingestion + 1 000 logs simulés                     | 2         | 24/06              | T08, T05               |
| T13  | Politique de rétention configurable                           | 2         | 24/06              | T08                    |
| T14  | Configuration RBAC de base                                    | 2         | 24/06              | T09, T03               |
| T15  | Module d’archivage SHA-256                                    | 2         | 25/06              | T12                    |
| T16  | Moteur de corrélation — règles séquentielles                  | 2         | 26/06              | T11                    |
| T17  | Fenêtres temporelles configurables                            | 2         | 27/06              | T11, T16               |
| T18  | Système d’alertes 4 niveaux                                   | 2         | 27/06              | T11, T16               |
| T19  | Définition de 5 scénarios MITRE ATT&CK                        | 2         | 27/06              | T11, T16               |
| T20  | Moteur de recherche multi-critères                            | 2         | 25/06              | T12, T13               |
| T21  | **LIVRABLE S1** – Infrastructure, Fondations & Corrélation   | 1         | 28/06              | T10, T15, T17          |

| T22  | Playbook SOAR AUTO — blocage IP sur pare-feu                  | 1         | 29/06              | T18, T19               |
| T23  | Playbook SOAR AUTO — désactivation de compte AD               | 1         | 29/06              | T18, T19               |
| T24  | Playbook SOAR CONFIRM — délai 60 s (comptes internes)         | 2         | 30/06              | T22, T23               |
| T25  | Scoring de confiance + suppression des doublons               | 1         | 29/06              | T17, T18               |
| T26  | Dashboard principal : top alertes, flux suspects              | 1         | 30/06              | T18, T25               |
| T27  | Vue Analyste (logs bruts, timeline, pivot)                    | 2         | 30/06              | T20, T26               |
| T28  | Vue RSSI (synthétique, KPIs, carte d’attaque)                 | 2         | 30/06              | T26                    |
| T29  | Visualisation timeline horodatée (forensique)                 | 1         | 30/06              | T20                    |
| T30  | Vue Crisis Room — plein écran, feux tricolores                | 1         | 01/07              | T26, T28               |
| T31  | UEBA — profil comportemental, scoring 0-100                   | 2         | 01/07              | T25, T12               |
| T32  | Notification multicanal : email + webhook                     | 1         | 01/07              | T24, T25               |
| T33  | Documentation technique (archi, BDD, API)                     | 2         | 01/07              | T21                    |
| T34  | Intégration end-to-end + tests de bout en bout                | 1         | 02/07              | T30, T31, T32, T33     |
| T35  | Audit de sécurité final + hardening                           | 1         | 02/07              | T14, T34               |
| T36  | Générateur de rapports PDF automatiques                       | 1         | 02/07              | T27, T28, T29          |
| T37  | Préparation + répétition démo CTU live                        | 1         | 02/07              | T34, T35, T36          |
| T38  | **SOUTENANCE ORALE** – 15 min + démo CTU live                 | 1         | 03/07              | T37                    |

*Les dates sont données en jours calendaires consécutifs. Le 27/06 (samedi) et le 28/06 (dimanche) ne sont pas travaillés ; les dates « début au plus tôt » en tiennent compte.*

## 5. Analyse PERT

L’analyse PERT identifie le chemin critique, les marges de chaque tâche et les leviers de pilotage des délais. Les calculs sont effectués en **jours ouvrés**, le jour 1 étant le lundi 22 juin 2026.

### 5.1 Dates au plus tôt, au plus tard et marges

| ID   | Durée | Début au + tôt | Fin au + tôt | Début au + tard | Fin au + tard | Marge totale | Chemin critique ? |
|------|-------|----------------|--------------|-----------------|---------------|--------------|-------------------|
| T01  | 2     | 1              | 2            | 1               | 2             | 0            | ✓                 |
| T02  | 2     | 3              | 4            | 5               | 6             | 2            |                   |
| T03  | 3     | 3              | 5            | 3               | 5             | 0            | ✓                 |
| T04  | 2     | 1              | 2            | 4               | 5             | 3            |                   |
| T05  | 2     | 5              | 6            | 7               | 8             | 2            |                   |
| T06  | 2     | 6              | 7            | 6               | 7             | 0            | ✓                 |
| T07  | 2     | 8              | 9            | 8               | 9             | 0            | ✓                 |
| T08  | 2     | 3              | 4            | 6               | 7             | 3            |                   |
| T09  | 2     | 3              | 4            | 7               | 8             | 4            |                   |
| T10  | 2     | 8              | 9            | 10              | 11            | 2            |                   |
| T11  | 2     | 9              | 10           | 9               | 10            | 0            | ✓                 |
| T12  | 2     | 6              | 7            | 9               | 10            | 3            |                   |
| T13  | 2     | 6              | 7            | 9               | 10            | 3            |                   |
| T14  | 2     | 6              | 7            | 10              | 11            | 4            |                   |
| T15  | 2     | 8              | 9            | 11              | 12            | 3            |                   |
| T16  | 2     | 11             | 12           | 11              | 12            | 0            | ✓                 |
| T17  | 2     | 13             | 14           | 14              | 15            | 1            |                   |
| T18  | 2     | 13             | 14           | 13              | 14            | 0            | ✓                 |
| T19  | 2     | 13             | 14           | 14              | 15            | 1            |                   |
| T20  | 2     | 8              | 9            | 12              | 13            | 4            |                   |
| T21  | 1     | 14             | 14           | 15              | 15            | 1            |                   |
| T22  | 1     | 15             | 15           | 15              | 15            | 0            | ✓                 |
| T23  | 1     | 15             | 15           | 16              | 16            | 1            |                   |
| T24  | 2     | 16             | 17           | 16              | 17            | 0            | ✓                 |
| T25  | 1     | 14             | 14           | 16              | 16            | 2            |                   |
| T26  | 1     | 15             | 15           | 17              | 17            | 2            |                   |
| T27  | 2     | 16             | 17           | 19              | 20            | 3            |                   |
| T28  | 2     | 16             | 17           | 19              | 20            | 3            |                   |
| T29  | 1     | 10             | 10           | 16              | 16            | 6            |                   |
| T30  | 1     | 17             | 17           | 20              | 20            | 3            |                   |
| T31  | 2     | 15             | 16           | 17              | 18            | 2            |                   |
| T32  | 1     | 18             | 18           | 18              | 18            | 0            | ✓                 |
| T33  | 2     | 15             | 16           | 18              | 19            | 3            |                   |
| T34  | 1     | 19             | 19           | 19              | 19            | 0            | ✓                 |
| T35  | 1     | 20             | 20           | 21              | 21            | 1            |                   |
| T36  | 1     | 18             | 18           | 21              | 21            | 3            |                   |
| T37  | 1     | 21             | 21           | 21              | 21            | 0            | ✓                 |
| T38  | 1     | 22             | 22           | 22              | 22            | 0            | ✓                 |

*Jours ouvrés numérotés de 1 (22/06) à 22 (03/07). Les weekends 27-28 juin sont exclus.*

### 5.2 Chemin critique

Le chemin critique, formé des tâches sans marge totale, est :
T01(2j) → T03(3j) → T06(2j) → T07(2j) → T11(2j) → T16(2j) → T18(2j)
→ T22(1j) → T24(2j) → T32(1j) → T34(1j) → T37(1j) → T38(1j)

**Longueur du chemin critique** : 19 jours ouvrés sur les 22 disponibles. Aucun retard n’est permis sur ces tâches sous peine de décaler la soutenance.

### 5.3 Diagramme PERT – vue simplifiée du chemin critique

Le diagramme ci-dessous met en évidence les 13 tâches du chemin critique (en **rouge**) et les liaisons principales.

```mermaid
graph LR
  T01["T01 : 2j"] --> T03["T03 : 3j"]
  T03 --> T06["T06 : 2j"]
  T06 --> T07["T07 : 2j"]
  T07 --> T11["T11 : 2j"]
  T11 --> T16["T16 : 2j"]
  T16 --> T18["T18 : 2j"]
  T18 --> T22["T22 : 1j"]
  T22 --> T24["T24 : 2j"]
  T24 --> T32["T32 : 1j"]
  T32 --> T34["T34 : 1j"]
  T34 --> T37["T37 : 1j"]
  T37 --> T38["T38 : 1j"]

  style T01 fill:#FF6666,stroke:#333
  style T03 fill:#FF6666,stroke:#333
  style T06 fill:#FF6666,stroke:#333
  style T07 fill:#FF6666,stroke:#333
  style T11 fill:#FF6666,stroke:#333
  style T16 fill:#FF6666,stroke:#333
  style T18 fill:#FF6666,stroke:#333
  style T22 fill:#FF6666,stroke:#333
  style T24 fill:#FF6666,stroke:#333
  style T32 fill:#FF6666,stroke:#333
  style T34 fill:#FF6666,stroke:#333
  style T37 fill:#FF6666,stroke:#333
  style T38 fill:#FF6666,stroke:#333
  ```
## 6. Synthèse globale des durées par domaine

| Domaine          | Nb tâches | Jours min | Jours max | Durée totale (j) |
| ---------------- | --------- | --------- | --------- | ---------------- |
| Infra & DevOps   | 6         | 1         | 2         | 10               |
| Développement    | 10        | 1         | 3         | 17               |
| Data             | 6         | 1         | 2         | 10               |
| Sécurité         | 7         | 1         | 2         | 11               |
| Frontend / UEBA  | 7         | 1         | 2         | 11               |
| Intégration      | 2         | 1         | 1         | 2                |
| Livrables        | 2         | 1         | 1         | 2                |
| **TOTAL**        | **40**    | **6**     | **13**    | **63**           |

## 7. Analyse des marges et parallélisation

- **Tâches sans prédécesseur** : T01 et T04 démarrent le projet en parallèle.
- **Semaine 1** : La majeure partie des tâches hors chemin critique (T02, T04, T08, T09, T10, T12, T13, T14, T20) disposent de marges de 2 à 4 jours, offrant une certaine flexibilité.
- **Semaine 2** : Les marges sont plus faibles ; seules T23, T25, T27, T28, T29, T30, T31, T33, T35, T36 ont de la marge (1 à 6 jours). Les tâches du chemin critique (T22, T24, T32, T34, T37) exigent une exécution sans retard.
- **Risques** : Tout dépassement sur T03 (API), T06 (normalisation), T11 (règles threshold), T18 (alertes), T22/T24 (playbooks) ou T34 (intégration) reporte directement la soutenance.

## 8. Indicateurs de performance (KPIs) du planning

| Indicateur                         | Valeur                                |
| ---------------------------------- | ------------------------------------- |
| Durée totale du projet (j ouvrés)  | 10 (du 22/06 au 03/07)                |
| Nombre total de tâches             | 40 (38 + 2 livrables)                 |
| Tâches sur le chemin critique      | 13                                    |
| Marge moyenne (hors critique)      | 2,6 jours                             |
| Taux de parallélisation S1         | 55 %                                  |
| Taux de parallélisation S2         | 65 %                                  |
| Durée quotidienne de travail       | 10 h/jour (sprint académique)         |
| Jours de marge avant soutenance    | 0 (date butoir)                       |