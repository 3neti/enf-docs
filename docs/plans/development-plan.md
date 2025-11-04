
# 📋 Notra Development Plan Outline

## I. System Architecture Overview

- Microservice architecture with RESTful APIs
- Secure cloud infrastructure (AWS/GCP/DO)
- Modular frontends (web/mobile) and backend (Laravel/PHP)
- Integrates with:

    - Truth Engine™
    - eKYC/face match services (e.g., Hyperverge)
    - Digital Wallet (e.g., Bavix, GCash, Maya)
    - Supreme Court compliance audit APIs

---

## II. Core Modules

### 1. [🧠 Backend Services (Laravel 12 API-first)](backend-services.md)
- Authentication & authorization (OAuth2/JWT)
- User & role management (Admin, ENP, Subscriber, SC)
- Document handling (upload, sign, seal, store)
- Booking & scheduling (calendar, reminders, availability)
- Video session orchestration (e.g., Zoom API, Jitsi)
- eKYC and selfie verification
- Digital signature & notarization log
- Audit trail + blockchain anchoring (if required)
- Payment gateway integration
- Notifications (email, SMS, in-app)

---

## III. Portals & Interfaces

### 2. 🧑‍⚖️ Web Portal – ENP (Electronic Notary Public)
For accredited lawyers to perform notarizations
- Dashboard: upcoming sessions, history
- Client queue & booking management
- Real-time video interface
- Document annotation & signing interface
- Notes and audit tagging
- Verification & seal issuance
- Activity log & court submission tools

---

### 3. 🧑 Web Portal – Subscriber (Customer)
For users (individuals or orgs) requesting notarization
- Dashboard: past/future sessions
- Upload, sign, and request notarization
- Book and manage sessions
- Secure document locker
- Identity verification (eKYC)
- Payment & billing
- Download notarized files + QR verification

---

### 4. 🛠️ Admin Portal
For operations and policy control
- User & ENP verification
- Session logs and audit controls
- Document access trails
- Rate limits, pricing, court compliance config
- eKYC override and review
- Support tools
- CMS (FAQs, terms, pages, emails)

---

### 5. 📝 Ad Hoc Notarization Page (Public Endpoint)
For one-off use (QR link, SMS link, walk-in-style)
- Minimalist document upload + ID check
- One-click book and pay
- Session status tracker
- Receipt + link to view notarized copy

---

### 6. 📱 Mobile App – Subscriber (Customer)
For iOS and Android (Vue Native, Flutter, or Inertia+Cordova)
- Same features as subscriber web
- Push notifications
- Face scan, selfie, GPS, camera access
- Wallet integration (send/request for notarization)

---

### 7. 📱 Mobile App – ENP (Lawyer)
- Session alerts
- Join video call
- Sign/seal on the go
- Limited review & access logs

---

### 8. ⚖️ Supreme Court Web Portal
For regulatory visibility & oversight
- Real-time access to sealed session metadata
- Logs of notarizations with audit trails
- Exportable reports (daily/monthly)
- On-demand document re-check
- Admin access with IP restriction

---

## IV. DevOps & Compliance

- CI/CD pipelines (GitHub Actions, Envoyer, etc.)
- Infrastructure as Code (Terraform / Ansible)
- Regular backup & disaster recovery plan
- Encrypted file storage (AES-256 at rest, HTTPS in transit)
- Log monitoring & anomaly detection (Datadog, Sentry)
- Data privacy compliance (DPA, GDPR alignment)
- Supreme Court compliance audit logging

---

## V. Timeline Suggestion (Phased)

| Phase | Scope |
|-------|-------|
| Phase 1 | Core APIs, Subscriber Web Portal, Admin Portal |
| Phase 2 | ENP Web Portal, eKYC & Booking, Video Integration |
| Phase 3 | Mobile Apps (Customer + ENP), Ad Hoc Page |
| Phase 4 | Supreme Court Portal, Audit APIs, Blockchain Proof |
| Phase 5 | Scaling, Payment Integrations, Analytics, AI Auto-Summary |

---

## VI. Optional Enhancements

- AI contract parsing / clause checking
- QR verification with public document hashes
- API access for B2B clients (law firms, LGUs)
- Voice-based consent & verification
- Partner Notary Directory & marketplace
- Offline notarization mode (store-and-forward)
