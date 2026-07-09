---
title: Gestion des Coûts
description: 
published: true
date: 2026-07-09T21:54:25.834Z
tags: gestion_projet
editor: markdown
dateCreated: 2026-06-29T21:31:13.368Z
---

# Gestion des Coûts – Smart SIEM CTU

## 1. Budget synthétique

| Catégorie                | Montant (FCFA) |
| ------------------------ | -------------- |
| Ressources Humaines      | 1 550 000      |
| Infrastructure technique | 0              |
| Outils et équipements    | 15 000         |
| Déplacements et réunions | 105 000        |
| **Budget hors imprévus** | **1 670 000**  |
| Imprévus (15 %)          | 250 500        |
| **BUDGET FINAL**         | **1 920 500**  |

La part des ressources humaines représente **93 %** du budget total, ce qui est cohérent pour un projet logiciel en environnement académique utilisant exclusivement des technologies open source.

## 2. Ressources Humaines

| Membre             | Rôle                    | H/sem | Semaines | H tot. | Coût horaire (FCFA) | Coût total (FCFA) |
| ------------------ | ----------------------- | ----- | -------- | ------ | ------------------- | ----------------- |
| FOTUE Aris         | Chef de Projet          | 50    | 2        | 100    | 3 000               | 300 000           |
| DJIATSAD           | Dev Backend / Ing.      | 50    | 2        | 100    | 5 000               | 500 000           |
| Dunamis Data (DEMGHUO) | Ingénieur Data       | 50    | 2        | 100    | 2 500               | 250 000           |
| Marie TSAM Alain   | Ingénieur DevOps        | 50    | 2        | 100    | 2 500               | 250 000           |
| MAKOSSO            | Développeur Frontend    | 50    | 2        | 100    | 2 500               | 250 000           |
| **TOTAL**          |                         | **250**| **2**   | **500**|                     | **1 550 000**     |

### Tarifs appliqués

- Chef de Projet : 3 000 FCFA / heure
- Développeur Backend / Data : 5 000 FCFA / heure
- Autres membres (DevOps, Frontend, Data) : 2 500 FCFA / heure

## 3. Infrastructure technique

| Poste                         | Détail                               | Coût (FCFA) |
| ----------------------------- | ------------------------------------ | ----------- |
| Hébergement backend           | Développement local                  | 0           |
| Base de données PostgreSQL    | Installation locale                  | 0           |
| Dépôt Git (GitHub)            | Plan étudiant gratuit                | 0           |
| Elasticsearch                 | Open source                          | 0           |
| Kibana / OpenSearch           | Open source                          | 0           |
| Wazuh Agent                   | Tests comparatifs                    | 0           |
| CI/CD (GitHub Actions)        | Plan étudiant gratuit                | 0           |
| Docker / Docker Compose       | Open source                          | 0           |
| Certificat SSL                | Let's Encrypt (gratuit)              | 0           |
| API sandbox MITRE ATT&CK      | Accès web gratuit                    | 0           |
| **TOTAL INFRASTRUCTURE**      |                                      | **0**       |

> **Remarque** : Des coûts apparaîtraient en environnement de production (AWS, Elastic Cloud, Twilio pour SMS). Ils sont exclus du périmètre académique.

## 4. Outils et équipements

| Poste                                | Qté | Coût (FCFA) |
| ------------------------------------ | --- | ----------- |
| Ordinateurs portables (amortis.)     | 5   | 0           |
| Abonnement WiFi partagé (2 sem.)    | 1   | 15 000      |
| Téléphones de test Android/iOS       | 2   | 0           |
| Câbles et accessoires                | 1   | 0           |
| **TOTAL ÉQUIPEMENTS**                |     | **15 000**  |

## 5. Déplacements et réunions

| Poste                                         | Coût (FCFA) |
| --------------------------------------------- | ----------- |
| Transport (10 réunions × 5 membres × 1 000 F) | 50 000      |
| Impression documents (rapports, livrables)     | 30 000      |
| Frais de soutenance (impression + reliure)    | 25 000      |
| **TOTAL DÉPLACEMENTS**                        | **105 000** |

## 6. Répartition budgétaire par semaine

| Catégorie              | Semaine 1   | Semaine 2   | Total       |
| ---------------------- | ----------- | ----------- | ----------- |
| Ressources Humaines    | 775 000     | 775 000     | 1 550 000   |
| Infrastructure         | 0           | 0           | 0           |
| Équipements            | 15 000      | 0           | 15 000      |
| Déplacements           | 52 500      | 52 500      | 105 000     |
| **Sous-total**         | **842 500** | **827 500** | **1 670 000**|
| Imprévus (15 %)        | 126 375     | 124 125     | 250 500     |
| **Total par semaine**  | **968 875** | **951 625** | **1 920 500**|

## 7. Analyse coûts-durées par domaine fonctionnel

| Domaine          | Tâches | Jours×H | % durée | Coût estimé (FCFA) |
| ---------------- | ------ | ------- | ------- | ------------------ |
| Développement    | 10     | 17      | 27 %    | 442 000            |
| Sécurité         | 7      | 11      | 17 %    | 286 000            |
| Frontend / UEBA  | 7      | 11      | 17 %    | 286 000            |
| Infra & DevOps   | 6      | 10      | 16 %    | 260 000            |
| Data             | 6      | 10      | 16 %    | 260 000            |
| Intégration      | 2      | 2       | 3 %     | 52 000             |
| Livrables        | 2      | 2       | 3 %     | 52 000             |
| **TOTAL**        | **40** | **63**  | **100 %**| **1 638 000**      |

> *Coût calculé sur la base du tarif horaire moyen de l’équipe (2 600 FCFA/h). L’écart avec le budget RH (1 550 000 FCFA) s’explique par la distinction entre jours-homme estimés et heures réelles facturées.*

## 8. Indicateurs de performance (KPIs) financiers

| Indicateur                    | Valeur              |
| ----------------------------- | ------------------- |
| Coût moyen par tâche          | 42 750 FCFA         |
| Coût par jour ouvré           | 167 000 FCFA        |
| Coût par membre par jour      | 33 400 FCFA         |
| Taux RH / budget total        | 93 %                |
| Taux infrastructure / budget  | 0 % (open source)   |
| Tâches sur chemin critique    | 12 sur 38           |
| Imprévus budgétés             | 15 % (250 500 FCFA) |

## 9. Risques budgétaires identifiés et mesures de mitigation

| Risque                                | Prob.  | Impact | Mesure de mitigation                                      |
| ------------------------------------- | ------ | ------ | --------------------------------------------------------- |
| Dépassement de durée sur UEBA (T31)   | Moyenne| Élevé  | Scoring basique prioritaire, ML avancé post-soutenance    |
| Incompatibilité d’outils open source  | Faible | Moyen  | Tests d’intégration dès la Semaine 1 (T05)                |
| Absence d’un membre de l’équipe       | Faible | Élevé  | Binômage des tâches critiques, documentation partagée     |
| Surcharge Semaine 2 (16 tâches)       | Moyenne| Élevé  | Parallélisation maximale, daily standup de 15 min         |
| Problème réseau / WiFi                | Faible | Faible | Hotspot mobile de secours (budgétisé à 15 000 FCFA)       |