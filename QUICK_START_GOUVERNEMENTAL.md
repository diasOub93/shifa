# ⚡ Quick Start - Shifa+ Gouvernemental

## 🏛️ Application Gouvernementale - Spring Boot + Angular

**Contexte** : Application nationale pour l'État marocain

---

## 📋 Prérequis

### Obligatoires
- ✅ **Java 21 LTS** (OpenJDK ou Oracle)
- ✅ **Maven 3.9+** ou **Gradle 8+**
- ✅ **Node.js 20 LTS** et npm
- ✅ **Angular CLI 17+**
- ✅ **Docker Desktop**
- ✅ **Git**
- ✅ **PostgreSQL 15+** (ou Oracle 19c+)

### Vérification
```bash
java --version      # 21.0.0 ou plus
mvn --version       # 3.9.0 ou plus
node --version      # v20.0.0 ou plus
ng version          # 17.0.0 ou plus
docker --version    # 20.0.0 ou plus
```

---

## 🚀 Démarrage Rapide (30 minutes)

### 1. Configurer l'Infrastructure
```bash
# Cloner le projet
git clone <repo-url>
cd shifa

# Copier les variables d'environnement
cp env.example .env

# Démarrer les services Docker (PostgreSQL, Redis, Kafka)
docker-compose up -d

# Vérifier que tout tourne
docker ps
```

### 2. Backend Spring Boot
```bash
cd backend

# Créer le projet Spring Boot
# Ou utiliser https://start.spring.io
spring init \
  --build=maven \
  --java-version=21 \
  --dependencies=web,data-jpa,postgresql,security,kafka,actuator \
  --group-id=ma.gov.sante \
  --artifact-id=shifa-backend \
  shifa-backend

cd shifa-backend

# Installer les dépendances
mvn clean install

# Créer les tables
mvn flyway:migrate

# Démarrer le serveur
mvn spring-boot:run
```

✅ Backend API : http://localhost:8080

### 3. Frontend Angular
```bash
cd ../frontend

# Créer l'application Angular
ng new shifa-frontend --routing --style=scss --strict

cd shifa-frontend

# Installer Angular Material
ng add @angular/material

# Installer PrimeNG
npm install primeng primeicons

# Démarrer le serveur dev
ng serve
```

✅ Frontend : http://localhost:4200

### 4. Microservices (Optionnel pour POC)
```bash
cd backend

# Service Registry (Eureka)
spring init --dependencies=cloud-eureka-server eureka-server

# Config Server
spring init --dependencies=cloud-config-server config-server

# API Gateway
spring init --dependencies=cloud-gateway gateway-service

# Démarrer chaque service
cd eureka-server && mvn spring-boot:run &
cd config-server && mvn spring-boot:run &
cd gateway-service && mvn spring-boot:run &
```

---

## 🔧 Services Docker

### docker-compose.yml
```yaml
version: '3.8'

services:
  postgres:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: shifa_db
      POSTGRES_USER: shifa_admin
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine
    command: redis-server --requirepass ${REDIS_PASSWORD}
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data

  kafka:
    image: confluentinc/cp-kafka:7.5.0
    depends_on:
      - zookeeper
    ports:
      - "9092:9092"
    environment:
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://localhost:9092

  zookeeper:
    image: confluentinc/cp-zookeeper:7.5.0
    ports:
      - "2181:2181"
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181

  keycloak:
    image: quay.io/keycloak/keycloak:23.0
    command: start-dev
    environment:
      KEYCLOAK_ADMIN: admin
      KEYCLOAK_ADMIN_PASSWORD: ${KEYCLOAK_PASSWORD}
    ports:
      - "8180:8080"

volumes:
  postgres_data:
  redis_data:
```

### Démarrage
```bash
docker-compose up -d
```

**Services disponibles** :
- PostgreSQL : localhost:5432
- Redis : localhost:6379
- Kafka : localhost:9092
- Keycloak : http://localhost:8180

---

## 📂 Structure Projet

```
shifa/
├── backend/
│   ├── eureka-server/        # Service Discovery
│   ├── config-server/        # Configuration
│   ├── gateway-service/      # API Gateway
│   ├── auth-service/         # Authentification
│   ├── patient-service/      # Service Patients
│   ├── medecin-service/      # Service Médecins
│   ├── remboursement-service/# Service Remboursements
│   └── shared/               # Code partagé
│
├── frontend/
│   ├── shifa-frontend/       # Application Angular
│   │   ├── src/
│   │   │   ├── app/
│   │   │   │   ├── features/
│   │   │   │   ├── shared/
│   │   │   │   └── core/
│   │   │   └── environments/
│   │   └── angular.json
│
├── docker-compose.yml
├── .env
└── README.md
```

---

## 🎯 Premiers Développements

### 1. Créer une Entité JPA
```java
@Entity
@Table(name = "patients")
public class Patient {
    @Id
    @GeneratedValue(strategy = GenerationType.UUID)
    private UUID id;
    
    @Column(nullable = false)
    private String nom;
    
    @Column(nullable = false)
    private String prenom;
    
    @Column(unique = true, length = 8)
    private String cin;
    
    // Getters, Setters, Constructors
}
```

### 2. Créer un Repository
```java
@Repository
public interface PatientRepository extends JpaRepository<Patient, UUID> {
    Optional<Patient> findByCin(String cin);
}
```

### 3. Créer un Service
```java
@Service
@Transactional
public class PatientService {
    
    @Autowired
    private PatientRepository repository;
    
    public Patient creer(Patient patient) {
        return repository.save(patient);
    }
    
    public List<Patient> listerTous() {
        return repository.findAll();
    }
}
```

### 4. Créer un Controller REST
```java
@RestController
@RequestMapping("/api/patients")
public class PatientController {
    
    @Autowired
    private PatientService service;
    
    @PostMapping
    public ResponseEntity<Patient> creer(@RequestBody @Valid Patient patient) {
        return ResponseEntity.ok(service.creer(patient));
    }
    
    @GetMapping
    public ResponseEntity<List<Patient>> listerTous() {
        return ResponseEntity.ok(service.listerTous());
    }
}
```

### 5. Component Angular
```typescript
@Component({
  selector: 'app-patients',
  template: `
    <p-table [value]="patients" [loading]="loading">
      <ng-template pTemplate="header">
        <tr>
          <th>CIN</th>
          <th>Nom</th>
          <th>Prénom</th>
        </tr>
      </ng-template>
      <ng-template pTemplate="body" let-patient>
        <tr>
          <td>{{ patient.cin }}</td>
          <td>{{ patient.nom }}</td>
          <td>{{ patient.prenom }}</td>
        </tr>
      </ng-template>
    </p-table>
  `
})
export class PatientsComponent implements OnInit {
  patients: Patient[] = [];
  loading = false;
  
  constructor(private patientService: PatientService) {}
  
  ngOnInit() {
    this.chargerPatients();
  }
  
  chargerPatients() {
    this.loading = true;
    this.patientService.listerTous().subscribe({
      next: (data) => this.patients = data,
      complete: () => this.loading = false
    });
  }
}
```

---

## 📚 Documentation Complète

Pour aller plus loin :
- **STACK_GOUVERNEMENTAL.md** : Stack détaillé
- **SECURITE_CONFORMITE.md** : Sécurité et conformité
- **API_ARCHITECTURE.md** : Architecture API
- **DECISION_CONTEXTE.md** : Pourquoi ce stack

---

## ✅ Checklist Installation

- [ ] Java 21 installé
- [ ] Maven/Gradle installé
- [ ] Node.js 20+ installé
- [ ] Angular CLI installé
- [ ] Docker lancé
- [ ] PostgreSQL accessible
- [ ] Redis accessible
- [ ] Kafka accessible (optionnel POC)
- [ ] Backend Spring Boot démarre (port 8080)
- [ ] Frontend Angular démarre (port 4200)
- [ ] Keycloak accessible (port 8180)

---

## 🆘 Problèmes Courants

### Port déjà utilisé (8080)
```bash
# Trouver le processus
netstat -ano | findstr :8080

# Tuer le processus
taskkill /PID <PID> /F
```

### Erreur de connexion PostgreSQL
```bash
# Vérifier que Docker tourne
docker ps

# Vérifier les logs
docker logs shifa_postgres
```

### Erreur Maven
```bash
# Nettoyer et réinstaller
mvn clean install -U
```

---

## 🎯 Prochaines Étapes

1. **Semaine 1-2** : Setup infrastructure complète
2. **Semaine 3-4** : Authentification avec Keycloak
3. **Semaine 5-8** : Modules métier (Patients, Médecins)
4. **Semaine 9-12** : Remboursements et intégrations
5. **Semaine 13-16** : Tests et sécurité
6. **Semaine 17-20** : Déploiement

**Budget estimé 3 ans** : 40-50M MAD  
**Équipe recommandée** : 20+ développeurs

---

**Créé pour** : Shifa+ Application Gouvernementale  
**Date** : Octobre 2025  
**Stack** : Spring Boot 3 + Angular 17 + Kafka

**Bon développement ! 🇲🇦**


