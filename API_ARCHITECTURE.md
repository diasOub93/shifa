# 🌐 Architecture API - Shifa+ Gouvernemental

## 🏛️ Application Gouvernementale - Spring Boot + Angular

**Contexte** : Application nationale pour l'État marocain  
**Stack** : Spring Boot 3 + Angular 17 + PostgreSQL + Kafka  
**Architecture** : Microservices enterprise

---

## 📋 Vue d'Ensemble

Cette documentation décrit l'architecture complète de l'API REST de Shifa+, les endpoints, les modèles de données et les flux de travail pour l'application gouvernementale.

---

## 🏗️ Architecture Globale (Microservices)

```
┌─────────────────────────────────────────────────────┐
│                    FRONTEND                         │
│          (Angular 17 + TypeScript + Material)        │
└──────────────────┬──────────────────────────────────┘
                   │ HTTPS (REST API)
                   │ WebSocket/STOMP (Notifications)
┌──────────────────▼──────────────────────────────────┐
│              API GATEWAY (Spring Cloud)            │
│            (Spring Cloud Gateway)                   │
│  ┌────────────────────────────────────────────┐    │
│  │  Keycloak Integration (OAuth2/JWT)         │    │
│  │  PKI Integration (CIN électronique)        │    │
│  │  Rate Limiting (Bucket4j)                  │    │
│  │  CORS Configuration                        │    │
│  │  Validation (Bean Validation)             │    │
│  │  Logging & Audit (SLF4J + Logback)        │    │
│  │  Circuit Breaker (Resilience4j)           │    │
│  └────────────────────────────────────────────┘    │
└──────────────────┬──────────────────────────────────┘
                   │
        ┌──────────┴──────────┬──────────────┬─────────────┐
        │                     │              │             │
┌───────▼────────┐  ┌─────────▼────┐  ┌─────▼────┐  ┌────▼────┐
│  Auth Service  │  │ User Service │  │ Patient  │  │ Médecin │
│  (Keycloak)    │  │  (Spring)    │  │ Service  │  │ Service │
└────────────────┘  └──────────────┘  └──────────┘  └─────────┘
        │                     │              │             │
┌───────▼────────┐  ┌─────────▼────┐  ┌─────▼────┐  ┌────▼────┐
│ Document       │  │Remboursement │  │Assurance │  │  Audit  │
│ Service        │  │ Service      │  │ Service  │  │ Service │
└────────────────┘  └──────────────┘  └──────────┘  └─────────┘
        │                     │              │             │
        └──────────┬──────────┴──────────────┴─────────────┘
                   │
        ┌──────────▼──────────┬─────────────┬─────────────┐
        │                     │             │             │
┌───────▼────────┐  ┌─────────▼────┐  ┌────▼─────┐  ┌────▼────┐
│  PostgreSQL    │  │    Redis     │  │  MinIO/S3 │  │   ELK   │
│  (Master/Repl) │  │   (Cache)    │  │(Storage) │  │ (Logs)  │
└────────────────┘  └──────────────┘  └──────────┘  └─────────┘
        │                     │              │             │
        └──────────┬──────────┴──────────────┴─────────────┘
                   │
        ┌──────────▼──────────┐
        │   Apache Kafka      │
        │  (Event Streaming)  │
        └─────────────────────┘
```

---

## 🔍 Clarification : Spring Boot vs Spring Cloud Gateway

### Qu'est-ce que Spring Boot ?

**Spring Boot** est un framework Java qui simplifie la création d'applications autonomes. Tous les services de l'architecture Shifa+ sont des applications Spring Boot :

- ✅ **API Gateway** : Application Spring Boot utilisant Spring Cloud Gateway
- ✅ **Patient Service** : Application Spring Boot classique
- ✅ **Médecin Service** : Application Spring Boot classique
- ✅ **Document Service** : Application Spring Boot classique
- ✅ **Tous les autres microservices** : Applications Spring Boot

### Qu'est-ce que Spring Cloud Gateway ?

**Spring Cloud Gateway** est un composant spécialisé de Spring Cloud qui permet de créer une **API Gateway**. C'est une application Spring Boot, mais avec un rôle spécifique :

| Aspect | Spring Boot | Spring Cloud Gateway |
|--------|-------------|----------------------|
| **Nature** | Framework général | Composant spécialisé |
| **Rôle** | Créer des applications | Créer une Gateway |
| **Technologie** | Spring MVC ou WebFlux | WebFlux uniquement (réactif) |
| **Responsabilité** | Logique métier | Routing, sécurité, monitoring |
| **Dépendance** | `spring-boot-starter-web` | `spring-cloud-starter-gateway` |

### Architecture dans Shifa+

```
┌─────────────────────────────────────────────┐
│  API GATEWAY                                │
│  (Application Spring Boot +                 │
│   Spring Cloud Gateway)                     │
│  - Point d'entrée unique                    │
│  - Routing vers les microservices           │
│  - Authentification (Keycloak)              │
│  - Rate Limiting                            │
└──────────────┬──────────────────────────────┘
               │
    ┌──────────┴──────────┬──────────────┐
    │                     │              │
┌───▼────┐         ┌──────▼────┐  ┌─────▼────┐
│Patient │         │ Médecin   │  │Document  │
│Service │         │ Service   │  │ Service  │
│(Spring │         │(Spring    │  │(Spring   │
│ Boot)  │         │ Boot)     │  │ Boot)    │
└────────┘         └───────────┘  └──────────┘
```

**Important** :
- L'**API Gateway** est une application Spring Boot qui utilise le composant **Spring Cloud Gateway**
- Les **microservices** (Patient, Médecin, etc.) sont des applications Spring Boot classiques
- Tous utilisent Spring Boot, mais avec des rôles différents

### Exemple de Code

**API Gateway (Spring Boot + Spring Cloud Gateway)** :
```java
@SpringBootApplication
public class ApiGatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(ApiGatewayApplication.class, args);
    }
}

@Configuration
public class GatewayConfig {
    @Bean
    public RouteLocator customRouteLocator(RouteLocatorBuilder builder) {
        return builder.routes()
            .route("patient-service", r -> r
                .path("/api/patients/**")
                .uri("lb://patient-service"))
            .build();
    }
}
```

**Patient Service (Spring Boot classique)** :
```java
@SpringBootApplication
public class PatientServiceApplication {
    public static void main(String[] args) {
        SpringApplication.run(PatientServiceApplication.class, args);
    }
}

@RestController
@RequestMapping("/api/patients")
public class PatientController {
    @GetMapping("/me")
    public Patient getMyProfile() {
        // Logique métier
    }
}
```

---

## 🔐 Authentification (Keycloak + PKI)

### Architecture d'Authentification

L'authentification utilise **Keycloak** pour la gestion des identités et **PKI** (CIN électronique) pour l'authentification gouvernementale.

### Endpoints

#### POST `/api/auth/register`
Inscription d'un nouvel utilisateur.

**Request Body** :
```json
{
  "email": "patient@example.com",
  "password": "SecurePass123!",
  "role": "PATIENT",
  "cin": "AB123456",
  "profile": {
    "nom": "Alami",
    "prenom": "Mohammed",
    "dateNaissance": "1990-01-15",
    "telephone": "+212612345678"
  }
}
```

**Response** :
```json
{
  "success": true,
  "message": "Compte créé avec succès. Veuillez vérifier votre email.",
  "userId": "550e8400-e29b-41d4-a716-446655440000"
}
```

#### POST `/api/auth/login`
Connexion utilisateur standard.

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
  "accessToken": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...",
  "refreshToken": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...",
  "tokenType": "Bearer",
  "expiresIn": 3600,
  "user": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "email": "patient@example.com",
    "role": "PATIENT",
    "profile": {
      "nom": "Alami",
      "prenom": "Mohammed"
    }
  }
}
```

#### POST `/api/auth/login/pki`
Connexion avec CIN électronique (PKI).

**Request Body** :
```json
{
  "cin": "AB123456",
  "pin": "****",
  "certificate": "base64_encoded_certificate"
}
```

**Response** :
```json
{
  "accessToken": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...",
  "refreshToken": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...",
  "tokenType": "Bearer",
  "expiresIn": 3600,
  "user": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "cin": "AB123456",
    "role": "PATIENT",
    "profile": {
      "nom": "Alami",
      "prenom": "Mohammed"
    }
  }
}
```

#### POST `/api/auth/mfa/setup`
Configuration de l'authentification à deux facteurs (TOTP).

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
  "refreshToken": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

#### POST `/api/auth/logout`
Déconnexion (invalide le refresh token dans Keycloak).

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
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "nom": "Alami",
  "prenom": "Mohammed",
  "dateNaissance": "1990-01-15",
  "cin": "AB123456",
  "telephone": "+212612345678",
  "email": "patient@example.com",
  "numeroAssurance": "CNOPS123456",
  "organismeAssurance": "CNOPS",
  "createdAt": "2024-01-15T10:00:00Z",
  "updatedAt": "2024-01-15T10:00:00Z"
}
```

#### PATCH `/api/patients/me`
Mettre à jour le profil.

**Request Body** :
```json
{
  "telephone": "+212612345679",
  "email": "nouveau@example.com"
}
```

#### GET `/api/patients/me/medical-record`
Récupérer le dossier médical complet (chiffré).

**Response** :
```json
{
  "id": "550e8400-e29b-41d4-a716-446655440001",
  "patientId": "550e8400-e29b-41d4-a716-446655440000",
  "groupeSanguin": "O+",
  "allergies": "Pénicilline",
  "antecedents": "Diabète type 2",
  "documents": [
    {
      "id": "550e8400-e29b-41d4-a716-446655440002",
      "type": "ordonnance",
      "nom": "Ordonnance Dr. Bennani - 2024-01-15.pdf",
      "url": "https://shifa-documents.s3.amazonaws.com/...",
      "encrypted": true,
      "createdAt": "2024-01-15T14:30:00Z"
    }
  ],
  "ordonnances": [
    {
      "id": "550e8400-e29b-41d4-a716-446655440003",
      "medecin": {
        "id": "550e8400-e29b-41d4-a716-446655440004",
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
Upload d'un document médical (chiffrement AES-256 + HSM).

**Request (multipart/form-data)** :
```
file: [binary]
type: "ordonnance" | "radio" | "analyse" | "autre"
description: "Description optionnelle"
```

**Response** :
```json
{
  "id": "550e8400-e29b-41d4-a716-446655440002",
  "url": "https://shifa-documents.s3.amazonaws.com/...",
  "type": "ordonnance",
  "nom": "ordonnance_2024-01-15.pdf",
  "taille": 245678,
  "mimeType": "application/pdf",
  "encrypted": true,
  "createdAt": "2024-01-15T14:30:00Z"
}
```

#### GET `/api/patients/:id` (Admin/Médecin)
Récupérer un patient spécifique (avec vérification des permissions RBAC).

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
      "id": "550e8400-e29b-41d4-a716-446655440000",
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
  "patientId": "550e8400-e29b-41d4-a716-446655440000",
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
      "id": "550e8400-e29b-41d4-a716-446655440005",
      "patient": {
        "id": "550e8400-e29b-41d4-a716-446655440000",
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
          "id": "550e8400-e29b-41d4-a716-446655440006",
          "type": "ordonnance",
          "url": "https://shifa-documents.s3.amazonaws.com/..."
        },
        {
          "id": "550e8400-e29b-41d4-a716-446655440007",
          "type": "facture",
          "url": "https://shifa-documents.s3.amazonaws.com/..."
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
  "id": "550e8400-e29b-41d4-a716-446655440005",
  "status": "EN_ATTENTE",
  "reference": "RBM-2024-0001",
  "message": "Demande de remboursement soumise avec succès",
  "estimatedProcessingTime": "3-5 jours ouvrés",
  "kafkaEventId": "kafka-event-123456"
}
```

**Note** : La demande déclenche un événement Kafka pour le traitement asynchrone.

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
patientId: "550e8400-e29b-41d4-a716-446655440000" (si médecin/assurance)
```

**Response** :
```json
{
  "id": "550e8400-e29b-41d4-a716-446655440002",
  "url": "https://shifa-documents.s3.amazonaws.com/...",
  "type": "ordonnance",
  "nom": "ordonnance_2024-01-15.pdf",
  "taille": 245678,
  "mimeType": "application/pdf",
  "encrypted": true,
  "encryptionKeyId": "hsm-key-123456",
  "createdAt": "2024-01-15T10:00:00Z"
}
```

**Note** : Les documents sont chiffrés avec AES-256 et les clés sont gérées par HSM.

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
      "id": "550e8400-e29b-41d4-a716-446655440010",
      "type": "REIMBURSEMENT_STATUS_CHANGED",
      "title": "Remboursement validé",
      "message": "Votre demande de remboursement RBM-2024-0001 a été validée. Montant: 680.00 MAD",
      "read": false,
      "createdAt": "2024-01-18T14:20:00Z",
      "data": {
        "remboursementId": "550e8400-e29b-41d4-a716-446655440005",
        "status": "VALIDEE"
      },
      "kafkaTopic": "notifications",
      "kafkaPartition": 0
    }
  ],
  "unreadCount": 3
}
```

#### PATCH `/api/notifications/:id/read`
Marquer une notification comme lue.

#### PATCH `/api/notifications/read-all`
Marquer toutes les notifications comme lues.

### WebSocket/STOMP (Temps Réel)

**Connexion Angular** :
```typescript
import { Client } from '@stomp/stompjs';

const client = new Client({
  brokerURL: 'ws://localhost:8080/ws',
  connectHeaders: {
    Authorization: `Bearer ${accessToken}`
  }
});

client.activate();

client.onConnect = (frame) => {
  client.subscribe('/user/queue/notifications', (message) => {
    const notification = JSON.parse(message.body);
    console.log('Nouvelle notification:', notification);
  });

  client.subscribe('/topic/remboursements', (message) => {
    const data = JSON.parse(message.body);
    console.log('Statut remboursement changé:', data);
  });
};
```

**Note** : Les notifications sont également publiées via Kafka et distribuées via WebSocket/STOMP.

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
      "id": "550e8400-e29b-41d4-a716-446655440020",
      "timestamp": "2024-01-15T10:00:00Z",
      "userId": "550e8400-e29b-41d4-a716-446655440004",
      "userRole": "MEDECIN",
      "action": "VIEW_MEDICAL_RECORD",
      "resource": {
        "type": "Patient",
        "id": "550e8400-e29b-41d4-a716-446655440000"
      },
      "result": "SUCCESS",
      "ipAddress": "192.168.1.100",
      "userAgent": "Mozilla/5.0...",
      "severity": "HIGH",
      "sessionId": "session-123456"
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

### Rate Limiting (Bucket4j + Redis)

| Endpoint | Limite |
|----------|--------|
| `/api/auth/login` | 5 requêtes / minute |
| `/api/auth/login/pki` | 10 requêtes / minute |
| `/api/auth/register` | 3 requêtes / heure |
| `/api/documents/upload` | 10 requêtes / minute |
| **Global** | 100 requêtes / minute |

### Headers de Sécurité

**Request** :
```
Authorization: Bearer {accessToken}
Content-Type: application/json
X-Request-ID: uuid (optionnel, pour traçabilité)
X-Client-Certificate: base64 (pour PKI)
```

**Response** :
```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1705320000
X-Request-ID: uuid
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
X-XSS-Protection: 1; mode=block
Strict-Transport-Security: max-age=31536000; includeSubDomains
```

### Sécurité Enterprise

- **Chiffrement** : AES-256 (documents) avec clés gérées par HSM
- **PKI** : Intégration CIN électronique pour authentification gouvernementale
- **Keycloak** : Gestion centralisée des identités et accès (IAM)
- **RBAC** : Contrôle d'accès basé sur les rôles (Role-Based Access Control)
- **Audit Logs** : Tous les accès sont enregistrés et audités
- **HTTPS** : Obligatoire en production avec certificats gouvernementaux

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
| **Development** | http://localhost:8080 | Développement local |
| **Staging** | https://staging-api.shifa.gov.ma | Tests |
| **Production** | https://api.shifa.gov.ma | Production |

---

## 📖 Documentation Interactive

**Swagger UI** : http://localhost:8080/swagger-ui.html

Générée automatiquement avec **SpringDoc OpenAPI 3**.

**OpenAPI Spec** : http://localhost:8080/v3/api-docs

### Exemple de Configuration SpringDoc

```java
@Configuration
public class OpenApiConfig {
    @Bean
    public OpenAPI shifaOpenAPI() {
        return new OpenAPI()
            .info(new Info()
                .title("Shifa+ API")
                .description("API REST pour l'application gouvernementale Shifa+")
                .version("v1.0")
                .contact(new Contact()
                    .name("Ministère de la Santé")
                    .email("support@shifa.gov.ma")))
            .addSecurityItem(new SecurityRequirement().addList("bearerAuth"))
            .components(new Components()
                .addSecuritySchemes("bearerAuth",
                    new SecurityScheme()
                        .type(SecurityScheme.Type.HTTP)
                        .scheme("bearer")
                        .bearerFormat("JWT")));
    }
}
```

---

## 🔄 Architecture Microservices

### Services

1. **Auth Service** : Keycloak + PKI
2. **User Service** : Gestion des utilisateurs
3. **Patient Service** : Gestion des patients
4. **Médecin Service** : Gestion des médecins
5. **Document Service** : Upload et stockage de documents
6. **Remboursement Service** : Workflow de remboursements
7. **Notification Service** : Notifications via Kafka
8. **Audit Service** : Logs d'audit

### Communication

- **REST** : Appels synchrones entre services
- **Kafka** : Événements asynchrones (notifications, changements de statut)
- **Service Discovery** : Eureka ou Consul
- **Load Balancing** : Spring Cloud LoadBalancer
- **Circuit Breaker** : Resilience4j

---

## 📊 Monitoring & Observabilité

### Actuator Endpoints

- `/actuator/health` : Santé de l'application
- `/actuator/metrics` : Métriques Prometheus
- `/actuator/info` : Informations sur l'application
- `/actuator/loggers` : Gestion des logs

### Intégrations

- **Prometheus** : Collecte de métriques
- **Grafana** : Visualisation des métriques
- **ELK Stack** : Centralisation des logs
- **Jaeger/Zipkin** : Distributed tracing

---

**Dernière mise à jour** : Octobre 2025  
**Contexte** : Application Gouvernementale  
**Stack** : Spring Boot 3 + Angular 17 + PostgreSQL + Kafka  
**Architecture** : Microservices Enterprise

