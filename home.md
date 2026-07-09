---
title: Document de gestion projet
description: 
published: true
date: 2026-07-09T21:54:11.433Z
tags: 
editor: markdown
dateCreated: 2026-07-01T11:05:53.851Z
---

# Rapport de Gestion de Projet

**Système de Gestion et d'Analyse des Événements de Sécurité**  
*Cellule Antiterroriste CTU*  
Projet Transversal de 3 semaines | UCAC-ICAM, Douala | Version 1.0 – 25/03/2026

---

## 1. Introduction

Le présent document constitue le **document pivot de gestion de projet** du Smart SIEM, développé dans le cadre du projet transversal de 3ᵉ année du Cycle Ingénieur Informatique de l’UCAC-ICAM. Il a pour vocation de fournir une vision synthétique du projet, d’en présenter le contexte, les objectifs et les contraintes, et de servir de point d’entrée unique vers l’ensemble des livrables de gestion de projet.

Le Smart SIEM est une plateforme logicielle de sécurité qui centralise, normalise, corrèle et analyse des journaux d’événements hétérogènes. Conçue pour équiper la cellule antiterroriste fictive CTU, elle doit détecter les menaces en temps réel et automatiser les réponses aux incidents selon les standards du MITRE ATT&CK.

Ce rapport fédère l’ensemble des documents de gestion produits au cours du projet : analyse de l’existant, spécifications fonctionnelles et non-fonctionnelles, planification, gestion des coûts, analyse des risques, gestion de la qualité, portée et gouvernance. Chaque volet est détaillé dans un document dédié, accessible depuis les sections ci-dessous.

---

## 2. Contexte du Projet

### 2.1 Problématique de cybersécurité

Les équipes de sécurité opérationnelle (SOC) sont confrontées à un volume d’événements en constante augmentation. L’analyse manuelle de plusieurs dizaines de milliers de logs par heure n’est plus tenable : le risque de passer à côté d’une attaque réelle est élevé, et le délai de réponse trop long.

Le Smart SIEM répond à cette problématique en automatisant la chaîne complète de traitement des événements de sécurité :

- Collecter tous les journaux issus de sources hétérogènes (serveurs, pare-feux, endpoints, annuaires) ;
- Normaliser les données dans un format unifié (JSON enrichi) ;
- Corréler les événements en temps réel à l’aide de règles de seuil et de patterns, en s’appuyant sur le référentiel MITRE ATT&CK ;
- Alerter les équipes selon une criticité graduée (INFO à CRITICAL) via email, webhook ou SMS ;
- Automatiser les réponses immédiates (blocage d’IP, désactivation de compte, isolation de machine) à l’aide de playbooks SOAR ;
- Visualiser l’état de sécurité sur des tableaux de bord adaptés aux différents profils d’utilisateurs (analyste, RSSI, auditeur) ;
- Garantir l’intégrité et la traçabilité des logs pour d’éventuelles enquêtes judiciaires (hachage SHA-256, pistes d’audit).

### 2.2 Objectifs du projet

Les objectifs pédagogiques et techniques du Smart SIEM sont les suivants :

1. **Centraliser** les journaux d’événements issus de sources multiples.
2. **Normaliser** ces données dans un Golden Schema JSON unifié.
3. **Détecter** les menaces en temps réel via un moteur de corrélation configurable.
4. **Alerter** automatiquement selon quatre niveaux de criticité.
5. **Automatiser** les réponses aux incidents (blocage, désactivation, isolation) avec journalisation complète.
6. **Visualiser** l’état de sécurité à travers des tableaux de bord interactifs et des rapports PDF.
7. **Sécuriser** la plateforme par chiffrement TLS, authentification MFA, RBAC et intégrité cryptographique.
8. **Analyser** les comportements anormaux (UEBA) et scorer dynamiquement les risques.

### 2.3 Contraintes

| Contrainte          | Description                                                                                  |
| ------------------- | -------------------------------------------------------------------------------------------- |
| **Délai**           | 3 semaines calendaires, 10 jours ouvrés. Deux livrables intermédiaires + soutenance finale. |
| **Équipe**          | 5 étudiants. Les rôles sont définis dans la matrice RACI.                                   |
| **Environnement**   | Fonctionnement en local (VM/Docker) pour la démonstration. Aucune ressource cloud payante.  |
| **Volumétrie**      | Simulation à 1 000 evt/h ; l’architecture doit être scalable.                                |
| **Sécurité**        | TLS 1.2+, authentification MFA (TOTP), RBAC strict.                                         |
| **Budget**          | Prototype intégralement développé sur outils open source ; budget global maîtrisé (cf. document Gestion des Coûts). |

---

## 3. Documents de Gestion de Projet

La bonne conduite du projet Smart SIEM s’appuie sur un ensemble de documents de gestion couvrant l’intégralité du cycle de vie. Chaque volet répond à un besoin précis : analyser l’environnement, formaliser les exigences, planifier, maîtriser les coûts, anticiper les risques, garantir la qualité et piloter la gouvernance.  
Vous trouverez ci-après, pour chaque document, une introduction rappelant son positionnement dans la démarche projet et un objectif synthétique validant sa finalité.

### 3.1 Analyse de l’Existant

**Introduction**  
Avant d’engager toute conception, il est indispensable de cartographier l’écosystème des SIEM du marché (Splunk, QRadar, Wazuh, Sentinel…), d’identifier leurs forces, leurs faiblesses et les architectures éprouvées. Cette étude comparative permet d’ancrer le Smart SIEM dans une réalité technologique et de justifier les choix structurants du projet.

**Objectif du document**  
‑ Établir un état de l’art des solutions SIEM majeures.  
‑ Extraire les meilleures pratiques et les écueils à éviter.  
‑ Argumenter les décisions techniques (langage, stack, infrastructure).  
‑ Servir de référence pour les choix de conception.

📄 **Document complet** : [`analyse_existant.md`](./analyse-de-l'existant.md)

### 3.2 Parties Prenantes et Contraintes

**Introduction**  
La réussite d’un projet transverse dépend de l’identification exhaustive des acteurs qui l’influencent et des contraintes qui le bordent. Ce document cartographie les parties prenantes (enseignants, équipe, jury, pairs académiques), leurs attentes, leur niveau d’implication, et recense l’ensemble des limitations – délais, environnement technique, budget – qui conditionnent le projet.

**Objectif du document**  
‑ Recenser tous les acteurs impactés par le projet.  
‑ Analyser leurs attentes, leur influence et les canaux de communication adaptés.  
‑ Formaliser les contraintes externes et internes pesant sur le projet.  
‑ Garantir que les décisions ultérieures tiennent compte de ces paramètres.

📄 **Document complet** : [`parties_prenantes.md`](./specifications/partieprenantes-contraintes.md)

### 3.3 Gestion de la Portée

**Introduction**  
La dérive fonctionnelle est l’un des risques majeurs d’un projet réalisé en temps limité. Ce document fige le périmètre exact du Smart SIEM : ce qui sera livré, ce qui est exclu, et les conditions mesurables qui détermineront le succès. Il sert de contrat interne entre l’équipe et le commanditaire (fictif, ici la CTU).

**Objectif du document**  
‑ Définir sans ambiguïté les fonctionnalités incluses et exclues.  
‑ Établir des critères d’acceptation et de succès quantifiables.  
‑ Éviter l’ajout de « gold plating » en cours de réalisation.  
‑ Faciliter le contrôle de l’avancement par rapport au périmètre.

📄 **Document complet** : [`portee_projet.md`](./gestionprojet/gestion-portee.md)

### 3.4 Exigences Fonctionnelles et Non-Fonctionnelles

**Introduction**  
Le Smart SIEM est spécifié à travers un corpus d’exigences détaillées, rédigées selon la norme « Le système doit… ». Les exigences fonctionnelles couvrent l’intégralité du pipeline (collecte, normalisation, corrélation, réponse automatisée, UEBA, visualisation). Les exigences non-fonctionnelles définissent les qualités attendues : performances, disponibilité, sécurité, maintenabilité et utilisabilité. Ce document constitue la référence contractuelle pour le développement et les tests.

**Objectif du document**  
‑ Exprimer de manière exhaustive, non ambiguë et vérifiable les besoins fonctionnels.  
‑ Définir les critères de qualité mesurables (temps de réponse, latence, taux d’erreur).  
‑ Servir de base à la rédaction des cas de test et au processus de validation.  
‑ Aligner tous les membres de l’équipe sur ce que le produit final doit accomplir.

📄 **Documents complets** :  
- [`exigences_fonctionnelles.md`](./specifications/exigences-fonctionnelles.md)  
- [`exigences_non_fonctionnelles.md`](./specifications/exigences-non-fonctionnelles.md)

### 3.5 Planification et Gestion des Durées

**Introduction**  
La contrainte de trois semaines calendaires exige une planification rigoureuse. Ce document présente le découpage en tâches (38 activités principales), leur ordonnancement sous forme de diagramme de Gantt, l’identification du chemin critique et l’analyse des marges. Il constitue l’outil de suivi d’avancement partagé par l’équipe.

**Objectif du document**  
‑ Décomposer le projet en lots de travail réalistes et mesurables.  
‑ Identifier les dépendances et le chemin critique pour anticiper les retards.  
‑ Fournir un planning visuel servant de tableau de bord pendant l’exécution.  
‑ Faciliter la prise de décision en cas de dérive (arbitrage priorité/marge).

📄 **Document complet** : [`planning_gestion_durees.md`](./gestionprojet/gestion-duree.md)

### 3.6 Gestion des Coûts

**Introduction**  
Bien que le projet soit pédagogique, une estimation financière réaliste permet de sensibiliser l’équipe à la gestion budgétaire. Ce document ventile le coût total (1 920 500 FCFA) par catégorie (ressources humaines, équipements, divers) et le croise avec la planification pour obtenir des indicateurs de performance (coût par jour, par tâche, par membre).

**Objectif du document**  
‑ Évaluer le coût complet du projet dans un cadre réaliste.  
‑ Sensibiliser l’équipe à la dimension économique d’un projet SI.  
‑ Fournir des indicateurs simples de maîtrise budgétaire.  
‑ Servir de base à d’éventuels ajustements en cours de réalisation.

📄 **Document complet** : [`gestion_couts.md`](./gestionprojet/gestion-couts.md)

### 3.7 Gestion des Risques

**Introduction**  
Tout projet comporte des incertitudes. Ce document recense les risques techniques, organisationnels, liés au cahier des charges et à la sécurité, les évalue selon une matrice Probabilité × Impact, puis définit pour chacun un plan de réponse spécifique. Un plan de contingence global assure la continuité en cas de défaillance majeure.

**Objectif du document**  
‑ Identifier et hiérarchiser les menaces pouvant affecter le projet.  
‑ Déterminer des actions préventives et correctives proportionnées.  
‑ Minimiser l’impact des risques avérés sur les délais, la qualité et la sécurité.  
‑ Rassurer les parties prenantes quant à la maîtrise des incertitudes.

📄 **Document complet** : [`gestion_risques.md`](./gestionprojet/gestion-risques.md)

### 3.8 Gestion de la Qualité

**Introduction**  
La qualité ne se contrôle pas a posteriori, elle se construit tout au long du projet. Ce document définit la politique qualité : objectifs, rôles, critères d’acceptation (fonctionnels, techniques, sécurité), méthodes de contrôle (revues de code, tests unitaires/intégration, audits) et matrice de validation des exigences. Il garantit que chaque livrable satisfait aux standards définis.

**Objectif du document**  
‑ Établir les indicateurs et seuils qualité spécifiques au Smart SIEM.  
‑ Planifier les activités de vérification et de validation à chaque phase.  
‑ Assurer la traçabilité entre exigences, tests et défauts.  
‑ Fournir le référentiel pour les audits internes et les démonstrations hebdomadaires.

📄 **Document complet** : [`gestion_qualite.md`](./gestionprojet/gestion-qualite.md)

### 3.9 Gouvernance et Gestion des Parties Prenantes

**Introduction**  
La structure de gouvernance clarifie qui décide, qui exécute, qui est consulté et qui est informé. Elle repose sur une matrice RACI, des rituels de pilotage (standup quotidien, démo hebdomadaire, revue de code) et des outils de collaboration (Git, Trello/Notion, Slack). Ce volet garantit une communication fluide et une prise de décision rapide, conditions indispensables à un projet intensif.

**Objectif du document**  
‑ Définir l’organisation de l’équipe et les responsabilités de chaque rôle.  
‑ Établir des règles de fonctionnement claires (fréquence des points, canaux, escalades).  
‑ Assurer l’engagement et l’information continue de toutes les parties prenantes.  
‑ Fournir un cadre de pilotage adaptable pour absorber les imprévus sans désorganiser l’équipe.

📄 **Document complet** : [`gouvernance_parties_prenantes.md`](./gestionprojet/gestion-stakeholders.md)

## 4. Conclusion

Le projet Smart SIEM illustre la mise en œuvre concrète des disciplines de la gestion de projet dans un contexte exigeant de cybersécurité et de formation d’ingénieurs.

L’ensemble des documents produits constitue un corpus cohérent et traçable, garantissant que chaque décision technique ou organisationnelle s’appuie sur une analyse préalable. Le découpage en modules: collecte, normalisation, corrélation, réponse automatisée, UEBA, visualisation. Permet un développement progressif et une intégration maîtrisée, conformément aux bonnes pratiques de l’ingénierie logicielle.