# Frictionless Customer Journey Design for the ENF

## Purpose
Define a low-friction customer experience for the Electronic Notarization Facility (ENF), balancing instant, link-based access with optional accounts for repeat and enterprise users. This will be the reference for UX flows, pricing, and feature gating across modules (KYC, TRUTH Engine, Wallet, Sessions, Compliance).

---

## Design Principles
- **Zero-friction entry:** No forced signup for first-time notarization.
- **Progressive disclosure:** Ask only what’s needed when it’s needed (e.g., KYC at first notarization).
- **One-link access:** Zoom-like session link; no app install.
- **Instant delivery:** Notarized PDF + TRUTH Envelope immediately after completion.
- **Reusability:** Store verified identity for reuse (with consent).
- **Audit & compliance by default:** TRUTH envelope + logs + 10-year retention.
- **Payments that just work:** In-country wallets, cards, bank rails; escrow release after ENP confirmation.

---

## Two Journey Models

### 1) On-Demand (Default, No Account Required)
**Who:** One-off users, citizens, occasional business users  
**Flow:**
1. Click “Start Notarization” or ENP session link.
2. Upload document (browser).
3. eKYC (ID OCR + selfie + liveness).
4. Join video session via link.
5. Pay fee (wallet/GCash/card).
6. Receive notarized PDF + TRUTH Envelope (download + email).
7. Optional: create an account to save and manage documents.

**Pros:** Minimal friction; fastest time-to-completion.  
**Tradeoffs:** Less continuity unless user opts into an account afterward.

---

### 2) Account-Based / Subscription (Optional)
**Who:** Law firms, banks, real estate, HR teams, frequent users  
**Flow:**
1. Sign up / SSO.
2. Reuse KYC credential (or KYC once).
3. Upload or select stored docs.
4. Schedule or launch session; bulk or templated flows.
5. Auto-billing via wallet; invoicing.
6. Retrieve, search, and share documents from dashboard.

**Pros:** Reusability, bulk workflows, analytics, team access.  
**Tradeoffs:** Slightly higher initial friction; justified for frequent users.

---

## Recommended Hybrid Strategy (PH Context)
- **Default experience:** On-Demand (no account).
- **Upsell path:** Offer account creation post-completion (“Save for later, reuse your verified identity, track invoices.”).
- **Enterprise landing:** Dedicated entry for firms with bulk tools, APIs, teams, and tariff/settlement controls.

---

## Global Best Practices Referenced (Abstracted)
- **US (link-based REN):** Email/SMS link; eKYC + live video; no mandatory login.
- **Singapore (gov ID):** Reusable government identity; account required for official services.
- **EU (eID):** Strong digital identity; account-centered.
- **India/UAE:** Hybrid: one-time KYC + OTP; optional portal for frequent users.

---

## Experience Playbook (Frictionless UX)
- **Entry:** One CTA; deep link support with prefilled metadata.
- **KYC:** Single screen, real-time feedback; retry without losing progress.
- **Session:** WebRTC/SDK in-browser; consent banner; bandwidth auto-adjust.
- **Signing:** In-browser signature; show live hash preview and “What is TRUTH Envelope?” helper.
- **Payment:** Inline checkout; bank/e-wallet; success handoff to receipt.
- **Delivery:** Instant file + TRUTH Envelope; QR for verification; copy link.
- **Recovery:** Smart resume for dropped sessions; email/SMS rejoin.

---

## System Best Practices
- **Tokens not passwords** for on-demand sessions.
- **Re-auth on sensitive steps** (e.g., payout, revocation).
- **RA 10173 (DPA) consent prompts** at KYC and video start.
- **TRUTH-first artifacts:** Always bind notarized doc to a TRUTH Envelope.
- **Optional chain anchoring:** Timestamp on blockchain for non-repudiation.
- **Observability:** End-to-end metrics on completion time, drop-off, and pass rates.

---

## Pricing & Monetization Options
- **On-Demand:** Per-notarization fee (simple, shown up-front).
- **Subscriptions:** Monthly seats for firms; bundled minutes/sessions; discounted per-document pricing.
- **Add-ons:** Rush sessions, bilingual ENP, certified copies, blockchain anchoring, extended retention.
- **Settlement:** Automatic ENP revenue share via Wallet & Clearinghouse.

---

## Governance & Compliance
- **Session evidence:** Recording + logs retained ≥ 10 years.
- **SC/ENA access:** Dedicated compliance interface + export.
- **Auditability:** Every artifact has a TRUTH hash; public verification link/QR.
- **Data minimization:** Only required data retained; configurable retention policies.

---

## KPIs & Success Metrics
- **TTV (time-to-verify):** From link click to KYC pass.
- **TTS (time-to-sign):** From upload to final signature.
- **Completion rate:** Sessions successfully notarized.
- **Drop-off points:** KYC, payment, join failures.
- **CSAT / NPS:** Post-session survey scores.
- **ENP utilization:** Sessions per day, approval times, earnings.

---

## Implementation Phasing
- **Phase 1 (MVP):** On-demand flow; ENP session link; KYC; payment; TRUTH envelope; email delivery.
- **Phase 2:** Customer accounts; dashboard; document search; reuse KYC; invoicing.
- **Phase 3:** Enterprise features; APIs; bulk notarization; templates; team roles.
- **Phase 4:** Advanced analytics; blockchain anchoring; multi-language; internationalization.

---

## Risks & Mitigations
- **KYC false negatives:** Multi-try fallback, manual review queue.
- **Session instability:** Adaptive bitrate; call reconnection; PSTN fallback if needed.
- **Payment failure:** Multiple rails and retries; pre-authorization option.
- **User confusion:** Contextual help; tooltips; progressive guidance; live chat option.
- **Regulatory updates:** Config flags for policy changes; modular compliance rules.

---

## Artifact Checklist (per notarization)
- Notarized PDF (PDF/A).
- TRUTH Envelope (JSON + QR).
- Session recording reference.
- KYC envelope reference.
- Payment receipt.
- Audit trail entry.

---
