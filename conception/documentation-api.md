---
title: Documentation API
description: 
published: true
date: 2026-07-09T21:54:20.060Z
tags: conception
editor: markdown
dateCreated: 2026-07-09T18:35:45.878Z
---

# Documentation Swagger pour Smart SIEM CTU – API Backend

Ceci est la spécification OpenAPI pour le backend Smart SIEM CTU. L'API couvre l'ingestion de journaux, la corrélation, l'automatisation SOAR, l'UEBA, la criminalistique et plus encore, selon les exigences fonctionnelles FR-01 à FR-08.

---

## Aperçu des points de terminaison

| Catégorie                | Description |
|--------------------------|-------------|
| **App**                  | Vérification de santé simple. |
| **Authentification**     | Connexion, vérification MFA, inscription, activation/désactivation MFA et récupération de profil. |
| **Audit**                | Récupérer les journaux de piste d'audit et vérifier l'intégrité des lots. |
| **Journaux**             | Ingérer des journaux bruts (sécurisé par clé API), recherche plein texte et valeurs uniques des champs. |
| **Tableau de bord**      | Statistiques de la salle de crise et graphiques de synthèse configurables. |
| **Incidents**            | Lister, filtrer et mettre à jour les incidents de sécurité. |
| **Règles**               | Gestion CRUD des règles de corrélation (mutations réservées à l'administrateur). |
| **SOAR**                 | Exécuter et abandonner des playbooks, actions directes sur le pare‑feu (bloquer IP/port, blocage temporaire, débloquer, vérifier IP, gestion des alias), workflows d'approbation, annulation d'actions et vérifications de santé. |
| **UEBA**                 | Profils de risque des utilisateurs, lignes de base et statistiques globales. |
| **Admin**                | Gestion des utilisateurs et des politiques de rétention (admin uniquement). |
| **Rapports**             | Générer, surveiller, télécharger et lister les rapports en PDF/Excel/CSV. |
| **Événements signalés**  | Signaler, lister, mettre à jour, retirer le signalement et lier les événements pour enquête croisée. |

---

## Détails des endpoints

### 1. App – Vérification de santé

| Méthode | Chemin           | Description |
|---------|------------------|-------------|
| `GET`   | `/api/v1/health` | Renvoie l'état de santé du service (OK / dégradé). |

---

### 2. Authentification

| Méthode | Chemin                           | Description |
|---------|----------------------------------|-------------|
| `POST`  | `/api/v1/auth/login`             | Connexion avec identifiants (email/mot de passe). |
| `POST`  | `/api/v1/auth/mfa/verify`        | Vérification du code MFA après login. |
| `POST`  | `/api/v1/auth/register`          | Inscription d'un nouvel utilisateur. |
| `POST`  | `/api/v1/auth/mfa/enable`        | Activer la MFA pour l'utilisateur connecté. |
| `POST`  | `/api/v1/auth/mfa/disable`       | Désactiver la MFA. |
| `GET`   | `/api/v1/auth/profile`           | Récupérer le profil de l'utilisateur connecté. |

---

### 3. Audit

| Méthode | Chemin                         | Description |
|---------|--------------------------------|-------------|
| `GET`   | `/api/v1/audit/logs`           | Récupérer les journaux de piste d'audit (filtres possibles). |
| `GET`   | `/api/v1/audit/integrity/{id}` | Vérifier l'intégrité d'un lot d'audit via son identifiant. |

---

### 4. Journaux (Logs)

| Méthode | Chemin                               | Description |
|---------|--------------------------------------|-------------|
| `POST`  | `/api/v1/logs/ingest`                | Ingérer des journaux bruts (authentification par clé API). |
| `GET`   | `/api/v1/logs/search`                | Recherche plein texte dans les journaux (paramètres de requête). |
| `GET`   | `/api/v1/logs/fields/{field}/values` | Récupérer les valeurs uniques d'un champ donné. |

---

### 5. Tableau de bord

| Méthode | Chemin                           | Description |
|---------|----------------------------------|-------------|
| `GET`   | `/api/v1/dashboard/stats`        | Statistiques globales de la salle de crise (incidents, alertes, etc.). |
| `GET`   | `/api/v1/dashboard/charts`       | Graphiques de synthèse configurables (ex: évolution des incidents). |

---

### 6. Incidents

| Méthode | Chemin                       | Description |
|---------|------------------------------|-------------|
| `GET`   | `/api/v1/incidents`          | Lister les incidents (avec pagination et filtres). |
| `GET`   | `/api/v1/incidents/{id}`     | Détails d'un incident spécifique. |
| `PUT`   | `/api/v1/incidents/{id}`     | Mettre à jour un incident (statut, priorité, assignation). |

---

### 7. Règles de corrélation

| Méthode | Chemin                    | Description |
|---------|---------------------------|-------------|
| `GET`   | `/api/v1/rules`           | Lister les règles de corrélation. |
| `GET`   | `/api/v1/rules/{id}`      | Obtenir une règle par son ID. |
| `POST`  | `/api/v1/rules`           | Créer une nouvelle règle (admin uniquement). |
| `PUT`   | `/api/v1/rules/{id}`      | Mettre à jour une règle (admin uniquement). |
| `DELETE`| `/api/v1/rules/{id}`      | Supprimer une règle (admin uniquement). |

---

### 8. SOAR

| Méthode | Chemin                                         | Description |
|---------|------------------------------------------------|-------------|
| `POST`  | `/api/v1/soar/playbooks/{id}/execute`          | Exécuter un playbook. |
| `POST`  | `/api/v1/soar/playbooks/{id}/abort`            | Abandonner un playbook en cours. |
| `POST`  | `/api/v1/soar/firewall/block`                  | Bloquer une IP ou un port sur le pare‑feu. |
| `POST`  | `/api/v1/soar/firewall/tempblock`              | Bloquer temporairement une IP. |
| `POST`  | `/api/v1/soar/firewall/unblock`                | Débloquer une IP ou un port. |
| `GET`   | `/api/v1/soar/firewall/check/{ip}`             | Vérifier le statut d'une IP (bloquée ou non). |
| `POST`  | `/api/v1/soar/firewall/alias`                  | Gérer les alias (création, mise à jour). |
| `POST`  | `/api/v1/soar/approvals`                       | Soumettre une action pour approbation. |
| `POST`  | `/api/v1/soar/actions/{id}/cancel`             | Annuler une action en attente. |
| `GET`   | `/api/v1/soar/health`                          | Vérifier la santé des connecteurs SOAR. |

---

### 9. UEBA

| Méthode | Chemin                         | Description |
|---------|--------------------------------|-------------|
| `GET`   | `/api/v1/ueba/users/{id}/risk` | Obtenir le profil de risque d'un utilisateur. |
| `GET`   | `/api/v1/ueba/baselines`       | Récupérer les lignes de base comportementales. |
| `GET`   | `/api/v1/ueba/global/stats`    | Statistiques globales de risque (moyenne, écarts, etc.). |

---

### 10. Admin (réservé aux administrateurs)

| Méthode | Chemin                             | Description |
|---------|------------------------------------|-------------|
| `GET`   | `/api/v1/admin/users`              | Lister tous les utilisateurs. |
| `POST`  | `/api/v1/admin/users`              | Créer un nouvel utilisateur. |
| `PUT`   | `/api/v1/admin/users/{id}`         | Modifier un utilisateur. |
| `DELETE`| `/api/v1/admin/users/{id}`         | Supprimer un utilisateur. |
| `GET`   | `/api/v1/admin/retention`          | Obtenir la politique de rétention actuelle. |
| `PUT`   | `/api/v1/admin/retention`          | Mettre à jour la politique de rétention. |

---

### 11. Rapports

| Méthode | Chemin                          | Description |
|---------|---------------------------------|-------------|
| `POST`  | `/api/v1/reports/generate`      | Générer un rapport (format PDF/Excel/CSV). |
| `GET`   | `/api/v1/reports/{id}/status`   | Surveiller l'état d'avancement d'un rapport. |
| `GET`   | `/api/v1/reports/{id}/download` | Télécharger un rapport généré. |
| `GET`   | `/api/v1/reports`               | Lister tous les rapports disponibles. |

---

### 12. Événements signalés

| Méthode | Chemin                                     | Description |
|---------|--------------------------------------------|-------------|
| `POST`  | `/api/v1/signaled-events`                  | Signaler un nouvel événement. |
| `GET`   | `/api/v1/signaled-events`                  | Lister les événements signalés (filtres). |
| `GET`   | `/api/v1/signaled-events/{id}`             | Obtenir un événement signalé par son ID. |
| `PUT`   | `/api/v1/signaled-events/{id}`             | Mettre à jour un événement signalé. |
| `DELETE`| `/api/v1/signaled-events/{id}/withdraw`    | Retirer le signalement d'un événement. |
| `POST`  | `/api/v1/signaled-events/{id}/link`        | Lier des événements pour enquête croisée. |

---
```yaml
openapi: 3.0.0
paths:
  /api/v1:
    get:
      operationId: AppController_getHello
      parameters: []
      responses:
        '200':
          description: ''
      tags:
        - App
  /api/v1/auth/login:
    post:
      operationId: AuthController_signIn
      parameters: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/SignInDto'
      responses:
        '200':
          description: Renvoie JWT ou mfa_required
      summary: Authentifier l'utilisateur et obtenir un JWT
      tags:
        - Authentification
  /api/v1/auth/mfa/verify:
    post:
      operationId: AuthController_verifyMfa
      parameters: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/VerifyMfaDto'
      responses:
        '200':
          description: Renvoie JWT après vérification MFA
      summary: Vérifier le code MFA et terminer la connexion
      tags:
        - Authentification
  /api/v1/auth/register:
    post:
      operationId: AuthController_signUp
      parameters: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/SignUpDto'
      responses:
        '201':
          description: Utilisateur enregistré avec succès
      summary: Enregistrer un nouvel utilisateur
      tags:
        - Authentification
  /api/v1/auth/mfa/enable:
    post:
      operationId: AuthController_enableMfa
      parameters: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/EnableMfaDto'
      responses:
        '200':
          description: MFA activé
      security:
        - BearerAuth: []
      summary: Activer MFA avec e-mail pour la réception des OTP
      tags:
        - Authentification
  /api/v1/auth/mfa/disable:
    post:
      operationId: AuthController_disableMfa
      parameters: []
      responses:
        '200':
          description: MFA désactivé
      security:
        - BearerAuth: []
      summary: Désactiver MFA pour l'utilisateur actuel
      tags:
        - Authentification
  /api/v1/auth/profile:
    get:
      operationId: AuthController_getProfile
      parameters: []
      responses:
        '200':
          description: Données de l'utilisateur actuel
      security:
        - BearerAuth: []
      summary: Obtenir le profil de l'utilisateur actuel
      tags:
        - Authentification
  /api/v1/audit/trail:
    get:
      operationId: AuditController_getAuditTrail
      parameters:
        - name: user_id
          required: false
          in: query
          schema:
            type: string
        - name: action
          required: false
          in: query
          schema:
            type: string
        - name: from
          required: false
          in: query
          schema:
            type: string
        - name: to
          required: false
          in: query
          schema:
            type: string
      responses:
        '200':
          description: Entrées de piste d'audit
      security:
        - BearerAuth: []
      summary: Obtenir les journaux de piste d'audit (FR-04.3)
      tags:
        - Audit
  /api/v1/audit/integrity/{batchId}:
    get:
      operationId: AuditController_verifyBatchIntegrity
      parameters:
        - name: batchId
          required: true
          in: path
          schema:
            type: string
      responses:
        '200':
          description: État de l'intégrité
      security:
        - BearerAuth: []
      summary: 'FR-02.3 : Vérifier l''intégrité SHA-256 d''un lot de journaux'
      tags:
        - Audit
  /api/v1/logs:
    post:
      operationId: LogsController_ingest
      parameters: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: array
              items:
                type: string
      responses:
        '201':
          description: Journaux acceptés pour traitement
      security:
        - X-API-Key: []
      summary: Ingérer des journaux bruts depuis les agents collecteurs (FR-01.1)
      tags:
        - Journaux
  /api/v1/logs/search:
    get:
      operationId: LogsController_search
      parameters: []
      responses:
        '200':
          description: Résultats de journaux paginés
      security:
        - BearerAuth: []
      summary: Recherche avancée de journaux avec texte intégral (FR-05.2)
      tags:
        - Journaux
  /api/v1/logs/unique-values:
    get:
      operationId: LogsController_uniqueValues
      parameters:
        - name: field
          required: true
          in: query
          description: >-
            Champ pour lequel obtenir les valeurs uniques (ex. hostname, source_type,
            source_ip, action)
          schema:
            example: hostname
            type: string
        - name: q
          required: false
          in: query
          description: Requête optionnelle pour filtrer avant l'agrégation
          schema:
            type: string
        - name: size
          required: false
          in: query
          description: Nombre maximal de valeurs uniques à renvoyer
          schema:
            default: 100
            type: number
      responses:
        '200':
          description: Valeurs uniques avec le nombre total correspondant
      security:
        - BearerAuth: []
      summary: Obtenir les valeurs uniques d'un champ de journal (ex. hostname, source_type)
      tags:
        - Journaux
  /api/v1/dashboard/stats:
    get:
      operationId: DashboardController_getCrisisStats
      parameters: []
      responses:
        '200':
          description: Données de la salle de crise
      security:
        - BearerAuth: []
      summary: Obtenir les statistiques en temps réel de la CTU (FR-05.1)
      tags:
        - Tableau de bord
  /api/v1/dashboard/overview:
    get:
      operationId: DashboardController_getOverview
      parameters:
        - name: interval
          required: false
          in: query
          schema:
            enum:
              - 16h
              - 24h
              - 7d
              - 30d
            type: string
        - name: source_type
          required: false
          in: query
          schema:
            type: string
      responses:
        '200':
          description: Charge utile complète de l'aperçu du tableau de bord
      security:
        - BearerAuth: []
      summary: Obtenir tous les graphiques du tableau de bord en un seul appel
      tags:
        - Tableau de bord
  /api/v1/incidents:
    get:
      operationId: IncidentsController_listIncidents
      parameters:
        - name: status
          required: false
          in: query
          schema:
            enum:
              - OPEN
              - IN_PROGRESS
              - RESOLVED
              - FALSE_POSITIVE
            type: string
        - name: severity
          required: false
          in: query
          schema:
            enum:
              - INFO
              - WARNING
              - HIGH
              - CRITICAL
            type: string
        - name: from
          required: false
          in: query
          schema:
            type: string
        - name: to
          required: false
          in: query
          schema:
            type: string
      responses:
        '200':
          description: Liste des incidents
      security:
        - BearerAuth: []
      summary: Lister tous les incidents avec filtres
      tags:
        - Incidents
  /api/v1/incidents/{incidentId}:
    get:
      operationId: IncidentsController_getIncident
      parameters:
        - name: incidentId
          required: true
          in: path
          schema:
            type: string
      responses:
        '200':
          description: Détails de l'incident
      security:
        - BearerAuth: []
      summary: Obtenir les détails complets d'un incident
      tags:
        - Incidents
    patch:
      operationId: IncidentsController_updateIncident
      parameters:
        - name: incidentId
          required: true
          in: path
          schema:
            type: string
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/IncidentUpdateDto'
      responses:
        '200':
          description: Incident mis à jour
      security:
        - BearerAuth: []
      summary: Mettre à jour le statut d'un incident (ANALYSTE ou ADMIN uniquement)
      tags:
        - Incidents
  /api/v1/rules:
    get:
      operationId: RulesController_listRules
      parameters: []
      responses:
        '200':
          description: Liste des règles
      security:
        - BearerAuth: []
      summary: Lister toutes les règles de corrélation (FR-03.1)
      tags:
        - Règles
    post:
      operationId: RulesController_createRule
      parameters: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateCorrelationRuleDto'
      responses:
        '201':
          description: Règle créée
      security:
        - BearerAuth: []
      summary: Créer une nouvelle règle de corrélation (ADMIN uniquement)
      tags:
        - Règles
  /api/v1/rules/{ruleId}:
    get:
      operationId: RulesController_getRule
      parameters:
        - name: ruleId
          required: true
          in: path
          schema:
            type: string
      responses:
        '200':
          description: Règle trouvée
      security:
        - BearerAuth: []
      summary: Obtenir une règle spécifique
      tags:
        - Règles
    patch:
      operationId: RulesController_updateRule
      parameters:
        - name: ruleId
          required: true
          in: path
          schema:
            type: string
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/UpdateCorrelationRuleDto'
      responses:
        '200':
          description: Mise à jour effectuée
      security:
        - BearerAuth: []
      summary: Mettre à jour une règle existante (ADMIN uniquement)
      tags:
        - Règles
    delete:
      operationId: RulesController_deleteRule
      parameters:
        - name: ruleId
          required: true
          in: path
          schema:
            type: string
      responses:
        '200':
          description: Supprimée avec succès
      security:
        - BearerAuth: []
      summary: Désactiver/Supprimer une règle (ADMIN uniquement)
      tags:
        - Règles
  /api/v1/soar/execute:
    post:
      operationId: SoarController_executePlaybook
      parameters: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/PlaybookExecutionDto'
      responses:
        '201':
          description: Playbook mis en file d'attente
      security:
        - BearerAuth: []
      summary: Exécuter un playbook SOAR à partir d'un incident
      tags:
        - SOAR
  /api/v1/soar/abort:
    post:
      operationId: SoarController_abortPlaybook
      parameters: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/AbortPlaybookDto'
      responses:
        '200':
          description: Abandonné avec succès
      security:
        - BearerAuth: []
      summary: Abandonner une exécution de playbook en attente
      tags:
        - SOAR
  /api/v1/soar/block-ip:
    post:
      operationId: SoarController_directBlockIp
      parameters: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/DirectBlockIpDto'
      responses:
        '202':
          description: ''
      security:
        - BearerAuth: []
      summary: Bloquer une adresse IP sur le pare-feu actif
      tags:
        - SOAR
  /api/v1/soar/block-port:
    post:
      operationId: SoarController_directBlockPort
      parameters: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/DirectBlockPortDto'
      responses:
        '202':
          description: ''
      security:
        - BearerAuth: []
      summary: Bloquer un port spécifique depuis une IP sur le pare-feu actif
      tags:
        - SOAR
  /api/v1/soar/temporary-block:
    post:
      operationId: SoarController_directTempBlock
      parameters: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/DirectTempBlockDto'
      responses:
        '202':
          description: ''
      security:
        - BearerAuth: []
      summary: Bloquer temporairement une IP pour une durée donnée
      tags:
        - SOAR
  /api/v1/soar/check-ip/{ip}:
    get:
      operationId: SoarController_directCheckIp
      parameters:
        - name: ip
          required: true
          in: path
          schema:
            type: string
      responses:
        '200':
          description: ''
      security:
        - BearerAuth: []
      summary: Vérifier si une IP est bloquée sur le pare-feu actif
      tags:
        - SOAR
  /api/v1/soar/unblock-ip:
    post:
      operationId: SoarController_unblockIp
      parameters: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/UnblockIpDto'
      responses:
        '200':
          description: ''
      security:
        - BearerAuth: []
      summary: Débloquer une adresse IP sur le pare-feu actif
      tags:
        - SOAR
  /api/v1/soar/rules:
    get:
      operationId: SoarController_listRules
      parameters: []
      responses:
        '200':
          description: ''
      security:
        - BearerAuth: []
      summary: Lister toutes les règles de pare-feu gérées par Smart SIEM
      tags:
        - SOAR
  /api/v1/soar/rule/{name}:
    delete:
      operationId: SoarController_deleteRule
      parameters:
        - name: name
          required: true
          in: path
          schema:
            type: string
      responses:
        '200':
          description: ''
      security:
        - BearerAuth: []
      summary: Supprimer une règle de pare-feu spécifique par son nom
      tags:
        - SOAR
  /api/v1/soar/health:
    get:
      operationId: SoarController_healthCheck
      parameters: []
      responses:
        '200':
          description: ''
      security:
        - BearerAuth: []
      summary: Obtenir l'état de santé du fournisseur de pare-feu actif
      tags:
        - SOAR
  /api/v1/soar/pending:
    get:
      operationId: SoarController_getPendingExecutions
      parameters: []
      responses:
        '200':
          description: ''
      security:
        - BearerAuth: []
      summary: Lister toutes les exécutions de playbook en attente d'approbation par un analyste (mode CONFIRM)
      tags:
        - SOAR
  /api/v1/soar/approve/{executionId}:
    post:
      operationId: SoarController_approveExecution
      parameters:
        - name: executionId
          required: true
          in: path
          schema:
            type: string
      responses:
        '200':
          description: ''
      security:
        - BearerAuth: []
      summary: Approuver une exécution de playbook en mode CONFIRM
      tags:
        - SOAR
  /api/v1/soar/aliases:
    post:
      operationId: SoarController_createAlias
      parameters: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/DirectAliasDto'
      responses:
        '202':
          description: ''
      security:
        - BearerAuth: []
      summary: Créer un alias IP sur pfSense (pfSense uniquement)
      tags:
        - SOAR
    get:
      operationId: SoarController_listAliases
      parameters: []
      responses:
        '200':
          description: ''
      security:
        - BearerAuth: []
      summary: Lister tous les alias de pfSense (pfSense uniquement)
      tags:
        - SOAR
  /api/v1/soar/aliases/{id}/delete:
    post:
      operationId: SoarController_deleteAlias
      parameters:
        - name: id
          required: true
          in: path
          schema:
            type: string
      responses:
        '202':
          description: ''
      security:
        - BearerAuth: []
      summary: Supprimer un alias IP de pfSense par son ID (pfSense uniquement)
      tags:
        - SOAR
  /api/v1/soar/actions:
    get:
      operationId: SoarController_listActions
      parameters:
        - name: incident_id
          required: true
          in: query
          schema:
            type: string
      responses:
        '200':
          description: ''
      security:
        - BearerAuth: []
      summary: Lister toutes les actions SOAR (blocage/déblocage/isolement) avec prise en charge de l'annulation
      tags:
        - SOAR
  /api/v1/soar/actions/{id}/undo:
    post:
      operationId: SoarController_undoAction
      parameters:
        - name: id
          required: true
          in: path
          schema:
            type: string
      responses:
        '200':
          description: ''
      security:
        - BearerAuth: []
      summary: Annuler une action SOAR (débloquer une IP ou désisoler un hôte)
      tags:
        - SOAR
  /api/v1/soar/status:
    get:
      operationId: SoarController_getStatus
      parameters: []
      responses:
        '200':
          description: État du fournisseur de pare-feu avec version, nombre de règles, etc.
      security:
        - BearerAuth: []
      summary: Obtenir l'état du fournisseur de pare-feu actif
      tags:
        - SOAR
  /api/v1/ueba/users:
    get:
      operationId: UebaController_listUebaProfiles
      parameters:
        - name: min_risk
          required: false
          in: query
          schema:
            type: number
        - name: max_risk
          required: false
          in: query
          schema:
            type: number
      responses:
        '200':
          description: Profils UEBA triés par risque (décroissant)
      security:
        - BearerAuth: []
      summary: Lister tous les profils de risque UEBA (FR-04.8)
      tags:
        - UEBA
  /api/v1/ueba/users/{userPrincipal}:
    get:
      operationId: UebaController_getUebaProfile
      parameters:
        - name: userPrincipal
          required: true
          in: path
          schema:
            type: string
      responses:
        '200':
          description: Données du profil avec ligne de base et score de risque
      security:
        - BearerAuth: []
      summary: Obtenir le profil de risque UEBA d'un utilisateur spécifique
      tags:
        - UEBA
  /api/v1/ueba/stats:
    get:
      operationId: UebaController_getUebaStats
      parameters: []
      responses:
        '200':
          description: Métriques UEBA agrégées
      security:
        - BearerAuth: []
      summary: Statistiques UEBA globales pour la salle de crise
      tags:
        - UEBA
  /api/v1/ueba/baselines/rebuild:
    post:
      operationId: UebaController_triggerBaselineRebuild
      parameters: []
      responses:
        '201':
          description: Reconstruction initiée
      security:
        - BearerAuth: []
      summary: Déclencher la reconstruction manuelle des lignes de base pour tous les utilisateurs
      tags:
        - UEBA
  /api/v1/admin/users:
    get:
      operationId: AdminController_listUsers
      parameters: []
      responses:
        '200':
          description: Liste des utilisateurs
      security:
        - BearerAuth: []
      summary: Lister tous les utilisateurs CTU (ADMIN uniquement)
      tags:
        - Admin
    post:
      operationId: AdminController_createUser
      parameters: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateUserDto'
      responses:
        '201':
          description: Utilisateur créé
      security:
        - BearerAuth: []
      summary: Créer un nouvel utilisateur (ADMIN uniquement)
      tags:
        - Admin
  /api/v1/admin/users/{userId}:
    put:
      operationId: AdminController_updateUser
      parameters:
        - name: userId
          required: true
          in: path
          schema:
            type: string
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/UpdateUserDto'
      responses:
        '200':
          description: Utilisateur mis à jour
      security:
        - BearerAuth: []
      summary: Mettre à jour le rôle/statut d'un utilisateur (ADMIN uniquement)
      tags:
        - Admin
    delete:
      operationId: AdminController_deleteUser
      parameters:
        - name: userId
          required: true
          in: path
          schema:
            type: string
      responses:
        '200':
          description: Supprimé
      security:
        - BearerAuth: []
      summary: Supprimer/désactiver un utilisateur (ADMIN uniquement)
      tags:
        - Admin
  /api/v1/admin/retention:
    get:
      operationId: AdminController_getRetentionPolicies
      parameters: []
      responses:
        '200':
          description: Politiques
      security:
        - BearerAuth: []
      summary: Obtenir les politiques de rétention actuelles (FR-02.2)
      tags:
        - Admin
    put:
      operationId: AdminController_updateRetentionPolicy
      parameters: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/UpdateRetentionDto'
      responses:
        '200':
          description: Mise à jour effectuée
      security:
        - BearerAuth: []
      summary: Mettre à jour la politique de rétention (ADMIN uniquement)
      tags:
        - Admin
  /api/v1/reports/generate:
    post:
      operationId: ReportsController_generate
      parameters: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/ReportGenerationDto'
      responses:
        '201':
          description: Génération du rapport mise en file d'attente
      security:
        - BearerAuth: []
      summary: Générer un rapport PDF/Excel/CSV
      tags:
        - Rapports
  /api/v1/reports/status/{jobId}:
    get:
      operationId: ReportsController_status
      parameters:
        - name: jobId
          required: true
          in: path
          schema:
            type: string
      responses:
        '200':
          description: État du travail
      security:
        - BearerAuth: []
      summary: Vérifier l'état de la génération du rapport
      tags:
        - Rapports
  /api/v1/reports/download/{jobId}:
    get:
      operationId: ReportsController_download
      parameters:
        - name: jobId
          required: true
          in: path
          schema:
            type: string
      responses:
        '200':
          description: Téléchargement du fichier
      security:
        - BearerAuth: []
      summary: Télécharger le fichier de rapport généré
      tags:
        - Rapports
  /api/v1/reports/list:
    get:
      operationId: ReportsController_list
      parameters: []
      responses:
        '200':
          description: Liste des fichiers groupés
      security:
        - BearerAuth: []
      summary: Lister tous les rapports disponibles
      tags:
        - Rapports
  /api/v1/logs/flagged:
    post:
      operationId: FlaggedEventsController_flag
      parameters: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/FlagEventDto'
      responses:
        '201':
          description: Événement signalé avec succès
      security:
        - BearerAuth: []
      summary: Signaler un événement de journal pour enquête croisée (FR-06.3)
      tags:
        - Événements signalés
    get:
      operationId: FlaggedEventsController_list
      parameters: []
      responses:
        '200':
          description: Liste paginée des événements signalés
      security:
        - BearerAuth: []
      summary: Lister tous les événements signalés avec filtres
      tags:
        - Événements signalés
  /api/v1/logs/flagged/{ingestionHash}:
    patch:
      operationId: FlaggedEventsController_update
      parameters:
        - name: ingestionHash
          required: true
          in: path
          schema:
            type: string
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/UpdateFlagDto'
      responses:
        '200':
          description: Signalement mis à jour
      security:
        - BearerAuth: []
      summary: Mettre à jour un signalement (statut, raison, groupe d'enquête)
      tags:
        - Événements signalés
    delete:
      operationId: FlaggedEventsController_unflag
      parameters:
        - name: ingestionHash
          required: true
          in: path
          schema:
            type: string
      responses:
        '200':
          description: Signalement retiré
      security:
        - BearerAuth: []
      summary: Retirer le signalement d'un événement
      tags:
        - Événements signalés
  /api/v1/logs/flagged/{fromHash}/link/{toHash}:
    post:
      operationId: FlaggedEventsController_link
      parameters:
        - name: fromHash
          required: true
          in: path
          schema:
            type: string
        - name: toHash
          required: true
          in: path
          schema:
            type: string
      responses:
        '201':
          description: Événements liés
      security:
        - BearerAuth: []
      summary: Lier deux événements signalés pour enquête croisée
      tags:
        - Événements signalés
info:
  title: Smart SIEM CTU - API Backend
  description: >-
    API pour le système de gestion des informations et des événements de sécurité
    de l'Unité de Contre-Terrorisme (CTU).

    Couvre l'ingestion de journaux, la corrélation, l'automatisation SOAR, l'UEBA et la criminalistique.

    Basé sur les exigences fonctionnelles FR-01 à FR-08 du Cahier des Charges.
  version: 1.0.0
  contact: {}
tags: []
servers: []
components:
  securitySchemes:
    BearerAuth:
      scheme: bearer
      bearerFormat: JWT
      type: http
    X-API-Key:
      type: apiKey
      in: header
      name: X-API-Key
      description: Clé API pour les agents collecteurs
  schemas:
    SignInDto:
      type: object
      properties: {}
    VerifyMfaDto:
      type: object
      properties:
        session_id:
          type: string
        code:
          type: string
          description: Code à 6 chiffres envoyé par e-mail
      required:
        - session_id
        - code
    SignUpDto:
      type: object
      properties:
        email:
          type: string
    EnableMfaDto:
      type: object
      properties:
        email:
          type: string
          description: Adresse e-mail pour recevoir les codes OTP
      required:
        - email
    IncidentUpdateDto:
      type: object
      properties:
        status:
          type: string
          enum:
            - IN_PROGRESS
            - RESOLVED
            - FALSE_POSITIVE
        summary:
          type: string
        assigned_to:
          type: string
    CreateCorrelationRuleDto:
      type: object
      properties:
        id:
          type: string
          example: R001
        name:
          type: string
          example: SSH Brute Force
        tactic:
          type: string
          example: TA0001
        technique:
          type: string
          example: T1110
        definition:
          type: object
          description: Définition de la règle JSON (seuil, fenêtre, filtres)
        confidence_weight:
          type: number
          default: 1
        is_active:
          type: boolean
          default: true
      required:
        - id
        - name
        - definition
    UpdateCorrelationRuleDto:
      type: object
      properties:
        name:
          type: string
          example: SSH Brute Force
        definition:
          type: object
        tactic:
          type: string
          example: TA0001
        technique:
          type: string
          example: T1110
        confidence_weight:
          type: number
          default: 1
        is_active:
          type: boolean
          default: true
    PlaybookExecutionDto:
      type: object
      properties:
        incident_id:
          type: string
        playbook_name:
          type: string
          enum:
            - block_ip
            - isolate_endpoint
            - disable_account
            - block_port
            - temporary_block
            - remove_rule
            - create_alias
            - delete_alias
            - check_ip
            - notify_teams
        mode:
          type: string
          enum:
            - AUTO
            - CONFIRM
      required:
        - incident_id
        - playbook_name
        - mode
    AbortPlaybookDto:
      type: object
      properties:
        execution_id:
          type: string
      required:
        - execution_id
    DirectBlockIpDto:
      type: object
      properties:
        ip:
          type: string
          description: Adresse IP à bloquer
        reason:
          type: string
          description: Raison du blocage
      required:
        - ip
    DirectBlockPortDto:
      type: object
      properties:
        ip:
          type: string
          description: Adresse IP à bloquer
        port:
          type: number
          default: 3389
        protocol:
          type: string
          enum:
            - tcp
            - udp
        reason:
          type: string
      required:
        - ip
        - port
        - protocol
    DirectTempBlockDto:
      type: object
      properties:
        ip:
          type: string
          description: Adresse IP à bloquer
        ttl_seconds:
          type: number
          default: 1800
          description: Durée de vie en secondes
        reason:
          type: string
      required:
        - ip
        - ttl_seconds
    UnblockIpDto:
      type: object
      properties:
        ip:
          type: string
          description: Adresse IP à débloquer
      required:
        - ip
    DirectAliasDto:
      type: object
      properties:
        name:
          type: string
        addresses:
          type: array
          items:
            type: string
        description:
          type: string
      required:
        - name
        - addresses
    CreateUserDto:
      type: object
      properties:
        username:
          type: string
        password:
          type: string
        role:
          type: string
          enum:
            - READER
            - ANALYST
            - ADMIN
      required:
        - username
        - password
        - role
    UpdateUserDto:
      type: object
      properties:
        role:
          type: string
          enum:
            - READER
            - ANALYST
            - ADMIN
        is_active:
          type: boolean
    UpdateRetentionDto:
      type: object
      properties:
        policy_id:
          type: number
        duration_days:
          type: number
      required:
        - policy_id
        - duration_days
    ReportGenerationDto:
      type: object
      properties:
        start_date:
          type: string
        end_date:
          type: string
        format:
          type: string
          enum:
            - pdf
            - excel
            - csv
        incident_ids:
          type: array
          items:
            type: string
      required:
        - start_date
        - end_date
        - format
    FlagEventDto:
      type: object
      properties: {}
    UpdateFlagDto:
      type: object
      properties: {}