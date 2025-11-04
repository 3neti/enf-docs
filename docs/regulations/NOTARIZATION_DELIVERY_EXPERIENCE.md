# 📦 Notarization Delivery Experience
## Electronic Notarization Facility (ENF) powered by TRUTH Engine and HyperVerge eKYC

### Purpose
To illustrate what the customer physically and visually receives after a notarization session — including the notarized PDF file, TRUTH envelope data file, and public verification options.

---

## 1️⃣ Overview of Delivered Artifacts

| Artifact | Format | Description | Consumer |
|-----------|---------|--------------|-----------|
| **Notarized Document** | `.pdf` (PDF/A) | Human-readable document with ENP’s digital seal, timestamp, TRUTH hash, and QR code. | Customer, Courts, Agencies |
| **TRUTH Envelope** | `.json` or `.yaml` | Machine-verifiable notarization proof containing canonical hash, signatures, and blockchain anchor. | Systems, Auditors, SC Interface |
| **Verification Link / QR** | URL or printed QR | Links directly to the public verification portal validating the TRUTH Envelope. | Anyone verifying authenticity |

---

## 2️⃣ Delivery Methods

### A. Email Delivery
```
Subject: Your Notarized Document is Ready

Dear [Customer Name],

Your notarized document is now complete and securely stored.

Attachments:
  📄 Affidavit_of_Support_Notarized.pdf
  🔐 Affidavit_of_Support_TruthEnvelope.json

Verify authenticity at:
https://verify.enf.ph/truth/abcdef123456

Thank you for using the Electronic Notarization Facility.
— ENF Support Team
```

### B. Web Download Page
```
✅ Notarization Complete

Document: Affidavit_of_Support.pdf
Session ID: REN-20251008-1234
Timestamp: 2025-10-08 14:22:16 (PHT)
ENP: Atty. Maria Dela Cruz

[📄 Download Notarized PDF]
[🔐 Download TRUTH Envelope]
[🔍 Verify Online]

QR Preview:
+-------------------------+
| █▀▀▀█ ▄▄▄ █▀▀▀█ ▀█▀ ▄▄ |
| ▄▀▄▀▄ ▀▀▀ ▀▄▀▄▀ ▄█▄ ▀█ |
| ▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀ |
| truth://abcdef123456   |
+-------------------------+

Verification Link:
https://verify.enf.ph/truth/abcdef123456
```

---

## 3️⃣ Sample UI Wireframe (Markdown-Friendly)
```
┌─────────────────────────────────────────────┐
│ ✅  NOTARIZATION COMPLETE                   │
│---------------------------------------------│
│ Document: Affidavit_of_Support.pdf          │
│ TRUTH UID: truth://abcdef123456             │
│ Timestamp: 2025-10-08 14:22:16 (PHT)        │
│ ENP: Atty. Maria Dela Cruz                  │
│---------------------------------------------│
│ [ Download Notarized PDF  ]                 │
│ [ Download TRUTH Envelope  ]                │
│ [ Verify Document Online   ]                │
│---------------------------------------------│
│ 🔍  QR Verification Preview                 │
│ +-----------------------------+             │
│ | ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓ |             │
│ | truth://abcdef123456       |             │
│ +-----------------------------+             │
│---------------------------------------------│
│ ℹ  Each notarized document includes:        │
│  • Digital seal and timestamp               │
│  • TRUTH Engine hash and UID                │
│  • Optional blockchain proof reference      │
│---------------------------------------------│
│ Powered by TRUTH Engine × HyperVerge eKYC   │
└─────────────────────────────────────────────┘
```

---

## 4️⃣ Embedded TRUTH Data Snippet (Example)
```json
{
  "uid": "truth:abcdef123456",
  "timestamp": "2025-10-08T09:23:47Z",
  "hash": "37F9A2E47B...C1",
  "document_name": "Affidavit_of_Support.pdf",
  "signatories": [
    { "role": "Customer", "id": "KYC-00123" },
    { "role": "ENP", "id": "ENP-00987" }
  ],
  "session_ref": "REN-20251008-1234",
  "truth_signature": {
    "type": "SHA256-RSA",
    "value": "MEYCIQDr9f..."
  },
  "blockchain_anchor": {
    "tx_id": "0xA9F1AABB...",
    "network": "Polygon",
    "block_timestamp": "2025-10-08T09:23:47Z"
  }
}
```

---

## 5️⃣ User Interaction Flow (Simplified)
```
ENP Completes Session
        ↓
TRUTH Engine Generates Envelope
        ↓
PDF and JSON Bundled Securely
        ↓
User Notification (Email / Web)
        ↓
User Downloads Files
        ↓
Verification via Portal or QR
```

---

## 6️⃣ Legal and Technical Notes
- **PDF/A format** ensures archival compatibility and visual fidelity.
- **TRUTH hash and UID** guarantee tamper-evident binding between PDF and JSON.
- **QR code** enables offline verification from printed copies.
- **Blockchain anchor** optional but enhances non-repudiation.
- **RA 10173-compliant** consent prompts precede all deliveries.
- **Retention period:** minimum 10 years in encrypted archive.

---

## 7️⃣ Future Enhancements
- **TRUTH Viewer Plugin:** Inline JSON visualization inside the PDF viewer.
- **Mobile-first verification app:** Scan QR → verify → show authenticity badge.
- **Multi-language delivery templates:** English, Filipino, Arabic.
- **Webhook-based verification:** Integration with third-party apps or courts.

---

**End of Document**
