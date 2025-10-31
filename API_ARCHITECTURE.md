# 🌐 Architecture API - Shifa+

## 📋 Vue d'Ensemble

Cette documentation décrit l'architecture complète de l'API REST de Shifa+, les endpoints, les modèles de données et les flux de travail.

---

## 🏗️ Architecture Globale

```
┌─────────────────────────────────────────────────────┐
│                    FRONTEND                         │
│              (Next.js 14 + TypeScript)              │
└──────────────────┬──────────────────────────────────┘
                   │ HTTPS (REST API)
                   │ WebSocket (Notifications)
┌──────────────────▼──────────────────────────────────┐
│                  API GATEWAY                        │
│             (NestJS + Express)                      │
│  ┌────────────────────────────────────────────┐    │
│  │  Authentication Middleware (JWT)           │    │
│  │  Rate Limiting                             │    │
│  │  CORS                                      │    │
│  │  Validation (class-validator)             │    │
│  │  Logging & Audit                          │    │
│  └────────────────────────────────────────────┘    │
└──────────────────┬──────────────────────────────────┘
                   │
        ┌──────────┴──────────┬──────────────┬─────────────┐
        │                     │              │             │
┌───────▼────────┐  ┌─────────▼────┐  ┌─────▼────┐  ┌────▼────┐
│  Auth Module   │  │ Users Module │  │ Patients │  │ Médecins│
└────────────────┘  └──────────────┘  └──────────┘  └─────────┘
        │                     │              │             │
┌───────▼────────┐  ┌─────────▼────┐  ┌─────▼────┐  ┌────▼────┐
│ Documents      │  │Remboursements│  │Assurances│  │  Audit  │
└────────────────┘  └──────────────┘  └──────────┘  └─────────┘
        │                     │              │             │
        └──────────┬──────────┴──────────────┴─────────────┘
                   │
        ┌──────────▼──────────┬─────────────┬─────────────┐
        │                     │             │             │
┌───────▼────────┐  ┌─────────▼────┐  ┌────▼─────┐  ┌────▼────┐
│  PostgreSQL    │  │    Redis     │  │  MinIO   │  │ Sentry  │
│  (Données)     │  │   (Cache)    │  │(Storage) │  │ (Logs)  │
└────────────────┘  └──────────────┘  └──────────┘  └─────────┘
```

---

## 🔐 Authentification

### Endpoints

#### POST `/api/auth/register`
Inscription d'un nouvel utilisateur.

**Request Body** :
```json
{
  "email": "patient@example.com",
  "password": "SecurePass123!",
  "role": "PATIENT",
  "profile": {
    "nom": "Alami",
    "prenom": "Mohammed",
    "dateNaissance": "1990-01-15",
    "cin": "AB123456",
    "telephone": "+212612345678"
  }
}
```

**Response** :
```json
{
  "success": true,
  "message": "Compte créé avec succès. Veuillez vérifier votre email.",
  "userId": "clxxx..."
}
```

#### POST `/api/auth/login`
Connexion utilisateur.

**Request Body** :
```json
{
  "email": "patient@example.com",
  "password": "SecurePass123!"
}
```

**Response** :
```json
{
  "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": "clxxx...",
    "email": "patient@example.com",
    "role": "PATIENT",
    "profile": {
      "nom": "Alami",
      "prenom": "Mohammed"
    }
  },
  "requiresMfa": false
}
```

#### POST `/api/auth/mfa/setup`
Configuration de l'authentification à deux facteurs.

**Response** :
```json
{
  "qrCode": "data:image/png;base64,...",
  "secret": "JBSWY3DPEHPK3PXP",
  "backupCodes": [
    "12345678",
    "87654321",
    "..."
  ]
}
```

#### POST `/api/auth/mfa/verify`
Vérification du code MFA.

**Request Body** :
```json
{
  "token": "123456"
}
```

#### POST `/api/auth/refresh`
Renouvellement du token d'accès.

**Request Body** :
```json
{
  "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

#### POST `/api/auth/logout`
Déconnexion (invalide le refresh token).

#### POST `/api/auth/forgot-password`
Demande de réinitialisation de mot de passe.

#### POST `/api/auth/reset-password`
Réinitialisation du mot de passe.

---

## 👤 Patients

### Endpoints

#### GET `/api/patients/me`
Récupérer le profil du patient connecté.

**Headers** :
```
Authorization: Bearer {accessToken}
```

**Response** :
```json
{
  "id": "clxxx...",
  "nom": "Alami",
  "prenom": "Mohammed",
  "dateNaissance": "1990-01-15",
  "cin": "AB123456",
  "telephone": "+212612345678",
  "email": "patient@example.com",
  "numeroAssurance": "CNOPS123456",
  "organismeAssurance": "CNOPS",
  "createdAt": "2024-01-15T10:00:00Z"
}
```

#### PATCH `/api/patients/me`
Mettre à jour le profil.

#### GET `/api/patients/me/medical-record`
Récupérer le dossier médical complet.

**Response** :
```json
{
  "id": "clxxx...",
  "patientId": "clxxx...",
  "groupeSanguin": "O+",
  "allergies": "Pénicilline",
  "antecedents": "Diabète type 2",
  "documents": [
    {
      "id": "clxxx...",
      "type": "ordonnance",
      "nom": "Ordonnance Dr. Bennani - 2024-01-15.pdf",
      "url": "https://...",
      "createdAt": "2024-01-15T14:30:00Z"
    }
  ],
  "ordonnances": [
    {
      "id": "clxxx...",
      "medecin": {
        "nom": "Dr. Bennani",
        "specialite": "Cardiologue"
      },
      "dateConsultation": "2024-01-15",
      "diagnostic": "Hypertension artérielle",
      "prescriptions": [
        {
          "medicament": "Amlodipine 5mg",
          "posologie": "1 comprimé par jour",
          "duree": "30 jours"
        }
      ]
    }
  ]
}
```

#### POST `/api/patients/me/documents/upload`
Upload d'un document médical.

**Request (multipart/form-data)** :
```
file: [binary]
type: "ordonnance" | "radio" | "analyse" | "autre"
description: "Description optionnelle"
```

#### GET `/api/patients/:id` (Admin/Médecin)
Récupérer un patient spécifique (avec vérification des permissions).

---

## ⚕️ Médecins

### Endpoints

#### GET `/api/medecins/me`
Profil du médecin connecté.

#### GET `/api/medecins/me/patients`
Liste des patients suivis.

**Query Params** :
- `page`: numéro de page (défaut: 1)
- `limit`: résultats par page (défaut: 20)
- `search`: recherche par nom/CIN

**Response** :
```json
{
  "data": [
    {
      "id": "clxxx...",
      "nom": "Alami",
      "prenom": "Mohammed",
      "dateNaissance": "1990-01-15",
      "lastConsultation": "2024-01-15",
      "nextAppointment": "2024-02-15"
    }
  ],
  "meta": {
    "total": 150,
    "page": 1,
    "limit": 20,
    "totalPages": 8
  }
}
```

#### POST `/api/medecins/me/ordonnances`
Créer une nouvelle ordonnance.

**Request Body** :
```json
{
  "patientId": "clxxx...",
  "dateConsultation": "2024-01-15",
  "diagnostic": "Hypertension artérielle",
  "prescriptions": [
    {
      "medicament": "Amlodipine 5mg",
      "posologie": "1 comprimé par jour le matin",
      "duree": "30 jours"
    }
  ]
}
```

#### GET `/api/medecins/me/ordonnances`
Liste des ordonnances créées.

#### GET `/api/medecins/:id/availability`
Disponibilités d'un médecin (pour prises de RDV).

---

## 💰 Remboursements

### Endpoints

#### GET `/api/remboursements`
Liste des demandes de remboursement.

**Access** : Patient (ses demandes), Assurance (toutes), Admin (toutes)

**Query Params** :
- `status`: EN_ATTENTE | EN_COURS | VALIDEE | REJETEE | PAYEE
- `page`, `limit`

**Response** :
```json
{
  "data": [
    {
      "id": "clxxx...",
      "patient": {
        "nom": "Alami",
        "prenom": "Mohammed",
        "numeroAssurance": "CNOPS123456"
      },
      "montant": 850.00,
      "montantRembourse": null,
      "status": "EN_ATTENTE",
      "typeActe": "consultation_specialiste",
      "organisme": "CNOPS",
      "dateDepot": "2024-01-15T10:00:00Z",
      "documents": [
        {
          "type": "ordonnance",
          "url": "https://..."
        },
        {
          "type": "facture",
          "url": "https://..."
        }
      ]
    }
  ],
  "meta": {
    "total": 15,
    "page": 1,
    "limit": 20,
    "totalPages": 1
  }
}
```

#### POST `/api/remboursements`
Créer une demande de remboursement.

**Request (multipart/form-data)** :
```json
{
  "typeActe": "consultation_specialiste",
  "montant": 850.00,
  "organisme": "CNOPS",
  "ordonnance": [file],
  "facture": [file],
  "autresDocuments": [file, file]
}
```

**Response** :
```json
{
  "id": "clxxx...",
  "status": "EN_ATTENTE",
  "reference": "RBM-2024-0001",
  "message": "Demande de remboursement soumise avec succès",
  "estimatedProcessingTime": "3-5 jours ouvrés"
}
```

#### GET `/api/remboursements/:id`
Détails d'une demande spécifique.

#### PATCH `/api/remboursements/:id/status` (Assurance)
Mettre à jour le statut d'une demande.

**Request Body** :
```json
{
  "status": "VALIDEE",
  "montantRembourse": 680.00,
  "commentaire": "Remboursement selon les taux CNOPS en vigueur"
}
```

#### GET `/api/remboursements/:id/timeline`
Historique des changements de statut.

**Response** :
```json
{
  "timeline": [
    {
      "status": "EN_ATTENTE",
      "timestamp": "2024-01-15T10:00:00Z",
      "actor": "SYSTEM",
      "comment": "Demande créée"
    },
    {
      "status": "EN_COURS",
      "timestamp": "2024-01-16T09:30:00Z",
      "actor": "Agent CNOPS",
      "comment": "Dossier en cours de traitement"
    },
    {
      "status": "VALIDEE",
      "timestamp": "2024-01-18T14:20:00Z",
      "actor": "Agent CNOPS",
      "comment": "Remboursement validé"
    }
  ]
}
```

---

## 📄 Documents

### Endpoints

#### POST `/api/documents/upload`
Upload sécurisé de documents.

**Request (multipart/form-data)** :
```
file: [binary]
type: "ordonnance" | "facture" | "radio" | "analyse" | "autre"
patientId: "clxxx..." (si médecin/assurance)
```

**Response** :
```json
{
  "id": "clxxx...",
  "url": "https://shifa-documents.s3.amazonaws.com/...",
  "type": "ordonnance",
  "nom": "ordonnance_2024-01-15.pdf",
  "taille": 245678,
  "mimeType": "application/pdf",
  "encrypted": true,
  "createdAt": "2024-01-15T10:00:00Z"
}
```

#### GET `/api/documents/:id`
Télécharger un document (avec vérification des permissions).

**Response** : Binaire (PDF, image, etc.)

#### DELETE `/api/documents/:id`
Supprimer un document.

---

## 🏥 Établissements

### Endpoints

#### GET `/api/etablissements`
Liste des établissements (hôpitaux, cliniques, laboratoires).

**Query Params** :
- `type`: hopital | clinique | laboratoire | pharmacie
- `ville`: Casablanca | Rabat | ...
- `specialite`: Cardiologie | Radiologie | ...

#### GET `/api/etablissements/:id`
Détails d'un établissement.

---

## 🔔 Notifications

### Endpoints

#### GET `/api/notifications`
Liste des notifications de l'utilisateur.

**Response** :
```json
{
  "data": [
    {
      "id": "clxxx...",
      "type": "REIMBURSEMENT_STATUS_CHANGED",
      "title": "Remboursement validé",
      "message": "Votre demande de remboursement RBM-2024-0001 a été validée. Montant: 680.00 MAD",
      "read": false,
      "createdAt": "2024-01-18T14:20:00Z",
      "data": {
        "remboursementId": "clxxx...",
        "status": "VALIDEE"
      }
    }
  ],
  "unreadCount": 3
}
```

#### PATCH `/api/notifications/:id/read`
Marquer une notification comme lue.

#### PATCH `/api/notifications/read-all`
Marquer toutes les notifications comme lues.

### WebSocket (Temps Réel)

**Connexion** :
```javascript
const socket = io('ws://localhost:3001', {
  auth: {
    token: accessToken
  }
});

socket.on('notification', (notification) => {
  console.log('Nouvelle notification:', notification);
});

socket.on('remboursement:status_changed', (data) => {
  console.log('Statut remboursement changé:', data);
});
```

---

## 📊 Statistiques & Rapports

### Endpoints

#### GET `/api/stats/patient` (Patient)
Statistiques personnelles.

**Response** :
```json
{
  "remboursements": {
    "total": 15,
    "enCours": 2,
    "valides": 10,
    "rejetes": 3,
    "montantTotal": 12500.00,
    "montantRembourse": 9800.00
  },
  "consultations": {
    "total": 25,
    "derniere": "2024-01-15"
  }
}
```

#### GET `/api/stats/medecin` (Médecin)
Statistiques du médecin.

#### GET `/api/stats/assurance` (Assurance)
Statistiques globales pour les assurances.

**Response** :
```json
{
  "demandes": {
    "total": 15420,
    "enAttente": 245,
    "enCours": 1230,
    "validees": 12890,
    "rejetees": 1055
  },
  "montants": {
    "totalDemande": 125400000.00,
    "totalRembourse": 98500000.00,
    "tauxRemboursement": 78.5
  },
  "delaiMoyen": {
    "traitement": 4.2,
    "paiement": 8.5
  }
}
```

---

## 🔒 Audit Logs

### Endpoints

#### GET `/api/audit-logs` (Admin)
Logs d'audit complets.

**Query Params** :
- `userId`: filtrer par utilisateur
- `action`: filtrer par action
- `entity`: filtrer par entité
- `startDate`, `endDate`: période
- `page`, `limit`

**Response** :
```json
{
  "data": [
    {
      "id": "clxxx...",
      "timestamp": "2024-01-15T10:00:00Z",
      "userId": "clxxx...",
      "userRole": "MEDECIN",
      "action": "VIEW_MEDICAL_RECORD",
      "resource": {
        "type": "Patient",
        "id": "clxxx..."
      },
      "result": "SUCCESS",
      "ipAddress": "192.168.1.100",
      "userAgent": "Mozilla/5.0...",
      "severity": "HIGH"
    }
  ],
  "meta": {
    "total": 15420,
    "page": 1,
    "limit": 50
  }
}
```

---

## 🚨 Gestion des Erreurs

### Format Standard d'Erreur

```json
{
  "statusCode": 400,
  "error": "Bad Request",
  "message": "Validation failed",
  "details": [
    {
      "field": "email",
      "message": "Email invalide"
    }
  ],
  "timestamp": "2024-01-15T10:00:00Z",
  "path": "/api/auth/register"
}
```

### Codes HTTP Utilisés

| Code | Signification | Usage |
|------|---------------|-------|
| **200** | OK | Succès général |
| **201** | Created | Ressource créée |
| **204** | No Content | Suppression réussie |
| **400** | Bad Request | Validation échouée |
| **401** | Unauthorized | Non authentifié |
| **403** | Forbidden | Pas de permission |
| **404** | Not Found | Ressource introuvable |
| **409** | Conflict | Conflit (ex: email déjà utilisé) |
| **422** | Unprocessable Entity | Données invalides |
| **429** | Too Many Requests | Rate limit dépassé |
| **500** | Internal Server Error | Erreur serveur |
| **503** | Service Unavailable | Maintenance |

---

## 📝 Pagination

Format standard pour tous les endpoints paginés :

**Query Params** :
- `page`: numéro de page (défaut: 1)
- `limit`: résultats par page (défaut: 20, max: 100)

**Response** :
```json
{
  "data": [...],
  "meta": {
    "total": 150,
    "page": 1,
    "limit": 20,
    "totalPages": 8,
    "hasNextPage": true,
    "hasPrevPage": false
  }
}
```

---

## 🔍 Filtrage & Recherche

Format standard pour les filtres :

**Query Params** :
- `search`: recherche textuelle globale
- `filter[field]`: filtrer par un champ spécifique
- `sort`: champ de tri (préfixer par `-` pour ordre décroissant)

**Exemple** :
```
GET /api/patients?search=Alami&filter[ville]=Casablanca&sort=-createdAt&page=1&limit=20
```

---

## 🔐 Sécurité API

### Rate Limiting

| Endpoint | Limite |
|----------|--------|
| `/api/auth/login` | 5 requêtes / minute |
| `/api/auth/register` | 3 requêtes / heure |
| `/api/documents/upload` | 10 requêtes / minute |
| **Global** | 100 requêtes / minute |

### Headers de Sécurité

**Request** :
```
Authorization: Bearer {accessToken}
Content-Type: application/json
X-Request-ID: uuid (optionnel, pour traçabilité)
```

**Response** :
```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1705320000
X-Request-ID: uuid
```

---

## 📚 Versioning

L'API utilise le versioning par URL :
- **v1** : `/api/v1/...` (version actuelle)
- **v2** : `/api/v2/...` (future)

La version par défaut (sans préfixe) pointe vers v1.

---

## 🧪 Environnements

| Environnement | URL | Usage |
|---------------|-----|-------|
| **Development** | http://localhost:3001 | Développement local |
| **Staging** | https://staging-api.shifa.ma | Tests |
| **Production** | https://api.shifa.ma | Production |

---

## 📖 Documentation Interactive

**Swagger UI** : http://localhost:3001/api/docs

Générée automatiquement avec `@nestjs/swagger`.

---

**Dernière mise à jour** : Octobre 2025

