# 🇵🇭 Strategy Outline: Becoming an Electronic Notarization Facility (ENF)

Prepared for: 3neti R&D / HyperVerge-powered Platform  
Reference: Supreme Court Rules on Electronic Notarization (2021, Philippines)

---

## 📌 Salient Points from the Rules on Electronic Notarization

### 1. Definition and Scope
- **Electronic Notarization (e-notarization)** is notarization conducted via ICT systems such as:
    - Digital signatures
    - Video conferencing
    - Secure recordkeeping
- Applies to:
    - Notaries Public
    - ENFs (Electronic Notarization Facilities)
    - QCAs (Qualified Certificate Authorities)
    - Subscribers (signatories)

### 2. What is an ENF?
An **ENF** is a *platform provider* that facilitates electronic notarization.

#### Key Requirements:
- Must be **100% Filipino-owned**
- Registered with **SEC** or **DTI**
- Must host all data **within the Philippines**
- Must support:
    - 2FA
    - Secure video conferencing
    - Liveness detection
    - Identity verification
    - Public Key Infrastructure (PKI)
    - X.509 digital certificates
    - Audit logs and timestamping
    - Data retention for **10 years**

### 3. Accreditation & Compliance
- Accredited by the **Supreme Court's eNotarization Committee**
- Must pass annual audits and system inspections
- Obligated to retain and provide:
    - Video session recordings
    - Identity verification logs
    - Notarized document history

---

## ✅ ENF Strategy Outline

### A. Legal and Organizational Setup

- [ ] Register as a **Philippine corporation** (100% Filipino-owned)
- [ ] Include “e-notarization services” in business purpose
- [ ] Prepare **accreditation** documents for SC/OCA

---

### B. Technical Stack Design with HyperVerge

Use your **existing HyperVerge API access** to meet the verification requirements:

| Requirement | Solution |
|------------|----------|
| ID Verification | `HyperVerge eKYC API` (OCR + ID Match) |
| Liveness Check | `HyperVerge Selfie Validation API` |
| Audit Logging | Capture timestamps, confidence scores, IPs |
| Facial Match | Use face match API with captured selfie |
| Storage | Store ID + selfie + video logs |
| Video Conferencing | Zoom SDK / Jitsi / WebRTC with recording |
| Digital Signatures | Partner with a Qualified Certificate Authority |

---

### C. Infrastructure Requirements

- [ ] Host system on **Philippine-based servers**
- [ ] Implement:
    - [ ] Secure access control
    - [ ] End-to-end encryption
    - [ ] Biometric session logs
    - [ ] X.509 certificate integration
- [ ] Retain all records and notarization logs for **10 years**

---

### D. Partnerships & Dependencies

- [ ] Partner with a Supreme Court-recognized **QCA**
- [ ] Select and integrate a **video conferencing** stack
- [ ] Design onboarding for **Notaries Public** with e-signature keys

---

### E. Business and Sustainability Model

#### Revenue Streams
- Pay-per-notarization fees
- B2B SaaS for law firms, real estate platforms
- API bundling (eKYC + ENF)

#### Target Markets
- Overseas Filipino Workers (OFWs)
- Local notaries needing remote services
- Title companies, banks, gov’t agencies

#### Optional Offerings
- White-label ENF platform
- Document notarization dashboard
- SMS-based ID confirmation (optional)

---

### F. Implementation Checklist

| Task | Status |
|------|--------|
| ✅ HyperVerge API Integration (ID, Face, Liveness) | ✅ |
| ☐ Register 100% Filipino-owned company | ☐ |
| ☐ Apply for ENF Accreditation | ☐ |
| ☐ Host Infra in PH data center | ☐ |
| ☐ Partner with a QCA (digital signatures) | ☐ |
| ☐ Set up video recording & timestamping infra | ☐ |
| ☐ Draft notary onboarding & key management | ☐ |
| ☐ Prepare documentation for OCA audit | ☐ |

---

### 📎 Optional Deliverables

- [ ] PDF version of this strategy
- [ ] Slide deck for partner onboarding / investors
- [ ] Technical architecture diagram of your ENF
- [ ] Business Plan and Cost Model

---
