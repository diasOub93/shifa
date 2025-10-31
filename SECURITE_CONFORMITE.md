# 🔒 Sécurité & Conformité - Shifa+

## ⚠️ ATTENTION : Document Critique

Ce document décrit les mesures de sécurité et de conformité **OBLIGATOIRES** pour Shifa+.  
Ces exigences ne sont **PAS OPTIONNELLES** dans le secteur de la santé.

---

## 📋 Table des Matières

1. [Conformité Réglementaire](#conformité-réglementaire)
2. [Sécurité des Données](#sécurité-des-données)
3. [Authentification & Contrôle d'Accès](#authentification--contrôle-daccès)
4. [Chiffrement](#chiffrement)
5. [Audit & Traçabilité](#audit--traçabilité)
6. [Protection des Données Personnelles](#protection-des-données-personnelles)
7. [Sécurité Applicative](#sécurité-applicative)
8. [Infrastructure & Hébergement](#infrastructure--hébergement)
9. [Gestion des Incidents](#gestion-des-incidents)
10. [Formation & Sensibilisation](#formation--sensibilisation)

---

## 1. Conformité Réglementaire

### 🇲🇦 Cadre Juridique Marocain

#### Loi 09-08 (Protection des Données Personnelles)
✅ **Obligations** :
- Déclaration à la CNDP (Commission Nationale de Contrôle de la Protection des Données à Caractère Personnel)
- Désignation d'un responsable de traitement
- Registre des traitements de données
- Consentement explicite des utilisateurs
- Droit d'accès, de rectification et de suppression

#### Loi 31-08 (Mesures de Protection du Consommateur)
✅ **Obligations** :
- Transparence des tarifs
- Information claire sur les services
- Protection contre les pratiques commerciales trompeuses

#### Code de Déontologie Médicale Marocain
✅ **Obligations** :
- Secret médical absolu
- Consentement éclairé du patient
- Traçabilité des actes médicaux

### 🇪🇺 RGPD (Applicable si expansion européenne)

✅ **Principes à Respecter** :
1. **Licéité, loyauté et transparence**
2. **Limitation des finalités**
3. **Minimisation des données**
4. **Exactitude**
5. **Limitation de la conservation**
6. **Intégrité et confidentialité**
7. **Responsabilité** (accountability)

### 📄 Certifications Recommandées

| Certification | Priorité | Description |
|--------------|----------|-------------|
| **ISO 27001** | ⭐⭐⭐ Critique | Système de management de la sécurité |
| **ISO 27017** | ⭐⭐ Haute | Sécurité des services cloud |
| **ISO 27018** | ⭐⭐ Haute | Protection des données personnelles cloud |
| **HDS** (France) | ⭐ Moyenne | Hébergement Données de Santé (si applicable) |
| **SOC 2 Type II** | ⭐ Moyenne | Contrôles de sécurité opérationnels |

---

## 2. Sécurité des Données

### 🛡️ Classification des Données

| Niveau | Type de Données | Exemples | Protection |
|--------|----------------|----------|------------|
| **Niveau 4 - Critique** | Données de santé sensibles | Résultats HIV, antécédents psychiatriques | Chiffrement AES-256 + Accès ultra-restreint |
| **Niveau 3 - Confidentiel** | Données médicales standard | Ordonnances, résultats d'analyses | Chiffrement AES-256 + Accès contrôlé |
| **Niveau 2 - Interne** | Données administratives | Nom, prénom, adresse | Chiffrement + Accès authentifié |
| **Niveau 1 - Public** | Informations générales | FAQ, articles santé | Protection standard |

### 🔐 Mesures de Protection

#### Chiffrement au Repos
```typescript
// Exemple de chiffrement de document médical
import { createCipheriv, randomBytes } from 'crypto';

export class EncryptionService {
  private algorithm = 'aes-256-gcm';
  private key = Buffer.from(process.env.ENCRYPTION_KEY, 'hex');

  encrypt(data: Buffer): {
    encrypted: Buffer;
    iv: Buffer;
    authTag: Buffer;
  } {
    const iv = randomBytes(16);
    const cipher = createCipheriv(this.algorithm, this.key, iv);
    
    const encrypted = Buffer.concat([
      cipher.update(data),
      cipher.final()
    ]);
    
    const authTag = cipher.getAuthTag();
    
    return { encrypted, iv, authTag };
  }

  decrypt(encrypted: Buffer, iv: Buffer, authTag: Buffer): Buffer {
    const decipher = createDecipheriv(this.algorithm, this.key, iv);
    decipher.setAuthTag(authTag);
    
    return Buffer.concat([
      decipher.update(encrypted),
      decipher.final()
    ]);
  }
}
```

#### Chiffrement en Transit
- ✅ **TLS 1.3** obligatoire
- ✅ Certificats SSL/TLS valides (Let's Encrypt ou commercial)
- ✅ HSTS (HTTP Strict Transport Security)
- ✅ Désactivation de TLS 1.0 et 1.1
- ✅ Forward Secrecy (FS)

#### Protection des Backups
- ✅ Backups chiffrés (AES-256)
- ✅ Stockage géographiquement distant
- ✅ Tests de restauration mensuels
- ✅ Rétention : 7 ans minimum (légal)
- ✅ Destruction sécurisée après rétention

---

## 3. Authentification & Contrôle d'Accès

### 🔑 Authentification Multi-Facteurs (MFA)

**Obligatoire pour** :
- ✅ Tous les professionnels de santé
- ✅ Personnel des assurances
- ✅ Administrateurs
- ✅ Comptes avec accès aux données sensibles

**Méthodes supportées** :
1. **TOTP** (Time-based One-Time Password) - Google Authenticator, Authy
2. **SMS OTP** (One-Time Password par SMS)
3. **Email OTP** (en dernier recours)
4. **Clés de sécurité** (FIDO2/WebAuthn) - recommandé

### 🛡️ Politique de Mots de Passe

```typescript
// Règles de validation des mots de passe
const passwordPolicy = {
  minLength: 12,
  maxLength: 128,
  requireUppercase: true,
  requireLowercase: true,
  requireNumbers: true,
  requireSpecialChars: true,
  preventCommonPasswords: true,
  preventUserInfoInPassword: true,
  expiryDays: 90, // Rotation tous les 3 mois
  preventReuseCount: 5, // Pas de réutilisation des 5 derniers
};

// Hashing avec bcrypt (cost factor 12 minimum)
import * as bcrypt from 'bcrypt';

const saltRounds = 12;
const hashedPassword = await bcrypt.hash(password, saltRounds);
```

### 🎭 Contrôle d'Accès Basé sur les Rôles (RBAC)

```typescript
// Définition des rôles et permissions
enum Role {
  PATIENT = 'PATIENT',
  MEDECIN = 'MEDECIN',
  PHARMACIEN = 'PHARMACIEN',
  LABORATOIRE = 'LABORATOIRE',
  ASSURANCE = 'ASSURANCE',
  ADMIN = 'ADMIN',
  SUPER_ADMIN = 'SUPER_ADMIN'
}

enum Permission {
  // Dossiers médicaux
  VIEW_OWN_MEDICAL_RECORD = 'VIEW_OWN_MEDICAL_RECORD',
  VIEW_ANY_MEDICAL_RECORD = 'VIEW_ANY_MEDICAL_RECORD',
  EDIT_MEDICAL_RECORD = 'EDIT_MEDICAL_RECORD',
  
  // Ordonnances
  CREATE_PRESCRIPTION = 'CREATE_PRESCRIPTION',
  VIEW_PRESCRIPTION = 'VIEW_PRESCRIPTION',
  
  // Remboursements
  SUBMIT_REIMBURSEMENT = 'SUBMIT_REIMBURSEMENT',
  PROCESS_REIMBURSEMENT = 'PROCESS_REIMBURSEMENT',
  APPROVE_REIMBURSEMENT = 'APPROVE_REIMBURSEMENT',
  
  // Administration
  MANAGE_USERS = 'MANAGE_USERS',
  VIEW_AUDIT_LOGS = 'VIEW_AUDIT_LOGS',
  MANAGE_SYSTEM = 'MANAGE_SYSTEM'
}

// Matrice Rôle-Permission
const rolePermissions: Record<Role, Permission[]> = {
  [Role.PATIENT]: [
    Permission.VIEW_OWN_MEDICAL_RECORD,
    Permission.SUBMIT_REIMBURSEMENT,
    Permission.VIEW_PRESCRIPTION
  ],
  [Role.MEDECIN]: [
    Permission.VIEW_ANY_MEDICAL_RECORD,
    Permission.EDIT_MEDICAL_RECORD,
    Permission.CREATE_PRESCRIPTION,
    Permission.VIEW_PRESCRIPTION
  ],
  [Role.ASSURANCE]: [
    Permission.VIEW_ANY_MEDICAL_RECORD, // Limité au contexte de remboursement
    Permission.PROCESS_REIMBURSEMENT,
    Permission.APPROVE_REIMBURSEMENT
  ],
  [Role.ADMIN]: [
    Permission.MANAGE_USERS,
    Permission.VIEW_AUDIT_LOGS
  ],
  [Role.SUPER_ADMIN]: Object.values(Permission) // Toutes les permissions
};
```

### 🕒 Gestion des Sessions

```typescript
// Configuration des sessions sécurisées
const sessionConfig = {
  secret: process.env.SESSION_SECRET,
  resave: false,
  saveUninitialized: false,
  cookie: {
    secure: true, // HTTPS uniquement
    httpOnly: true, // Pas d'accès JavaScript
    sameSite: 'strict',
    maxAge: 30 * 60 * 1000, // 30 minutes
  },
  rolling: true, // Renouvellement à chaque requête
};

// Timeout d'inactivité : 15 minutes pour données sensibles
const INACTIVITY_TIMEOUT = 15 * 60 * 1000;
```

---

## 4. Chiffrement

### 🔐 Standards de Chiffrement

| Type | Algorithme | Taille de Clé | Usage |
|------|-----------|---------------|-------|
| **Symétrique** | AES-GCM | 256 bits | Documents, données au repos |
| **Asymétrique** | RSA | 4096 bits | Échange de clés |
| **Hash** | bcrypt | Cost 12+ | Mots de passe |
| **Hash** | SHA-256 | - | Intégrité des fichiers |
| **Hash** | SHA-512 | - | Signatures numériques |

### 🔑 Gestion des Clés (Key Management)

#### Hiérarchie des Clés
```
Master Key (HSM ou KMS)
    ↓
Data Encryption Keys (DEK) - Par type de donnée
    ↓
Key Encryption Keys (KEK) - Par tenant/organisation
    ↓
Données chiffrées
```

#### Bonnes Pratiques
- ✅ Rotation des clés tous les 6 mois
- ✅ Clés stockées dans un HSM ou KMS (AWS KMS, Azure Key Vault)
- ✅ Séparation des clés par environnement (dev, staging, prod)
- ✅ Backup sécurisé des clés
- ✅ Procédure de révocation documentée

### 📄 Chiffrement des Documents Médicaux

```typescript
// Service de chiffrement de documents
@Injectable()
export class DocumentEncryptionService {
  private kms: KMSClient; // AWS KMS ou équivalent
  
  async encryptDocument(
    documentBuffer: Buffer,
    patientId: string
  ): Promise<EncryptedDocument> {
    // 1. Générer une clé de chiffrement de données (DEK) unique
    const dataKey = await this.kms.generateDataKey({
      KeyId: process.env.MASTER_KEY_ID,
      KeySpec: 'AES_256'
    });
    
    // 2. Chiffrer le document avec la DEK
    const { encrypted, iv, authTag } = this.encrypt(
      documentBuffer,
      dataKey.Plaintext
    );
    
    // 3. Stocker la DEK chiffrée (par la master key) avec le document
    return {
      encryptedData: encrypted,
      encryptedDataKey: dataKey.CiphertextBlob,
      iv,
      authTag,
      algorithm: 'aes-256-gcm',
      patientId,
      encryptedAt: new Date()
    };
  }
  
  async decryptDocument(
    encryptedDoc: EncryptedDocument
  ): Promise<Buffer> {
    // 1. Déchiffrer la DEK avec la master key
    const dataKey = await this.kms.decrypt({
      CiphertextBlob: encryptedDoc.encryptedDataKey
    });
    
    // 2. Déchiffrer le document avec la DEK
    return this.decrypt(
      encryptedDoc.encryptedData,
      dataKey.Plaintext,
      encryptedDoc.iv,
      encryptedDoc.authTag
    );
  }
}
```

---

## 5. Audit & Traçabilité

### 📊 Logging des Actions Sensibles

**Événements à Logger** :
- ✅ Authentification (succès/échec)
- ✅ Accès aux dossiers médicaux
- ✅ Modification de données patient
- ✅ Soumission/validation de remboursement
- ✅ Upload/téléchargement de documents
- ✅ Modification de permissions
- ✅ Exports de données
- ✅ Tentatives d'accès non autorisé

### 📝 Format des Logs d'Audit

```typescript
interface AuditLog {
  id: string;
  timestamp: Date;
  userId: string;
  userRole: Role;
  action: AuditAction;
  resource: {
    type: string; // Patient, Document, Remboursement, etc.
    id: string;
  };
  result: 'SUCCESS' | 'FAILURE';
  ipAddress: string;
  userAgent: string;
  location?: {
    city: string;
    country: string;
  };
  metadata?: Record<string, any>;
  severity: 'LOW' | 'MEDIUM' | 'HIGH' | 'CRITICAL';
}

enum AuditAction {
  // Authentification
  LOGIN = 'LOGIN',
  LOGOUT = 'LOGOUT',
  LOGIN_FAILED = 'LOGIN_FAILED',
  PASSWORD_RESET = 'PASSWORD_RESET',
  
  // Accès aux données
  VIEW_MEDICAL_RECORD = 'VIEW_MEDICAL_RECORD',
  DOWNLOAD_DOCUMENT = 'DOWNLOAD_DOCUMENT',
  EXPORT_DATA = 'EXPORT_DATA',
  
  // Modifications
  CREATE_PATIENT = 'CREATE_PATIENT',
  UPDATE_PATIENT = 'UPDATE_PATIENT',
  DELETE_PATIENT = 'DELETE_PATIENT',
  
  CREATE_PRESCRIPTION = 'CREATE_PRESCRIPTION',
  
  // Remboursements
  SUBMIT_REIMBURSEMENT = 'SUBMIT_REIMBURSEMENT',
  APPROVE_REIMBURSEMENT = 'APPROVE_REIMBURSEMENT',
  REJECT_REIMBURSEMENT = 'REJECT_REIMBURSEMENT',
  
  // Administration
  GRANT_PERMISSION = 'GRANT_PERMISSION',
  REVOKE_PERMISSION = 'REVOKE_PERMISSION',
  
  // Sécurité
  UNAUTHORIZED_ACCESS_ATTEMPT = 'UNAUTHORIZED_ACCESS_ATTEMPT',
  SUSPICIOUS_ACTIVITY = 'SUSPICIOUS_ACTIVITY'
}

// Exemple d'utilisation
@Injectable()
export class AuditService {
  async log(auditData: Partial<AuditLog>): Promise<void> {
    const log = {
      ...auditData,
      id: uuid(),
      timestamp: new Date(),
    };
    
    // 1. Enregistrer dans la base de données
    await this.prisma.auditLog.create({ data: log });
    
    // 2. Envoyer vers un système de logs centralisé (ELK, Splunk, etc.)
    await this.logAggregator.send(log);
    
    // 3. Alerter si critique
    if (log.severity === 'CRITICAL') {
      await this.alerting.sendAlert(log);
    }
  }
}
```

### ⏰ Rétention des Logs

| Type de Log | Durée de Conservation | Justification |
|-------------|----------------------|---------------|
| **Logs d'audit santé** | 10 ans minimum | Obligation légale |
| **Logs d'authentification** | 1 an | Sécurité |
| **Logs applicatifs** | 3 mois | Debug et monitoring |
| **Logs de performance** | 1 mois | Optimisation |

---

## 6. Protection des Données Personnelles

### 🛡️ Principes RGPD Appliqués

#### 1. Minimisation des Données
```typescript
// ❌ Mauvais - Collecte excessive
interface PatientForm {
  // ... données médicales nécessaires
  favoriteColor: string; // Non pertinent!
  zodiacSign: string; // Non pertinent!
}

// ✅ Bon - Uniquement les données nécessaires
interface PatientForm {
  nom: string;
  prenom: string;
  dateNaissance: Date;
  cin: string;
  telephone: string;
  email: string;
  numeroAssurance?: string;
  // Seulement les données nécessaires au service
}
```

#### 2. Consentement Explicite
```typescript
interface ConsentRecord {
  patientId: string;
  consentType: ConsentType;
  granted: boolean;
  grantedAt: Date;
  ipAddress: string;
  version: string; // Version des CGU/politique de confidentialité
}

enum ConsentType {
  TERMS_OF_SERVICE = 'TERMS_OF_SERVICE',
  PRIVACY_POLICY = 'PRIVACY_POLICY',
  DATA_PROCESSING = 'DATA_PROCESSING',
  MARKETING = 'MARKETING', // Optionnel
  DATA_SHARING = 'DATA_SHARING', // Partage avec tiers
}
```

#### 3. Droit d'Accès (Export des Données)
```typescript
@Controller('api/patients/me')
export class PatientDataController {
  @Get('/export')
  @UseGuards(JwtAuthGuard)
  async exportMyData(@CurrentUser() user: User) {
    // Collecter toutes les données du patient
    const patientData = {
      profile: await this.prisma.patient.findUnique({
        where: { userId: user.id }
      }),
      medicalRecord: await this.prisma.dossierMedical.findUnique({
        where: { patientId: patient.id }
      }),
      prescriptions: await this.prisma.ordonnance.findMany({
        where: { dossierId: medicalRecord.id }
      }),
      reimbursements: await this.prisma.remboursement.findMany({
        where: { patientId: patient.id }
      }),
      documents: await this.prisma.document.findMany({
        where: { dossierId: medicalRecord.id }
      }),
      auditLogs: await this.prisma.auditLog.findMany({
        where: { 
          entity: 'Patient',
          entityId: patient.id
        }
      }),
    };
    
    // Générer un PDF ou JSON
    const exportFile = await this.generateExport(patientData);
    
    // Logger l'export
    await this.audit.log({
      action: AuditAction.EXPORT_DATA,
      userId: user.id,
      severity: 'HIGH'
    });
    
    return exportFile;
  }
}
```

#### 4. Droit à l'Oubli
```typescript
@Delete('/account')
@UseGuards(JwtAuthGuard)
async deleteAccount(@CurrentUser() user: User) {
  // Attention : Contraintes légales pour les données de santé
  // Certaines données doivent être conservées 10 ans minimum
  
  // 1. Anonymiser les données plutôt que supprimer
  await this.anonymizePatientData(user.id);
  
  // 2. Supprimer les données non soumises à obligation légale
  await this.deleteNonEssentialData(user.id);
  
  // 3. Logs de suppression (obligatoire)
  await this.audit.log({
    action: AuditAction.DELETE_PATIENT,
    userId: user.id,
    severity: 'CRITICAL',
    metadata: {
      reason: 'User requested account deletion',
      dataRetained: 'Medical records anonymized and retained for legal compliance'
    }
  });
}

private async anonymizePatientData(userId: string) {
  // Remplacer les données identifiantes par des valeurs anonymes
  await this.prisma.patient.update({
    where: { userId },
    data: {
      nom: `ANONYME_${randomBytes(8).toString('hex')}`,
      prenom: 'ANONYME',
      cin: `DELETED_${Date.now()}`,
      email: `deleted_${Date.now()}@anonymized.local`,
      telephone: 'DELETED',
      adresse: 'DELETED',
      // Conserver les données médicales anonymisées (obligation légale)
    }
  });
}
```

### 🔐 Pseudonymisation

```typescript
// Pseudonymiser les identifiants pour les études statistiques
function pseudonymize(patientId: string, salt: string): string {
  return createHash('sha256')
    .update(patientId + salt)
    .digest('hex');
}

// Exemple d'utilisation pour analytics
interface AnonymizedAnalytics {
  pseudoId: string; // Hash du patientId
  age: number;
  region: string; // Ville -> Région
  pathologie: string;
  // Pas de données directement identifiantes
}
```

---

## 7. Sécurité Applicative

### 🛡️ Protection contre les Vulnérabilités OWASP Top 10

#### 1. Injection (SQL, NoSQL, etc.)
```typescript
// ❌ Mauvais - Vulnérable à l'injection SQL
const query = `SELECT * FROM patients WHERE cin = '${userInput}'`;

// ✅ Bon - Utiliser un ORM (Prisma)
const patient = await prisma.patient.findUnique({
  where: { cin: userInput } // Paramétré automatiquement
});

// ✅ Validation des entrées
import { IsString, IsNotEmpty, Length } from 'class-validator';

class FindPatientDto {
  @IsString()
  @IsNotEmpty()
  @Length(8, 8) // CIN marocain = 8 caractères
  cin: string;
}
```

#### 2. Broken Authentication
```typescript
// ✅ Implémentation sécurisée
- MFA obligatoire
- Rate limiting sur /login
- Compte bloqué après 5 tentatives échouées
- Déconnexion automatique après inactivité
- Tokens JWT avec expiration courte
- Refresh tokens avec rotation
```

#### 3. Sensitive Data Exposure
```typescript
// ❌ Mauvais - Fuite de données sensibles
res.json({
  patient: {
    id: '123',
    nom: 'Alami',
    prenom: 'Mohammed',
    password: '$2b$12$...', // ❌ Ne JAMAIS exposer!
    ssn: '1234567890', // ❌ Données sensibles
  }
});

// ✅ Bon - DTO avec exclusion des champs sensibles
@Exclude()
export class PatientDto {
  @Expose()
  id: string;
  
  @Expose()
  nom: string;
  
  @Expose()
  prenom: string;
  
  // password est exclu par défaut (@Exclude() sur la classe)
}
```

#### 4. XML External Entities (XXE)
```typescript
// ✅ Désactiver le parsing XML externe
import * as xml2js from 'xml2js';

const parser = new xml2js.Parser({
  explicitArray: false,
  ignoreAttrs: false,
  // Sécurité XXE
  xmlns: false,
  xmldec: { standalone: null },
});
```

#### 5. Broken Access Control
```typescript
// ✅ Vérification stricte des permissions
@Get('/patients/:id/medical-record')
@UseGuards(JwtAuthGuard, RolesGuard)
@Roles(Role.MEDECIN, Role.PATIENT)
async getMedicalRecord(
  @Param('id') patientId: string,
  @CurrentUser() user: User
) {
  // Vérifier que l'utilisateur a le droit d'accéder à CE patient
  if (user.role === Role.PATIENT && user.patientId !== patientId) {
    throw new ForbiddenException('You can only access your own medical record');
  }
  
  if (user.role === Role.MEDECIN) {
    // Vérifier que le médecin a une relation thérapeutique active
    const hasAccess = await this.checkTherapeuticRelationship(
      user.medecinId,
      patientId
    );
    if (!hasAccess) {
      throw new ForbiddenException('No therapeutic relationship');
    }
  }
  
  // Logger l'accès
  await this.audit.log({
    action: AuditAction.VIEW_MEDICAL_RECORD,
    userId: user.id,
    resource: { type: 'Patient', id: patientId },
    severity: 'HIGH'
  });
  
  return this.getMedicalRecordData(patientId);
}
```

#### 6. Security Misconfiguration
```typescript
// main.ts - Configuration sécurisée
import helmet from 'helmet';
import * as compression from 'compression';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  
  // Headers de sécurité
  app.use(helmet({
    contentSecurityPolicy: {
      directives: {
        defaultSrc: ["'self'"],
        styleSrc: ["'self'", "'unsafe-inline'"],
        scriptSrc: ["'self'"],
        imgSrc: ["'self'", 'data:', 'https:'],
      },
    },
    hsts: {
      maxAge: 31536000,
      includeSubDomains: true,
      preload: true
    },
  }));
  
  // CORS strict
  app.enableCors({
    origin: process.env.FRONTEND_URL,
    credentials: true,
    methods: ['GET', 'POST', 'PUT', 'PATCH', 'DELETE'],
    allowedHeaders: ['Content-Type', 'Authorization'],
  });
  
  // Compression
  app.use(compression());
  
  // Validation globale
  app.useGlobalPipes(new ValidationPipe({
    whitelist: true,
    forbidNonWhitelisted: true,
    transform: true,
  }));
  
  // Rate limiting
  app.use(
    rateLimit({
      windowMs: 15 * 60 * 1000, // 15 minutes
      max: 100, // Limite à 100 requêtes
    })
  );
  
  await app.listen(process.env.PORT || 3001);
}
```

#### 7. Cross-Site Scripting (XSS)
```typescript
// ✅ Frontend - Sanitization
import DOMPurify from 'dompurify';

function DisplayUserContent({ content }: { content: string }) {
  const sanitized = DOMPurify.sanitize(content);
  return <div dangerouslySetInnerHTML={{ __html: sanitized }} />;
}

// ✅ Backend - Validation et échappement
import { IsString, IsNotEmpty } from 'class-validator';
import * as validator from 'validator';

class CreateNoteDto {
  @IsString()
  @IsNotEmpty()
  @Transform(({ value }) => validator.escape(value))
  content: string;
}
```

#### 8. Insecure Deserialization
```typescript
// ✅ Validation stricte des données désérialisées
import { plainToInstance } from 'class-transformer';
import { validate } from 'class-validator';

async function safeDeserialize<T>(
  cls: new () => T,
  plain: any
): Promise<T> {
  const instance = plainToInstance(cls, plain);
  const errors = await validate(instance);
  
  if (errors.length > 0) {
    throw new BadRequestException('Validation failed');
  }
  
  return instance;
}
```

#### 9. Using Components with Known Vulnerabilities
```bash
# Audit régulier des dépendances
npm audit
npm audit fix

# Utiliser Snyk ou Dependabot
snyk test
```

#### 10. Insufficient Logging & Monitoring
```typescript
// ✅ Logging complet (voir section Audit ci-dessus)
// ✅ Monitoring en temps réel avec alertes
// ✅ Détection d'anomalies
```

### 🚨 Rate Limiting & Protection DDoS

```typescript
// Rate limiting par endpoint et par utilisateur
@ThrottlerModule.forRoot({
  ttl: 60,
  limit: 10,
})

// Rate limiting spécifique par endpoint sensible
@Throttle(5, 60) // 5 requêtes par minute
@Post('/auth/login')
async login(@Body() credentials: LoginDto) {
  // ...
}
```

---

## 8. Infrastructure & Hébergement

### 🏗️ Architecture Sécurisée

```
                    Internet
                       │
                       ▼
              ┌────────────────┐
              │  WAF / CDN     │ (Cloudflare, AWS WAF)
              │  - DDoS Protect │
              │  - SSL/TLS      │
              └────────┬───────┘
                       │
              ┌────────▼───────┐
              │ Load Balancer  │
              └────────┬───────┘
                       │
         ┌─────────────┴─────────────┐
         │                           │
    ┌────▼─────┐              ┌──────▼────┐
    │  App     │              │   App     │
    │ Server 1 │              │ Server 2  │
    └────┬─────┘              └──────┬────┘
         │                           │
         └─────────────┬─────────────┘
                       │
         ┌─────────────┴─────────────┬─────────────┐
         │                           │             │
    ┌────▼────────┐          ┌───────▼───┐  ┌─────▼──────┐
    │ PostgreSQL  │          │   Redis   │  │   MinIO    │
    │ (Primary)   │          │  (Cache)  │  │(Documents) │
    └────┬────────┘          └───────────┘  └────────────┘
         │
    ┌────▼────────┐
    │ PostgreSQL  │
    │ (Replica)   │
    └─────────────┘
```

### ☁️ Hébergement

#### Options Recommandées

| Provider | Avantages | Inconvénients |
|----------|-----------|---------------|
| **AWS** | Complet, scalable, conformité | Coûteux, complexe |
| **Azure** | Conformité EU/Afrique, HDS | Coûteux |
| **OVH Cloud** | Prix, RGPD, datacenters FR | Moins de services |
| **DigitalOcean** | Simple, économique | Moins enterprise |
| **Hébergement local (Maroc)** | Souveraineté des données | Infrastructure à valider |

#### Critères de Sélection
- ✅ Datacenters certifiés ISO 27001
- ✅ Conformité RGPD
- ✅ SLA ≥ 99.9%
- ✅ Backup automatique
- ✅ Support 24/7
- ✅ Chiffrement au repos et en transit

### 🔒 Sécurité Réseau

```typescript
// Firewall rules (exemple AWS Security Groups)
Inbound Rules:
- Port 443 (HTTPS) : 0.0.0.0/0 (Internet)
- Port 80 (HTTP) : 0.0.0.0/0 → Redirect to 443
- Port 22 (SSH) : Bastion Host uniquement
- Port 5432 (PostgreSQL) : Application servers uniquement
- Port 6379 (Redis) : Application servers uniquement

Outbound Rules:
- Tout le trafic sortant autorisé (à restreindre si possible)
```

### 🛡️ Isolation

- ✅ VPC (Virtual Private Cloud) séparé
- ✅ Subnets privés pour BDD
- ✅ Subnets publics pour load balancers uniquement
- ✅ Network ACLs restrictifs
- ✅ Bastion host pour administration

---

## 9. Gestion des Incidents

### 🚨 Plan de Réponse aux Incidents (PRI)

#### Phase 1 : Détection
- Monitoring 24/7
- Alertes automatiques (Sentry, PagerDuty)
- Logs centralisés

#### Phase 2 : Confinement
1. Isoler le système compromis
2. Bloquer l'accès malveillant
3. Préserver les preuves (logs)

#### Phase 3 : Éradication
1. Identifier la cause racine
2. Supprimer la menace
3. Patcher les vulnérabilités

#### Phase 4 : Récupération
1. Restaurer depuis backup
2. Vérifier l'intégrité
3. Remettre en service progressivement

#### Phase 5 : Leçons Apprises
1. Post-mortem
2. Documentation
3. Mise à jour des procédures

### 📞 Contacts d'Urgence

```yaml
Security Team:
  - Lead: security@shifa.ma
  - Phone: +212 XXX XXX XXX
  
Infrastructure:
  - DevOps Lead: devops@shifa.ma
  - On-call: +212 XXX XXX XXX
  
Legal/Compliance:
  - DPO: dpo@shifa.ma
  - Phone: +212 XXX XXX XXX
  
External:
  - Hosting Provider: support@provider.com
  - Cert-MA (DGSSI): cert@dgssi.gov.ma
```

### 📋 Obligation de Notification

#### CNDP (Maroc)
- Délai : **72 heures** maximum après découverte
- Contenu : Nature de la violation, données concernées, mesures prises

#### RGPD (si applicable)
- Délai : **72 heures** maximum
- Notification aux personnes concernées si risque élevé

---

## 10. Formation & Sensibilisation

### 👨‍💻 Formation Continue

**Personnel IT** :
- ✅ Formation sécurité applicative (annuelle)
- ✅ Certifications recommandées : CEH, CISSP, OSCP
- ✅ Veille sécurité continue

**Tous les Employés** :
- ✅ Sensibilisation à la sécurité (trimestrielle)
- ✅ Simulation de phishing
- ✅ Gestion des mots de passe
- ✅ Identification des menaces

**Professionnels de Santé** :
- ✅ RGPD et protection des données (obligatoire)
- ✅ Secret médical numérique
- ✅ Utilisation sécurisée de la plateforme

### 📚 Documentation

- ✅ Politique de sécurité formalisée
- ✅ Procédures opérationnelles
- ✅ Plan de continuité d'activité (PCA)
- ✅ Plan de reprise d'activité (PRA)
- ✅ Registre RGPD

---

## ✅ Checklist de Conformité

### Avant le Lancement

- [ ] Analyse d'impact (DPIA/AIPD) complétée
- [ ] DPO désigné
- [ ] Déclaration CNDP effectuée
- [ ] Politique de confidentialité publiée
- [ ] CGU/CGV validées juridiquement
- [ ] Consentements utilisateurs implémentés
- [ ] Audit de sécurité (pentest) réalisé
- [ ] Backup et PRA testés
- [ ] Chiffrement activé partout
- [ ] Logs d'audit opérationnels
- [ ] Formation équipe effectuée
- [ ] Plan de réponse aux incidents documenté
- [ ] Contacts d'urgence définis
- [ ] Certificats SSL/TLS actifs
- [ ] Rate limiting configuré
- [ ] Monitoring et alertes actifs

### Récurrent (Mensuel/Annuel)

- [ ] Audit des permissions utilisateurs
- [ ] Revue des logs d'audit
- [ ] Test de restauration des backups
- [ ] Mise à jour des dépendances
- [ ] Scan de vulnérabilités
- [ ] Revue des accès administrateurs
- [ ] Formation de sensibilisation
- [ ] Exercice de simulation d'incident
- [ ] Audit de conformité RGPD

---

## 📞 Support & Ressources

### Autorités Marocaines
- **CNDP** : www.cndp.ma - Protection des données
- **DGSSI** : www.dgssi.gov.ma - Cybersécurité
- **ANRT** : www.anrt.ma - Réglementation télécoms

### Standards Internationaux
- **ISO 27001** : Sécurité de l'information
- **RGPD** : Protection des données EU
- **OWASP** : Sécurité applicative

### Outils Recommandés
- **Sécurité** : Snyk, OWASP ZAP, Burp Suite
- **Monitoring** : Sentry, Datadog, New Relic
- **Logs** : ELK Stack, Splunk
- **Pentest** : HackerOne, Bugcrowd

---

**IMPORTANT** : Ce document est un guide. Consultez des experts en cybersécurité et des juristes spécialisés en droit de la santé pour une conformité complète.

**Dernière mise à jour** : Octobre 2025

