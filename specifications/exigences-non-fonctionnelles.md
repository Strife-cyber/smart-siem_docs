---
title: Exigences Non Fonctionnelles
description: 
published: true
date: 2026-07-09T21:54:45.447Z
tags: exigences
editor: markdown
dateCreated: 2026-06-29T14:41:20.790Z
---

# Exigences Non-Fonctionnelles & Interfaces – Smart SIEM CTU

## 1. Performances

| Catégorie       | Exigence                                                                                     | Seuil d’acceptation |
| --------------- | -------------------------------------------------------------------------------------------- | ------------------- |
| **Latence**     | Pipeline ingestion → stockage quasi-instantané.                                              | < 500 ms du `curl` à l’écriture en base. |
| **Débit**       | Le moteur de corrélation doit traiter les flux entrants.                                     | 5 000 événements simulés traités en < 30 s. |
| **Disponibilité**| Résilience aux pannes partielles.                                                           | Si le corrélateur plante, l’API de collecte continue de recevoir les logs (queue). |

## 2. Utilisabilité (UX)

| Exigence                                       | Seuil d’acceptation |
| ---------------------------------------------- | ------------------- |
| La « Crisis Room » doit être compréhensible en 10 secondes. | Un test utilisateur (étudiant non formé) décrit la situation globale sans explications préalables. |

## 3. Maintenabilité

- Code documenté : Toutes les API disposent d’une documentation Swagger/OpenAPI accessible via `/docs`.
- Règles de corrélation externalisées en JSON (FR-03.1).

## 4. Sécurité

- Communications chiffrées : TLS 1.2+ activé sur tous les endpoints REST.
- Authentification forte : MFA (TOTP) pour Analyste et Admin (FR-06.2).
- Hachage d’intégrité : SHA-256 par lot de logs (FR-02.3).

## 5. Interfaces Externes

### Interface Utilisateur (IHM)
- Framework : React ou Vue.js (au choix de l’équipe).
- Communication : REST/HTTPS (JSON).

### API REST
| Endpoint              | Méthode | Description |
| --------------------- | ------- | ----------- |
| `/api/v1/logs`        | POST    | Ingestion de logs (JSON body). |
| `/api/v1/health`      | GET     | Santé du système → `{ status: "UP" }`. |
| `/api/v1/search`      | GET     | Recherche multi-critères (`?q=...&from=...&to=...`). |
| `/api/v1/audit/verify`| POST    | Vérification d’intégrité d’un lot de logs. |

### Interfaces Matérielles (simulation)
- Port Syslog UDP : `514` (ouvert dans le conteneur Docker).
- Port Web : `8080` (HTTP) / `8443` (HTTPS).

## 6. Annexe – Règles de corrélation JSON (extrait)

```json
{
  "id": "R001",
  "name": "SSH Brute Force",
  "tactic": "Initial Access",
  "query": "event_type:auth AND action:fail",
  "group_by": "source_ip",
  "window_seconds": 60,
  "threshold": 5,
  "severity": 7,
  "soar_playbook": "block_ip"
}