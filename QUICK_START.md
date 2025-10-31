# ⚡ Quick Start - Shifa+

## 🚀 Démarrage en 5 Minutes

### Prérequis
```bash
node --version  # v18+
docker --version
git --version
```

---

## 📦 Installation Rapide

### 1. Configuration de Base
```bash
# Cloner et configurer
git clone <repo-url>
cd shifa
cp env.example .env

# Démarrer les services Docker
docker-compose up -d

# Vérifier que tout tourne
docker ps
```

### 2. Frontend (Next.js)
```bash
# Créer le projet
npx create-next-app@latest frontend --typescript --tailwind --app --src-dir --no-git

cd frontend

# Installer shadcn/ui
npx shadcn-ui@latest init
npx shadcn-ui@latest add button card input form table dialog toast

# Démarrer
npm run dev
```

✅ Frontend : http://localhost:3000

### 3. Backend (NestJS)
```bash
# Installer NestJS CLI
npm i -g @nestjs/cli

# Créer le projet
cd ..
nest new backend --package-manager npm

cd backend

# Installer Prisma
npm install prisma @prisma/client
npx prisma init

# Configurer le schéma (copier depuis STACK_TECHNIQUE.md)

# Appliquer les migrations
npx prisma migrate dev --name init

# Installer les dépendances
npm install @nestjs/config @nestjs/jwt @nestjs/passport passport passport-jwt
npm install @nestjs/throttler class-validator class-transformer bcrypt helmet
npm install -D @types/bcrypt @types/passport-jwt

# Démarrer
npm run start:dev
```

✅ Backend : http://localhost:3001

---

## 🎯 Commandes Essentielles

### Docker
```bash
# Démarrer
docker-compose up -d

# Arrêter
docker-compose down

# Logs
docker-compose logs -f

# Redémarrer un service
docker-compose restart postgres
```

### Prisma
```bash
cd backend

# Migration
npx prisma migrate dev --name ma_migration

# Studio (UI)
npx prisma studio

# Générer le client
npx prisma generate

# Reset (dev uniquement!)
npx prisma migrate reset
```

### Frontend
```bash
cd frontend

# Dev
npm run dev

# Build
npm run build

# Production
npm run start
```

### Backend
```bash
cd backend

# Dev (hot-reload)
npm run start:dev

# Production
npm run build
npm run start:prod

# Tests
npm run test
```

---

## 🔧 Accès aux Services

| Service | URL | Credentials |
|---------|-----|-------------|
| **Frontend** | http://localhost:3000 | - |
| **Backend API** | http://localhost:3001 | - |
| **PostgreSQL** | localhost:5432 | shifa_user / shifa_secure_pass_2024 |
| **Redis** | localhost:6379 | redis_secure_pass |
| **MinIO Console** | http://localhost:9001 | minioadmin / minioadmin123 |
| **pgAdmin** | http://localhost:5050 | admin@shifa.ma / admin |
| **Prisma Studio** | http://localhost:5555 | - |

---

## 📁 Structure Attendue

```
shifa/
├── frontend/          # Next.js app (port 3000)
├── backend/           # NestJS API (port 3001)
├── docker-compose.yml
├── README.md
└── .env
```

---

## ✅ Vérifications

### Services Docker actifs
```bash
docker ps
# Doit montrer : postgres, redis, minio (+ pgadmin si --profile dev)
```

### Backend fonctionne
```bash
curl http://localhost:3001
# Réponse : Hello World!
```

### Frontend fonctionne
Ouvrir http://localhost:3000 dans le navigateur

### Base de données connectée
```bash
cd backend
npx prisma studio
# UI s'ouvre sur http://localhost:5555
```

---

## 🆘 Problèmes Courants

### Port déjà utilisé
```bash
# Windows
netstat -ano | findstr :3000
taskkill /PID <PID> /F

# Mac/Linux
lsof -i :3000
kill -9 <PID>
```

### Docker ne démarre pas
```bash
# Redémarrer Docker Desktop
# Vérifier les logs
docker-compose logs
```

### Erreur Prisma
```bash
cd backend
npx prisma generate
npx prisma migrate reset
```

---

## 📚 Documentation Complète

- **README.md** : Vue d'ensemble du projet
- **STACK_TECHNIQUE.md** : Stack détaillé et exemples de code
- **GUIDE_DEMARRAGE.md** : Guide complet étape par étape
- **SECURITE_CONFORMITE.md** : Sécurité et conformité RGPD

---

## 🎯 Prochaines Étapes

1. ✅ Configuration de base (vous êtes ici)
2. ⏳ Implémenter l'authentification
3. ⏳ Créer les modules principaux
4. ⏳ Développer les dashboards
5. ⏳ Tests et sécurité
6. ⏳ Déploiement

---

**Besoin d'aide ?** Consultez `GUIDE_DEMARRAGE.md` pour plus de détails.

**Dernière mise à jour** : Octobre 2025

