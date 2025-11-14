# 🏥 Shifa+ - Plateforme Nationale de Dématérialisation du Parcours de Santé

## 📋 Description
Shifa+ est une plateforme numérique nationale intégrée développée pour l'État marocain qui vise à dématérialiser le parcours de santé à l'échelle nationale. Elle connecte patients, professionnels de santé et organismes d'assurance dans un écosystème unique, fluide et sécurisé.

**Secteur**: HealthTech / InsurTech Gouvernemental  
**Zone**: 🇲🇦 Maroc (Nationale)  
**Client**: État Marocain / Ministère de la Santé  
**Échelle**: 5-10 millions d'utilisateurs  
**Statut**: En développement - Phase Architecture

## 🎯 Objectifs
- Simplifier les démarches médicales
- Réduire les délais de remboursement
- Renforcer la transparence du système de santé marocain
- Digitaliser le parcours de soins de manière sécurisée et respectueuse des données personnelles

## ✨ Fonctionnalités Principales
- ✅ Soumettre, suivre et valider les demandes de remboursement médical en temps réel
- 📁 Gérer les dossiers patients et documents médicaux de manière centralisée
- 🔗 Connecter tous les acteurs : médecins, laboratoires, pharmacies et assurances
- ⚡ Automatiser les échanges de données de santé en toute sécurité
- 🌱 Réduire l'usage du papier et les délais administratifs

## 👥 Public Cible
1. **Patients** : Simplification des démarches et accès digital au dossier médical
2. **Professionnels de santé** : Gestion unifiée des prescriptions et dossiers
3. **Assurances** (CNOPS, CNSS, AMO, privées) : Digitalisation et automatisation
4. **Établissements médicaux** : Partage sécurisé entre cliniques, labos et hôpitaux

---

## 🛠️ Stack Technique - Application Gouvernementale

### **Frontend**
- **Framework**: **Angular 17+** (TypeScript)
  - Framework enterprise standard
  - Support Long Term Support (LTS) garanti
  - Architecture modulaire et scalable
  - Références gouvernementales nombreuses
  
- **UI/UX**:
  - **Angular Material** : Composants Material Design
  - **PrimeNG** : Bibliothèque UI enterprise
  - **NGX-Bootstrap** : Composants Bootstrap pour Angular
  - Design System conforme accessibilité (WCAG 2.1)
  
- **État et Formulaires**:
  - **NgRx** : Gestion d'état enterprise (Redux pattern)
  - **Reactive Forms** : Formulaires Angular natifs
  - **RxJS** : Programmation réactive
  
- **Langage**: **TypeScript** (obligatoire)

### **Backend**
- **Framework**: **Spring Boot 3.x** (Java 21 LTS)
  - **Spring Cloud** : Architecture microservices
  - **Spring Security** : Sécurité niveau entreprise
  - **Spring Data JPA** : Accès aux données
  - **Spring Integration** : Intégrations SOAP/REST/Kafka
  - Support commercial VMware/Oracle garanti
  
- **API**: **REST** + **SOAP** (pour intégrations legacy)
  - REST pour APIs modernes
  - SOAP pour CNOPS, CNSS, AMO (legacy)
  - OpenAPI/Swagger documentation automatique

### **Base de Données**
- **Base principale**: **PostgreSQL 15+** ou **Oracle 19c+**
  - PostgreSQL : Open-source, performant, certifié
  - Oracle : Si imposé par cahier des charges
  - Fiable, ACID compliant
  - Support transactions distribuées
  - Haute disponibilité (réplication)
  
- **Cache**: **Redis 7+**
  - Cache distribué
  - Sessions utilisateurs
  - Rate limiting
  - Pub/Sub pour événements temps réel
  
- **Stockage de fichiers**: 
  - **MinIO** (S3-compatible, on-premise)
  - **AWS S3** (si cloud certifié autorisé)
  - Chiffrement AES-256-GCM obligatoire
  - Versioning et archivage légal

### **ORM / Accès Données**
- **Hibernate / JPA** (Spring Data JPA)
  - Standard Java enterprise
  - Support PostgreSQL et Oracle
  - Transactions distribuées
  - Cache de second niveau (EhCache)
  - Migrations : **Flyway** ou **Liquibase**

### **Messaging & Événements**
- **Apache Kafka**
  - Bus d'événements distribués
  - Audit trail immuable
  - Intégrations asynchrones (CNOPS, CNSS, etc.)
  - Scalabilité horizontale
  - Garantie de livraison

### **Authentification & Sécurité**
- **Auth**: **Keycloak** (SSO Enterprise)
  - Authentification centralisée
  - Multi-facteur (2FA/MFA) obligatoire
  - OAuth2 / OpenID Connect
  - SAML 2.0 pour intégrations gouvernementales
  - Intégration CIN électronique (PKI)
  
- **Sécurité**:
  - **Spring Security** : Framework sécurité enterprise
  - **BCrypt** : Hashing mots de passe (strength 12+)
  - **JWT** : Tokens signés et chiffrés
  - **Rate limiting** : Spring Cloud Gateway
  - **CORS** : Configuration stricte
  - **OWASP** : Protection contre Top 10
  - **WAF** : Web Application Firewall
  
- **Chiffrement**:
  - **AES-256-GCM** : Données sensibles au repos
  - **TLS 1.3** : Communications
  - **HSM** : Hardware Security Module pour clés critiques
  - **PKI** : Infrastructure à clés publiques (CIN électronique)
  - **Signature électronique** : Conformité DGSSI

### **Temps Réel**
- **WebSockets** (Spring WebSocket + STOMP)
  - Notifications en temps réel
  - Suivi des demandes de remboursement
  - Messagerie sécurisée

### **Gestion des Documents**
- **Apache PDFBox** : Génération et manipulation de PDF
- **iText** : Génération de PDF avancée (commerciale)
- **ImageMagick** : Traitement d'images
- **Apache Tika** : Extraction de contenu multi-formats
- **Tesseract OCR** : Reconnaissance optique de caractères

### **Paiements & Transactions**
- **Stripe** ou intégration locale marocaine (**CMI**, **Maroc Telecommerce**)
- Traçabilité complète des transactions

### **API & Intégrations**
- **Swagger/OpenAPI** : Documentation automatique
- **Axios** : Client HTTP
- **Webhooks** : Notifications aux systèmes tiers
- **API Gateway** : Pour gérer les appels externes

### **Testing**
- **Jest** : Tests unitaires
- **React Testing Library** : Tests de composants
- **Supertest** : Tests d'API
- **Playwright** ou **Cypress** : Tests E2E
- **k6** ou **Artillery** : Tests de charge

### **DevOps & Déploiement**
- **Conteneurisation**: **Docker** + **Kubernetes**
  - Orchestration microservices
  - Haute disponibilité
  - Auto-scaling
  
- **CI/CD**: **GitLab CI/CD** ou **Jenkins**
  - Pipeline automatisé
  - Tests qualité obligatoires
  - Déploiement progressif (blue/green)
  
- **Hébergement**:
  - **Option 1**: **On-Premise** (Datacenter gouvernemental Maroc)
  - **Option 2**: **Cloud Certifié** (AWS GovCloud, Azure Gov, OVH)
  - **Exigences**: Conformité DGSSI, données au Maroc
  - **Architecture**: Multi-zone, haute disponibilité
  
- **Monitoring & Observabilité**:
  - **Prometheus + Grafana** : Métriques temps réel
  - **ELK Stack** (Elasticsearch, Logstash, Kibana) : Logs centralisés
  - **Jaeger** : Distributed tracing
  - **Sentry** : Tracking d'erreurs
  - **Uptime Monitoring** : Disponibilité 24/7

### **Conformité & Audits**
- **Logs d'audit** : Toutes les actions tracées (immutables)
- **RGPD** : 
  - Consentement explicite
  - Droit à l'oubli (avec contraintes légales santé)
  - Exportation des données
  - Minimisation des données
- **Loi 09-08** (Maroc) : Protection données personnelles
- **DGSSI** : Conformité sécurité des systèmes d'information
- **ISO 27001** : Management de la sécurité de l'information
- **ISO 27017/27018** : Sécurité cloud
- **SOC 2 Type II** : Contrôles opérationnels
- **Certification HDS** : Si applicable (hébergement données santé)

### **Versionning & Collaboration**
- **Git** : Contrôle de version
- **GitHub** ou **GitLab** : Hébergement du code
- **Conventional Commits** : Messages de commit structurés
- **Husky** : Git hooks pour quality checks

---

## 🏗️ Architecture Microservices - Niveau Gouvernemental

```
                         Internet / VPN Gouvernemental
                                    │
                                    ▼
┌───────────────────────────────────────────────────────────────┐
│              Load Balancer + WAF (Web Application Firewall)   │
└───────────────────────────────┬───────────────────────────────┘
                                │
                                ▼
┌───────────────────────────────────────────────────────────────┐
│                    FRONTEND (Angular 17+)                      │
│              Multi-tenant | Responsive | Accessible            │
└───────────────────────────────┬───────────────────────────────┘
                                │ HTTPS + TLS 1.3
                                ▼
┌───────────────────────────────────────────────────────────────┐
│              API GATEWAY (Spring Cloud Gateway)                │
│   • Routing & Load Balancing                                  │
│   • Rate Limiting & Throttling                                │
│   • Authentication (Keycloak)                                 │
│   • Monitoring & Logging                                      │
└───────────────────────────────┬───────────────────────────────┘
                                │
                ┌───────────────┼───────────────┐
                │               │               │
                ▼               ▼               ▼
┌─────────────────────┐ ┌─────────────────┐ ┌──────────────────┐
│  Service Registry   │ │  Config Server  │ │  Keycloak (SSO)  │
│    (Eureka)         │ │  (Spring Cloud) │ │  • OAuth2/OIDC   │
└─────────────────────┘ └─────────────────┘ │  • SAML 2.0      │
                                            │  • MFA/2FA       │
                                            └──────────────────┘
                                │
        ┌───────────────────────┼───────────────────────┐
        │                       │                       │
        ▼                       ▼                       ▼
┌───────────────┐     ┌────────────────┐    ┌─────────────────┐
│   Service     │     │    Service     │    │    Service      │
│   Patients    │     │   Médecins     │    │  Remboursements │
│ (Spring Boot) │     │ (Spring Boot)  │    │  (Spring Boot)  │
└───────────────┘     └────────────────┘    └─────────────────┘
        │                       │                       │
        ▼                       ▼                       ▼
┌───────────────┐     ┌────────────────┐    ┌─────────────────┐
│   Service     │     │    Service     │    │    Service      │
│   Documents   │     │  Notifications │    │   Assurances    │
│ (Spring Boot) │     │ (Spring Boot)  │    │  (Spring Boot)  │
└───────────────┘     └────────────────┘    └─────────────────┘
        │                       │                       │
        └───────────────────────┼───────────────────────┘
                                │
                    ┌───────────┴───────────┐
                    │   Apache Kafka        │
                    │  • Event Bus          │
                    │  • Audit Trail        │
                    │  • Async Integration  │
                    └───────────┬───────────┘
                                │
        ┌───────────────────────┼───────────────────────┐
        │                       │                       │
        ▼                       ▼                       ▼
┌───────────────┐     ┌────────────────┐    ┌─────────────────┐
│  PostgreSQL   │     │     Redis      │    │   MinIO (S3)    │
│  (Master/     │     │   (Cache &     │    │  (Documents     │
│   Replica)    │     │    Sessions)   │    │   Chiffrés)     │
└───────────────┘     └────────────────┘    └─────────────────┘
        │
        ▼
┌───────────────────────────────────────────────────────────────┐
│              Monitoring & Observabilité                        │
│  • Prometheus + Grafana (Métriques)                           │
│  • ELK Stack (Logs centralisés)                               │
│  • Jaeger (Distributed Tracing)                               │
│  • Sentry (Erreurs)                                           │
└───────────────────────────────────────────────────────────────┘
```

---

## 📦 Structure de Projet Recommandée

```
shifa/
├── frontend/                 # Application Next.js
│   ├── app/                 # App Router (Next.js 14+)
│   │   ├── (auth)/         # Routes d'authentification
│   │   ├── (dashboard)/    # Dashboards par rôle
│   │   │   ├── patient/
│   │   │   ├── medecin/
│   │   │   ├── assurance/
│   │   │   └── admin/
│   │   ├── api/            # API Routes Next.js
│   │   └── layout.tsx
│   ├── components/          # Composants réutilisables
│   │   ├── ui/             # Composants shadcn/ui
│   │   ├── forms/
│   │   ├── layouts/
│   │   └── shared/
│   ├── lib/                 # Utilitaires et configs
│   ├── hooks/               # Custom React hooks
│   ├── types/               # Types TypeScript
│   └── public/              # Assets statiques
│
├── backend/                  # API NestJS
│   ├── src/
│   │   ├── auth/            # Module d'authentification
│   │   ├── users/           # Gestion utilisateurs
│   │   ├── patients/        # Module patients
│   │   ├── medecins/        # Module médecins
│   │   ├── remboursements/  # Gestion remboursements
│   │   ├── documents/       # Gestion documents
│   │   ├── assurances/      # Module assurances
│   │   ├── notifications/   # Système de notifications
│   │   ├── audit/           # Logs d'audit
│   │   ├── common/          # Code partagé
│   │   └── main.ts
│   ├── prisma/              # Schémas et migrations
│   │   ├── schema.prisma
│   │   └── migrations/
│   └── test/
│
├── shared/                   # Code partagé entre front et back
│   ├── types/               # Types TypeScript communs
│   └── constants/           # Constantes
│
├── docker/                   # Configurations Docker
│   ├── docker-compose.yml
│   ├── Dockerfile.frontend
│   └── Dockerfile.backend
│
├── docs/                     # Documentation
│   ├── api/                 # Documentation API
│   ├── architecture/        # Diagrammes d'architecture
│   └── conformite/          # Documents de conformité
│
└── scripts/                  # Scripts utilitaires
    ├── seed.ts              # Population de la BDD
    └── deploy.sh            # Scripts de déploiement
```

---

## 🚀 Démarrage Rapide

### Prérequis
- Node.js 18+ et npm/pnpm
- PostgreSQL 15+
- Redis
- Docker (optionnel mais recommandé)

### Installation
```bash
# Cloner le repository
git clone <repo-url>
cd shifa

# Installer les dépendances frontend
cd frontend
npm install

# Installer les dépendances backend
cd ../backend
npm install

# Configurer les variables d'environnement
cp .env.example .env
# Éditer .env avec vos configurations

# Lancer les migrations de base de données
npx prisma migrate dev

# Démarrer en mode développement
npm run dev
```

---

## 💰 Modèle de Revenus
- 💼 **Abonnement B2B** : Professionnels et établissements
- 💳 **Frais de service** : Commission sur remboursements
- ⭐ **Offres Premium** : Services à valeur ajoutée patients
- 🤝 **Partenariats** : Contrats AMO, CNOPS, CNSS, assurances privées

---

## 📄 Licence
Propriétaire - Tous droits réservés

## 👥 Équipe
En cours de constitution

---

**Note** : Cette plateforme manipule des données de santé sensibles. La sécurité, la confidentialité et la conformité réglementaire sont des priorités absolues à chaque étape du développement.

