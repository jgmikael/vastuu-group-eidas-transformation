# Vastuu Group - eIDAS 2.0 Transformation

**Digital Identity Infrastructure for Trusted Worker Services**

This repository documents the transformation of Vastuu Group Oy's "Luotettava työntekijä" (Trusted Worker) service into an eIDAS 2.0-compatible infrastructure based on EU Digital Identity Wallet (EUDI) and EU Business Wallet (EBW) standards.

---

## 📋 Contents

1. **[Process Description](PROCESS_DESCRIPTION.md)** - Detailed transformation process and AI agent automation
2. **[Technical Architecture](TECHNICAL_ARCHITECTURE.md)** - Complete system architecture and design
3. **[Semantic Models](semantic/)** - OWL, SHACL, and JSON-LD vocabulary definitions
4. **[Implementation Roadmap](ROADMAP.md)** - Phased transformation plan
5. **[Use Cases](use-cases/)** - Concrete scenarios and examples

---

## 🎯 Executive Summary

### Current State (Baseline)
Vastuu Group's "Luotettava työntekijä" service currently operates as:
- Centralized registry and API service
- Worker and employer data aggregator
- Verification checkpoint (client, main contractor)
- Document-based (PDF, API interfaces, reports)

**Core Problem:** 
- Data siloed within service, not in user's possession
- No cross-border support
- Doesn't support "once-only" principle

### Target State (Wallet-Native Infrastructure)
Transform into a model where:
- **Worker** = identity holder (PID in EUDI Wallet)
- **Company** = EBW Wallet holder (EBWOID)
- **Vastuu Group** = attribute issuer + trust operator

---

## 🏗️ Key Transformation Pillars

### 1. Identity Layer
- **Natural persons:** PID (Person Identification Data) in EUDI Wallet
- **Legal entities:** EBWOID (EU Business Wallet Owner ID)
- Authentication no longer based on service-specific credentials

### 2. Attestation Layer (Core Change)
Current "Trusted Worker" data transformed into **W3C Verifiable Credentials**:

| Current Data | VC Attestation |
|--------------|----------------|
| Tax number | `WorkerTaxNumberCredential` |
| Employment relationship | `EmploymentRelationshipCredential` |
| A1 certificate | `SocialSecurityCoverageCredential` |
| Qualifications | `ProfessionalQualificationCredential` |
| Compliance data | `ComplianceStatusCredential` |

**Format:**
- JSON-LD
- Semantic model (OWL + SHACL)
- Signature (eIDAS QES/QEAA)

### 3. Wallet Layer
- **Worker stores credentials in:** EUDI Wallet
- **Company stores in:** EBW Wallet (organization-level attributes)

👉 **Vastuu Group transforms from "data warehouse" to:**
- Trust Anchor + Issuer

### 4. Verification Layer
- At construction site or with client: QR / API / wallet-to-wallet
- Presentation exchange
- Selective disclosure (only necessary information)

### 5. Semantic Layer (Critical)
All attestations modeled using:
- **SKOS** → concepts (e.g., worker status)
- **OWL** → data model (NCBV / EBW Vocabulary)
- **SHACL** → validation
- **JSON-LD** → serialization

---

## 🤖 AI Agent Automation

This repository also documents how **worker and employer AI agents** can automate job search and hiring processes using:
- EU Digital Wallets
- Verifiable Credentials
- Process evidence (application, consent, offer, contract, onboarding, work commencement)

### Core Concept
> **Job search and hiring transform from document shuffling into evidence-based, agent-orchestrated processes where parties exchange only necessary verified facts, not scattered PDFs or manually verified notices.**

### Agent Roles

**Worker's AI Agent:**
1. Builds verified profile from wallet credentials
2. Searches suitable job opportunities
3. Composes minimal-necessity applications
4. Manages consents and approvals
5. Stores new process evidence

**Employer's AI Agent:**
1. Models role requirements
2. Verifies applicant credentials
3. Requests precise additional evidence
4. Generates offers and contracts
5. Automates onboarding, compliance checks, and access rights

---

## 📊 Benefits

### Business Benefits
- Eliminates duplicate reporting
- Real-time compliance
- International usability

### Technical Benefits
- Shift from documents → data
- API → event-driven / wallet-driven
- Centralized → decentralized

### Strategic Benefits
- Position Vastuu Group as EU-level compliance identity provider
- Enable cross-border labor mobility
- Reduce administrative burden by 60-80%

---

## 🛣️ Transformation Path

### Phase 1 — Semantic Modeling
- Map current attributes → OWL + SHACL
- Harmonize with EU Core Vocabularies and NCBV/EBW Vocabulary

### Phase 2 — VC Model Definition
- Create credential schemas
- Define JSON-LD contexts

### Phase 3 — Issuer Service
- Build VC issuance API
- Integrate with:
  - Tax Administration (Verohallinto)
  - Income Register (Tulorekisteri)
  - Employers

### Phase 4 — Wallet Integration
- EUDI Wallet SDK
- EBW Wallet APIs

### Phase 5 — Verification Services
- Construction site application
- Client interfaces

### Phase 6 — EU Interoperability
- eIDAS trust framework
- Cross-border acceptance

---

## 🔐 Trust & Governance

### Vastuu Group's New Role

**From:**
- Data collector
- Registry keeper
- Verifier

**To:**
1. **Issuer** (Attribute issuer)
   - Validates sources (Tax Authority, Kela, employer)
   - Issues VCs

2. **Trust Service Provider** (eIDAS 2.0)
   - Possible QTSP role
   - Electronic signature / seal

3. **Data Space Operator**
   - Participates in Business Information Data Space
   - Provides semantic models and validation services

---

## 🌍 European Context

This transformation aligns with:
- **eIDAS 2.0 Regulation** (EU 2024/1183)
- **EU Digital Identity Wallet (EUDI)**
- **EU Business Wallet (EBW)**
- **European Interoperability Framework (EIF)**
- **Once-Only Technical System (OOTS)**

---

## 📁 Repository Structure

```
/
├── README.md                          # This file
├── PROCESS_DESCRIPTION.md             # Detailed process flows
├── TECHNICAL_ARCHITECTURE.md          # System architecture
├── ROADMAP.md                         # Implementation timeline
├── semantic/                          # Semantic models
│   ├── vocabulary.ttl                 # OWL ontology
│   ├── shapes.ttl                     # SHACL shapes
│   ├── contexts/                      # JSON-LD contexts
│   └── examples/                      # Sample credentials
├── use-cases/                         # Concrete scenarios
│   ├── construction-worker.md
│   ├── cross-border-posting.md
│   └── multi-employer.md
├── architecture/                      # Architecture diagrams
│   ├── current-state.md
│   ├── target-state.md
│   └── transition.md
└── docs/                              # Additional documentation
    ├── governance.md
    ├── privacy.md
    └── security.md
```

---

## 🚀 Quick Start

1. **Understand the vision:** Read [PROCESS_DESCRIPTION.md](PROCESS_DESCRIPTION.md)
2. **Review the architecture:** See [TECHNICAL_ARCHITECTURE.md](TECHNICAL_ARCHITECTURE.md)
3. **Explore use cases:** Browse [use-cases/](use-cases/)
4. **Check semantic models:** Examine [semantic/](semantic/)

---

## 📖 Key Documents

### Finnish Source Materials
This repository is based on two comprehensive Finnish documents:
1. **Vastuu Group Transformation** - eIDAS 2.0 infrastructure transformation
2. **AI Agent Automation** - Worker and employer agent processes

### Technical Standards
- [W3C Verifiable Credentials Data Model 2.0](https://www.w3.org/TR/vc-data-model-2.0/)
- [W3C Decentralized Identifiers (DIDs)](https://www.w3.org/TR/did-core/)
- [eIDAS 2.0 Regulation (EU 2024/1183)](https://eur-lex.europa.eu/eli/reg/2024/1183)
- [EU Core Vocabularies](https://joinup.ec.europa.eu/collection/semantic-interoperability-community-semic/core-vocabularies)

---

## 👥 Target Audience

- **Vastuu Group stakeholders** - Strategic decision makers
- **Technical architects** - System designers and developers
- **Compliance officers** - Regulatory and legal teams
- **EU wallet providers** - Integration partners
- **Construction industry** - Primary user domain
- **Policy makers** - Digital identity ecosystem

---

## 📞 Contact & Contribution

**Maintainer:** Mikael  
**Repository:** https://github.com/jgmikael/vastuu-group-eidas-transformation  
**License:** MIT

For questions, suggestions, or collaboration inquiries, please open an issue or pull request.

---

**Built with 🔧 for the future of trusted digital labor markets**  
*Transforming compliance from burden to capability*
