
# 🇵🇭 Strategy Outline: Accreditation as an Electronic Notarization Facility (ENF)

This strategy outlines the steps and technical foundation required for a Philippine-based technology provider to become an **accredited Electronic Notarization Facility (ENF)** under the **Rules on Electronic Notarization** (A.M. No. 24-10-14-SC). The platform is designed to support **Remote Electronic Notarization (REN)** and **In-Person Electronic Notarization (IEN)** using secure, compliant, and innovative technologies including **HyperVerge eKYC**, **blockchain-based non-repudiation**, and the **TRUTH Engine**.

---

## 📌 Salient Points from the Rules on Electronic Notarization

### 1. Electronic Notarization Framework
- Notarial acts are permitted via electronic documents and signatures, provided they are conducted through an accredited ENF.
- Two modes are supported:
    - **In-Person Electronic Notarization (IEN)** – physical presence.
    - **Remote Electronic Notarization (REN)** – video conferencing.

### 2. ENF Accreditation Requirements
- ENFs must pass technical, operational, and legal accreditation by the **Supreme Court of the Philippines**.
- Technical requirements include eKYC, liveness detection, MFA, video conferencing, secure document handling, and audit logs.

### 3. Storage & Compliance
- No explicit requirement for PH-based data hosting, but:
    - Data must be **secure**, **encrypted**, and **auditable**.
    - ENFs must support **10-year retention** and access by the **ENA**.
    - Must comply with the **Data Privacy Act of 2012 (RA 10173)**.

### 4. Cryptographic Signature Infrastructure
- The Rules **imply** the use of **Public Key Infrastructure (PKI)** through the required use of **digital signatures using asymmetric cryptosystems**.
- ENFs must support:
    - **Secure electronic signatures**
    - **Non-repudiation**
    - **Certificate-based validation**

### 5. TRUTH Engine Integration
- The ENF platform can integrate the **TRUTH Engine** to encode notarized artifacts into portable, verifiable envelopes (PDFs, QR, JSON, YAML).
- This ensures:
    - **Canonicalization** of notarized documents
    - **Hash-based integrity checks**
    - **Replay protection and audit trails**
    - **Encoding for QR, file, or NFC transport**

### 6. Optional Blockchain Anchoring
- Blockchain may be optionally integrated to anchor document hashes and session signatures.
- This provides:
    - Immutable **proof of notarization**
    - Tamper detection and **verifiable timestamping**
    - Distributed verification of notarized records

---

## ✅ Strategic Implementation Plan

### A. Legal & Corporate Compliance

- Register as a **100% Filipino-owned entity** under SEC, DTI, or CDA.
- Prepare:
    - Business registration documents
    - Notarization service charter
    - Signed **Data Outsourcing Agreement** with the Supreme Court
    - ₱5,000 application fee and ₱100,000 performance bond

---

### B. Technical Stack: HyperVerge + TRUTH + Blockchain

| Requirement                           | Implementation                     |
|--------------------------------------|-----------------------------------|
| ID Verification                      | HyperVerge ID OCR + Face Match     |
| Liveness Detection                   | HyperVerge Selfie Validation API   |
| Multifactor Authentication (MFA)     | Face + OTP + Device Binding        |
| Signature Capture                    | Digital signature + PDF/A          |
| Geolocation & VPN Detection          | IP/Device validation + Blocking    |
| Session Recording                    | WebRTC/Zoom/Jitsi + Archiving      |
| Audit Trail                          | Immutable logs + hash references   |
| Document Verification                | TRUTH Engine + Blockchain option   |

---

### C. TRUTH Engine Application in ENF

- Canonicalize notarized document as a **DTO**
- Encode into **TRUTH Envelope** (hash + UID + timestamp)
- Output as:
    - File (JSON, YAML, PDF)
    - QR or NFC chunks
- Optional signature field can contain:
    - ENP digital cert signature
    - OTP or scratchpad signature
- Replay protection via `uid` + audit logging

---

### D. Compliance Architecture

- Store all notarization data for 10+ years (cloud or on-premise)
- Provide dashboard access for the **Electronic Notary Administrator**
- Implement:
    - **RA 10173** data handling
    - End-to-end encryption
    - Role-based access control
- Restrict notarizations to Philippines jurisdiction (with extraterritorial exceptions)

---

### E. Business Models

| Model                  | Description |
|------------------------|-------------|
| Pay-per-notarization   | ENPs pay per transaction |
| SaaS subscriptions     | For law firms, gov't agencies |
| White-label platform   | Private-labeled ENF for partners |
| eKYC + ENF Bundles     | Integrated onboarding and notarization API |

Target customers:
- OFWs
- Lawyers, banks, real estate firms
- Agencies (LRA, DFA, POEA, etc.)

---

### F. Implementation Checklist

| Task | Status |
|------|--------|
| Register as legal entity | ☐ |
| Integrate HyperVerge eKYC + liveness | ✅ |
| Implement TRUTH Engine for notarized DTOs | ☐ |
| Add support for digital signature and PDF/A | ☐ |
| Deploy audit and replay protection | ☐ |
| Submit SC application and bond | ☐ |
| Implement ENA access + compliance reporting | ☐ |
| Optional: Blockchain anchoring of documents | ☐ |

---

## 🔁 Next Steps & Assets

- [ ] Export notarized documents to TRUTH Envelope (PDF, QR, YAML, JSON)
- [ ] Deploy web portal for ENP onboarding and audit logs
- [ ] Generate accreditation dossier and documentation
- [ ] Publish API documentation for eKYC + Notarization pipeline

---

## 🔐 Conclusion

The Electronic Notarization Facility envisioned here provides a **modern, secure, and extensible framework** for digital notarization in the Philippines.  
With **HyperVerge eKYC**, **TRUTH Engine verification**, and optional **blockchain anchoring**, the platform ensures:

- Legal compliance
- Data integrity
- Tamper-proof notarization
- Long-term verifiability
