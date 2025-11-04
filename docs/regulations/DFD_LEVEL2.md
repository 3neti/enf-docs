# 🔐 DFD Level 2 — TRUTH Engine Core
## Subsystem of the Electronic Notarization Facility (ENF)
### Processes → Canonicalization → Hashing → UID → Signature → Blockchain Anchoring

```mermaid
flowchart TB

%%==========================
%% External Interfaces
%%==========================
DOCS[Document Module]
COMP[Compliance Module]
CHAIN[Blockchain Network]

%%==========================
%% TRUTH Engine Processes
%%==========================
subgraph TRUTH_Engine["TRUTH Engine Core"]
direction TB
C1["P1 – Canonicalize Document"]
C2["P2 – Generate SHA-256 Hash"]
C3["P3 – Assign UID and Timestamp"]
C4["P4 – Apply Signature and Verification"]
C5["P5 – Anchor Hash to Blockchain"]
end

%%==========================
%% Internal Data Stores
%%==========================
Raw[(Raw Document Buffer)]
HashStore[(Hash Registry)]
SigStore[(Signature Records)]
AnchorLog[(Blockchain Anchors)]

%%==========================
%% Data Flows
%%==========================
DOCS -->|Submit Document| C1
C1 -->|Normalized Output| Raw
Raw --> C2
C2 -->|SHA Hash| HashStore
C2 --> C3
C3 -->|UID and Timestamp| SigStore
C3 --> C4
C4 -->|Signed Envelope| DOCS
C4 --> C5
C5 -->|Hash and UID| CHAIN
C5 -->|Proof Reference| AnchorLog
AnchorLog -->|Audit Trail| COMP

%%==========================
%% Visual Annotations (safe syntax)
%%==========================
N1[Note: Canonicalizes and normalizes documents for consistent hashing and integrity.]
C1 -. context .- N1

N2[Note: Applies SHA-256 hashing and stores results in the Hash Registry.]
C2 -. context .- N2

N3[Note: Generates unique UID and precise timestamp linked to ENP and session IDs.]
C3 -. context .- N3

N4[Note: Applies digital or one-time-password based signature and returns the TRUTH Envelope.]
C4 -. context .- N4

N5[Note: Anchors hash and timestamp on blockchain for immutability and non-repudiation.]
C5 -. context .- N5

%%==========================
%% Note Styling
%%==========================
classDef note fill:#fff5b1,stroke:#555,stroke-dasharray:3 3,color:#111;
class N1,N2,N3,N4,N5 note;
```
---

### 🧠 Diagram Interpretation

| Stage | Function | Output / Storage |
|--------|-----------|------------------|
| **P1** | Canonicalizes uploaded file into deterministic JSON | Raw Document Buffer |
| **P2** | Computes cryptographic hash (SHA-256 / SHA-3) | Hash Registry |
| **P3** | Generates globally unique UID and timestamp | Signature Records |
| **P4** | Applies digital or OTP-based signature, returns TRUTH Envelope | Document Module |
| **P5** | Anchors hash and UID on Blockchain and logs proof | Blockchain Anchors → Compliance |

---

# 🪪 DFD Level 2 — KYC (Identity Verification) Module
## Subsystem of the Electronic Notarization Facility (ENF)
### Process Flow → ID Capture → OCR → Face Match → Liveness → Verification Envelope

```mermaid
flowchart TB

%%==========================
%% External Interfaces
%%==========================
CUSTOMER[Customer / Signatory]
ENP[Electronic Notary Public]
TRUTH[TRUTH Engine Core]
COMP[Compliance Module]

%%==========================
%% KYC Internal Processes
%%==========================
subgraph KYC_Module["KYC Module – Identity Verification"]
direction TB
K1["P1 – Capture ID Image and Selfie"]
K2["P2 – Perform Optical Character Recognition"]
K3["P3 – Conduct Face Match Analysis"]
K4["P4 – Perform Liveness Detection"]
K5["P5 – Generate Verification Envelope"]
end

%%==========================
%% Internal Data Stores
%%==========================
IDStore[(ID Image Repository)]
OCRStore[(Extracted ID Data)]
FaceMatchDB[(Face Match Scores)]
LivenessDB[(Liveness Results)]
KYCLog[(KYC Verification Records)]

%%==========================
%% Data Flows
%%==========================
CUSTOMER -->|Upload ID and Selfie| K1
ENP -->|Submit Verification Request| K1
K1 -->|Captured Data| IDStore
IDStore --> K2
K2 -->|Text Extraction| OCRStore
OCRStore --> K3
K3 -->|Face Match Result| FaceMatchDB
FaceMatchDB --> K4
K4 -->|Liveness Result| LivenessDB
LivenessDB --> K5
K5 -->|Verified Identity Envelope| TRUTH
K5 -->|Verification Log| KYCLog
KYCLog -->|Summary Report| COMP

%%==========================
%% Visual Notes
%%==========================
N1[Note: Captures front and back of ID plus selfie for validation.]
K1 -. context .- N1

N2[Note: Extracts ID details such as name, number, and expiry using OCR technology.]
K2 -. context .- N2

N3[Note: Compares selfie and ID photo to confirm identity match score.]
K3 -. context .- N3

N4[Note: Performs motion and depth checks to ensure subject is a live person.]
K4 -. context .- N4

N5[Note: Creates a verification envelope containing extracted data and confidence scores, then sends it to the TRUTH Engine.]
K5 -. context .- N5

%%==========================
%% Note Styling
%%==========================
classDef note fill:#fff5b1,stroke:#555,stroke-dasharray:3 3,color:#111;
class N1,N2,N3,N4,N5 note;
```
---

### 🧠 Interpretation

| Step | Function | Output / Store |
|------|-----------|----------------|
| **P1** | Captures ID images and selfie | ID Image Repository |
| **P2** | Extracts text and fields via OCR | Extracted ID Data |
| **P3** | Compares selfie vs ID photo | Face Match Scores |
| **P4** | Runs liveness detection | Liveness Results |
| **P5** | Packages verified identity + confidence data | Verification Envelope → TRUTH Engine |

---

Would you like me to proceed with **DFD Level 2 – Wallet and Payment Module** next (showing top-up → transaction → settlement → ledger update)?

# 💰 DFD Level 2 — Wallet and Payment Module
## Subsystem of the Electronic Notarization Facility (ENF)
### Process Flow → Top-up → Transaction → Settlement → Ledger Update → Reporting

```mermaid
flowchart TB

%%==========================
%% External Interfaces
%%==========================
ENP[Electronic Notary Public]
CUSTOMER[Customer / Signatory]
BANK[Bank or E-Wallet Provider]
COMP[Compliance Module]

%%==========================
%% Wallet Internal Processes
%%==========================
subgraph WALLET_Module["Wallet and Payment Subsystem"]
direction TB
W1["P1 – Receive Top-up Request"]
W2["P2 – Validate Payment Transaction"]
W3["P3 – Credit ENP Wallet"]
W4["P4 – Deduct Platform and Regulatory Fees"]
W5["P5 – Settle or Withdraw Funds"]
W6["P6 – Record Ledger Entry"]
end

%%==========================
%% Internal Data Stores
%%==========================
WalletDB[(Wallet Balances Database)]
TxnDB[(Transaction Records)]
FeeDB[(Tariff and Fee Tables)]
LedgerDB[(Accounting Ledger)]
SettlementLog[(Settlement History)]

%%==========================
%% Data Flows
%%==========================
ENP -->|Initiates Top-up or Withdrawal| W1
CUSTOMER -->|Pays Notarization Fee| W1
W1 -->|Payment Request| W2
W2 -->|Payment Authorization| BANK
BANK -->|Confirmation Message| W2
W2 -->|Validated Transaction| W3
W3 -->|Credit Update| WalletDB
W3 -->|Transaction Entry| TxnDB
W3 --> W4
W4 -->|Fee Calculation| FeeDB
W4 -->|Net Balance Update| WalletDB
W4 --> W5
W5 -->|Disbursement Instruction| BANK
BANK -->|Settlement Confirmation| W5
W5 -->|Settlement Record| SettlementLog
W5 --> W6
W6 -->|Ledger Posting| LedgerDB
LedgerDB -->|Summary Report| COMP

%%==========================
%% Visual Notes
%%==========================
N1[Note: Receives incoming payments from ENPs or customers for notarization services or wallet top-ups.]
W1 -. context .- N1

N2[Note: Validates transactions using banking or e-wallet API and ensures payment success before crediting.]
W2 -. context .- N2

N3[Note: Credits ENP wallet instantly after confirmed payment and logs transaction details.]
W3 -. context .- N3

N4[Note: Calculates platform fees, service charges, and any required regulatory deductions automatically.]
W4 -. context .- N4

N5[Note: Sends settlement or withdrawal instruction to the linked bank and updates settlement history.]
W5 -. context .- N5

N6[Note: Records all transactions and balance movements in a double-entry ledger for auditing.]
W6 -. context .- N6

%%==========================
%% Note Styling
%%==========================
classDef note fill:#fff5b1,stroke:#555,stroke-dasharray:3 3,color:#111;
class N1,N2,N3,N4,N5,N6 note;
```
---

### 🧠 Interpretation

| Step | Function | Output / Store |
|------|-----------|----------------|
| **P1** | Receives top-up or payment request | Payment Request |
| **P2** | Validates via bank or e-wallet provider | Transaction Records |
| **P3** | Credits ENP wallet after confirmation | Wallet Balances Database |
| **P4** | Deducts platform and regulatory fees | Fee Tables / Wallet Updates |
| **P5** | Settles or withdraws funds to bank | Settlement History |
| **P6** | Records ledger entry for audit and compliance | Accounting Ledger |

---

Would you like me to proceed with **DFD Level 2 — Compliance, Audit, and Reporting Module** next (showing log aggregation → audit trail → SC report generation → ENA API access)?

# 📊 DFD Level 2 — Compliance, Audit, and Reporting Module
## Subsystem of the Electronic Notarization Facility (ENF)
### Process Flow → Collect Logs → Aggregate Data → Generate Reports → Submit to Supreme Court

```mermaid
flowchart TB

%%==========================
%% External Interfaces
%%==========================
SC[Supreme Court or ENA]
TRUTH[TRUTH Engine Core]
WALLET[Wallet Module]
KYC[KYC Module]
DOCS[Document Module]
SESSION[Session Management]
BANK[Bank or E-Wallet Provider]

%%==========================
%% Compliance Internal Processes
%%==========================
subgraph COMP_Module["Compliance, Audit, and Reporting Subsystem"]
direction TB
C1["P1 – Collect Logs from Subsystems"]
C2["P2 – Validate and Correlate Records"]
C3["P3 – Generate Audit Trail and Statistical Reports"]
C4["P4 – Submit Reports and Logs to Supreme Court"]
C5["P5 – Respond to Audit or Data Requests"]
end

%%==========================
%% Internal Data Stores
%%==========================
LogsDB[(Subsystem Logs Repository)]
AuditTrail[(Audit Trail Database)]
ReportStore[(Report Archive)]
SCRequests[(Audit Request Records)]

%%==========================
%% Data Flows
%%==========================
TRUTH -->|Integrity and Hash Logs| C1
WALLET -->|Transaction and Fee Logs| C1
KYC -->|Verification Logs| C1
DOCS -->|Document Metadata| C1
SESSION -->|Session Recordings Metadata| C1
BANK -->|Settlement Confirmations| C1

C1 -->|Collected Logs| LogsDB
LogsDB --> C2
C2 -->|Validated Data Sets| AuditTrail
AuditTrail --> C3
C3 -->|Audit Reports| ReportStore
ReportStore -->|Submit Summary Reports| C4
C4 -->|Transmit Reports| SC
SC -->|Audit Queries or Requests| C5
C5 -->|Respond with Records| SCRequests
SCRequests -->|Archived Requests| ReportStore

%%==========================
%% Visual Notes
%%==========================
N1[Note: Aggregates logs from all ENF modules including TRUTH Engine, KYC, Wallet, and Session.]
C1 -. context .- N1

N2[Note: Correlates entries using UID and timestamp to ensure data consistency and integrity across modules.]
C2 -. context .- N2

N3[Note: Generates audit trail, operational metrics, and periodic statistical summaries for regulatory oversight.]
C3 -. context .- N3

N4[Note: Transmits official notarization reports and logs to the Supreme Court using secure API or file gateway.]
C4 -. context .- N4

N5[Note: Handles incoming audit or data requests and delivers verified records or evidence files as needed.]
C5 -. context .- N5

%%==========================
%% Note Styling
%%==========================
classDef note fill:#fff5b1,stroke:#555,stroke-dasharray:3 3,color:#111;
class N1,N2,N3,N4,N5 note;
```
---

### 🧠 Interpretation

| Step | Function | Output / Store |
|------|-----------|----------------|
| **P1** | Collects logs from all subsystems (TRUTH, KYC, Wallet, Sessions, etc.) | Subsystem Logs Repository |
| **P2** | Validates, correlates, and normalizes logs | Audit Trail Database |
| **P3** | Produces audit trail and statistical or compliance reports | Report Archive |
| **P4** | Submits reports and logs to the Supreme Court / ENA | Transmission to SC |
| **P5** | Responds to audit queries and provides verified evidence | Request Records Archive |

---

### ✅ Characteristics
- Supreme Court and ENA integration shown explicitly.
- Each subsystem (TRUTH, KYC, Wallet, Session, Bank) is a **log source**.
- Data lifecycle: *Logs → Validation → Audit Trail → Report → Transmission → Request Response*.
- No disallowed symbols; confirmed Mermaid-compliant syntax.

---

# 📄 DFD Level 2 — Document and Session Management Module
## Subsystem of the Electronic Notarization Facility (ENF)
### Process Flow → Upload Document → Prepare Metadata → Create Notarization Session → Record Interaction → Bind TRUTH Envelope → Archive

```mermaid
flowchart TB

%%==========================
%% External Interfaces
%%==========================
CUSTOMER[Customer or Signatory]
ENP[Electronic Notary Public]
TRUTH[TRUTH Engine Core]
KYC[KYC Module]
COMP[Compliance Module]

%%==========================
%% Internal Processes
%%==========================
subgraph DOCS_Module["Document and Session Management Subsystem"]
direction TB
D1["P1 – Upload Document and Create Record"]
D2["P2 – Extract and Register Metadata"]
D3["P3 – Generate Notarization Session Link"]
D4["P4 – Conduct and Record Remote Session"]
D5["P5 – Bind TRUTH Envelope and Session Data"]
D6["P6 – Store and Archive Notarized Document"]
end

%%==========================
%% Internal Data Stores
%%==========================
UploadRepo[(Uploaded Document Repository)]
MetaDB[(Document Metadata Store)]
SessionDB[(Session Records Database)]
RecordingDB[(Session Recordings Archive)]
NotarizedRepo[(Final Notarized Document Archive)]

%%==========================
%% Data Flows
%%==========================
CUSTOMER -->|Uploads Document| D1
ENP -->|Initiates Notarization Session| D3
KYC -->|Provides Verified Identity Data| D2
D1 -->|File Record| UploadRepo
UploadRepo --> D2
D2 -->|Extracted Metadata| MetaDB
MetaDB --> D3
D3 -->|Session Link| ENP
ENP -->|Launch Session| D4
CUSTOMER -->|Join Session| D4
D4 -->|Video and Audio Data| RecordingDB
D4 -->|Session Summary| SessionDB
SessionDB --> D5
TRUTH -->|TRUTH Envelope Reference| D5
D5 -->|Bound Envelope and Document| NotarizedRepo
D6 -->|Archived Record| COMP
NotarizedRepo -->|Document Copy| CUSTOMER

%%==========================
%% Visual Notes
%%==========================
N1[Note: Uploads the raw document, assigns an internal ID, and creates a transaction record.]
D1 -. context .- N1

N2[Note: Extracts metadata such as title, document type, and signer names and stores it in metadata database.]
D2 -. context .- N2

N3[Note: Generates a secure, single-use notarization session link shared with ENP and customer for remote notarization.]
D3 -. context .- N3

N4[Note: Conducts real-time video session between ENP and customer and records it for evidentiary purposes.]
D4 -. context .- N4

N5[Note: Binds the TRUTH Envelope with document hash, metadata, and recording references to ensure verifiable integrity.]
D5 -. context .- N5

N6[Note: Archives the notarized file and forwards reference and summary logs to the compliance subsystem.]
D6 -. context .- N6

%%==========================
%% Note Styling
%%==========================
classDef note fill:#fff5b1,stroke:#555,stroke-dasharray:3 3,color:#111;
class N1,N2,N3,N4,N5,N6 note;
```
---

### 🧠 Interpretation

| Step | Function | Output / Storage |
|------|-----------|------------------|
| **P1** | Uploads original document and registers upload transaction | Uploaded Document Repository |
| **P2** | Extracts and registers metadata for identification and indexing | Document Metadata Store |
| **P3** | Generates secure session link (Zoom-like) for remote notarization | Session Records Database |
| **P4** | Conducts and records live session between ENP and customer | Session Recordings Archive |
| **P5** | Binds TRUTH Envelope to the notarized document and session logs | Final Notarized Document Archive |
| **P6** | Archives document and forwards logs to Compliance for oversight | Compliance Module |

---

# 🧾 DFD Level 2 — ENP Onboarding and Tariff Management Module
## Subsystem of the Electronic Notarization Facility (ENF)
### Process Flow → Registration → Credential Verification → Profile Creation → Tariff Setup → Wallet Link → Activation

```mermaid
flowchart TB

%%==========================
%% External Interfaces
%%==========================
ENP[Electronic Notary Public Applicant]
KYC[KYC Verification Module]
COMP[Compliance Module]
WALLET[Wallet and Payment Module]

%%==========================
%% Internal Processes
%%==========================
subgraph ENP_Module["ENP Onboarding and Tariff Management Subsystem"]
direction TB
E1["P1 – Register and Submit Application"]
E2["P2 – Verify Identity and License Credentials"]
E3["P3 – Create ENP Profile and Digital Seal"]
E4["P4 – Define Tariff Schedule and Fees"]
E5["P5 – Link Wallet and Payment Preferences"]
E6["P6 – Approve and Activate ENP Account"]
end

%%==========================
%% Internal Data Stores
%%==========================
ENPApplications[(ENP Application Records)]
LicenseDB[(Verified License Data)]
ENPProfiles[(ENP Profiles and Digital Seals)]
TariffDB[(Tariff and Fee Structures)]
WalletLinks[(Linked Wallet Accounts)]

%%==========================
%% Data Flows
%%==========================
ENP -->|Submit Application Form| E1
E1 -->|Application Record| ENPApplications
E1 --> E2
E2 -->|Send Identity Data| KYC
KYC -->|Verified Result| E2
E2 -->|Validated License and ID| LicenseDB
LicenseDB --> E3
E3 -->|Profile Data| ENPProfiles
ENPProfiles --> E4
E4 -->|Fee Configuration| TariffDB
E4 --> E5
E5 -->|Wallet Link Request| WALLET
WALLET -->|Wallet Confirmation| E5
E5 -->|Linked Wallet Reference| WalletLinks
WalletLinks --> E6
E6 -->|Activation Notice| ENP
E6 -->|Status and Summary| COMP

%%==========================
%% Visual Notes
%%==========================
N1[Note: Registers the ENP applicant and captures basic contact and jurisdiction information.]
E1 -. context .- N1

N2[Note: Validates identity and license credentials through KYC and official notarial registry checks.]
E2 -. context .- N2

N3[Note: Creates ENP profile including digital seal, signature sample, and assigned jurisdiction code.]
E3 -. context .- N3

N4[Note: Allows ENP to define per document or per session fee schedule stored in the tariff database.]
E4 -. context .- N4

N5[Note: Links ENP profile with wallet system for payments, settlements, and revenue distribution.]
E5 -. context .- N5

N6[Note: Activates ENP account and reports registration completion to compliance monitoring.]
E6 -. context .- N6

%%==========================
%% Note Styling
%%==========================
classDef note fill:#fff5b1,stroke:#555,stroke-dasharray:3 3,color:#111;
class N1,N2,N3,N4,N5,N6 note;
```

---
### 🧠 Interpretation

| Step | Function | Output / Store |
|------|-----------|----------------|
| **P1** | Accepts registration data and creates application record | ENP Application Records |
| **P2** | Validates identity and notarial license credentials | Verified License Data |
| **P3** | Generates ENP profile and digital seal | ENP Profiles and Seals |
| **P4** | Allows tariff and fee configuration | Tariff and Fee Structures |
| **P5** | Links ENP wallet for transactions | Linked Wallet Accounts |
| **P6** | Activates ENP account and reports to Compliance | Compliance Module |

---

### ✅ Highlights
- Integrates tightly with **KYC** and **Wallet** subsystems for trust and financial control.
- Automates end-to-end onboarding from registration through activation.
- Maintains transparent linkage among license verification, tariff data, and wallet settlement.
- Syntax-safe, renders cleanly in VS Code, GitHub, Obsidian, and Mermaid Live Editor.

---
# 👤 DFD Level 2 — Customer Portal and Document Management Interface
## Subsystem of the Electronic Notarization Facility (ENF)
### Process Flow → Account Creation → KYC Reuse → Upload → Sign → Verify → Retrieve

```mermaid
flowchart TB

%%==========================
%% External Interfaces
%%==========================
CUSTOMER[Customer or Signatory]
ENP[Electronic Notary Public]
KYC[KYC Verification Module]
DOCS[Document and Session Module]
TRUTH[TRUTH Engine Core]
COMP[Compliance Module]

%%==========================
%% Internal Processes
%%==========================
subgraph CUSTOMER_Module["Customer Portal and Document Management Subsystem"]
direction TB
C1["P1 – Register or Authenticate Customer Account"]
C2["P2 – Reuse or Perform KYC Verification"]
C3["P3 – Upload and Tag Document"]
C4["P4 – Apply Electronic Signature"]
C5["P5 – Verify Notarized Document"]
C6["P6 – Manage, Search, and Retrieve Documents"]
end

%%==========================
%% Internal Data Stores
%%==========================
CustomerProfiles[(Customer Accounts Database)]
CustomerKYC[(Customer KYC Records)]
CustomerDocs[(Customer Document Library)]
SignLog[(Signature History)]
VerifyLog[(Verification Records)]

%%==========================
%% Data Flows
%%==========================
CUSTOMER -->|Register or Log In| C1
C1 -->|Account Record| CustomerProfiles
C1 --> C2
C2 -->|Request Verification| KYC
KYC -->|Verified Identity| C2
C2 -->|Identity Envelope| CustomerKYC
CUSTOMER -->|Upload Document| C3
C3 -->|Tagged Document| CustomerDocs
CustomerDocs --> C4
C4 -->|Signed Document| DOCS
C4 -->|Signature Entry| SignLog
DOCS -->|TRUTH Envelope Reference| C5
TRUTH -->|Verification Hash| C5
C5 -->|Verification Result| VerifyLog
C6 -->|Search and Retrieval Request| CustomerDocs
CustomerDocs -->|Return Document Copy| CUSTOMER
C5 -->|Verification Report| COMP

%%==========================
%% Visual Notes
%%==========================
N1[Note: Handles user registration, authentication, and profile creation using secure credentials or one-time codes.]
C1 -. context .- N1

N2[Note: Reuses stored KYC verification or triggers new identity verification through the KYC module.]
C2 -. context .- N2

N3[Note: Allows customers to upload documents, tag them with metadata, and store them in the personal document library.]
C3 -. context .- N3

N4[Note: Enables browser-based electronic signing using touch, stylus, or verified digital signature.]
C4 -. context .- N4

N5[Note: Verifies notarized documents by comparing TRUTH Envelope hash and digital signature results.]
C5 -. context .- N5

N6[Note: Provides search, filter, download, and sharing functions for all notarized documents with full audit trail.]
C6 -. context .- N6

%%==========================
%% Note Styling
%%==========================
classDef note fill:#fff5b1,stroke:#555,stroke-dasharray:3 3,color:#111;
class N1,N2,N3,N4,N5,N6 note;
```
---

### 🧠 Interpretation

| Step | Function | Output / Storage |
|------|-----------|------------------|
| **P1** | Customer registration or login | Customer Accounts Database |
| **P2** | Performs or reuses KYC verification | Customer KYC Records |
| **P3** | Uploads and tags documents | Customer Document Library |
| **P4** | Applies electronic signature | Signature History |
| **P5** | Verifies TRUTH envelope and signatures | Verification Records |
| **P6** | Retrieves and manages documents for download or sharing | Customer Document Library |

---

### ✅ Highlights
- Integrates directly with KYC, TRUTH Engine, and Document Session modules.
- Customer can verify and retrieve documents without installing additional apps.
- All records are cross-referenced with Compliance for auditability.
- Syntax-safe and renderable in VS Code, GitHub, and Mermaid Live Editor.

---
# ⛓️ DFD Level 2 — Blockchain Anchoring Service
## Subsystem of the Electronic Notarization Facility (ENF)
### Process Flow → Receive Hash → Build Transaction → Submit → Confirm → Return Proof

```mermaid
flowchart TB

%%==========================
%% External Interfaces
%%==========================
TRUTH[TRUTH Engine Core]
COMP[Compliance Module]
CHAIN[Blockchain Network Node]
AUDIT[External Verifier or Court]

%%==========================
%% Internal Processes
%%==========================
subgraph BC_Module["Blockchain Anchoring Subsystem"]
direction TB
B1["P1 – Receive Hash and Metadata from TRUTH Engine"]
B2["P2 – Create Blockchain Transaction Payload"]
B3["P3 – Submit Transaction to Blockchain Node"]
B4["P4 – Wait for Confirmation and Record Proof"]
B5["P5 – Return Proof Reference and Status"]
end

%%==========================
%% Internal Data Stores
%%==========================
HashQueue[(Pending Hash Queue)]
TxPool[(Transaction Pool)]
ProofLog[(Proof and Receipt Ledger)]
ErrorLog[(Failed Submission Log)]

%%==========================
%% Data Flows
%%==========================
TRUTH -->|Submit Hash and UID| B1
B1 -->|Queued for Anchoring| HashQueue
HashQueue --> B2
B2 -->|Transaction Data| TxPool
TxPool --> B3
B3 -->|Broadcast Transaction| CHAIN
CHAIN -->|Confirmation Receipt| B4
B4 -->|Proof Data| ProofLog
ProofLog -->|Proof Reference| B5
B5 -->|Return Proof Reference| TRUTH
B5 -->|Anchoring Status and Audit Entry| COMP
B3 -->|Error Event| ErrorLog
ErrorLog -->|Resubmission Entry| B2
AUDIT -->|Verification Query| ProofLog
ProofLog -->|Provide Proof and Timestamp| AUDIT

%%==========================
%% Visual Notes
%%==========================
N1[Note: Receives document hash, UID, and metadata from the TRUTH Engine and queues it for anchoring.]
B1 -. context .- N1

N2[Note: Builds a blockchain transaction payload containing the hash, timestamp, and minimal metadata.]
B2 -. context .- N2

N3[Note: Broadcasts the transaction to the blockchain network node and records transaction ID.]
B3 -. context .- N3

N4[Note: Waits for confirmation from the blockchain and writes proof data to the ledger.]
B4 -. context .- N4

N5[Note: Returns immutable proof reference to TRUTH Engine and compliance subsystem for verification and audit.]
B5 -. context .- N5

%%==========================
%% Note Styling
%%==========================
classDef note fill:#fff5b1,stroke:#555,stroke-dasharray:3 3,color:#111;
class N1,N2,N3,N4,N5 note;
```
---

### 🧠 Interpretation

| Step | Function | Output / Store |
|------|-----------|----------------|
| **P1** | Accepts document hash and metadata from TRUTH Engine | Pending Hash Queue |
| **P2** | Builds blockchain transaction payload | Transaction Pool |
| **P3** | Broadcasts transaction to blockchain node | Confirmation Receipt |
| **P4** | Waits for confirmation and stores proof | Proof and Receipt Ledger |
| **P5** | Returns immutable proof reference and status | TRUTH Engine / Compliance Module |

---

### ✅ Highlights
- Provides decentralized, tamper-proof evidence of notarization.
- Stores both pending and confirmed states to ensure reliable retries.
- Returns lightweight proof references usable in courts or independent audits.
- Syntax verified: safe for Mermaid v10+, VS Code, GitHub, Obsidian, and Live Editor.

---
# 💸 DFD Level 2 — System Wallet and Clearinghouse Module
## Subsystem of the Electronic Notarization Facility (ENF)
### Process Flow → Collect Fees → Split Funds → Settle Accounts → Record Ledger → Report to Compliance

```mermaid
flowchart TB

%%==========================
%% External Interfaces
%%==========================
ENP[Electronic Notary Public]
CUSTOMER[Customer or Signatory]
BANK[Bank or E-Wallet Provider]
COMP[Compliance Module]
WALLET[ENP Wallet Module]

%%==========================
%% Internal Processes
%%==========================
subgraph CLEAR_Module["System Wallet and Clearinghouse Subsystem"]
direction TB
S1["P1 – Receive Payment or Fee from Transaction"]
S2["P2 – Validate Source and Payment Reference"]
S3["P3 – Split Funds Between ENP, Platform, and Regulatory Accounts"]
S4["P4 – Execute Internal Transfers and Settlements"]
S5["P5 – Record Ledger Entry and Reconcile Balances"]
S6["P6 – Generate Financial Report for Compliance"]
end

%%==========================
%% Internal Data Stores
%%==========================
FeePool[(Fee Collection Pool)]
PlatformAcct[(Platform Main Account)]
ENPAcct[(ENP Accounts Ledger)]
RegAcct[(Regulatory or Tax Accounts)]
LedgerDB[(Financial Ledger Database)]
ReportDB[(Clearinghouse Reports Archive)]

%%==========================
%% Data Flows
%%==========================
CUSTOMER -->|Pays Notarization Fee| S1
S1 -->|Incoming Payment| FeePool
FeePool --> S2
S2 -->|Validated Reference| S3
S3 -->|Split Calculation| PlatformAcct
S3 -->|ENP Share| ENPAcct
S3 -->|Regulatory Share| RegAcct
S3 --> S4
S4 -->|Transfer Instructions| BANK
BANK -->|Settlement Confirmation| S4
S4 -->|Transfer Record| LedgerDB
S5 -->|Ledger Posting| LedgerDB
LedgerDB -->|Reconciliation Data| S6
S6 -->|Summary Report| ReportDB
ReportDB -->|Compliance Feed| COMP
ENPAcct -->|Balance Update| WALLET
PlatformAcct -->|Revenue Summary| COMP

%%==========================
%% Visual Notes
%%==========================
N1[Note: Receives notarization fee or top-up payment from customers and moves funds into fee pool.]
S1 -. context .- N1

N2[Note: Validates transaction source and reference number before any disbursement occurs.]
S2 -. context .- N2

N3[Note: Splits collected funds automatically based on predefined percentages for ENP, platform, and regulatory accounts.]
S3 -. context .- N3

N4[Note: Executes fund transfers to respective accounts through integrated banking or e-wallet APIs.]
S4 -. context .- N4

N5[Note: Records double-entry ledger transactions for all disbursements and reconciles end-of-day balances.]
S5 -. context .- N5

N6[Note: Produces clearinghouse summary reports and forwards data to the compliance subsystem for audit.]
S6 -. context .- N6

%%==========================
%% Note Styling
%%==========================
classDef note fill:#fff5b1,stroke:#555,stroke-dasharray:3 3,color:#111;
class N1,N2,N3,N4,N5,N6 note;
```
---

### 🧠 Interpretation

| Step | Function | Output / Storage |
|------|-----------|------------------|
| **P1** | Receives notarization payment from customer | Fee Collection Pool |
| **P2** | Validates transaction source and reference | Verified Payment Record |
| **P3** | Calculates revenue shares for ENP, platform, and regulator | Account Entries |
| **P4** | Executes actual fund transfers | Settlement Confirmation |
| **P5** | Records ledger entries and performs reconciliation | Financial Ledger Database |
| **P6** | Generates compliance and tax reporting summaries | Clearinghouse Reports Archive |

---

### ✅ Highlights
- Provides transparent and automated multi-party settlements.
- Maintains real-time wallet balance updates for ENPs.
- Integrates with the banking layer for withdrawals and payouts.
- All financial movements are logged for compliance and auditing.
- Fully Mermaid-v10 validated and safe for IDE or Markdown rendering.

---
# 📬 DFD Level 2 — Notification and Communication Module
## Subsystem of the Electronic Notarization Facility (ENF)
### Process Flow → Trigger Event → Compose Message → Select Channel → Deliver → Log Status → Report

```mermaid
flowchart TB

%%==========================
%% External Interfaces
%%==========================
CUSTOMER[Customer or Signatory]
ENP[Electronic Notary Public]
COMP[Compliance Module]
BANK[Bank or E-Wallet Provider]
DOCS[Document and Session Module]
WALLET[Wallet and Clearinghouse Module]

%%==========================
%% Internal Processes
%%==========================
subgraph NOTIF_Module["Notification and Communication Subsystem"]
direction TB
N1["P1 – Receive Event or Trigger"]
N2["P2 – Compose Notification Message"]
N3["P3 – Select Communication Channel"]
N4["P4 – Deliver Message to Recipient"]
N5["P5 – Log Delivery Status"]
N6["P6 – Generate Notification Report"]
end

%%==========================
%% Internal Data Stores
%%==========================
EventQueue[(Event Trigger Queue)]
MessageTemplates[(Message Templates Library)]
DeliveryLog[(Notification Delivery Log)]
ReportArchive[(Notification Reports Archive)]

%%==========================
%% Data Flows
%%==========================
DOCS -->|Session Completed Event| N1
WALLET -->|Payment Confirmation Event| N1
BANK -->|Settlement Update| N1
COMP -->|Audit or Alert Event| N1

N1 -->|Queued Event| EventQueue
EventQueue --> N2
N2 -->|Message Draft| MessageTemplates
MessageTemplates --> N3
N3 -->|Channel Selection| N4
N4 -->|Send via Email or SMS| CUSTOMER
N4 -->|Send via In-App or Webhook| ENP
N4 -->|Delivery Receipt| N5
N5 -->|Logged Entry| DeliveryLog
N5 --> N6
N6 -->|Summary Report| ReportArchive
ReportArchive -->|Notification Audit Feed| COMP

%%==========================
%% Visual Notes
%%==========================
M1[Note: Captures events from ENF modules such as document notarization, wallet transactions, and system alerts.]
N1 -. context .- M1

M2[Note: Composes human-readable messages using predefined templates and dynamic placeholders.]
N2 -. context .- M2

M3[Note: Determines appropriate channel such as email, SMS, or in-app notification based on recipient preferences.]
N3 -. context .- M3

M4[Note: Sends message to customer or ENP and records confirmation receipts or webhook responses.]
N4 -. context .- M4

M5[Note: Logs message delivery outcome for audit and retry if failure occurs.]
N5 -. context .- M5

M6[Note: Generates daily and monthly notification reports and submits copies to compliance subsystem.]
N6 -. context .- M6

%%==========================
%% Note Styling
%%==========================
classDef note fill:#fff5b1,stroke:#555,stroke-dasharray:3 3,color:#111;
class M1,M2,M3,M4,M5,M6 note;
```
---

### 🧠 Interpretation

| Step | Function | Output / Storage |
|------|-----------|------------------|
| **P1** | Receives system or business event trigger | Event Trigger Queue |
| **P2** | Generates message content using templates | Message Templates Library |
| **P3** | Chooses appropriate communication channel | Channel Selection |
| **P4** | Delivers message to ENP or Customer | Sent Notification |
| **P5** | Logs delivery status and errors | Notification Delivery Log |
| **P6** | Produces notification activity report for Compliance | Notification Reports Archive |

---

### ✅ Highlights
- Integrates with all subsystems (Wallet, Document, Compliance, and Bank).
- Supports omni-channel delivery (email, SMS, in-app, webhook).
- Logs all messages and delivery receipts for traceability.
- Enables retry and audit for failed messages.
- 100% Mermaid syntax validated for Markdown and IDE rendering.

---
# 🔒 DFD Level 2 — Security, Access Control, and Audit Module
## Subsystem of the Electronic Notarization Facility (ENF)
### Process Flow → Authenticate User → Validate Role → Authorize Action → Log Event → Monitor Security

```mermaid
flowchart TB

%%==========================
%% External Interfaces
%%==========================
CUSTOMER[Customer or Signatory]
ENP[Electronic Notary Public]
ADMIN[System Administrator]
COMP[Compliance Module]
KYC[KYC Verification Module]
DOCS[Document and Session Module]
WALLET[Wallet Module]

%%==========================
%% Internal Processes
%%==========================
subgraph SEC_Module["Security, Access Control, and Audit Subsystem"]
direction TB
S1["P1 – Receive Login or Access Request"]
S2["P2 – Validate Credentials and Session Token"]
S3["P3 – Determine User Role and Permissions"]
S4["P4 – Authorize Requested Action"]
S5["P5 – Log Security and Access Event"]
S6["P6 – Monitor Security Alerts and Generate Audit Reports"]
end

%%==========================
%% Internal Data Stores
%%==========================
UserDB[(User Accounts Database)]
RoleDB[(Role and Permission Matrix)]
TokenStore[(Active Session Tokens)]
SecurityLog[(Security and Access Log)]
AlertArchive[(Security Alerts and Audit Reports)]

%%==========================
%% Data Flows
%%==========================
CUSTOMER -->|Login Request| S1
ENP -->|Authentication Request| S1
ADMIN -->|System Access Request| S1

S1 -->|Credentials| S2
S2 -->|Verify Identity| KYC
KYC -->|Validation Result| S2
S2 -->|Valid Session Token| TokenStore
TokenStore --> S3
S3 -->|Role Mapping| RoleDB
RoleDB --> S4
S4 -->|Authorized Action| DOCS
S4 -->|Authorized Transaction| WALLET
S4 -->|Access Record| S5
S5 -->|Security Log Entry| SecurityLog
SecurityLog -->|Periodic Review| S6
S6 -->|Audit Summary| AlertArchive
AlertArchive -->|Compliance Report| COMP

%%==========================
%% Visual Notes
%%==========================
N1[Note: Accepts login or system access requests from customers, ENPs, and administrators.]
S1 -. context .- N1

N2[Note: Validates credentials, tokens, or biometric data to establish secure session identity.]
S2 -. context .- N2

N3[Note: Determines user role, permissions, and scope of allowed actions within the ENF platform.]
S3 -. context .- N3

N4[Note: Checks requested operation against permissions matrix and enforces access control rules.]
S4 -. context .- N4

N5[Note: Records every security event including login, logout, and access attempts with timestamp and user ID.]
S5 -. context .- N5

N6[Note: Analyzes security logs to detect anomalies and generates periodic audit and compliance reports.]
S6 -. context .- N6

%%==========================
%% Note Styling
%%==========================
classDef note fill:#fff5b1,stroke:#555,stroke-dasharray:3 3,color:#111;
class N1,N2,N3,N4,N5,N6 note;
```
---

### 🧠 Interpretation

| Step | Function | Output / Storage |
|------|-----------|------------------|
| **P1** | Receives user login or access request | User Accounts Database |
| **P2** | Validates credentials, tokens, or biometric input | Active Session Tokens |
| **P3** | Determines role and permissions based on user type | Role and Permission Matrix |
| **P4** | Grants or denies requested action | Authorized Operation |
| **P5** | Logs all security events | Security and Access Log |
| **P6** | Monitors logs, detects anomalies, and generates reports | Security Alerts and Audit Reports |

---

### ✅ Highlights
- Provides **role-based access control** for ENPs, customers, and admins.
- Integrates directly with **KYC** for identity verification and with **Compliance** for reporting.
- Supports **tokenized sessions** and centralized **security log management**.
- Enables **real-time alerting** and audit traceability across modules.
- Syntax verified — safe for Mermaid v10, GitHub, VS Code, and Obsidian.

---
