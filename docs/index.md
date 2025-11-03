# Electronic Notarization Facility (ENF) Documentation

Welcome to the comprehensive documentation for building and operating an **Electronic Notarization Facility (ENF)** in the Philippines.

## Overview

This documentation covers the architecture, compliance requirements, and development planning for an ENF platform that meets the standards set by the **Rules on Electronic Notarization** (A.M. No. 24-10-14-SC) issued by the Supreme Court of the Philippines.

## What is an ENF?

An Electronic Notarization Facility is an accredited platform that enables notaries public to perform notarial acts electronically, supporting both:

- **In-Person Electronic Notarization (IEN)** – physical presence with electronic signatures
- **Remote Electronic Notarization (REN)** – video conferencing-based notarization

## Key Features

### 🔐 Security & Compliance
- **HyperVerge eKYC** integration for identity verification
- **TRUTH Engine** for document canonicalization and verification
- **Blockchain anchoring** for tamper-proof audit trails (optional)
- **10-year data retention** as mandated by regulations

### 🎯 Core Capabilities
- Multi-factor authentication (MFA)
- Liveness detection and facial recognition
- Real-time video conferencing for REN
- Secure document handling and storage
- Comprehensive audit logging

### 📋 Compliance
- Data Privacy Act of 2012 (RA 10173) compliance
- Supreme Court accreditation requirements
- Public Key Infrastructure (PKI) support
- Electronic Notary Administrator (ENA) reporting

## Documentation Structure

### [ENF Strategy](STRATEGY_OUTLINE_v3.md)
Comprehensive strategy for ENF accreditation, including technical requirements, business models, and implementation checklist.

### [Customer Journey](CUSTOMER_JOURNEY_MAP.md)
User experience flows for both on-demand and account-based notarization services.

### [Notarization Flow](DUAL-PATH_CANONICALIZATION_AND_NOTARIZATION_FLOW.md)
Technical workflow for dual-path document canonicalization and notarization processing.

## Getting Started

To understand the ENF platform:

1. Review the [ENF Strategy](STRATEGY_OUTLINE_v3.md) for business and technical requirements
2. Explore the [Customer Journey](CUSTOMER_JOURNEY_MAP.md) to understand user flows
3. Study the [Notarization Flow](DUAL-PATH_CANONICALIZATION_AND_NOTARIZATION_FLOW.md) for technical implementation

## Technology Stack

- **Identity Verification**: HyperVerge eKYC + Liveness Detection
- **Document Processing**: TRUTH Engine canonicalization
- **Signatures**: PKI-based digital signatures
- **Storage**: Encrypted, compliant document repository
- **Audit**: Immutable logging and optional blockchain anchoring
- **Video**: WebRTC/Zoom/Jitsi for remote notarization

## Target Users

- Overseas Filipino Workers (OFWs)
- Law firms and legal professionals
- Real estate agencies
- Government agencies (LRA, DFA, POEA, etc.)
- Banks and financial institutions
- Corporate HR departments

## About

Developed by **3neti R&D OPC**

This platform aims to modernize notarization services in the Philippines through secure, compliant, and innovative technology solutions.
