# 🚀 Guide de Démarrage Rapide - Shifa+ Gouvernemental

## 🏛️ Application Gouvernementale - Spring Boot + Angular

Ce guide vous permettra de démarrer le projet **Shifa+** en local en quelques minutes.

**Contexte** : Application nationale pour l'État marocain  
**Stack** : Spring Boot 3 + Angular 17 + PostgreSQL + Kafka

---

## 📋 Prérequis

Avant de commencer, assurez-vous d'avoir installé :

### Requis
- ✅ **Java 21 LTS** (OpenJDK ou Oracle) : [Télécharger](https://adoptium.net/)
- ✅ **Maven 3.9+** : [Télécharger](https://maven.apache.org/download.cgi)
- ✅ **Node.js 20 LTS** et npm : [Télécharger](https://nodejs.org/)
- ✅ **Angular CLI 17+** : `npm install -g @angular/cli@17`
- ✅ **Docker Desktop** : [Télécharger](https://www.docker.com/products/docker-desktop/)
- ✅ **Git** : [Télécharger](https://git-scm.com/)

### Optionnel (pour dev sans Docker)
- **PostgreSQL** 15+ : [Télécharger](https://www.postgresql.org/download/)
- **Redis** : [Télécharger](https://redis.io/download/)
- **Apache Kafka** : [Télécharger](https://kafka.apache.org/downloads)

### Vérification
```bash
java --version      # Java 21.0.0 ou plus
mvn --version       # Maven 3.9.0 ou plus
node --version      # v20.0.0 ou plus
ng version          # Angular CLI 17.0.0 ou plus
docker --version    # 20.0.0 ou plus
git --version       # 2.0.0 ou plus
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

### Étape 3 : Démarrer les services (PostgreSQL, Redis, Kafka, MinIO)
```bash
docker-compose up -d
```

**Vérification** :
- PostgreSQL : http://localhost:5432
- Redis : localhost:6379
- Kafka : localhost:9092
- MinIO Console : http://localhost:9001 (minioadmin / minioadmin123)
- pgAdmin (dev) : http://localhost:5050

### Étape 4 : Créer le backend Spring Boot

**Option A : Via Spring Initializr (Recommandé)**

1. Aller sur https://start.spring.io
2. Configurer :
   - **Project** : Maven
   - **Language** : Java
   - **Spring Boot** : 3.2.x
   - **Java** : 21
   - **Packaging** : Jar
   - **Dependencies** :
     - Spring Web
     - Spring Data JPA
     - PostgreSQL Driver
     - Spring Security
     - Spring for Apache Kafka
     - Spring Boot Actuator
     - Flyway Migration
     - Validation
     - Lombok
3. Télécharger et extraire dans le dossier `backend/`

**Option B : Via Spring CLI**
```bash
# Installer Spring CLI (optionnel)
# Télécharger depuis https://spring.io/tools

# Créer le projet
spring init \
  --build=maven \
  --java-version=21 \
  --dependencies=web,data-jpa,postgresql,security,kafka,actuator,flyway,validation,lombok \
  --group-id=ma.gov.sante \
  --artifact-id=shifa-backend \
  --name=shifa-backend \
  backend
```

### Étape 5 : Configurer le backend Spring Boot
```bash
cd backend

# Installer les dépendances
mvn clean install

# Configurer application.properties ou application.yml
# Voir QUICK_START_GOUVERNEMENTAL.md pour la configuration complète
```

### Étape 6 : Configurer Flyway (Migrations DB)
```bash
# Dans backend/src/main/resources/db/migration/
# Créer V1__init_schema.sql avec le schéma de base de données

# Appliquer les migrations
mvn flyway:migrate
```

### Étape 7 : Créer le frontend Angular
```bash
# Revenir à la racine
cd ..

# Créer l'application Angular
ng new shifa-frontend \
  --routing \
  --style=scss \
  --strict \
  --package-manager=npm

cd shifa-frontend
```

### Étape 8 : Installer Angular Material et PrimeNG
```bash
# Dans le dossier frontend/
# Installer Angular Material
ng add @angular/material

# Répondre aux questions :
# ✔ Choose a prebuilt theme name: Indigo/Pink (ou autre)
# ✔ Set up global Angular Material typography styles? Yes
# ✔ Include the Angular animations module? Yes

# Installer PrimeNG
npm install primeng primeicons
npm install @angular/animations

# Installer les dépendances supplémentaires
npm install @angular/common @angular/forms rxjs
```

### Étape 9 : Configurer le frontend
```bash
# Configurer les modules dans app.module.ts
# Voir QUICK_START_GOUVERNEMENTAL.md pour la configuration complète
```

### Étape 10 : Démarrer l'application

**Terminal 1 - Backend** :
```bash
cd backend
mvn spring-boot:run
```
→ Backend disponible sur http://localhost:8080  
→ Actuator : http://localhost:8080/actuator/health  
→ Swagger : http://localhost:8080/swagger-ui.html

**Terminal 2 - Frontend** :
```bash
cd shifa-frontend
ng serve
```
→ Frontend disponible sur http://localhost:4200

---

## 🎯 Option 2 : Démarrage sans Docker

### Prérequis supplémentaires
Installer manuellement :
- PostgreSQL 15+
- Redis
- Apache Kafka

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

### Configuration Kafka
```bash
# Télécharger Kafka depuis https://kafka.apache.org/downloads
# Extraire et démarrer Zookeeper
bin/zookeeper-server-start.sh config/zookeeper.properties

# Dans un autre terminal, démarrer Kafka
bin/kafka-server-start.sh config/server.properties
```

### Suivre les étapes 1, 2, 4-10 ci-dessus
Mais au lieu de `docker-compose up`, vous utilisez vos installations locales.

---

## 📁 Structure Attendue du Projet

Après configuration complète :

```
shifa/
├── shifa-frontend/         # Application Angular
│   ├── src/
│   │   ├── app/
│   │   │   ├── features/
│   │   │   ├── shared/
│   │   │   └── core/
│   │   ├── assets/
│   │   └── environments/
│   ├── angular.json
│   ├── package.json
│   └── tsconfig.json
│
├── backend/                # API Spring Boot
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/ma/gov/sante/shifa/
│   │   │   │   ├── ShifaApplication.java
│   │   │   │   ├── auth/
│   │   │   │   ├── users/
│   │   │   │   ├── patients/
│   │   │   │   └── config/
│   │   │   └── resources/
│   │   │       ├── application.yml
│   │   │       └── db/migration/
│   │   └── test/
│   ├── pom.xml
│   └── mvnw
│
├── docker-compose.yml      # Services Docker (PostgreSQL, Redis, Kafka)
├── .gitignore
├── README.md
├── STACK_GOUVERNEMENTAL.md
├── QUICK_START_GOUVERNEMENTAL.md
└── GUIDE_DEMARRAGE.md
```

---

## 🧪 Vérifications

### 1. Backend fonctionne
```bash
curl http://localhost:8080/actuator/health
# Devrait retourner {"status":"UP"}
```

### 2. Frontend fonctionne
Ouvrir http://localhost:4200 dans le navigateur

### 3. Base de données connectée
```bash
# Se connecter à PostgreSQL
docker exec -it shifa_postgres psql -U shifa_user -d shifa_db

# Vérifier les tables
\dt

# Quitter
\q
```

### 4. Docker services actifs
```bash
docker ps
```
Devrait afficher : postgres, redis, kafka, minio

### 5. Swagger UI accessible
Ouvrir http://localhost:8080/swagger-ui.html dans le navigateur

---

## 🎨 Prochaines Étapes de Développement

### Phase 1 : POC (Mois 1-6)

#### Mois 1-2 : Configuration & Infrastructure
- [ ] Setup infrastructure complète (Kubernetes local)
- [ ] Architecture microservices de base
- [ ] Configuration CI/CD (Jenkins/GitLab CI)
- [ ] Monitoring (Prometheus/Grafana)

#### Mois 3-4 : Authentification & Sécurité
- [ ] Intégration Keycloak
- [ ] Module Auth avec PKI (CIN électronique)
- [ ] HSM pour clés de chiffrement
- [ ] Pages login/register Angular
- [ ] Guards et Interceptors Spring Security

#### Mois 5-6 : Modules de Base
- [ ] CRUD Patients (Spring Boot + Angular)
- [ ] CRUD Médecins (Spring Boot + Angular)
- [ ] Gestion des rôles et permissions (RBAC)
- [ ] Profils utilisateurs

### Phase 2 : Pilote (Mois 7-12)

#### Mois 7-8 : Dossiers Médicaux
- [ ] Création dossier patient
- [ ] Upload de documents (chiffrement AES-256 + HSM)
- [ ] Gestion des ordonnances
- [ ] Historique médical

#### Mois 9-10 : Remboursements
- [ ] Soumission de demande
- [ ] Workflow de validation (Spring State Machine)
- [ ] Suivi en temps réel (Kafka + WebSocket)
- [ ] Notifications (Kafka events)
- [ ] Intégrations CNOPS/CNSS

#### Mois 11-12 : Dashboards & Tests Pilote
- [ ] Dashboard Patient (Angular Material)
- [ ] Dashboard Médecin
- [ ] Dashboard Assurance
- [ ] Dashboard Admin
- [ ] Analytics avec Kafka Streams
- [ ] Tests pilote régionaux

### Phase 3 : National (Mois 13-18)

#### Mois 13-14 : Tests & Sécurité Enterprise
- [ ] Tests unitaires (JUnit 5) - 85%+ coverage
- [ ] Tests d'intégration (Spring Boot Test)
- [ ] Tests E2E (Cypress/Playwright)
- [ ] Audit de sécurité (SonarQube)
- [ ] Certifications (ISO 27001, DGSSI)

#### Mois 15-16 : Infrastructure Production
- [ ] Configuration Kubernetes production
- [ ] CI/CD Pipeline complet
- [ ] Monitoring enterprise (ELK Stack)
- [ ] Backups et Disaster Recovery

#### Mois 17-18 : Déploiement National
- [ ] Déploiement progressif (3 régions puis national)
- [ ] Support utilisateurs 24/7
- [ ] Formation utilisateurs
- [ ] Documentation utilisateur

---

## 📚 Commandes Utiles

### Docker
```bash
# Démarrer tous les services
docker-compose up -d

# Voir les logs
docker-compose logs -f

# Voir les logs d'un service spécifique
docker-compose logs -f postgres

# Arrêter les services
docker-compose down

# Supprimer les volumes (attention : perte de données!)
docker-compose down -v

# Redémarrer un service spécifique
docker-compose restart postgres
```

### Flyway (Migrations DB)
```bash
# Dans le dossier backend/
# Créer une nouvelle migration
# Créer un fichier V2__description.sql dans src/main/resources/db/migration/

# Appliquer les migrations
mvn flyway:migrate

# Vérifier le statut des migrations
mvn flyway:info

# Réinitialiser la BDD (dev uniquement!)
mvn flyway:clean
mvn flyway:migrate
```

### Frontend (Angular)
```bash
# Dev avec hot-reload
ng serve

# Build de production
ng build --configuration production

# Lancer les tests
ng test

# Linter
ng lint

# Générer un composant
ng generate component features/patient/patient-list

# Générer un service
ng generate service services/patient
```

### Backend (Spring Boot)
```bash
# Dev avec hot-reload
mvn spring-boot:run

# Build
mvn clean install

# Production
mvn clean package
java -jar target/shifa-backend-1.0.0.jar

# Tests
mvn test
mvn test -Dtest=PatientServiceTest

# Tests avec coverage
mvn test jacoco:report

# Vérifier les dépendances
mvn dependency:tree

# Nettoyer et réinstaller
mvn clean install -U
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
# Trouver le processus utilisant le port 8080 (Backend)
# Windows
netstat -ano | findstr :8080

# Mac/Linux
lsof -i :8080

# Trouver le processus utilisant le port 4200 (Frontend)
# Windows
netstat -ano | findstr :4200

# Mac/Linux
lsof -i :4200

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
- Vérifier l'espace disque disponible

### Erreurs Maven
```bash
# Nettoyer et réinstaller
mvn clean install -U

# Supprimer le cache Maven local (si nécessaire)
# Windows: %USERPROFILE%\.m2\repository
# Mac/Linux: ~/.m2/repository
```

### Erreurs Flyway
```bash
# Vérifier le statut des migrations
mvn flyway:info

# Réparer les migrations (si nécessaire)
mvn flyway:repair

# Vérifier la connexion à la base de données
# Vérifier application.yml ou application.properties
```

### Erreurs Angular
```bash
# Supprimer node_modules et réinstaller
rm -rf node_modules package-lock.json
npm install

# Nettoyer le cache Angular
ng cache clean

# Vérifier la version d'Angular CLI
ng version
```

### Erreurs Java
```bash
# Vérifier la version Java
java --version  # Doit être Java 21

# Vérifier JAVA_HOME
# Windows
echo %JAVA_HOME%

# Mac/Linux
echo $JAVA_HOME

# Si JAVA_HOME n'est pas défini, le configurer
```

### Kafka ne démarre pas
```bash
# Vérifier les logs Kafka
docker-compose logs kafka

# Vérifier que Zookeeper est démarré (si Kafka standalone)
# Redémarrer Kafka
docker-compose restart kafka
```

---

## 🆘 Besoin d'Aide ?

### Documentation
- **Spring Boot** : https://spring.io/projects/spring-boot
- **Spring Cloud Gateway** : https://spring.io/projects/spring-cloud-gateway
- **Angular** : https://angular.io/docs
- **Angular Material** : https://material.angular.io/
- **PrimeNG** : https://primeng.org/
- **Flyway** : https://flywaydb.org/documentation/
- **Kafka** : https://kafka.apache.org/documentation/
- **Docker** : https://docs.docker.com

### Support
- 📧 Email : support@shifa.gov.ma
- 💬 Slack : #shifa-dev
- 📖 Wiki interne : wiki.shifa.gov.ma
- 📚 Documentation projet : Voir `STACK_GOUVERNEMENTAL.md` et `QUICK_START_GOUVERNEMENTAL.md`

---

## ✅ Checklist de Configuration

- [ ] Java 21 LTS installé
- [ ] Maven 3.9+ installé
- [ ] Node.js 20 LTS installé
- [ ] Angular CLI 17+ installé
- [ ] Docker Desktop installé et lancé
- [ ] Projet cloné
- [ ] Fichier .env configuré
- [ ] Docker services lancés (postgres, redis, kafka, minio)
- [ ] Backend Spring Boot créé et configuré
- [ ] Flyway migrations appliquées
- [ ] Frontend Angular créé et dépendances installées
- [ ] Angular Material et PrimeNG installés
- [ ] Backend démarre sur http://localhost:8080
- [ ] Frontend démarre sur http://localhost:4200
- [ ] Actuator health check accessible
- [ ] Swagger UI accessible

---

**Bon développement ! 🚀**

*Dernière mise à jour : Octobre 2025*  
*Contexte : Application Gouvernementale*  
*Stack : Spring Boot 3 + Angular 17 + PostgreSQL + Kafka*

