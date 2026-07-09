---
title: Documentation BDD
description: 
published: true
date: 2026-07-09T21:54:22.852Z
tags: conception
editor: markdown
dateCreated: 2026-07-09T17:22:41.534Z
---

# Smart SIEM CTU — Documentation complète de la base de données

## 1. Versions et technologies utilisés

| Élément | Version / valeur | Source |
|---------|------------------|--------|
| Node.js (image Docker) | 20 | `Dockerfile` |
| NestJS | 11.x | `package.json` |
| Prisma | 7.8.0 | `package.json` |
| @prisma/adapter-pg | 7.8.0 | `package.json` |
| Client Elasticsearch npm | 9.4.2 | `package.json` |
| Serveur Elasticsearch | 9.4.2 | `docker-compose.yml` |
| Kibana | 9.4.3 | `docker-compose.yml` |
| Redis | 7-alpine | `docker-compose.yml` |
| PostgreSQL | `postgres:latest` | `docker-compose.yml` |
| BullMQ | 5.x | `package.json` |

## 2. Architecture des données (vue globale)

```
Collecteurs / API
       │
       ▼
┌──────────────────┐     ┌─────────────────────┐
│ NestJS Backend   │────▶│ PostgreSQL (Prisma) │
│                  │     │ users, incidents,   │
│                  │     │ rules, audit, ueba  │
└────────┬─────────┘     └─────────────────────┘
         │
         ├────────────────▶ Elasticsearch (ctu-logs)
         │                  logs normalisés, recherche
         │
         └────────────────▶ Redis (BullMQ)
                            jobs async (normalize, notify, retention)
```

### Flux principal

1. Les données métier (auth, incidents, règles, audit) vont dans **PostgreSQL**.
2. Les logs normalisés vont dans **Elasticsearch** (alias `ctu-logs`).
3. Les traitements lourds passent par **BullMQ** (Redis).
4. La corrélation lit Elasticsearch, crée des incidents PostgreSQL, peut déclencher SOAR.
5. La rétention archive puis purge les logs anciens (voir section 9).

## 3. PostgreSQL et Prisma

### 3.1 Enums (types énumérés)

| Enum | Valeurs | Usage |
|------|---------|-------|
| `UserRole` | READER, ANALYST, ADMIN | RBAC |
| `SeverityLevel` | INFO, WARNING, HIGH, CRITICAL | Gravité incident |
| `IncidentStatus` | OPEN, IN_PROGRESS, RESOLVED, FALSE_POSITIVE | Cycle de vie |
| `PlaybookMode` | AUTO, CONFIRM | Mode SOAR |
| `ExecutionStatus` | PENDING, EXECUTED, ABORTED, FAILED | État exécution playbook |
| `FlaggedEventStatus` | OPEN, INVESTIGATING, RESOLVED, FALSE_POSITIVE | Enquêtes manuelles |

### 3.2 Tables PostgreSQL (12 principales)

| Table | Rôle | Champs clés |
|-------|------|-------------|
| `users` | Comptes applicatifs | username, password_hash, role, email, mfa_enabled |
| `mfa_sessions` | OTP MFA temporaire | code, expires_at, used |
| `api_keys` | Auth machine-to-machine | key_hash, key_prefix |
| `correlation_rules` | Règles MITRE | definition (JSONB), is_active |
| `incidents` | Alertes SOC | severity, status, related_entities (JSONB) |
| `playbook_executions` | Traçabilité SOAR | playbook_name, mode, status, result_payload |
| `batch_manifests` | Intégrité lots (SHA-256) | sha256_hash, record_count |
| `ueba_profiles` | Profils comportementaux | baseline_data (JSONB), risk_score |
| `audit_trail` | Journal d'actions | action, ip_address, metadata |
| `retention_policies` | Config rétention | duration_days |
| `flagged_events` | Événements flaggés | ingestion_hash, event_snapshot |
| `flagged_event_links` | Liens entre flags | from_event_id, to_event_id |

### 3.3 Relations importantes

- `users` → `incidents` (assignation analyste)
- `correlation_rules` → `incidents` (règle déclenchante)
- `incidents` → `playbook_executions` (exécutions SOAR)
- `users` → `audit_trail` (journalisation)
- `flagged_events` ↔ `flagged_event_links` (graphe d'enquête)

### 3.4 Migrations SQL (6 fichiers)

| Migration | Impact |
|-----------|--------|
| `20260622185112_create_users_table` | Table User legacy (SERIAL) |
| `20260622190844_add_last_login_to_users_table` | Ajout last_login |
| `20260623073020_add_core_siem_tables` | Refonte complète (UUID, incidents, rules, etc.) |
| `20260623191539_add_api_keys_table` | Table api_keys |
| `20260629100000_add_email_mfa` | email, mfa_enabled, mfa_sessions |
| `20260706120000_add_flagged_events` | flagged_events + flagged_event_links |

## 4. Elasticsearch (base des logs)

### 4.1 Rôle

Elasticsearch stocke les **logs normalisés** (Golden Schema). Ce n'est pas une base relationnelle : ce sont des **documents JSON** indexés pour la recherche full-text et les agrégations.

### 4.2 Configuration vérifiée

| Paramètre | Valeur | Fichier |
|-----------|--------|---------|
| Alias d'écriture | `ctu-logs` | `elasticsearch.service.ts` |
| Index initial | `ctu-logs-000001` | idem |
| Template | `ctu-logs-template` | idem |
| Politique ILM | `ctu-30-days-ilm-policy` | idem |
| Rétention ILM | suppression après **30 jours** | phase `delete.min_age: 30d` |
| Rollover | 50 GB ou 7 jours | phase `hot` |

### 4.3 Champs principaux du Golden Schema

| Champ | Type ES | Description |
|-------|---------|-------------|
| `collected_at` | date | Horodatage collecte |
| `normalized_at` | date | Horodatage normalisation |
| `source_type` | keyword | Type source (linux_auth, firewall…) |
| `hostname` | keyword | Machine source |
| `source_ip` / `destination_ip` | ip | Adresses réseau |
| `user_principal` | keyword | Utilisateur |
| `action` / `outcome` | keyword | Action et résultat |
| `severity` | byte | Niveau 0-10 |
| `raw_message` | text | Message brut (recherche full-text) |
| `ingestion_hash` | keyword | SHA-256 (intégrité / dédup) |

### 4.4 Pipeline d'ingestion des logs

```
POST /logs → LogsService.ingest()
    → Queue BullMQ "logs" (job: normalize)
    → LogsProcessor.normalizeLogs() + enrichLog()
    → ElasticsearchService.bulkInsert() → alias ctu-logs
    → Queue BullMQ "ueba" (job: score) si user_principal présent
```

## 5. Redis + BullMQ

Redis sert de **broker** pour les files d'attente BullMQ :

| Queue | Rôle |
|-------|------|
| `logs` | Normalisation + rétention/archivage |
| `ueba` | Scoring comportemental |
| `notifications` | Alertes email incidents |
| `reports` | Nettoyage rapports |
| `correlation` | Cycle de détection (scheduler) |

Configuration dans `app.module.ts` :

```typescript
BullModule.forRoot({
  connection: {
    host: process.env.REDIS_HOST || 'localhost',
    port: Number(process.env.REDIS_PORT) || 6379,
  },
})
```

## 6. Accès aux données par module métier

| Module | Base utilisée | Opérations principales |
|--------|---------------|------------------------|
| Auth | PostgreSQL | login, register, MFA, JWT |
| Admin | PostgreSQL | CRUD users, retention_policies |
| Rules | PostgreSQL | CRUD correlation_rules |
| Correlation | ES + PostgreSQL | détection → incidents |
| Incidents | PostgreSQL | listing, update statut |
| SOAR | PostgreSQL + Firewall API | playbook_executions |
| UEBA | ES + PostgreSQL | baseline ES → profils PG |
| Dashboard | ES + PostgreSQL | stats incidents + agrégations ES |
| Audit | PostgreSQL | audit_trail |
| Reports | ES + PostgreSQL | extraction multi-sources |
| Flagged Events | PostgreSQL | enquêtes manuelles |
| Retention logs | Elasticsearch | archive JSON + purge |

## 7. Sécurité des données

| Mécanisme | Implémentation | Évaluation |
|-----------|----------------|------------|
| Mots de passe | bcryptjs, 12 salt rounds | Bon |
| API keys | SHA-256 + timingSafeEqual | Bon |
| MFA OTP | code en clair en base, TTL 5 min | Moyen |
| Audit trail | action + IP + user-agent + metadata | Bon |
| Secrets Docker | credentials en clair dans docker-compose | À corriger |


## 8. Rétention et archivage des logs

### 8.1 Deux mécanismes coexistent

| Mécanisme | Niveau | Fichier |
|-----------|--------|---------|
| ILM Elasticsearch | Infrastructure (auto) | `src/elasticsearch/elasticsearch.service.ts` |
| Job NestJS applicatif | Application (archive + purge) | `src/logs/logs-retention.service.ts` |

### 8.2 Job applicatif (ajout récent)

**Fichiers impliqués :**

- `src/logs/logs-retention.service.ts` — logique archive + purge
- `src/logs/logs.module.ts` — scheduler BullMQ `30 2 * * *` (02:30 UTC)
- `src/logs/processors/logs.processor.ts` — case `retention-archive`

**Étapes du job :**

1. Calculer la date limite : `now - 30 jours`
2. Lire tous les documents ES avec `collected_at < cutoff` (pagination 1000)
3. Écrire un fichier JSON : `backups/logs/logs-archive-before-<timestamp>.json`
4. Supprimer ces documents via `deleteByQuery`
5. Logger le résultat (archivés / supprimés)

### 8.3 Table `retention_policies` (PostgreSQL)

La table est exposée via l'API Admin (`GET/PUT /admin/retention`), et est branchée au job de rétention Elasticsearch. Les durées sont codées en dur (30 jours) dans le service et l'ILM.

## 9. Glossaire

| Terme | Définition |
|-------|------------|
| **ORM** | Outil qui mappe tables SQL ↔ objets code |
| **Migration** | Évolution versionnée du schéma SQL |
| **JSONB** | Colonne PostgreSQL stockant du JSON indexable |
| **ILM** | Index Lifecycle Management (Elasticsearch) |
| **Alias** | Nom logique pointant vers un ou plusieurs index ES |
| **BullMQ** | Bibliothèque de files d'attente Node.js sur Redis |
| **Golden Schema** | Format normalisé unique des logs SIEM |