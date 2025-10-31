# 🚀 Guide de Démarrage Rapide - Shifa+

Ce guide vous permettra de démarrer le projet **Shifa+** en local en quelques minutes.

## 📋 Prérequis

Avant de commencer, assurez-vous d'avoir installé :

### Requis
- ✅ **Node.js** 18+ et npm/pnpm : [Télécharger](https://nodejs.org/)
- ✅ **Docker Desktop** : [Télécharger](https://www.docker.com/products/docker-desktop/)
- ✅ **Git** : [Télécharger](https://git-scm.com/)

### Optionnel (pour dev sans Docker)
- **PostgreSQL** 15+ : [Télécharger](https://www.postgresql.org/download/)
- **Redis** : [Télécharger](https://redis.io/download/)

### Vérification
```bash
node --version   # v18.0.0 ou plus
npm --version    # 9.0.0 ou plus
docker --version # 20.0.0 ou plus
git --version    # 2.0.0 ou plus
```

---

## 🎯 Option 1 : Démarrage avec Docker (Recommandé)

### Étape 1 : Cloner le projet
```bash
git clone <url-du-repo>
cd shifa
```

### Étape 2 : Configurer les variables d'environnement
```bash
# Copier le fichier d'exemple
cp env.example .env

# Éditer le fichier .env avec vos valeurs
# Sur Windows : notepad .env
# Sur Mac/Linux : nano .env
```

### Étape 3 : Démarrer les services (PostgreSQL, Redis, MinIO)
```bash
docker-compose up -d
```

**Vérification** :
- PostgreSQL : http://localhost:5432
- Redis : localhost:6379
- MinIO Console : http://localhost:9001 (minioadmin / minioadmin123)
- pgAdmin (dev) : http://localhost:5050

### Étape 4 : Créer la structure frontend
```bash
# Créer l'application Next.js
npx create-next-app@latest frontend --typescript --tailwind --app --src-dir --no-git

# Répondre aux questions :
# ✔ Would you like to use ESLint? … Yes
# ✔ Would you like to use Turbopack? … No
# ✔ Would you like to customize the default import alias? … No

cd frontend
npm install
```

### Étape 5 : Installer shadcn/ui (Frontend)
```bash
# Dans le dossier frontend/
npx shadcn-ui@latest init

# Installer les composants de base
npx shadcn-ui@latest add button card input form table dialog toast
```

### Étape 6 : Créer le backend NestJS
```bash
# Revenir à la racine
cd ..

# Installer NestJS CLI globalement (si pas déjà fait)
npm i -g @nestjs/cli

# Créer le projet backend
nest new backend --package-manager npm

cd backend
```

### Étape 7 : Installer Prisma (Backend)
```bash
# Dans le dossier backend/
npm install prisma @prisma/client
npm install -D prisma

# Initialiser Prisma
npx prisma init

# Le fichier prisma/schema.prisma a été créé
```

### Étape 8 : Configurer Prisma
Copier le schéma depuis `STACK_TECHNIQUE.md` dans `backend/prisma/schema.prisma`

Ensuite :
```bash
# Créer et appliquer la migration
npx prisma migrate dev --name init

# Générer le client Prisma
npx prisma generate

# (Optionnel) Ouvrir Prisma Studio pour voir la BDD
npx prisma studio
```

### Étape 9 : Installer les dépendances backend
```bash
# Dans le dossier backend/
npm install @nestjs/config @nestjs/jwt @nestjs/passport passport passport-jwt
npm install @nestjs/throttler class-validator class-transformer
npm install bcrypt helmet
npm install -D @types/bcrypt @types/passport-jwt
```

### Étape 10 : Démarrer l'application

**Terminal 1 - Backend** :
```bash
cd backend
npm run start:dev
```
→ Backend disponible sur http://localhost:3001

**Terminal 2 - Frontend** :
```bash
cd frontend
npm run dev
```
→ Frontend disponible sur http://localhost:3000

---

## 🎯 Option 2 : Démarrage sans Docker

### Prérequis supplémentaires
Installer manuellement :
- PostgreSQL 15+
- Redis

### Configuration PostgreSQL
```bash
# Créer une base de données
createdb shifa_db

# Créer un utilisateur
psql -c "CREATE USER shifa_user WITH PASSWORD 'shifa_secure_pass_2024';"
psql -c "GRANT ALL PRIVILEGES ON DATABASE shifa_db TO shifa_user;"
```

### Configuration Redis
```bash
# Démarrer Redis
redis-server
```

### Suivre les étapes 1, 2, 4-10 ci-dessus
Mais au lieu de `docker-compose up`, vous utilisez vos installations locales.

---

## 📁 Structure Attendue du Projet

Après configuration complète :

```
shifa/
├── frontend/               # Application Next.js
│   ├── src/
│   │   ├── app/
│   │   ├── components/
│   │   └── lib/
│   ├── public/
│   ├── package.json
│   └── tsconfig.json
│
├── backend/                # API NestJS
│   ├── src/
│   │   ├── auth/
│   │   ├── users/
│   │   └── main.ts
│   ├── prisma/
│   │   └── schema.prisma
│   ├── package.json
│   └── tsconfig.json
│
├── docker-compose.yml      # Services Docker
├── .gitignore
├── README.md
├── STACK_TECHNIQUE.md
└── GUIDE_DEMARRAGE.md
```

---

## 🧪 Vérifications

### 1. Backend fonctionne
```bash
curl http://localhost:3001
# Devrait retourner "Hello World!" ou similaire
```

### 2. Frontend fonctionne
Ouvrir http://localhost:3000 dans le navigateur

### 3. Base de données connectée
```bash
cd backend
npx prisma studio
```
Une interface web s'ouvre sur http://localhost:5555

### 4. Docker services actifs
```bash
docker ps
```
Devrait afficher : postgres, redis, minio

---

## 🎨 Prochaines Étapes de Développement

### Phase 1 : Authentification (Semaine 1)
- [ ] Module d'authentification NestJS
- [ ] NextAuth.js configuration
- [ ] Pages de login/register
- [ ] JWT tokens & refresh tokens
- [ ] Guards & Decorators

### Phase 2 : Gestion Utilisateurs (Semaine 2)
- [ ] CRUD Patients
- [ ] CRUD Médecins
- [ ] CRUD Assurances
- [ ] Gestion des rôles et permissions
- [ ] Profils utilisateurs

### Phase 3 : Dossiers Médicaux (Semaine 3-4)
- [ ] Création dossier patient
- [ ] Upload de documents
- [ ] Chiffrement des documents
- [ ] Gestion des ordonnances
- [ ] Historique médical

### Phase 4 : Remboursements (Semaine 5-6)
- [ ] Soumission de demande
- [ ] Workflow de validation
- [ ] Suivi en temps réel
- [ ] Notifications
- [ ] Statistiques et rapports

### Phase 5 : Dashboards (Semaine 7-8)
- [ ] Dashboard Patient
- [ ] Dashboard Médecin
- [ ] Dashboard Assurance
- [ ] Dashboard Admin
- [ ] Analytics

### Phase 6 : Intégrations (Semaine 9-10)
- [ ] API CNOPS
- [ ] API CNSS
- [ ] API AMO
- [ ] Systèmes hospitaliers
- [ ] Laboratoires

### Phase 7 : Tests & Sécurité (Semaine 11-12)
- [ ] Tests unitaires (80%+ coverage)
- [ ] Tests E2E
- [ ] Audit de sécurité
- [ ] Pentesting
- [ ] Documentation API

### Phase 8 : Déploiement (Semaine 13-14)
- [ ] Configuration production
- [ ] CI/CD Pipeline
- [ ] Monitoring
- [ ] Backups
- [ ] DNS & Certificats SSL

---

## 📚 Commandes Utiles

### Docker
```bash
# Démarrer tous les services
docker-compose up -d

# Voir les logs
docker-compose logs -f

# Arrêter les services
docker-compose down

# Supprimer les volumes (attention : perte de données!)
docker-compose down -v

# Redémarrer un service spécifique
docker-compose restart postgres
```

### Prisma
```bash
# Créer une nouvelle migration
npx prisma migrate dev --name nom_de_la_migration

# Appliquer les migrations en production
npx prisma migrate deploy

# Réinitialiser la BDD (dev uniquement!)
npx prisma migrate reset

# Ouvrir Prisma Studio
npx prisma studio

# Générer le client après modification du schema
npx prisma generate
```

### Frontend (Next.js)
```bash
# Dev avec hot-reload
npm run dev

# Build de production
npm run build

# Lancer le build
npm run start

# Linter
npm run lint
```

### Backend (NestJS)
```bash
# Dev avec hot-reload
npm run start:dev

# Build
npm run build

# Production
npm run start:prod

# Tests
npm run test
npm run test:watch
npm run test:cov
npm run test:e2e
```

### Git
```bash
# Statut
git status

# Ajouter des fichiers
git add .

# Commit
git commit -m "feat: message descriptif"

# Push
git push origin main

# Créer une branche
git checkout -b feature/nom-feature
```

---

## ❗ Problèmes Courants

### Port déjà utilisé
```bash
# Trouver le processus utilisant le port 3000
# Windows
netstat -ano | findstr :3000

# Mac/Linux
lsof -i :3000

# Tuer le processus
# Windows
taskkill /PID <PID> /F

# Mac/Linux
kill -9 <PID>
```

### Docker ne démarre pas
- Vérifier que Docker Desktop est lancé
- Redémarrer Docker Desktop
- Vérifier les logs : `docker-compose logs`

### Erreurs Prisma
```bash
# Régénérer le client
npx prisma generate

# Réinitialiser la BDD
npx prisma migrate reset
```

### Erreurs de dépendances
```bash
# Supprimer node_modules et réinstaller
rm -rf node_modules package-lock.json
npm install
```

---

## 🆘 Besoin d'Aide ?

### Documentation
- **Next.js** : https://nextjs.org/docs
- **NestJS** : https://docs.nestjs.com
- **Prisma** : https://www.prisma.io/docs
- **Docker** : https://docs.docker.com

### Support
- 📧 Email : dev@shifa.ma
- 💬 Slack : #shifa-dev
- 📖 Wiki interne : wiki.shifa.ma

---

## ✅ Checklist de Configuration

- [ ] Node.js 18+ installé
- [ ] Docker Desktop installé et lancé
- [ ] Projet cloné
- [ ] Fichier .env configuré
- [ ] Docker services lancés (postgres, redis, minio)
- [ ] Frontend créé et dépendances installées
- [ ] Backend créé et dépendances installées
- [ ] Prisma configuré et migrations appliquées
- [ ] Frontend démarre sur http://localhost:3000
- [ ] Backend démarre sur http://localhost:3001
- [ ] Prisma Studio accessible

---

**Bon développement ! 🚀**

*Dernière mise à jour : Octobre 2025*

