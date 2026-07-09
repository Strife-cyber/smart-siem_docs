---
title: Parties prenantes et contraintes
description: 
published: true
date: 2026-07-09T21:54:47.676Z
tags: parties_prenantes
editor: markdown
dateCreated: 2026-06-29T14:47:31.796Z
---

# Parties Prenantes & Contraintes – Smart SIEM CTU

## 1. Acteurs (Personas)

| Acteur                | Rôle dans le SIEM            | Besoins critiques (Voice of Customer)                                                                                                             |
| --------------------- | ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Chloe O'Brian**     | Analyste Senior              | Corrélations en < 4 s ; pivot IP → logs en 1 clic ; taux de faux positifs minimal.                                                               |
| **Jack Bauer**        | Agent de terrain             | Réception des alertes CRITICAL sur mobile (SMS/Webhook) en < 30 s, même hors réseau CTU.                                                          |
| **Bill Buchanan**     | Directeur CTU                | Vue synthétique « Crisis Room » compréhensible en < 10 s pour décision de mission.                                                                |
| **Tony Almeida**      | Ops Data / Auditeur          | Export de logs intègres (hash SHA-256) pour enquêtes judiciaires ; configuration des durées de rétention.                                          |

## 2. Besoins métier par acteur

- **Chloe** : Fiabilité de la détection, rapidité d’investigation, interface de forensique interactive.
- **Jack** : Mobilité, réception d’alertes push, informations essentielles (IP, type d’attaque, localisation).
- **Bill** : Tableau de bord stratégique, indicateurs clairs (menaces en cours, top IP suspectes), temps de chargement garanti.
- **Tony** : Intégrité des preuves numériques, politique de rétention, auditabilité des actions.

## 3. Contraintes organisationnelles et techniques

| Contrainte          | Détail                                                                                                                             |
| ------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| **Délai**           | 3 semaines calendaires, livrables hebdomadaires obligatoires.                                                                     |
| **Effectif**        | Équipe de 5 étudiants (rôles définis dans la matrice RACI).                                                                       |
| **Environnement**   | Fonctionnement local sur VM/Docker pour la démonstration.                                                                         |
| **Volumétrie cible**| Simulation à 1 000 evt/h (architecture prévue pour supporter 2M evt/h).                                                           |
| **Sécurité**        | Chiffrement TLS 1.2+ en transit ; authentification MFA obligatoire pour Analyste et Admin.                                        |

## 4. Exigences de haut niveau déduites des besoins

- **Disponibilité** : La collecte de logs doit continuer même si le moteur de corrélation s’arrête (queue résiliente).
- **Traçabilité** : Chaque alerte et action SOAR doit être journalisée (qui, quoi, quand).
- **Simplicité** : La « Crisis Room » doit permettre une compréhension immédiate par un non-expert.