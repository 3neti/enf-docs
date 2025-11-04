# ENF Platform - Technical Architecture Document

**Version:** 1.0  
**Last Updated:** November 2025

---

## System Overview

The ENF Platform is built as a cloud-native, microservices-based architecture designed for scalability, reliability, and compliance with Philippine Supreme Court regulations.

### Architecture Principles

1. **Separation of Concerns** - Microservices for distinct business domains
2. **API-First Design** - RESTful APIs with OpenAPI/Swagger documentation
3. **Security by Default** - Zero-trust architecture, encryption everywhere
4. **Scalability** - Horizontal scaling for all services
5. **Observability** - Comprehensive logging, monitoring, and tracing
6. **Compliance** - Built-in audit trails, data retention, and regulatory reporting

---

## High-Level Architecture Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                     Client Applications                      │
├─────────────────┬─────────────────┬────────────────────────┤
│  Customer App   │    ENP App      │   Admin Dashboard      │
│  (React Native) │ (React Native/  │    (Next.js)           │
│                 │  Next.js)       │                        │
└────────┬────────┴────────┬────────┴───────────┬────────────┘
         │                 │                    │
         └─────────────────┼────────────────────┘
                           │
                    ┌──────▼──────┐
                    │  CloudFront │ (CDN)
                    │     CDN     │
                    └──────┬──────┘
                           │
                    ┌──────▼──────┐
                    │     ALB     │ (Load Balancer)
                    │  + WAF      │
                    └──────┬──────┘
                           │
         ┌─────────────────┼─────────────────┐
         │                 │                 │
    ┌────▼─────┐    ┌─────▼─────┐    ┌─────▼─────┐
    │   API    │    │  WebSocket│    │   Admin   │
    │  Gateway │    │  Gateway  │    │  Gateway  │
    └────┬─────┘    └─────┬─────┘    └─────┬─────┘
         │                │                 │
    ┌────┴─────────────────┴─────────────────┴────┐
    │           Microservices Layer                │
    ├──────────────────────────────────────────────┤
    │  User │ KYC │ Matching │ Notarization │ ... │
    └────┬────────────────────────────────────┬────┘
         │                                    │
    ┌────▼────────────────────────────────────▼────┐
    │         Data & Infrastructure Layer          │
    ├──────────────────────────────────────────────┤
    │  PostgreSQL │ Redis │ S3 │ SQS │ ElasticSearch│
    └──────────────────────────────────────────────┘
```

---

## Microservices Architecture

### 1. User Service
**Responsibility:** User authentication, profile management, session handling

**Tech Stack:**
- Node.js (Express) or Go
- PostgreSQL (user data)
- Redis (sessions, rate limiting)
- JWT for authentication

**Key Endpoints:**
```
POST /api/v1/users/register
POST /api/v1/users/login
GET  /api/v1/users/profile
PUT  /api/v1/users/profile
POST /api/v1/users/logout
POST /api/v1/users/refresh-token
POST /api/v1/users/forgot-password
POST /api/v1/users/reset-password
```

**Database Schema:**
```sql
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255) UNIQUE NOT NULL,
    phone VARCHAR(20) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    user_type ENUM('customer', 'enp', 'admin') NOT NULL,
    kyc_status ENUM('pending', 'verified', 'rejected') DEFAULT 'pending',
    is_active BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW(),
    last_login_at TIMESTAMP
);

CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_phone ON users(phone);
CREATE INDEX idx_users_type ON users(user_type);
```

---

### 2. KYC Service
**Responsibility:** Identity verification via HyperVerge API

**Tech Stack:**
- Node.js or Python
- HyperVerge SDK
- S3 for ID/selfie storage
- SQS for async processing

**Integration Flow:**
```
1. Customer uploads ID image → Upload to S3
2. Customer takes selfie → Upload to S3
3. Call HyperVerge API with image URLs
4. HyperVerge returns:
   - ID OCR data (name, birthday, ID number)
   - Face liveness score
   - Face match score (selfie vs ID photo)
5. Store results in PostgreSQL
6. Update user KYC status
7. Notify user via Notification Service
```

**Key Endpoints:**
```
POST /api/v1/kyc/sessions/create
POST /api/v1/kyc/upload-id
POST /api/v1/kyc/upload-selfie
POST /api/v1/kyc/submit
GET  /api/v1/kyc/status/:session_id
POST /api/v1/kyc/retry
```

**Database Schema:**
```sql
CREATE TABLE kyc_sessions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id),
    session_id VARCHAR(255) UNIQUE NOT NULL,
    id_type ENUM('drivers_license', 'passport', 'national_id', 'umid') NOT NULL,
    id_image_url TEXT NOT NULL,
    selfie_image_url TEXT NOT NULL,
    extracted_data JSONB, -- OCR results
    face_match_score DECIMAL(5,4), -- 0.0000 to 1.0000
    liveness_score DECIMAL(5,4),
    liveness_passed BOOLEAN,
    verification_status ENUM('pending', 'verified', 'rejected') DEFAULT 'pending',
    rejection_reason TEXT,
    verified_at TIMESTAMP,
    created_at TIMESTAMP DEFAULT NOW()
);

CREATE INDEX idx_kyc_user_id ON kyc_sessions(user_id);
CREATE INDEX idx_kyc_session_id ON kyc_sessions(session_id);
```

---

### 3. Matching Service
**Responsibility:** ENP discovery, availability, smart matching algorithm

**Tech Stack:**
- Go or Node.js (for high performance)
- Elasticsearch (for search)
- Redis (for availability cache)
- WebSocket (for real-time updates)

**Matching Algorithm:**
```javascript
function calculateMatchScore(enp, customer, request) {
    const rating = enp.average_rating || 0; // 0-5
    const availability = enp.is_online ? 1 : 0;
    const responseTime = Math.max(0, 1 - (enp.avg_response_time / 300)); // normalize to 0-1
    const priceMatch = 1 - Math.abs(customer.price_preference - enp.tariff) / enp.tariff;
    const specialization = enp.specializations.includes(request.document_type) ? 1 : 0;
    const distance = customer.location ? calculateDistance(customer, enp) : 0;
    const isFavorite = customer.favorites.includes(enp.id) ? 1 : 0;
    
    const score = (
        0.25 * (rating / 5) +
        0.20 * availability +
        0.15 * responseTime +
        0.10 * priceMatch +
        0.10 * specialization +
        0.05 * (1 - distance / 100) + // distance in km, max 100
        0.15 * isFavorite
    );
    
    return score;
}
```

**Key Endpoints:**
```
GET  /api/v1/matching/enps/search?lat=&lng=&radius=&specialization=
GET  /api/v1/matching/enps/available
POST /api/v1/matching/find-best-match
GET  /api/v1/matching/enps/:id/profile
POST /api/v1/matching/favorites/add
DELETE /api/v1/matching/favorites/:enp_id
```

---

### 4. Notarization Service
**Responsibility:** Core workflow orchestration, document processing

**Tech Stack:**
- Node.js or Go
- PostgreSQL (workflow state)
- SQS (task queues)
- Step Functions (AWS) for workflow

**Workflow State Machine:**
```
States:
1. REQUESTED - Customer submitted request
2. ENP_ASSIGNED - ENP accepted request
3. VIDEO_IN_PROGRESS - Video call active
4. VIDEO_COMPLETED - Call ended
5. NOTARIZING - ENP signing document
6. NOTARIZED - Document signed
7. DISTRIBUTING - Sending to recipients
8. COMPLETED - All done
9. CANCELLED - Cancelled by customer/ENP
10. FAILED - Error occurred
```

**Key Endpoints:**
```
POST /api/v1/notarizations/create
GET  /api/v1/notarizations/:id
POST /api/v1/notarizations/:id/assign-enp
POST /api/v1/notarizations/:id/start-video
POST /api/v1/notarizations/:id/end-video
POST /api/v1/notarizations/:id/sign
POST /api/v1/notarizations/:id/cancel
GET  /api/v1/notarizations/customer/:customer_id
GET  /api/v1/notarizations/enp/:enp_id
```

**Database Schema:**
```sql
CREATE TABLE notarizations (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    customer_id UUID REFERENCES users(id) NOT NULL,
    enp_id UUID REFERENCES users(id),
    document_id UUID REFERENCES documents(id) NOT NULL,
    status VARCHAR(50) NOT NULL,
    amount DECIMAL(10,2) NOT NULL,
    platform_fee DECIMAL(10,2) NOT NULL,
    enp_fee DECIMAL(10,2) NOT NULL,
    video_room_id VARCHAR(255),
    video_recording_url TEXT,
    video_duration_seconds INT,
    comprehension_confirmed BOOLEAN,
    duress_check_passed BOOLEAN,
    truth_envelope JSONB,
    blockchain_tx_hash VARCHAR(255),
    created_at TIMESTAMP DEFAULT NOW(),
    assigned_at TIMESTAMP,
    video_started_at TIMESTAMP,
    video_ended_at TIMESTAMP,
    notarized_at TIMESTAMP,
    completed_at TIMESTAMP
);

CREATE INDEX idx_notarizations_customer ON notarizations(customer_id);
CREATE INDEX idx_notarizations_enp ON notarizations(enp_id);
CREATE INDEX idx_notarizations_status ON notarizations(status);
CREATE INDEX idx_notarizations_created ON notarizations(created_at DESC);
```

---

### 5. Video Service
**Responsibility:** Video call management using Twilio Video API

**Tech Stack:**
- Node.js
- Twilio Video SDK
- S3 for recording storage
- Lambda for post-processing

**Integration:**
```javascript
// Create video room
const twilioClient = require('twilio')(accountSid, authToken);

async function createVideoRoom(notarizationId) {
    const room = await twilioClient.video.rooms.create({
        uniqueName: `notarization-${notarizationId}`,
        type: 'group',
        recordParticipantsOnConnect: true,
        statusCallback: `https://api.enf.ph/webhooks/twilio/status`,
        statusCallbackMethod: 'POST'
    });
    
    return {
        roomSid: room.sid,
        roomName: room.uniqueName
    };
}

// Generate access token for participant
async function generateAccessToken(userId, roomName, isENP) {
    const AccessToken = require('twilio').jwt.AccessToken;
    const VideoGrant = AccessToken.VideoGrant;
    
    const token = new AccessToken(accountSid, apiKey, apiSecret);
    token.identity = userId;
    
    const videoGrant = new VideoGrant({ room: roomName });
    token.addGrant(videoGrant);
    
    return token.toJwt();
}
```

**Key Endpoints:**
```
POST /api/v1/video/rooms/create
POST /api/v1/video/token
POST /api/v1/video/rooms/:room_id/end
GET  /api/v1/video/recordings/:recording_id
POST /webhooks/twilio/status
POST /webhooks/twilio/recording-complete
```

---

### 6. Payment Service
**Responsibility:** Wallet management, payment processing, payouts

**Tech Stack:**
- Node.js
- PayMongo/Xendit SDK
- PostgreSQL (transactions)
- Redis (idempotency keys)

**Payment Flow:**
```
Customer Payment:
1. Customer initiates notarization
2. Check wallet balance
   - If sufficient → Deduct from wallet
   - If insufficient → Charge payment method
3. Hold amount in escrow
4. After notarization complete → Release payment
   - Platform fee → ENF wallet
   - ENP fee → ENP wallet
5. Generate receipt

ENP Payout:
1. ENP requests withdrawal
2. Check minimum withdrawal amount (₱1,000)
3. Verify bank account
4. Create payout via Xendit/PayMongo
5. Update wallet balance
6. Send confirmation
```

**Key Endpoints:**
```
GET  /api/v1/payments/wallet
POST /api/v1/payments/wallet/topup
POST /api/v1/payments/charge
POST /api/v1/payments/refund/:transaction_id
POST /api/v1/payments/payout
GET  /api/v1/payments/transactions
GET  /api/v1/payments/transactions/:id
POST /webhooks/paymongo/events
POST /webhooks/xendit/invoices
```

**Database Schema:**
```sql
CREATE TABLE wallets (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id) UNIQUE NOT NULL,
    balance DECIMAL(10,2) DEFAULT 0.00,
    currency VARCHAR(3) DEFAULT 'PHP',
    updated_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE transactions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id) NOT NULL,
    notarization_id UUID REFERENCES notarizations(id),
    type ENUM('topup', 'charge', 'refund', 'payout', 'fee_credit') NOT NULL,
    amount DECIMAL(10,2) NOT NULL,
    platform_fee DECIMAL(10,2) DEFAULT 0.00,
    enp_fee DECIMAL(10,2) DEFAULT 0.00,
    payment_method VARCHAR(50), -- gcash, maya, card, wallet
    payment_provider VARCHAR(50), -- paymongo, xendit
    external_transaction_id VARCHAR(255),
    status ENUM('pending', 'completed', 'failed', 'refunded') DEFAULT 'pending',
    metadata JSONB,
    created_at TIMESTAMP DEFAULT NOW(),
    completed_at TIMESTAMP
);

CREATE INDEX idx_transactions_user ON transactions(user_id);
CREATE INDEX idx_transactions_notarization ON transactions(notarization_id);
CREATE INDEX idx_transactions_status ON transactions(status);
CREATE INDEX idx_transactions_created ON transactions(created_at DESC);
```

---

### 7. Document Service
**Responsibility:** Document upload, storage, retrieval, TRUTH Engine integration

**Tech Stack:**
- Node.js or Go
- S3 (document storage)
- Lambda (PDF processing, TRUTH Engine)
- PostgreSQL (metadata)

**Document Processing Pipeline:**
```
1. Upload → Validate (type, size, malware scan)
2. Generate unique ID → enf-doc-{uuid}
3. Upload to S3 (encrypted)
4. Calculate SHA-256 hash
5. Store metadata in PostgreSQL
6. After notarization → Generate TRUTH envelope
7. Distribute to recipients
```

**TRUTH Engine Integration:**
```javascript
async function generateTruthEnvelope(notarization) {
    const envelope = {
        uid: `ENF-${notarization.id}`,
        timestamp: notarization.notarized_at,
        document_hash: notarization.document.file_hash,
        customer: {
            kyc_session_id: notarization.customer.kyc_session_id,
            identity_verified: true,
            face_match_score: notarization.customer.kyc.face_match_score,
            name: notarization.customer.kyc.extracted_data.name,
            id_number: notarization.customer.kyc.extracted_data.id_number
        },
        enp: {
            license_number: notarization.enp.license_number,
            name: notarization.enp.full_name,
            digital_signature: notarization.enp_signature,
            notarization_seal: notarization.enp_seal
        },
        session: {
            video_recording_url: notarization.video_recording_url,
            duration_seconds: notarization.video_duration_seconds,
            comprehension_confirmed: notarization.comprehension_confirmed,
            duress_check_passed: notarization.duress_check_passed
        },
        compliance: {
            supreme_court_reported: true,
            reported_at: new Date().toISOString(),
            retention_until: addYears(new Date(), 10).toISOString()
        },
        blockchain_anchor: notarization.blockchain_tx_hash ? {
            transaction_hash: notarization.blockchain_tx_hash,
            network: 'ethereum-mainnet',
            block_number: notarization.blockchain_block
        } : null
    };
    
    // Sign envelope with ENF platform key
    envelope.platform_signature = await signWithPlatformKey(JSON.stringify(envelope));
    
    return envelope;
}
```

**Key Endpoints:**
```
POST /api/v1/documents/upload
GET  /api/v1/documents/:id
GET  /api/v1/documents/:id/download
GET  /api/v1/documents/:id/truth-envelope
POST /api/v1/documents/:id/verify
DELETE /api/v1/documents/:id
GET  /api/v1/documents/customer/:customer_id
```

---

### 8. Notification Service
**Responsibility:** Push notifications, SMS, email

**Tech Stack:**
- Node.js
- Firebase Cloud Messaging (push)
- Twilio/Semaphore (SMS)
- SendGrid (email)
- SQS (message queue)

**Notification Types:**
```javascript
const NOTIFICATION_TYPES = {
    // Customer notifications
    KYC_VERIFIED: 'kyc_verified',
    KYC_REJECTED: 'kyc_rejected',
    ENP_ASSIGNED: 'enp_assigned',
    VIDEO_STARTING: 'video_starting',
    NOTARIZATION_COMPLETE: 'notarization_complete',
    PAYMENT_SUCCESS: 'payment_success',
    PAYMENT_FAILED: 'payment_failed',
    
    // ENP notifications
    NEW_REQUEST: 'new_request',
    REQUEST_CANCELLED: 'request_cancelled',
    PAYMENT_RECEIVED: 'payment_received',
    PAYOUT_COMPLETE: 'payout_complete',
    
    // Admin notifications
    NEW_ENP_APPLICATION: 'new_enp_application',
    SYSTEM_ALERT: 'system_alert'
};
```

**Key Endpoints:**
```
POST /api/v1/notifications/send
POST /api/v1/notifications/send-bulk
GET  /api/v1/notifications/user/:user_id
PUT  /api/v1/notifications/:id/read
POST /api/v1/notifications/preferences
```

---

### 9. Compliance Service
**Responsibility:** Supreme Court reporting, ENA access, audit logs

**Tech Stack:**
- Node.js
- PostgreSQL (audit logs)
- S3 (report archives)
- Lambda (scheduled reports)

**Audit Logging:**
```javascript
async function logEvent(event) {
    await db.query(`
        INSERT INTO audit_logs (
            event_type,
            user_id,
            ip_address,
            user_agent,
            resource_type,
            resource_id,
            action,
            metadata,
            timestamp
        ) VALUES ($1, $2, $3, $4, $5, $6, $7, $8, NOW())
    `, [
        event.type,
        event.userId,
        event.ipAddress,
        event.userAgent,
        event.resourceType,
        event.resourceId,
        event.action,
        JSON.stringify(event.metadata)
    ]);
}
```

**Supreme Court Reporting:**
```javascript
async function generateQuarterlyReport(quarter, year) {
    const report = {
        period: `Q${quarter} ${year}`,
        total_notarizations: 0,
        total_enps: 0,
        total_customers: 0,
        notarizations_by_document_type: {},
        enp_performance: [],
        system_uptime: 0,
        incidents: []
    };
    
    // Query database for metrics
    // Generate PDF report
    // Upload to S3
    // Send to Supreme Court via secure API or email
    
    return report;
}
```

**Key Endpoints:**
```
GET  /api/v1/compliance/audit-logs
GET  /api/v1/compliance/reports/quarterly
POST /api/v1/compliance/reports/generate
GET  /api/v1/compliance/ena-dashboard
POST /api/v1/compliance/incident-report
```

---

## Data Flow Diagrams

### Notarization Request Flow

```
Customer → Mobile App
  ↓
  1. Upload Document → Document Service → S3
  ↓
  2. KYC Verification → KYC Service → HyperVerge API
  ↓
  3. Create Request → Notarization Service
  ↓
  4. Find ENP → Matching Service → Elasticsearch
  ↓
  5. Notify ENP → Notification Service → Firebase/SMS
  
ENP → Mobile/Web App
  ↓
  6. Accept Request → Notarization Service
  ↓
  7. Create Video Room → Video Service → Twilio
  ↓
  8. Join Call → Customer & ENP ← WebRTC
  ↓
  9. End Call → Video Service (save recording)
  ↓
  10. Sign Document → Document Service
      ↓
      - Generate TRUTH Envelope
      - Apply Digital Signature
      - Distribute to recipients (S3 + Email)
  ↓
  11. Process Payment → Payment Service
      ↓
      - Deduct from customer wallet
      - Credit platform fee
      - Credit ENP wallet
  ↓
  12. Update Status → Notarization Service
  ↓
  13. Log to Compliance → Compliance Service
  ↓
  14. Notify All Parties → Notification Service
```

---

## Security Architecture

### Authentication & Authorization

**JWT Token Structure:**
```json
{
  "sub": "user-uuid",
  "type": "customer|enp|admin",
  "email": "user@example.com",
  "kyc_verified": true,
  "iat": 1699999999,
  "exp": 1699999999
}
```

**Authorization Middleware:**
```javascript
function requireRole(roles) {
    return (req, res, next) => {
        const userRole = req.user.type;
        if (!roles.includes(userRole)) {
            return res.status(403).json({ error: 'Forbidden' });
        }
        next();
    };
}

// Usage
app.get('/api/v1/admin/users', 
    authenticate, 
    requireRole(['admin']), 
    adminController.getUsers
);
```

### Encryption

**Data at Rest:**
- S3: Server-side encryption with KMS (SSE-KMS)
- RDS: Encryption enabled for PostgreSQL
- Secrets: AWS Secrets Manager

**Data in Transit:**
- TLS 1.3 for all API endpoints
- Certificate pinning in mobile apps
- WebRTC encryption for video calls

**PII Encryption:**
```javascript
const crypto = require('crypto');

function encryptPII(data, key) {
    const iv = crypto.randomBytes(16);
    const cipher = crypto.createCipheriv('aes-256-gcm', key, iv);
    let encrypted = cipher.update(data, 'utf8', 'hex');
    encrypted += cipher.final('hex');
    const authTag = cipher.getAuthTag();
    
    return {
        encrypted,
        iv: iv.toString('hex'),
        authTag: authTag.toString('hex')
    };
}
```

### Rate Limiting

```javascript
const rateLimit = require('express-rate-limit');

const apiLimiter = rateLimit({
    windowMs: 15 * 60 * 1000, // 15 minutes
    max: 100, // limit each IP to 100 requests per windowMs
    message: 'Too many requests from this IP'
});

const authLimiter = rateLimit({
    windowMs: 15 * 60 * 1000,
    max: 5, // 5 login attempts per 15 minutes
    message: 'Too many login attempts'
});

app.use('/api/', apiLimiter);
app.use('/api/v1/auth/login', authLimiter);
```

---

## Database Design

### Sharding Strategy (Future)

For scalability beyond 10M users:

```
Shard by user_id hash:
- Shard 0: user_id % 4 = 0
- Shard 1: user_id % 4 = 1
- Shard 2: user_id % 4 = 2
- Shard 3: user_id % 4 = 3

Tables to shard:
- users
- notarizations
- documents
- transactions

Tables to replicate across all shards:
- enp_profiles (for discovery)
```

### Backup Strategy

```
Daily backups:
- Automated RDS snapshots (retained for 30 days)
- Point-in-time recovery enabled
- Cross-region replication for disaster recovery

Weekly backups:
- Full S3 backup to Glacier
- Database dump to S3

Monthly backups:
- Archive to compliance storage (10-year retention)
```

---

## Monitoring & Observability

### Key Metrics Dashboard

```
Application Metrics:
- Request rate (req/s)
- Response time (p50, p95, p99)
- Error rate (%)
- Active users
- Active notarizations
- Video call quality

Business Metrics:
- Transactions per hour/day
- Revenue (real-time)
- Customer acquisition
- ENP onboarding rate
- KYC success rate
- Payment success rate

Infrastructure Metrics:
- CPU utilization
- Memory usage
- Disk I/O
- Network throughput
- Database connections
- Queue depth
```

### Alerting Rules

```yaml
alerts:
  - name: HighErrorRate
    condition: error_rate > 1%
    for: 5m
    severity: critical
    notify: [pagerduty, slack]
    
  - name: SlowAPIResponse
    condition: p95_response_time > 2s
    for: 10m
    severity: warning
    notify: [slack]
    
  - name: LowKYCSuccessRate
    condition: kyc_success_rate < 85%
    for: 30m
    severity: warning
    notify: [slack, email]
    
  - name: PaymentFailures
    condition: payment_failure_rate > 5%
    for: 5m
    severity: critical
    notify: [pagerduty, slack]
```

---

## Deployment Architecture

### Kubernetes Cluster Setup

```yaml
# Namespace
apiVersion: v1
kind: Namespace
metadata:
  name: enf-production

---
# User Service Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: user-service
  namespace: enf-production
spec:
  replicas: 3
  selector:
    matchLabels:
      app: user-service
  template:
    metadata:
      labels:
        app: user-service
    spec:
      containers:
      - name: user-service
        image: enf/user-service:v1.0.0
        ports:
        - containerPort: 3000
        env:
        - name: DATABASE_URL
          valueFrom:
            secretKeyRef:
              name: database-credentials
              key: url
        resources:
          requests:
            memory: "256Mi"
            cpu: "250m"
          limits:
            memory: "512Mi"
            cpu: "500m"
        livenessProbe:
          httpGet:
            path: /health
            port: 3000
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /ready
            port: 3000
          initialDelaySeconds: 5
          periodSeconds: 5

---
# Service
apiVersion: v1
kind: Service
metadata:
  name: user-service
  namespace: enf-production
spec:
  selector:
    app: user-service
  ports:
  - port: 80
    targetPort: 3000
  type: ClusterIP
```

### Auto-Scaling Configuration

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: user-service-hpa
  namespace: enf-production
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: user-service
  minReplicas: 3
  maxReplicas: 20
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80
```

---

## Disaster Recovery Plan

### RTO & RPO Targets

```
Recovery Time Objective (RTO): 2 hours
Recovery Point Objective (RPO): 15 minutes

Backup Schedule:
- Continuous replication to secondary region
- 15-minute automated snapshots
- Daily backups retained for 30 days
- Monthly backups retained for 10 years
```

### Failover Procedure

```
1. Detect failure (automated monitoring)
2. Trigger alerts to on-call engineer
3. Assess severity and impact
4. If critical:
   a. Promote read replica to primary
   b. Update DNS to point to secondary region
   c. Restart services in secondary region
5. Communicate to stakeholders
6. Post-mortem within 48 hours
```

---

## Performance Optimization

### Caching Strategy

```
Layer 1 - Browser Cache
  - Static assets (images, CSS, JS): 1 year
  - API responses: No cache
  
Layer 2 - CDN Cache (CloudFront)
  - Static assets: 1 year
  - API responses: No cache
  
Layer 3 - Application Cache (Redis)
  - User sessions: 24 hours
  - ENP availability: 30 seconds
  - Search results: 5 minutes
  - Public ENP profiles: 1 hour
  
Layer 4 - Database Query Cache
  - PostgreSQL query cache enabled
  - Materialized views for reports
```

### Database Optimization

```sql
-- Indexes
CREATE INDEX CONCURRENTLY idx_notarizations_composite 
ON notarizations(customer_id, status, created_at DESC);

-- Partitioning (for notarizations table)
CREATE TABLE notarizations (
    -- columns
) PARTITION BY RANGE (created_at);

CREATE TABLE notarizations_2025_q1 
PARTITION OF notarizations 
FOR VALUES FROM ('2025-01-01') TO ('2025-04-01');

-- Materialized view for analytics
CREATE MATERIALIZED VIEW enp_performance AS
SELECT 
    enp_id,
    COUNT(*) as total_notarizations,
    AVG(video_duration_seconds) as avg_duration,
    AVG(rating) as avg_rating,
    SUM(enp_fee) as total_earnings
FROM notarizations
WHERE status = 'completed'
GROUP BY enp_id;

CREATE UNIQUE INDEX ON enp_performance(enp_id);

-- Refresh daily
REFRESH MATERIALIZED VIEW CONCURRENTLY enp_performance;
```

---

## Cost Optimization

### Monthly Infrastructure Costs (Estimated)

```
For 5,000 transactions/month:

AWS Services:
- ECS/EKS (compute): $300
- RDS PostgreSQL (db.r5.large): $200
- ElastiCache Redis: $100
- S3 Storage (1TB): $23
- CloudFront CDN: $50
- Load Balancer: $30
- SQS/SNS: $10
- CloudWatch: $50

Third-Party Services:
- Twilio Video (5,000 sessions x $0.004/min x 5min): $100
- HyperVerge eKYC (5,000 verifications x $0.50): $2,500
- PayMongo/Xendit (transaction fees): ~2-3% of GMV
- SendGrid (emails): $20
- Firebase (push): $10

Total: ~$3,400/month + payment processing fees

Revenue (at 5,000 transactions x ₱100 avg commission): ₱500,000
Cost: ~₱200,000
Gross Margin: 60%
```

---

## Testing Strategy

### Unit Tests
- Coverage target: >80%
- Tool: Jest (Node.js), pytest (Python)
- Run on every commit

### Integration Tests
- Test inter-service communication
- Mock external APIs
- Tool: Postman/Newman, Cypress

### End-to-End Tests
- Full user flows
- Tool: Cypress, Selenium
- Run nightly on staging

### Load Tests
- Tool: k6, Artillery
- Scenarios:
  - 1,000 concurrent users
  - 100 notarizations/minute
  - Video call load (50 simultaneous)

```javascript
// k6 load test example
import http from 'k6/http';
import { check, sleep } from 'k6';

export let options = {
    stages: [
        { duration: '2m', target: 100 }, // Ramp up
        { duration: '5m', target: 100 }, // Stay at 100 users
        { duration: '2m', target: 0 },   // Ramp down
    ],
};

export default function () {
    let res = http.get('https://api.enf.ph/api/v1/health');
    check(res, { 'status is 200': (r) => r.status === 200 });
    sleep(1);
}
```

---

## Conclusion

This technical architecture provides a robust, scalable, and compliant foundation for the ENF Platform. Key strengths:

1. **Microservices** for independent scaling and deployment
2. **Cloud-native** infrastructure for resilience
3. **Security-first** design with encryption and audit trails
4. **Regulatory compliance** built into every layer
5. **Observability** for proactive issue detection
6. **Cost-effective** with auto-scaling and optimization

Next steps: Detailed API specifications, database schema finalization, and infrastructure-as-code (Terraform) setup.
