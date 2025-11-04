# 📄 Document Input Strategy for ENF: Policy, Practice, and Design Opportunity
## How “Upload Document” Should Work in the Customer Journey

This document explains the strategic design decision on whether to limit uploads to PDFs or allow more flexible formats, considering the **Rules on Electronic Notarization (A.M. No. 24-10-14-SC)**, global best practices, and the TRUTH Engine’s design capabilities.

---

## 🧩 1️⃣ Policy Perspective – What the Rules Say

### 📜 a. Core Definition
> “Electronic notarization” refers to notarial acts performed on **electronic documents** using **electronic signatures** and **secure technologies** for verification, storage, and retrieval.

- The phrase “electronic document” is **technology-neutral** — it does not specify a file format (e.g., PDF).
- However, it must be:
    - **Fixed and unalterable** once notarized.
    - **Capable of being retained and reproduced** faithfully.

This implicitly rules out raw editable formats like `.docx` or `.pages` unless they are first converted to a fixed archival format (e.g., PDF/A or XML).

### 📜 b. Before Notarization
> The ENP must confirm the completeness, accuracy, and identity of the electronic document before applying a signature and seal.

✅ This allows **flexible input** — but requires **canonicalization** into a tamper-evident, reproducible format before notarization.  
In practice: convert Word, image, or metadata templates into **PDF/A** or **XML** first.

### 📜 c. Storage and Retention
> ENFs must preserve notarized electronic documents in a secure, tamper-evident format for at least ten (10) years.

Thus, the output format must be durable and non-editable — PDF/A or XML are industry standards.

---

## 🌏 2️⃣ Practice Perspective – Global Best Practices

| Country | Input Flexibility | Storage Format | Practice Summary |
|----------|------------------|----------------|------------------|
| 🇺🇸 **Notarize.com** | Accepts DOCX, PDF, PNG; auto-converts to PDF/A | PDF/A | Editable files converted before signing. |
| 🇸🇬 **eSignGov / MyInfo** | Metadata → PDF form | XML + PDF | Form data rendered into PDF + structured XML. |
| 🇮🇳 **SigniFlow** | Any upload | PDF/A | Only fixed version notarized. |
| 🇪🇺 **eIDAS Platforms** | Structured XML | XML (EN16931) | Canonical data is notarized. |

**Key insight:**
> Accept any file type from users, but always notarize a canonical, uneditable version.

---

## ⚙️ 3️⃣ Design Opportunity – Recommended ENF Strategy

### Step 1 – Flexible Input
Allow users to upload:
- `.pdf`, `.docx`, `.odt`, `.jpg`, `.png`, `.json`
- Or structured metadata (forms, templates).

### Step 2 – Canonicalization via TRUTH Engine
Use the TRUTH Engine to:
- Convert inputs to PDF/A or XML.
- Extract and bind metadata (title, signer, date, etc.).
- Generate a deterministic hash for integrity.

### Step 3 – Template + Metadata Flow
Support *metadata-first notarization* (template-based forms):

1. User fills out metadata fields (e.g., name, address, purpose).
2. ENF renders a PDF template (affidavit, contract, etc.).
3. ENP reviews and notarizes the rendered PDF.
4. TRUTH Engine binds both PDF and metadata JSON inside the same envelope.

**Example TRUTH Envelope:**
```json
{
  "uid": "truth:xyz789",
  "hash": "d2f1a8...",
  "source": {
    "template": "Affidavit_v2.1",
    "metadata": {
      "name": "Juan Dela Cruz",
      "purpose": "Visa Application"
    },
    "rendered_file": "Affidavit_of_Support.pdf"
  }
}
```

---

## ✅ Policy-Driven Implementation Table

| Phase | Policy Compliance | ENF Implementation |
|-------|--------------------|--------------------|
| **Input** | Accepts any electronic file | DOCX, PDF, JSON, image |
| **Canonicalization** | Must be fixed and reproducible | Auto-convert to PDF/A or XML |
| **Signing / Notarization** | ENP signs fixed version | PDF/A or TRUTH-bound XML |
| **Storage** | Preserve notarized version for ≥10 years | PDF/A + TRUTH JSON |
| **Verification** | Must be auditable and hash-verifiable | TRUTH Engine + QR verification |

---

## 🧠 Summary Recommendation

> **Be flexible in input, strict in output.**
>
> - Accept multiple formats for user convenience.
> - Canonicalize and flatten everything into PDF/A before signing.
> - Optionally include structured metadata within the TRUTH envelope.
> - Present the human-readable PDF and the machine-verifiable TRUTH JSON together as the notarized package.

---

## 🔗 Design Implication for ENF
This hybrid approach allows three notarization modes:
1. **Direct Upload** – user uploads PDF/Word and gets notarized PDF.
2. **Template-Based** – user fills metadata and system renders a PDF.
3. **API-Driven** – external system sends JSON payload; TRUTH Engine generates PDF and notarizes automatically.

All are compliant under current rules **as long as the final notarized artifact is immutable, reproducible, and auditable**.

---

**End of Document**
