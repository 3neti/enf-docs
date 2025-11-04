
# 🇵🇭 Strategy Outline: Becoming an Electronic Notarization Facility (ENF)

This strategy is based on the **Rules on Electronic Notarization** (A.M. No. 24-10-14-SC, February 4, 2025) issued by the **Supreme Court of the Philippines**. It aligns with your capabilities in **HyperVerge-based eKYC**, video/audio tech, secure API development, and legal compliance.

---

## 📌 Salient Points from the Rules on Electronic Notarization

### 1. Definition and Scope
- **Electronic Notarization** refers to notarial acts performed via **electronic documents**, **digital signatures**, and **video conferencing**, using a secure **Electronic Notarization Facility (ENF)**.
- Covers both:
    - **In-Person Electronic Notarization (IEN)** – with physical appearance before the ENP.
    - **Remote Electronic Notarization (REN)** – with virtual appearance via video.

### 2. Role of the ENF
- An **ENF** is the technology platform used by **Electronic Notaries Public (ENPs)**.
- ENFs must be **accredited** by the Supreme Court and meet a comprehensive list of **technical and operational requirements** under Rule VII of the Rules.

### 3. Data Handling & Sovereignty
- There is **no explicit mandate** that data must be hosted in the Philippines.
- However, the ENF must:
    - Ensure **secure storage** of notarized documents, recordings, and notarial logs.
    - Provide **real-time access** to the Supreme Court or its designated officers.
    - Comply with the **Data Privacy Act of 2012 (RA 10173)**.
    - Apply **tamper-evident**, **auditable**, and **encrypted** technical safeguards.

### 4. Cryptographic Signatures and PKI (Implied)
- While **PKI (Public Key Infrastructure)** is not explicitly named, it is **implied** via:
    - The requirement for **digital signatures** using asymmetric cryptography
    - The mandated use of **secure electronic signatures** for ENPs
- The Rules define **digital signature** as:
  > "...a transformation of an electronic document using an asymmetric or public cryptosystem..."
- Therefore, ENFs must support **PKI or equivalent infrastructure** for:
    - ENP signatures
    - Document integrity
    - Non-repudiation
    - Public verification

---

## ✅ Strategy for Accreditation as ENF Provider

### A. Legal and Organizational Setup

1. **Corporate Compliance**
    - Register as a **Philippine entity** (SEC, DTI, or CDA), as required in the Guidelines.
    - Prepare the following:
        - Business registration docs
        - Declaration of compliance with ENF Guidelines
        - Signed **Data Outsourcing Agreement** with the Supreme Court

2. **Eligibility Requirements**
    - Prepare an **instructional video** for ENPs using your ENF
    - Post a **₱100,000 performance bond**
    - Pay ₱5,000 application fee via Judiciary e-Payment
    - Ensure availability of all tech features listed under Rule VII, Section 2

---

### B. Technical Design Using HyperVerge APIs

You already use **HyperVerge’s eKYC** for ID OCR, face match, and liveness detection. You can leverage it directly:

| **Rule VII Requirement** | **HyperVerge Integration** |
|---------------------------|-----------------------------|
| e-KYC / ID Verification   | `HyperVerge ID OCR` + `Face Match API` |
| Liveness Detection        | `HyperVerge Selfie Validation API` |
| MFA / Anti-spoofing       | Combine face, OTP, and biometric factor |
| Audit Trails              | Log all API calls + confidence levels |
| Video Conferencing        | WebRTC, Jitsi, or Zoom SDK with recording |
| Geolocation / VPN Block   | Use IP-based + device-based geolocation |
| Signature Capture         | PDF signature embedding + audit |
| Retention and Backup      | Cloud-based storage with audit, encryption |

---

### C. Cloud, Storage & Compliance Approach

#### ✅ Updated Compliance Interpretation
The ENF **does not need to host servers in the Philippines**, but it must:
- Use **secure and encrypted** storage (cloud or on-premise)
- Retain records for **at least 10 years**
- Ensure **accessibility to the Supreme Court**
- Restrict use of **VPNs** (Rule VII, Sec. 2i)

#### Recommended Practice
- Use **Philippine-based cloud regions** (e.g., ePLDT, UnionBank Cloud, or AWS Local Zone in Manila)
- Implement **RA 10173-compliant** data policies
- Provide **real-time dashboard** for court access

---

### D. Video, Identity, and Signature Components

1. **Video Conference Stack**
    - Must allow real-time synchronous video (1280x720 minimum)
    - Must **record and retain** REN sessions
    - Embed disclaimers + DPA 2012 consent during sessions

2. **Electronic Signature & Notarial Seal**
    - PDF/A support with embedded ENP seal
    - Include barcode or QR code (per Rule IX)
    - Support for **digital signature with public key validation**
    - Store notarized PDFs and session logs together

3. **Document Verification**
    - Provide public and court-facing **document validation tool**
    - E.g., verify hash or QR code to confirm notarization

---

### E. Business & Monetization Model

| Model | Description |
|-------|-------------|
| **Pay-per-use** | ENPs pay per notarization session |
| **SaaS licensing** | Subscription for law firms, banks, agencies |
| **White-label ENF** | Let partners use your ENF with their branding |
| **Bundled eKYC + ENF** | Offer end-to-end identity + notarization API |

#### Target Clients
- OFWs
- Real estate, legal, and finance professionals
- Gov't partners (land titling, SSS, POEA, etc.)

---

### F. Implementation Checklist

| Task | Status |
|------|--------|
| Register as PH legal entity (SEC/DTI) | ☐ |
| Integrate HyperVerge eKYC + liveness | ✅ |
| Build ENF with SC-required features | ☐ |
| Prepare accreditation application | ☐ |
| Pay ₱5,000 SC application fee | ☐ |
| Post ₱100,000 performance bond | ☐ |
| Submit data outsourcing agreement | ☐ |
| Provide instructional materials | ☐ |
| Ensure DPA 2012 and uptime compliance | ☐ |
| Design SC access + data retention logic | ☐ |

---

## 📎 Optional Enhancements

- **ENP Dashboard**: Scheduling, logs, and earnings tracker
- **Public QR Verification Portal**
- **Offline fallback & sync support**
- **Document blockchain anchoring** (optional for proof-of-integrity)

---

## 🔁 Next Deliverables (Upon Request)

- [ ] PDF version of this Markdown
- [ ] Keynote / PowerPoint slide deck
- [ ] System architecture diagram (ENF + HyperVerge + SC integration)
- [ ] Pitch deck or investor brief
- [ ] Draft of ENF Accreditation Application Letter

---
