# 🛠️ Stack Technique Détaillé - Shifa+

## Vue d'Ensemble

### Pourquoi ce Stack ?

Ce stack a été choisi spécifiquement pour répondre aux exigences d'une plateforme de santé sécurisée, scalable et conforme aux réglementations :

1. **TypeScript partout** : Type-safety pour réduire les bugs dans un contexte critique
2. **Next.js 14+** : Performance optimale, SEO, et expérience développeur excellente
3. **NestJS** : Architecture enterprise-ready avec modularité et testabilité
4. **PostgreSQL** : Fiabilité et conformité ACID pour les données critiques
5. **Prisma** : Type-safety au niveau base de données

---

## 🎨 Frontend Stack

### Framework Principal
**Next.js 14.2+ avec App Router**
```bash
npx create-next-app@latest frontend --typescript --tailwind --app --src-dir
```

**Avantages** :
- ✅ SSR/SSG pour les performances et SEO
- ✅ Routing file-based optimisé
- ✅ API Routes intégrées (BFF pattern)
- ✅ Optimisation automatique des images
- ✅ Support TypeScript natif
- ✅ Server Components pour réduire le JS côté client

### UI & Styling

**Tailwind CSS 3+**
```bash
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

**shadcn/ui** (Composants)
```bash
npx shadcn-ui@latest init
npx shadcn-ui@latest add button card input form table dialog
```

**Composants recommandés** :
- `button`, `card`, `input`, `form`
- `table`, `dialog`, `dropdown-menu`
- `toast`, `alert`, `badge`
- `calendar`, `date-picker`
- `tabs`, `accordion`, `sheet`

**Icônes**
```bash
npm install lucide-react
```

### Gestion d'État

**Zustand** (Recommandé - Léger et Simple)
```bash
npm install zustand
```

**Exemple d'utilisation** :
```typescript
// stores/authStore.ts
import { create } from 'zustand'

interface AuthState {
  user: User | null
  login: (user: User) => void
  logout: () => void
}

export const useAuthStore = create<AuthState>((set) => ({
  user: null,
  login: (user) => set({ user }),
  logout: () => set({ user: null })
}))
```

### Formulaires & Validation

**React Hook Form + Zod**
```bash
npm install react-hook-form @hookform/resolvers zod
```

**Exemple** :
```typescript
import { useForm } from 'react-hook-form'
import { zodResolver } from '@hookform/resolvers/zod'
import * as z from 'zod'

const schema = z.object({
  email: z.string().email('Email invalide'),
  password: z.string().min(8, 'Minimum 8 caractères')
})

function LoginForm() {
  const form = useForm({
    resolver: zodResolver(schema)
  })
  
  // ...
}
```

### HTTP Client

**TanStack Query (React Query)**
```bash
npm install @tanstack/react-query @tanstack/react-query-devtools
```

**Avantages** :
- Cache automatique
- Revalidation en arrière-plan
- Gestion des états de chargement/erreur
- Optimistic updates

### Dates & Horaires
```bash
npm install date-fns
# ou
npm install dayjs
```

### Notifications & Toast
```bash
npm install sonner
# Déjà intégré avec shadcn/ui
```

---

## 🔧 Backend Stack

### Framework Principal

**NestJS 10+**
```bash
npm i -g @nestjs/cli
nest new backend
```

**Architecture NestJS** :
```
backend/src/
├── auth/                    # Module d'authentification
│   ├── auth.controller.ts
│   ├── auth.service.ts
│   ├── auth.module.ts
│   ├── guards/
│   │   ├── jwt-auth.guard.ts
│   │   └── roles.guard.ts
│   └── strategies/
│       └── jwt.strategy.ts
│
├── users/                   # Gestion utilisateurs
│   ├── dto/                # Data Transfer Objects
│   ├── entities/           # Entités Prisma
│   ├── users.controller.ts
│   ├── users.service.ts
│   └── users.module.ts
│
├── patients/               # Module patients
├── medecins/              # Module médecins
├── remboursements/        # Gestion remboursements
├── documents/             # Gestion documents
├── notifications/         # Système notifications
│
├── common/                # Code partagé
│   ├── decorators/
│   ├── filters/
│   ├── interceptors/
│   ├── pipes/
│   └── guards/
│
└── main.ts               # Point d'entrée
```

### Packages NestJS Essentiels
```bash
# Core
npm install @nestjs/common @nestjs/core @nestjs/platform-express

# Configuration
npm install @nestjs/config

# Validation
npm install class-validator class-transformer

# JWT & Auth
npm install @nestjs/jwt @nestjs/passport passport passport-jwt
npm install -D @types/passport-jwt

# Prisma
npm install @nestjs/prisma

# Throttling (Rate Limiting)
npm install @nestjs/throttler

# WebSockets (optionnel)
npm install @nestjs/websockets @nestjs/platform-socket.io

# Swagger (Documentation API)
npm install @nestjs/swagger swagger-ui-express
```

---

## 🗄️ Base de Données

### PostgreSQL avec Prisma

**Installation Prisma**
```bash
npm install prisma @prisma/client
npx prisma init
```

**Exemple schema.prisma**
```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

enum UserRole {
  PATIENT
  MEDECIN
  ASSURANCE
  PHARMACIE
  LABORATOIRE
  ADMIN
}

enum RemboursementStatus {
  EN_ATTENTE
  EN_COURS
  VALIDEE
  REJETEE
  PAYEE
}

model User {
  id        String   @id @default(cuid())
  email     String   @unique
  password  String
  role      UserRole
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
  
  patient   Patient?
  medecin   Medecin?
  
  @@map("users")
}

model Patient {
  id              String   @id @default(cuid())
  userId          String   @unique
  user            User     @relation(fields: [userId], references: [id])
  
  nom             String
  prenom          String
  dateNaissance   DateTime
  cin             String   @unique
  telephone       String
  adresse         String?
  
  numeroAssurance String?
  organismeAssurance String? // CNOPS, CNSS, AMO, etc.
  
  dossierMedical  DossierMedical?
  remboursements  Remboursement[]
  
  createdAt       DateTime @default(now())
  updatedAt       DateTime @updatedAt
  
  @@map("patients")
}

model Medecin {
  id              String   @id @default(cuid())
  userId          String   @unique
  user            User     @relation(fields: [userId], references: [id])
  
  nom             String
  prenom          String
  specialite      String
  inpe            String   @unique // Identifiant National du Professionnel de santé
  telephone       String
  adresse         String?
  
  ordonnances     Ordonnance[]
  
  createdAt       DateTime @default(now())
  updatedAt       DateTime @updatedAt
  
  @@map("medecins")
}

model DossierMedical {
  id          String   @id @default(cuid())
  patientId   String   @unique
  patient     Patient  @relation(fields: [patientId], references: [id])
  
  groupeSanguin String?
  allergies     String?
  antecedents   String?
  
  documents   Document[]
  ordonnances Ordonnance[]
  
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt
  
  @@map("dossiers_medicaux")
}

model Document {
  id              String   @id @default(cuid())
  dossierId       String
  dossier         DossierMedical @relation(fields: [dossierId], references: [id])
  
  type            String   // ordonnance, radio, analyse, etc.
  nom             String
  url             String   // URL S3/MinIO
  taille          Int      // en bytes
  mimeType        String
  
  encrypted       Boolean  @default(true)
  encryptionKey   String?  // Référence à la clé de chiffrement
  
  createdAt       DateTime @default(now())
  
  @@map("documents")
}

model Ordonnance {
  id              String   @id @default(cuid())
  medecinId       String
  medecin         Medecin  @relation(fields: [medecinId], references: [id])
  dossierId       String
  dossier         DossierMedical @relation(fields: [dossierId], references: [id])
  
  dateConsultation DateTime
  diagnostic      String
  prescriptions   String   // JSON des médicaments
  
  documentId      String?
  
  createdAt       DateTime @default(now())
  
  @@map("ordonnances")
}

model Remboursement {
  id              String   @id @default(cuid())
  patientId       String
  patient         Patient  @relation(fields: [patientId], references: [id])
  
  montant         Decimal  @db.Decimal(10, 2)
  montantRembourse Decimal? @db.Decimal(10, 2)
  status          RemboursementStatus @default(EN_ATTENTE)
  
  typeActe        String   // consultation, analyse, radio, etc.
  organisme       String   // CNOPS, CNSS, etc.
  
  documents       String[] // IDs des documents justificatifs
  
  dateDepot       DateTime @default(now())
  dateTraitement  DateTime?
  datePaiement    DateTime?
  
  commentaire     String?
  
  createdAt       DateTime @default(now())
  updatedAt       DateTime @updatedAt
  
  auditLogs       AuditLog[]
  
  @@map("remboursements")
}

model AuditLog {
  id              String   @id @default(cuid())
  
  userId          String
  action          String   // CREATE, UPDATE, DELETE, VIEW
  entity          String   // Patient, Remboursement, etc.
  entityId        String
  
  metadata        Json?    // Données supplémentaires
  ipAddress       String?
  userAgent       String?
  
  remboursementId String?
  remboursement   Remboursement? @relation(fields: [remboursementId], references: [id])
  
  createdAt       DateTime @default(now())
  
  @@map("audit_logs")
  @@index([userId, createdAt])
  @@index([entity, entityId])
}
```

**Commandes Prisma utiles** :
```bash
# Créer une migration
npx prisma migrate dev --name init

# Générer le client
npx prisma generate

# Ouvrir Prisma Studio (UI)
npx prisma studio

# Reset la base de données
npx prisma migrate reset
```

### Redis

**Installation**
```bash
npm install redis
npm install @nestjs/cache-manager cache-manager
npm install cache-manager-redis-yet
```

**Utilisation** :
- Cache des sessions
- Rate limiting
- Queues (remboursements à traiter)
- Pub/Sub pour les notifications temps réel

---

## 🔐 Authentification & Sécurité

### NextAuth.js v5 (Auth.js)

**Frontend (Next.js)**
```bash
npm install next-auth@beta
```

**Configuration** :
```typescript
// app/api/auth/[...nextauth]/route.ts
import NextAuth from "next-auth"
import CredentialsProvider from "next-auth/providers/credentials"

export const { handlers, auth, signIn, signOut } = NextAuth({
  providers: [
    CredentialsProvider({
      credentials: {
        email: { label: "Email", type: "email" },
        password: { label: "Mot de passe", type: "password" }
      },
      async authorize(credentials) {
        // Appel à votre API backend
        const res = await fetch("http://localhost:3001/auth/login", {
          method: "POST",
          body: JSON.stringify(credentials),
          headers: { "Content-Type": "application/json" }
        })
        
        const user = await res.json()
        
        if (res.ok && user) {
          return user
        }
        return null
      }
    })
  ],
  callbacks: {
    async jwt({ token, user }) {
      if (user) {
        token.role = user.role
      }
      return token
    },
    async session({ session, token }) {
      session.user.role = token.role
      return session
    }
  },
  pages: {
    signIn: '/auth/signin',
  }
})
```

### Sécurité Backend (NestJS)

**Packages de sécurité**
```bash
npm install helmet
npm install bcrypt
npm install -D @types/bcrypt
```

**Configuration main.ts**
```typescript
import { NestFactory } from '@nestjs/core'
import { ValidationPipe } from '@nestjs/common'
import helmet from 'helmet'

async function bootstrap() {
  const app = await NestFactory.create(AppModule)
  
  // Sécurité
  app.use(helmet())
  app.enableCors({
    origin: process.env.FRONTEND_URL,
    credentials: true
  })
  
  // Validation automatique
  app.useGlobalPipes(new ValidationPipe({
    whitelist: true,
    forbidNonWhitelisted: true,
    transform: true
  }))
  
  // Throttling (Rate Limiting)
  // Configuré via ThrottlerModule
  
  await app.listen(3001)
}
bootstrap()
```

---

## 📁 Gestion des Documents

**Packages nécessaires**
```bash
# Génération PDF
npm install pdfkit pdf-lib

# Traitement d'images
npm install sharp

# Upload de fichiers
npm install multer
npm install -D @types/multer

# AWS S3 ou MinIO
npm install @aws-sdk/client-s3
# ou
npm install minio
```

---

## ⚡ Temps Réel (WebSockets)

**Socket.io avec NestJS**
```bash
npm install @nestjs/websockets @nestjs/platform-socket.io
npm install socket.io-client # côté frontend
```

**Cas d'usage** :
- Notifications en temps réel
- Changement de statut des remboursements
- Chat support
- Mise à jour des dashboards

---

## 📊 Monitoring & Logs

```bash
# Logging
npm install winston

# Monitoring d'erreurs
npm install @sentry/node @sentry/nextjs
```

---

## 🧪 Testing

```bash
# Jest (déjà inclus avec Next.js et NestJS)
npm install -D jest @types/jest ts-jest

# React Testing Library
npm install -D @testing-library/react @testing-library/jest-dom

# E2E Testing
npm install -D @playwright/test
# ou
npm install -D cypress
```

---

## 🐳 Docker

**Dockerfile Frontend**
```dockerfile
FROM node:18-alpine AS base

FROM base AS deps
WORKDIR /app
COPY package*.json ./
RUN npm ci

FROM base AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

FROM base AS runner
WORKDIR /app
ENV NODE_ENV production
COPY --from=builder /app/public ./public
COPY --from=builder /app/.next/standalone ./
COPY --from=builder /app/.next/static ./.next/static

EXPOSE 3000
CMD ["node", "server.js"]
```

**docker-compose.yml**
```yaml
version: '3.8'

services:
  postgres:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: shifa_db
      POSTGRES_USER: shifa_user
      POSTGRES_PASSWORD: secure_password
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data

  minio:
    image: minio/minio
    command: server /data --console-address ":9001"
    environment:
      MINIO_ROOT_USER: minioadmin
      MINIO_ROOT_PASSWORD: minioadmin
    ports:
      - "9000:9000"
      - "9001:9001"
    volumes:
      - minio_data:/data

volumes:
  postgres_data:
  redis_data:
  minio_data:
```

---

## 📝 Variables d'Environnement

**Backend (.env)**
```env
# Database
DATABASE_URL="postgresql://shifa_user:secure_password@localhost:5432/shifa_db"

# Redis
REDIS_URL="redis://localhost:6379"

# JWT
JWT_SECRET="votre_secret_jwt_tres_securise"
JWT_EXPIRES_IN="7d"

# S3/MinIO
S3_ENDPOINT="http://localhost:9000"
S3_ACCESS_KEY="minioadmin"
S3_SECRET_KEY="minioadmin"
S3_BUCKET="shifa-documents"

# Application
PORT=3001
NODE_ENV="development"

# Frontend URL (CORS)
FRONTEND_URL="http://localhost:3000"

# Encryption (pour les documents sensibles)
ENCRYPTION_KEY="votre_cle_de_chiffrement_32_chars"
```

**Frontend (.env.local)**
```env
NEXT_PUBLIC_API_URL="http://localhost:3001"
NEXTAUTH_URL="http://localhost:3000"
NEXTAUTH_SECRET="votre_secret_nextauth"
```

---

## 🚀 Commandes de Développement

```bash
# Frontend
cd frontend
npm run dev        # Démarre le serveur de dev (port 3000)
npm run build      # Build de production
npm run start      # Lance le build de production
npm run lint       # Linter

# Backend
cd backend
npm run start:dev  # Démarre en mode dev avec hot-reload
npm run build      # Compile TypeScript
npm run start:prod # Lance en production
npm run test       # Tests unitaires
npm run test:e2e   # Tests E2E

# Prisma
npx prisma studio  # Interface graphique pour la BDD
npx prisma migrate dev  # Créer et appliquer une migration
npx prisma generate     # Générer le client Prisma
```

---

## 📚 Ressources & Documentation

- **Next.js** : https://nextjs.org/docs
- **NestJS** : https://docs.nestjs.com
- **Prisma** : https://www.prisma.io/docs
- **Tailwind CSS** : https://tailwindcss.com/docs
- **shadcn/ui** : https://ui.shadcn.com
- **NextAuth.js** : https://authjs.dev
- **TypeScript** : https://www.typescriptlang.org/docs

---

## ⚠️ Considérations Importantes pour le Secteur de la Santé

### 1. Sécurité des Données
- ✅ Chiffrement AES-256 pour les données au repos
- ✅ TLS 1.3 pour les communications
- ✅ Authentification multi-facteur obligatoire
- ✅ Rotation des clés de chiffrement
- ✅ Hashing des mots de passe avec bcrypt (cost factor ≥ 12)

### 2. Audit & Traçabilité
- ✅ Logger TOUTES les actions sensibles
- ✅ Horodatage précis (UTC)
- ✅ Conservation des logs pendant 10 ans minimum
- ✅ Logs immuables (write-only)

### 3. Conformité RGPD
- ✅ Consentement explicite et granulaire
- ✅ Droit d'accès et de portabilité
- ✅ Droit à l'oubli (avec contraintes légales)
- ✅ Minimisation des données collectées
- ✅ DPO (Data Protection Officer) désigné

### 4. Disponibilité
- ✅ Uptime minimum : 99.9% (SLA)
- ✅ Sauvegardes automatiques quotidiennes
- ✅ Plan de reprise d'activité (PRA)
- ✅ Redondance des serveurs

### 5. Tests & Qualité
- ✅ Couverture de tests ≥ 80%
- ✅ Tests de sécurité réguliers (pentesting)
- ✅ Code reviews obligatoires
- ✅ CI/CD avec checks automatiques

---

## 🎯 Prochaines Étapes

1. ✅ Définir le stack technique (fait)
2. ⏳ Créer la structure de projet
3. ⏳ Mettre en place l'authentification
4. ⏳ Créer les modèles de données (Prisma schema)
5. ⏳ Développer les modules principaux
6. ⏳ Implémenter la gestion des documents
7. ⏳ Créer les dashboards par rôle
8. ⏳ Implémenter le système de notifications
9. ⏳ Tests et sécurité
10. ⏳ Déploiement

---

**Dernière mise à jour** : Octobre 2025

