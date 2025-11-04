
# Core Modules – Backend Services Breakdown (Laravel 12 API-first)

## 🧠 1. Backend Services

---

### 🔐 Authentication & Authorization (OAuth2/JWT)

| Type       | Name                                | Notes |
|------------|-------------------------------------|-------|
| Action     | `AuthenticateUser`                  | Handles login via JWT or OAuth2 |
| Service    | `TokenService`                      | Issues, revokes, refreshes tokens |
| Event      | `UserAuthenticated`                 | Fired on successful login |
| Listener   | `LogAuthentication`                 | Writes to auth log |
| Endpoint   | `POST /api/login`                   | Login with credentials |
| Helper     | `auth_helpers.php`                  | `isAdmin()`, `isENP()` etc. |

---

### 👤 User & Role Management (Admin, ENP, Subscriber, SC)

| Type       | Name                                | Notes |
|------------|-------------------------------------|-------|
| Action     | `CreateUser`, `UpdateUserRole`      | Role assignment and management |
| Service    | `RoleService`, `UserService`        | Centralized user logic |
| DTO        | `UserData`, `RoleData`              | For internal and API use |
| Endpoint   | `GET /api/users`, `POST /api/users` | Admin panel for user management |
| Command    | `CreateSystemAdmin`                 | Bootstrap initial user/admin |
| Helper     | `role_helpers.php`                  | For role-based logic in policies, views |

---

### 📄 Document Handling (upload, sign, seal, store)

| Type       | Name                                | Notes |
|------------|-------------------------------------|-------|
| Action     | `UploadDocument`, `SignDocument`    | Core document flow |
| Service    | `DocumentService`, `SignatureService` | Hash, store, and sign docs |
| Job        | `SealDocumentJob`                   | Post-signature sealing |
| DTO        | `DocumentData`, `SignatureData`     | Metadata & content |
| Endpoint   | `POST /api/documents/upload`        | Multipart or base64 upload |
| Event      | `DocumentSigned`, `DocumentUploaded`| |
| Listener   | `NotifySignatories`                 | Email/SMS push |

---

### 🗓️ Booking & Scheduling (calendar, reminders, availability)

| Type       | Name                                | Notes |
|------------|-------------------------------------|-------|
| Action     | `BookSession`, `UpdateAvailability` | |
| Service    | `ScheduleService`                   | Handles rules, collisions, availability |
| DTO        | `ScheduleSlotData`, `BookingData`   | |
| Job        | `SendBookingReminder`               | Queued reminders |
| Endpoint   | `POST /api/bookings`, `GET /api/schedule` | |
| Event      | `SessionBooked`                     | |
| Listener   | `SendCalendarInvite`                | iCal, Google, etc. |

---

### 📹 Video Session Orchestration (Zoom API, Jitsi)

| Type       | Name                                | Notes |
|------------|-------------------------------------|-------|
| Service    | `VideoSessionService`               | Create/join via Zoom, Jitsi |
| Job        | `CreateZoomMeetingJob`              | Async Zoom call setup |
| DTO        | `VideoSessionData`                  | Contains link, passcode, etc. |
| Endpoint   | `GET /api/video-session/join`       | Returns session info |
| Event      | `VideoSessionStarted`, `Ended`      | |
| Listener   | `RecordSessionMetadata`             | |

---

### 🧍‍♂️ eKYC & Selfie Verification

| Type       | Name                                | Notes |
|------------|-------------------------------------|-------|
| Action     | `StartKYC`, `VerifySelfie`          | |
| Service    | `HypervergeKYCService`, `FaceMatchService` | Wraps external API |
| DTO        | `KYCResultData`, `SelfieData`       | |
| Endpoint   | `POST /api/ekyc/start`              | |
| Event      | `KYCVerified`                       | |
| Listener   | `CreateVerifiedUserProfile`         | |

---

### ✍️ Digital Signature & Notarization Log

| Type       | Name                                  | Notes |
|------------|---------------------------------------|-------|
| Service    | `NotarizationService`                 | Applies logic, seals, logs |
| Job        | `LogNotarizationActivity`             | Stores to blockchain or DB |
| DTO        | `NotarizationLogData`                 | |
| Endpoint   | `POST /api/notarize`                  | |
| Event      | `NotarizationCompleted`               | |
| Listener   | `AnchorToBlockchain` (optional)       | |

---

### 📚 Audit Trail + Blockchain Anchoring

| Type       | Name                                  | Notes |
|------------|---------------------------------------|-------|
| Service    | `AuditTrailService`, `BlockchainService` | |
| Job        | `AnchorDocumentHashJob`               | |
| DTO        | `AuditEntryData`                      | |
| Endpoint   | `GET /api/audit/document/{id}`        | |
| Command    | `ExportAuditLogs`                     | For download or batch push |

---

### 💳 Payment Gateway Integration

| Type       | Name                                | Notes |
|------------|-------------------------------------|-------|
| Service    | `PaymentService`, `BillingService`  | |
| Action     | `InitiatePayment`, `VerifyPayment`  | |
| DTO        | `PaymentIntentData`, `ReceiptData`  | |
| Endpoint   | `POST /api/payments/initiate`       | |
| Event      | `PaymentConfirmed`                  | |
| Listener   | `MarkBookingAsPaid`                 | |

---

### 🔔 Notifications (Email, SMS, In-App)

| Type       | Name                                | Notes |
|------------|-------------------------------------|-------|
| Service    | `NotificationService`               | |
| Event      | `NotificationQueued`                | |
| Listener   | `SendEmailNotification`, `SendSMSNotification` | |
| DTO        | `NotificationData`                  | |
| Helper     | `notification_helpers.php`          | Templates, urgency tags |

---

## ✅ Summary View

| Concern        | Key Services                        | Key Actions/Jobs            | API Endpoints             |
|----------------|-------------------------------------|-----------------------------|---------------------------|
| Auth           | `TokenService`                      | `AuthenticateUser`          | `/api/login`, `/api/token/refresh` |
| User Mgmt      | `UserService`, `RoleService`        | `CreateUser`, `AssignRole`  | `/api/users`, `/api/roles` |
| Documents      | `DocumentService`, `SignatureService` | `UploadDocument`, `SealDocumentJob` | `/api/documents/upload`, `/api/documents/sign` |
| KYC            | `HypervergeKYCService`              | `StartKYC`, `VerifySelfie`  | `/api/ekyc/start` |
| Video          | `VideoSessionService`               | `CreateZoomMeetingJob`      | `/api/video-session/join` |
| Notarization   | `NotarizationService`               | `LogNotarizationActivity`   | `/api/notarize` |
| Audit          | `AuditTrailService`                 | `AnchorDocumentHashJob`     | `/api/audit/document/{id}` |
| Payments       | `PaymentService`                    | `InitiatePayment`, `VerifyPayment` | `/api/payments/initiate` |
| Notifications  | `NotificationService`               | `SendEmailNotification`     | Various `/api/notify/*` |
