# 🏥 Shifa+ - Plateforme de Dématérialisation du Parcours de Santé au Maroc

## 📋 Description
Shifa+ est une plateforme numérique intégrée qui vise à dématérialiser le parcours de santé au Maroc. Elle connecte patients, professionnels de santé et organismes d'assurance dans un écosystème unique, fluide et sécurisé.

**Secteur**: HealthTech / InsurTech  
**Zone**: 🇲🇦 Maroc  
**Statut**: En développement

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

## 🛠️ Stack Technique Recommandé

### **Frontend**
- **Framework**: **Next.js 14+** (App Router)
  - Rendu côté serveur (SSR) pour les performances et SEO
  - Routage optimisé
  - Support TypeScript natif
  - API Routes intégrées
  
- **UI/UX**:
  - **Tailwind CSS** : Styling moderne et responsive
  - **shadcn/ui** : Composants accessibles et personnalisables
  - **Radix UI** : Composants primitifs accessibles
  - **Lucide React** : Icônes modernes
  
- **État et Formulaires**:
  - **Zustand** ou **React Context** : Gestion d'état globale légère
  - **React Hook Form** : Gestion de formulaires performante
  - **Zod** : Validation de schémas TypeScript-first
  
- **Langage**: **TypeScript** (obligatoire pour la sécurité et maintenabilité)

### **Backend**
- **Framework**: **Node.js avec Express.js** ou **NestJS**
  - **NestJS** (recommandé) : Architecture modulaire, TypeScript natif, similaire à Angular
  - Support natif pour les microservices
  - Décorateurs et injection de dépendances
  - Documentation OpenAPI automatique
  
- **API**: **REST** + **GraphQL** (optionnel pour les requêtes complexes)
  - REST pour les opérations CRUD standard
  - GraphQL pour les requêtes de dashboard complexes

### **Base de Données**
- **Base principale**: **PostgreSQL 15+**
  - Fiable, ACID compliant
  - Excellente performance pour les données relationnelles
  - Support JSON pour la flexibilité
  - Extensions pour la recherche full-text
  
- **Cache**: **Redis**
  - Cache des sessions utilisateurs
  - Rate limiting
  - Files d'attente (Bull Queue)
  
- **Stockage de fichiers**: 
  - **AWS S3** ou **MinIO** (alternative open-source)
  - Chiffrement des documents médicaux
  - Versionning des documents

### **ORM / Requêtes**
- **Prisma** (recommandé)
  - Type-safe
  - Migrations automatiques
  - Client TypeScript généré
  - Excellent support PostgreSQL
  
- Alternative : **TypeORM**

### **Authentification & Sécurité**
- **Auth**: **NextAuth.js v5** (Auth.js) ou **Clerk**
  - Multi-facteur (2FA/MFA) obligatoire pour professionnels
  - SSO pour les organisations
  - OAuth2 / OpenID Connect
  
- **Sécurité**:
  - **Helmet.js** : Sécurisation des headers HTTP
  - **bcrypt** ou **argon2** : Hashing des mots de passe
  - **JWT** : Tokens sécurisés avec rotation
  - **Rate limiting** : Protection contre les abus
  - **CORS** configuré strictement
  - **CSP (Content Security Policy)**
  
- **Chiffrement**:
  - **AES-256** pour les données sensibles au repos
  - **TLS 1.3** pour les communications
  - **crypto-js** ou modules natifs Node.js

### **Temps Réel**
- **Socket.io** ou **WebSockets natifs**
  - Notifications en temps réel
  - Suivi des demandes de remboursement
  - Chat support

### **Gestion des Documents**
- **pdf-lib** : Génération de PDF
- **sharp** : Traitement d'images optimisé
- **mammoth** : Conversion de documents Word
- **OCR (Tesseract.js)** : Extraction de texte des documents scannés

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
- **Conteneurisation**: **Docker** + **Docker Compose**
- **CI/CD**: **GitHub Actions** ou **GitLab CI**
- **Hébergement**:
  - **Production**: 
    - **AWS** (EC2, RDS, S3, CloudFront)
    - **DigitalOcean** (alternative économique)
    - **Azure** (conformité européenne/africaine)
  - **Dev**: **Replit** (pour prototypage rapide)
  - **Staging**: **Vercel** ou **Railway**
  
- **Monitoring**:
  - **Sentry** : Tracking des erreurs
  - **LogRocket** : Session replay
  - **Prometheus + Grafana** : Métriques système
  - **Uptime Kuma** : Monitoring de disponibilité

### **Conformité & Audits**
- **Logs d'audit** : Toutes les actions sensibles tracées
- **RGPD** : 
  - Consentement explicite
  - Droit à l'oubli
  - Exportation des données
  - Minimisation des données
- **ISO 27001** : Standards de sécurité
- **HDS** (Hébergement Données de Santé) : Si applicable au Maroc

### **Versionning & Collaboration**
- **Git** : Contrôle de version
- **GitHub** ou **GitLab** : Hébergement du code
- **Conventional Commits** : Messages de commit structurés
- **Husky** : Git hooks pour quality checks

---

## 🏗️ Architecture Recommandée

```
┌─────────────────────────────────────────────────┐
│                   FRONTEND                      │
│              Next.js + TypeScript               │
│         Tailwind + shadcn/ui + Zustand         │
└──────────────────┬──────────────────────────────┘
                   │ HTTPS/WSS
┌──────────────────▼──────────────────────────────┐
│              API GATEWAY / NGINX                │
│           Load Balancer + Rate Limit            │
└──────────────────┬──────────────────────────────┘
                   │
        ┌──────────┴──────────┐
        │                     │
┌───────▼────────┐   ┌────────▼───────┐
│  AUTH SERVICE  │   │  MAIN BACKEND  │
│  (NextAuth.js) │   │    (NestJS)    │
└───────┬────────┘   └────────┬───────┘
        │                     │
        └──────────┬──────────┘
                   │
        ┌──────────┴──────────┬──────────────┐
        │                     │              │
┌───────▼────────┐  ┌─────────▼────┐  ┌─────▼─────┐
│   PostgreSQL   │  │     Redis    │  │ S3/MinIO  │
│  (Données)     │  │   (Cache)    │  │(Documents)│
└────────────────┘  └──────────────┘  └───────────┘
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

