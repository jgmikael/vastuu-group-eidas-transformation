# Technical Architecture - Vastuu Group eIDAS 2.0 Transformation

**Complete system architecture for wallet-native trusted worker infrastructure**

---

## Table of Contents

1. [Architecture Overview](#1-architecture-overview)
2. [Architecture Layers](#2-architecture-layers)
3. [Component Architecture](#3-component-architecture)
4. [Data Models & Semantics](#4-data-models--semantics)
5. [Integration Architecture](#5-integration-architecture)
6. [Security Architecture](#6-security-architecture)
7. [Deployment Architecture](#7-deployment-architecture)
8. [AI Agent Architecture](#8-ai-agent-architecture)

---

## 1. Architecture Overview

### 1.1 Transformation Vision

```
FROM: Centralized Data Warehouse
TO:   Decentralized Identity & Attestation Infrastructure
```

**Current State:**
```
┌────────────────────────────────────────────────────┐
│         Vastuu Group Central System                │
│  ┌──────────────┐  ┌──────────────┐               │
│  │   Worker     │  │   Employer   │               │
│  │   Database   │  │   Database   │               │
│  └──────────────┘  └──────────────┘               │
│  ┌──────────────┐  ┌──────────────┐               │
│  │ Verification │  │   Reporting  │               │
│  │   Engine     │  │   Engine     │               │
│  └──────────────┘  └──────────────┘               │
│           │                  │                     │
│           └────────┬─────────┘                     │
│                    ▼                               │
│            PDF / API / Web                         │
└────────────────────────────────────────────────────┘
```

**Target State:**
```
┌─────────────────┐         ┌─────────────────┐
│  Public         │         │  Trust          │
│  Registers      │─────────│  Services       │
│  (Tax, Kela)    │         │  (eIDAS QTSP)   │
└────────┬────────┘         └────────┬────────┘
         │                           │
         ▼                           ▼
┌──────────────────────────────────────────────┐
│      Vastuu Group Issuer & Verifier          │
│  ┌────────────┐  ┌────────────┐             │
│  │ Credential │  │  Semantic  │             │
│  │  Issuance  │  │  Validator │             │
│  └────────────┘  └────────────┘             │
│  ┌────────────┐  ┌────────────┐             │
│  │   Trust    │  │   Policy   │             │
│  │  Anchor    │  │   Engine   │             │
│  └────────────┘  └────────────┘             │
└──────────┬────────────────┬──────────────────┘
           │                │
           ▼                ▼
    ┌──────────┐      ┌──────────┐
    │   EUDI   │      │   EBW    │
    │  Wallet  │      │  Wallet  │
    │ (Worker) │      │(Employer)│
    └──────────┘      └──────────┘
           │                │
           └────────┬───────┘
                    ▼
           ┌──────────────┐
           │  Verifiers   │
           │ (Sites, etc) │
           └──────────────┘
```

---

### 1.2 Six-Layer Architecture Model

Based on EU Business Wallet reference architecture:

```
┌─────────────────────────────────────────────────────────┐
│  Layer 6: Governance & Audit                            │
│  - Policy enforcement                                   │
│  - Audit trails                                         │
│  - Revocation management                                │
│  - Conformance testing                                  │
└─────────────────────────────────────────────────────────┘
┌─────────────────────────────────────────────────────────┐
│  Layer 5: Trust Services & Delivery                     │
│  - eIDAS signatures/seals (QES/QEAA)                   │
│  - Timestamps                                           │
│  - QERDS (delivery evidence)                           │
│  - eDelivery/AS4                                       │
└─────────────────────────────────────────────────────────┘
┌─────────────────────────────────────────────────────────┐
│  Layer 4: Interaction & Exchange Protocols              │
│  - OpenID4VCI (credential issuance)                    │
│  - OpenID4VP (credential presentation)                 │
│  - DIDComm (agent-to-agent)                           │
│  - REST APIs                                           │
└─────────────────────────────────────────────────────────┘
┌─────────────────────────────────────────────────────────┐
│  Layer 3: Exchange Objects & Syntax Bindings            │
│  - W3C VC 2.0 (JSON-LD)                                │
│  - SD-JWT (selective disclosure)                       │
│  - JSON/XML (legacy)                                   │
│  - PDF renditions                                      │
└─────────────────────────────────────────────────────────┘
┌─────────────────────────────────────────────────────────┐
│  Layer 2: Semantic Application Profiles                 │
│  - SHACL shapes (validation)                           │
│  - Code lists (SKOS)                                   │
│  - Profile rules                                       │
│  - Mapping artifacts                                   │
└─────────────────────────────────────────────────────────┘
┌─────────────────────────────────────────────────────────┐
│  Layer 1: Canonical Semantic Foundation                 │
│  - RDF/RDFS/OWL ontologies                             │
│  - SKOS concept schemes                                │
│  - Persistent URIs                                     │
│  - EU Core Vocabularies alignment                      │
└─────────────────────────────────────────────────────────┘
```

**Key Principle:**
> **Layers 1-2 are stable and mandatory. Layers 3-6 are replaceable bindings.**

---

## 2. Architecture Layers

### 2.1 Layer 1: Canonical Semantic Foundation

**Purpose:** Define shared meaning of business concepts

**Components:**

#### **Vastuu Group Vocabulary (vg:)**

```turtle
@prefix vg: <https://vastuu.fi/vocabulary/> .
@prefix ebw: <https://ebw.europa.eu/vocabulary/> .
@prefix foaf: <http://xmlns.com/foaf/0.1/> .
@prefix org: <http://www.w3.org/ns/org#> .
@prefix skos: <http://www.w3.org/2004/02/skos/core#> .
@prefix owl: <http://www.w3.org/2002/07/owl#> .

# Core Classes

vg:Worker a owl:Class ;
    rdfs:subClassOf ebw:NaturalPersonRepresentative ;
    rdfs:label "Construction Worker"@en, "Työntekijä"@fi ;
    rdfs:comment "A natural person employed in construction sector" .

vg:ConstructionEmployer a owl:Class ;
    rdfs:subClassOf ebw:EconomicOperator ;
    rdfs:label "Construction Employer"@en, "Rakennusalan työnantaja"@fi .

vg:WorkPermit a owl:Class ;
    rdfs:label "Work Permit"@en, "Työlupa"@fi ;
    rdfs:comment "Authorization to work in a specific country" .

vg:SafetyTraining a owl:Class ;
    rdfs:label "Safety Training"@en, "Työturvallisuuskoulutus"@fi .

vg:ProfessionalQualification a owl:Class ;
    rdfs:label "Professional Qualification"@en, "Ammattipätevyys"@fi .

vg:ComplianceStatus a owl:Class ;
    rdfs:label "Compliance Status"@en, "Vaatimustenmukaisuus"@fi .

# Object Properties

vg:hasWorkPermit a owl:ObjectProperty ;
    rdfs:domain vg:Worker ;
    rdfs:range vg:WorkPermit ;
    rdfs:label "has work permit"@en .

vg:hasSafetyTraining a owl:ObjectProperty ;
    rdfs:domain vg:Worker ;
    rdfs:range vg:SafetyTraining ;
    rdfs:label "has safety training"@en .

vg:hasQualification a owl:ObjectProperty ;
    rdfs:domain vg:Worker ;
    rdfs:range vg:ProfessionalQualification ;
    rdfs:label "has qualification"@en .

vg:employs a owl:ObjectProperty ;
    rdfs:domain vg:ConstructionEmployer ;
    rdfs:range vg:Worker ;
    rdfs:label "employs"@en .

vg:hasComplianceStatus a owl:ObjectProperty ;
    rdfs:domain vg:ConstructionEmployer ;
    rdfs:range vg:ComplianceStatus ;
    rdfs:label "has compliance status"@en .

# Datatype Properties

vg:taxNumber a owl:DatatypeProperty ;
    rdfs:domain vg:Worker ;
    rdfs:range xsd:string ;
    rdfs:label "tax number"@en ;
    skos:definition "Finnish tax identification number" .

vg:employerID a owl:DatatypeProperty ;
    rdfs:domain vg:ConstructionEmployer ;
    rdfs:range xsd:string ;
    rdfs:label "employer ID"@en .

vg:validFrom a owl:DatatypeProperty ;
    rdfs:range xsd:dateTime ;
    rdfs:label "valid from"@en .

vg:validUntil a owl:DatatypeProperty ;
    rdfs:range xsd:dateTime ;
    rdfs:label "valid until"@en .

vg:qualificationType a owl:DatatypeProperty ;
    rdfs:domain vg:ProfessionalQualification ;
    rdfs:range xsd:string ;
    rdfs:label "qualification type"@en .

vg:courseType a owl:DatatypeProperty ;
    rdfs:domain vg:SafetyTraining ;
    rdfs:range xsd:string ;
    rdfs:label "course type"@en .
```

**Alignment with EU Vocabularies:**

```turtle
# Link to EU Core Person
vg:Worker rdfs:subClassOf cv:Person .

# Link to EU Core Business
vg:ConstructionEmployer rdfs:subClassOf cv:LegalEntity .

# Link to EBW Vocabulary
vg:Worker rdfs:subClassOf ebw:NaturalPersonRepresentative .
vg:ConstructionEmployer rdfs:subClassOf ebw:EconomicOperator .

# Link to Professional Qualifications Directive
vg:ProfessionalQualification owl:equivalentClass pqd:Qualification .
```

---

### 2.2 Layer 2: Semantic Application Profiles

**Purpose:** Define validation constraints and business rules

**SHACL Shapes:**

```turtle
@prefix sh: <http://www.w3.org/ns/shacl#> .
@prefix vg: <https://vastuu.fi/vocabulary/> .

# Worker Shape
vg:WorkerShape a sh:NodeShape ;
    sh:targetClass vg:Worker ;
    sh:property [
        sh:path vg:taxNumber ;
        sh:minCount 1 ;
        sh:maxCount 1 ;
        sh:pattern "^FI[0-9]{8}$" ;
        sh:message "Tax number must be Finnish format (FI + 8 digits)" ;
    ] ;
    sh:property [
        sh:path vg:hasWorkPermit ;
        sh:minCount 1 ;
        sh:message "Worker must have at least one valid work permit" ;
    ] ;
    sh:property [
        sh:path vg:hasSafetyTraining ;
        sh:minCount 1 ;
        sh:message "Worker must have completed safety training" ;
    ] .

# Work Permit Shape
vg:WorkPermitShape a sh:NodeShape ;
    sh:targetClass vg:WorkPermit ;
    sh:property [
        sh:path vg:validFrom ;
        sh:minCount 1 ;
        sh:datatype xsd:dateTime ;
    ] ;
    sh:property [
        sh:path vg:validUntil ;
        sh:minCount 1 ;
        sh:datatype xsd:dateTime ;
    ] ;
    sh:sparql [
        sh:message "Work permit must be currently valid" ;
        sh:select """
            SELECT $this WHERE {
                $this vg:validFrom ?from ;
                      vg:validUntil ?until .
                FILTER(?from <= NOW() && NOW() <= ?until)
            }
        """ ;
    ] .

# Safety Training Shape
vg:SafetyTrainingShape a sh:NodeShape ;
    sh:targetClass vg:SafetyTraining ;
    sh:property [
        sh:path vg:courseType ;
        sh:in ( "ConstructionSafety" "ElectricalSafety" "HeightWork" ) ;
        sh:message "Course type must be one of the approved types" ;
    ] ;
    sh:property [
        sh:path vg:validUntil ;
        sh:minCount 1 ;
    ] .

# Employer Compliance Shape
vg:EmployerComplianceShape a sh:NodeShape ;
    sh:targetClass vg:ConstructionEmployer ;
    sh:property [
        sh:path vg:hasComplianceStatus ;
        sh:minCount 1 ;
        sh:message "Employer must have current compliance status" ;
    ] ;
    sh:property [
        sh:path ebw:vatNumber ;
        sh:pattern "^FI[0-9]{8}$" ;
    ] ;
    sh:property [
        sh:path vg:employerID ;
        sh:minCount 1 ;
        sh:maxCount 1 ;
    ] .
```

**SKOS Concept Schemes:**

```turtle
# Worker Status Taxonomy
vg:WorkerStatusScheme a skos:ConceptScheme ;
    skos:prefLabel "Worker Status Types"@en .

vg:Active a skos:Concept ;
    skos:inScheme vg:WorkerStatusScheme ;
    skos:prefLabel "Active"@en, "Aktiivinen"@fi ;
    skos:notation "ACTIVE" .

vg:Inactive a skos:Concept ;
    skos:inScheme vg:WorkerStatusScheme ;
    skos:prefLabel "Inactive"@en, "Passiivinen"@fi ;
    skos:notation "INACTIVE" .

vg:Suspended a skos:Concept ;
    skos:inScheme vg:WorkerStatusScheme ;
    skos:prefLabel "Suspended"@en, "Jäädytetty"@fi ;
    skos:notation "SUSPENDED" .

# Compliance Level Taxonomy
vg:ComplianceLevelScheme a skos:ConceptScheme ;
    skos:prefLabel "Compliance Levels"@en .

vg:FullCompliance a skos:Concept ;
    skos:inScheme vg:ComplianceLevelScheme ;
    skos:prefLabel "Full Compliance"@en ;
    skos:notation "FULL" .

vg:MinorConcerns a skos:Concept ;
    skos:inScheme vg:ComplianceLevelScheme ;
    skos:prefLabel "Minor Concerns"@en ;
    skos:notation "MINOR" .

vg:MajorConcerns a skos:Concept ;
    skos:inScheme vg:ComplianceLevelScheme ;
    skos:prefLabel "Major Concerns"@en ;
    skos:notation "MAJOR" .

vg:NonCompliant a skos:Concept ;
    skos:inScheme vg:ComplianceLevelScheme ;
    skos:prefLabel "Non-Compliant"@en ;
    skos:notation "NON_COMPLIANT" .
```

---

### 2.3 Layer 3: Exchange Objects & Syntax Bindings

**W3C Verifiable Credential Structure:**

```json
{
  "@context": [
    "https://www.w3.org/ns/credentials/v2",
    "https://vastuu.fi/contexts/worker/v1",
    "https://ebw.europa.eu/contexts/v1"
  ],
  "id": "urn:uuid:credential-identifier",
  "type": ["VerifiableCredential", "WorkerTaxNumberCredential"],
  "issuer": {
    "id": "did:ebsi:zVastuuGroupIssuer",
    "name": "Vastuu Group Oy",
    "type": "TrustAnchor"
  },
  "validFrom": "2024-01-01T00:00:00Z",
  "validUntil": "2024-12-31T23:59:59Z",
  "credentialSubject": {
    "id": "did:ebsi:zWorkerIdentifier",
    "type": "Worker",
    "taxNumber": "FI12345678",
    "taxNumberStatus": "Active",
    "issuedBy": "Finnish Tax Administration"
  },
  "credentialSchema": {
    "id": "https://vastuu.fi/schemas/tax-number-v1.json",
    "type": "JsonSchema"
  },
  "proof": {
    "type": "DataIntegrityProof",
    "cryptosuite": "ecdsa-rdfc-2019",
    "created": "2024-01-01T10:00:00Z",
    "proofPurpose": "assertionMethod",
    "verificationMethod": "did:ebsi:zVastuuGroupIssuer#key-1",
    "proofValue": "z3FXQwLxCqMN8hF7..."
  }
}
```

**JSON-LD Context:**

```json
{
  "@context": {
    "@version": 1.1,
    "@protected": true,
    "vg": "https://vastuu.fi/vocabulary/",
    "ebw": "https://ebw.europa.eu/vocabulary/",
    "xsd": "http://www.w3.org/2001/XMLSchema#",
    
    "Worker": "vg:Worker",
    "ConstructionEmployer": "vg:ConstructionEmployer",
    "WorkPermit": "vg:WorkPermit",
    "SafetyTraining": "vg:SafetyTraining",
    "ProfessionalQualification": "vg:ProfessionalQualification",
    
    "taxNumber": {
      "@id": "vg:taxNumber",
      "@type": "xsd:string"
    },
    "taxNumberStatus": {
      "@id": "vg:taxNumberStatus",
      "@type": "@vocab"
    },
    "hasWorkPermit": {
      "@id": "vg:hasWorkPermit",
      "@type": "@id"
    },
    "hasSafetyTraining": {
      "@id": "vg:hasSafetyTraining",
      "@type": "@id"
    },
    "validFrom": {
      "@id": "vg:validFrom",
      "@type": "xsd:dateTime"
    },
    "validUntil": {
      "@id": "vg:validUntil",
      "@type": "xsd:dateTime"
    }
  }
}
```

---

### 2.4 Layer 4: Interaction & Exchange Protocols

**OpenID for Verifiable Credential Issuance (OpenID4VCI):**

```
Credential Issuance Flow:
┌──────────┐                                    ┌──────────┐
│  Wallet  │                                    │  Issuer  │
└────┬─────┘                                    └────┬─────┘
     │                                               │
     │ 1. Discover issuer metadata                  │
     ├──────────────────────────────────────────────►│
     │   GET /.well-known/openid-credential-issuer  │
     │◄──────────────────────────────────────────────┤
     │   200 OK: issuer metadata                    │
     │                                               │
     │ 2. Initiate issuance                         │
     ├──────────────────────────────────────────────►│
     │   GET /credential-offer?...                  │
     │◄──────────────────────────────────────────────┤
     │   200 OK: credential offer                   │
     │                                               │
     │ 3. Authorization request                     │
     ├──────────────────────────────────────────────►│
     │   POST /authorize                            │
     │   {client_id, scope, ...}                    │
     │◄──────────────────────────────────────────────┤
     │   302 Redirect: authorization code           │
     │                                               │
     │ 4. Token request                             │
     ├──────────────────────────────────────────────►│
     │   POST /token                                │
     │   {grant_type, code, ...}                    │
     │◄──────────────────────────────────────────────┤
     │   200 OK: access_token                       │
     │                                               │
     │ 5. Credential request                        │
     ├──────────────────────────────────────────────►│
     │   POST /credential                           │
     │   Authorization: Bearer <token>              │
     │   {format, proof, ...}                       │
     │◄──────────────────────────────────────────────┤
     │   200 OK: credential (signed VC)             │
     │                                               │
```

**OpenID for Verifiable Presentations (OpenID4VP):**

```
Presentation Flow:
┌──────────┐                              ┌──────────┐
│  Wallet  │                              │ Verifier │
└────┬─────┘                              └────┬─────┘
     │                                         │
     │ 1. Presentation request                │
     │◄────────────────────────────────────────┤
     │   GET /presentation                    │
     │   ?presentation_definition=...         │
     │                                         │
     │ 2. User consent                        │
     ├─────────► [User reviews & approves]    │
     │                                         │
     │ 3. Create presentation                 │
     ├─────────► [Build VP with selected VCs] │
     │                                         │
     │ 4. Submit presentation                 │
     ├────────────────────────────────────────►│
     │   POST /presentation/submission        │
     │   {vp_token, presentation_submission}  │
     │                                         │
     │                                         │ 5. Verify
     │                                         ├──► Check signatures
     │                                         ├──► Validate SHACL
     │                                         └──► Check revocation
     │                                         │
     │◄────────────────────────────────────────┤
     │   200 OK: verification result          │
     │                                         │
```

**DIDComm for Agent-to-Agent Communication:**

```json
{
  "type": "https://didcomm.org/credential-request/1.0/request",
  "id": "abc-123-xyz",
  "from": "did:example:employer-agent",
  "to": ["did:example:worker-agent"],
  "created_time": 1704110400,
  "body": {
    "requestType": "JobApplication",
    "jobId": "job-2024-001",
    "requiredCredentials": [
      {
        "type": "TaxNumberCredential",
        "constraints": {
          "status": "Active"
        }
      },
      {
        "type": "SafetyTrainingCredential",
        "constraints": {
          "validUntil": {"$gt": "2024-12-31"}
        }
      }
    ]
  }
}
```

---

### 2.5 Layer 5: Trust Services & Delivery

**eIDAS Qualified Electronic Signature:**

```
Signing Process:
┌──────────────┐
│  Credential  │
│  (unsigned)  │
└──────┬───────┘
       │
       ▼
┌────────────────────────┐
│  Canonical Form        │
│  (RDF Dataset Canon.)  │
└──────┬─────────────────┘
       │
       ▼
┌────────────────────────┐
│  Hash (SHA-256)        │
└──────┬─────────────────┘
       │
       ▼
┌────────────────────────┐
│  eIDAS QTSP            │
│  Sign with QES         │
│  (ECDSA P-256)         │
└──────┬─────────────────┘
       │
       ▼
┌────────────────────────┐
│  Attach Proof          │
│  {                     │
│    type: DataIntegrity │
│    proofValue: z3FX... │
│    verificationMethod  │
│  }                     │
└──────┬─────────────────┘
       │
       ▼
┌────────────────────────┐
│  Signed Credential     │
└────────────────────────┘
```

**Trust Chain Verification:**

```
Verification:
1. Extract proof from credential
2. Resolve verificationMethod DID
   └─► GET did:ebsi:zVastuuGroupIssuer
       └─► Returns DID Document with public key
3. Verify signature
   ├─► Compute hash of canonical credential
   ├─► Verify ECDSA signature with public key
   └─► Result: VALID or INVALID
4. Check trust chain
   ├─► Vastuu Group issuer cert
   ├─► Intermediate CA cert
   ├─► Root CA cert (EU Trust List)
   └─► All valid in eIDAS framework
5. Check revocation status
   └─► Query revocation registry
       └─► Result: NOT_REVOKED
```

---

### 2.6 Layer 6: Governance & Audit

**Revocation Registry:**

```
Revocation Mechanism:
┌─────────────────┐
│  Credential ID  │
└────────┬────────┘
         │
         ▼
┌──────────────────────────┐
│  Revocation Registry     │
│  ┌────────────────────┐  │
│  │  Status List 2021  │  │
│  │  Bitstring Index   │  │
│  │  [0,0,0,1,0,0...] │  │
│  └────────────────────┘  │
└──────────────────────────┘
         │
         ▼
Query: /revocation-status/credentialId
Response:
{
  "status": "REVOKED",
  "revokedAt": "2024-06-15T10:00:00Z",
  "reason": "Employment terminated"
}
```

**Audit Trail:**

```sql
CREATE TABLE audit_log (
    id UUID PRIMARY KEY,
    timestamp TIMESTAMPTZ NOT NULL,
    event_type VARCHAR(50) NOT NULL,
    actor_did VARCHAR(200),
    subject_did VARCHAR(200),
    credential_id VARCHAR(200),
    action VARCHAR(50),
    metadata JSONB,
    signature TEXT
);

-- Example entries:
INSERT INTO audit_log VALUES
('uuid-1', '2024-01-15 10:00:00', 'CREDENTIAL_ISSUED', 
 'did:ebsi:zVastuuGroup', 'did:ebsi:zWorker123',
 'urn:uuid:cred-001', 'ISSUE',
 '{"type": "TaxNumberCredential"}', 'sig...'),

('uuid-2', '2024-01-20 14:30:00', 'CREDENTIAL_VERIFIED',
 'did:ebw:zConstructionSite', 'did:ebsi:zWorker123',
 'urn:uuid:cred-001', 'VERIFY',
 '{"result": "VALID", "purpose": "SiteAccess"}', 'sig...');
```

---

## 3. Component Architecture

### 3.1 Issuer Service Architecture

```
┌───────────────────────────────────────────────────────────┐
│                    Issuer Service                         │
│  ┌─────────────────────────────────────────────────────┐  │
│  │              API Gateway (Kong / Nginx)             │  │
│  │  - Rate limiting                                    │  │
│  │  - Authentication                                   │  │
│  │  - Request routing                                  │  │
│  └────────────┬────────────────────────────────────────┘  │
│               │                                           │
│  ┌────────────▼────────────┐  ┌───────────────────────┐  │
│  │  Issuance Service       │  │  Verification Service │  │
│  │  ┌──────────────────┐   │  │  ┌────────────────┐  │  │
│  │  │ Credential       │   │  │  │ Signature      │  │  │
│  │  │ Builder          │   │  │  │ Validator      │  │  │
│  │  └──────────────────┘   │  │  └────────────────┘  │  │
│  │  ┌──────────────────┐   │  │  ┌────────────────┐  │  │
│  │  │ Policy Engine    │   │  │  │ SHACL          │  │  │
│  │  │ (who gets what)  │   │  │  │ Validator      │  │  │
│  │  └──────────────────┘   │  │  └────────────────┘  │  │
│  │  ┌──────────────────┐   │  │  ┌────────────────┐  │  │
│  │  │ Template         │   │  │  │ Revocation     │  │  │
│  │  │ Manager          │   │  │  │ Checker        │  │  │
│  │  └──────────────────┘   │  │  └────────────────┘  │  │
│  └─────────────────────────┘  └───────────────────────┘  │
│               │                         │                 │
│  ┌────────────▼─────────────────────────▼──────────────┐  │
│  │              Signing Service                        │  │
│  │  ┌──────────────┐  ┌─────────────────────────────┐  │  │
│  │  │ HSM          │  │  eIDAS QTSP Integration     │  │  │
│  │  │ (Key Store)  │  │  (Remote Signing Service)   │  │  │
│  │  └──────────────┘  └─────────────────────────────┘  │  │
│  └───────────────────────────────────────────────────────┘│
│               │                                           │
│  ┌────────────▼────────────┐  ┌───────────────────────┐  │
│  │  DID Registry           │  │  Revocation Registry  │  │
│  │  (DID Documents)        │  │  (Status List 2021)   │  │
│  └─────────────────────────┘  └───────────────────────┘  │
│               │                         │                 │
│  ┌────────────▼─────────────────────────▼──────────────┐  │
│  │              Data Layer                             │  │
│  │  ┌──────────────┐  ┌──────────────┐  ┌──────────┐  │  │
│  │  │ PostgreSQL   │  │ Redis        │  │ MongoDB  │  │  │
│  │  │ (audit log)  │  │ (cache)      │  │ (docs)   │  │  │
│  │  └──────────────┘  └──────────────┘  └──────────┘  │  │
│  └───────────────────────────────────────────────────────┘│
└───────────────────────────────────────────────────────────┘
                        │
         ┌──────────────┴──────────────┐
         ▼                             ▼
┌─────────────────┐          ┌──────────────────┐
│ Source Systems  │          │  Wallet APIs     │
│ (Tax, Kela, etc)│          │  (OpenID4VCI)    │
└─────────────────┘          └──────────────────┘
```

**Technology Stack:**
- **Runtime:** Node.js 20+ with TypeScript
- **Web Framework:** Express.js / Fastify
- **Database:** PostgreSQL 15+ (primary), Redis (cache), MongoDB (documents)
- **DID Method:** `did:ebsi` (EBSI infrastructure)
- **Signing:** HSM (Hardware Security Module) + eIDAS QTSP integration
- **API Protocol:** OpenID4VCI, REST API
- **Deployment:** Docker + Kubernetes

---

### 3.2 Wallet Architecture

**EUDI Wallet (Worker):**

```
┌─────────────────────────────────────────────┐
│         EUDI Wallet (Mobile App)            │
│  ┌────────────────────────────────────────┐ │
│  │         User Interface Layer           │ │
│  │  ┌──────┐ ┌──────┐ ┌──────┐ ┌──────┐  │ │
│  │  │ Home │ │ Creds│ │ Scan │ │ Share│  │ │
│  │  └──────┘ └──────┘ └──────┘ └──────┘  │ │
│  └────────────────────────────────────────┘ │
│  ┌────────────────────────────────────────┐ │
│  │      Presentation Layer                │ │
│  │  - Selective disclosure UI             │ │
│  │  - Consent management                  │ │
│  │  - QR code generation                  │ │
│  └────────────────────────────────────────┘ │
│  ┌────────────────────────────────────────┐ │
│  │      Wallet Core                       │ │
│  │  ┌──────────────┐  ┌───────────────┐  │ │
│  │  │ Credential   │  │ DID Manager   │  │ │
│  │  │ Store        │  │               │  │ │
│  │  └──────────────┘  └───────────────┘  │ │
│  │  ┌──────────────┐  ┌───────────────┐  │ │
│  │  │ Verification │  │ Presentation  │  │ │
│  │  │ Engine       │  │ Engine        │  │ │
│  │  └──────────────┘  └───────────────┘  │ │
│  └────────────────────────────────────────┘ │
│  ┌────────────────────────────────────────┐ │
│  │      Security Layer                    │ │
│  │  - Biometric authentication            │ │
│  │  - Secure enclave / Keychain           │ │
│  │  - Encrypted storage                   │ │
│  └────────────────────────────────────────┘ │
│  ┌────────────────────────────────────────┐ │
│  │      Protocol Adapters                 │ │
│  │  ┌──────────────┐  ┌───────────────┐  │ │
│  │  │ OpenID4VCI   │  │ OpenID4VP     │  │ │
│  │  │ Client       │  │ Client        │  │ │
│  │  └──────────────┘  └───────────────┘  │ │
│  │  ┌──────────────┐  ┌───────────────┐  │ │
│  │  │ DIDComm      │  │ QR Scanner    │  │ │
│  │  │ Agent        │  │               │  │ │
│  │  └──────────────┘  └───────────────┘  │ │
│  └────────────────────────────────────────┘ │
└─────────────────────────────────────────────┘
```

**Platform:** React Native (iOS + Android)

**Security Features:**
- Biometric authentication (Face ID / Touch ID / Fingerprint)
- Encrypted credential storage (iOS Keychain / Android Keystore)
- PIN/password backup
- Secure communication (TLS 1.3+)

---

**EBW Wallet (Employer):**

```
┌─────────────────────────────────────────────┐
│       EBW Wallet (Web + Mobile)             │
│  ┌────────────────────────────────────────┐ │
│  │      Dashboard UI                      │ │
│  │  - Company credentials                 │ │
│  │  - Employee management                 │ │
│  │  - Compliance overview                 │ │
│  │  - Audit logs                          │ │
│  └────────────────────────────────────────┘ │
│  ┌────────────────────────────────────────┐ │
│  │      Business Logic                    │ │
│  │  ┌──────────────┐  ┌───────────────┐  │ │
│  │  │ Mandate      │  │ Multi-User    │  │ │
│  │  │ Manager      │  │ Access Control│  │ │
│  │  └──────────────┘  └───────────────┘  │ │
│  │  ┌──────────────┐  ┌───────────────┐  │ │
│  │  │ Bulk         │  │ API for       │  │ │
│  │  │ Operations   │  │ AI Agents     │  │ │
│  │  └──────────────┘  └───────────────┘  │ │
│  └────────────────────────────────────────┘ │
│  ┌────────────────────────────────────────┐ │
│  │      Wallet Core (same as EUDI)        │ │
│  │  - Credential storage                  │ │
│  │  - Verification                        │ │
│  │  - Presentation                        │ │
│  └────────────────────────────────────────┘ │
│  ┌────────────────────────────────────────┐ │
│  │      Integration Layer                 │ │
│  │  ┌──────────────┐  ┌───────────────┐  │ │
│  │  │ HR System    │  │ Accounting    │  │ │
│  │  │ Integration  │  │ Integration   │  │ │
│  │  └──────────────┘  └───────────────┘  │ │
│  └────────────────────────────────────────┘ │
└─────────────────────────────────────────────┘
```

**Platform:** Next.js (web) + React Native (mobile)

---

### 3.3 AI Agent Architecture

```
┌────────────────────────────────────────────────────┐
│              AI Agent Platform                     │
│  ┌──────────────────────────────────────────────┐  │
│  │           Agent Orchestration                │  │
│  │  ┌────────────┐  ┌────────────┐            │  │
│  │  │ Worker     │  │ Employer   │            │  │
│  │  │ Agent      │  │ Agent      │            │  │
│  │  └────────────┘  └────────────┘            │  │
│  └──────────────────────────────────────────────┘  │
│  ┌──────────────────────────────────────────────┐  │
│  │           Core Capabilities                  │  │
│  │  ┌────────────┐  ┌────────────┐  ┌────────┐│  │
│  │  │ Profile    │  │ Job        │  │ Policy ││  │
│  │  │ Builder    │  │ Matcher    │  │ Engine ││  │
│  │  └────────────┘  └────────────┘  └────────┘│  │
│  │  ┌────────────┐  ┌────────────┐  ┌────────┐│  │
│  │  │ Credential │  │ Contract   │  │ Workflow││ │
│  │  │ Verifier   │  │ Generator  │  │ Manager││  │
│  │  └────────────┘  └────────────┘  └────────┘│  │
│  └──────────────────────────────────────────────┘  │
│  ┌──────────────────────────────────────────────┐  │
│  │           AI/ML Layer                        │  │
│  │  ┌────────────┐  ┌────────────┐  ┌────────┐│  │
│  │  │ LLM        │  │ Semantic   │  │ NLP    ││  │
│  │  │ (GPT-4)    │  │ Reasoner   │  │ Parser ││  │
│  │  └────────────┘  └────────────┘  └────────┘│  │
│  └──────────────────────────────────────────────┘  │
│  ┌──────────────────────────────────────────────┐  │
│  │           Integration Layer                  │  │
│  │  ┌────────────┐  ┌────────────┐  ┌────────┐│  │
│  │  │ Wallet     │  │ Issuer     │  │ Verifier││ │
│  │  │ API        │  │ API        │  │ API    ││  │
│  │  └────────────┘  └────────────┘  └────────┘│  │
│  │  ┌────────────┐  ┌────────────┐            │  │
│  │  │ DIDComm    │  │ SPARQL     │            │  │
│  │  │ Protocol   │  │ Endpoint   │            │  │
│  │  └────────────┘  └────────────┘            │  │
│  └──────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────┘
```

**Agent Decision Process:**

```python
class WorkerAgent:
    def process_job_application(self, job_listing):
        # 1. Extract requirements from semantic model
        requirements = self.extract_requirements(job_listing)
        
        # 2. Inventory wallet credentials
        available_creds = self.wallet.list_credentials()
        
        # 3. Match requirements vs. credentials
        match_result = self.matcher.match(requirements, available_creds)
        
        # 4. Determine gaps
        gaps = match_result.missing_credentials
        
        if gaps:
            # 5a. Check if gaps are acquirable
            acquirable = self.check_acquisition_path(gaps)
            if acquirable:
                # Suggest to user
                self.suggest_credential_acquisition(gaps)
            else:
                # Not qualified, skip this job
                return {"status": "NOT_QUALIFIED", "gaps": gaps}
        
        # 6. Compose selective disclosure
        presentation = self.compose_presentation(
            requirements,
            available_creds,
            minimal=True  # Only required fields
        )
        
        # 7. Get user consent
        if self.get_user_consent(presentation, job_listing):
            # 8. Submit application
            response = self.submit_application(job_listing, presentation)
            
            # 9. Log evidence
            self.log_evidence({
                "type": "JobApplicationSubmitted",
                "jobId": job_listing.id,
                "timestamp": datetime.now(),
                "presentedCredentials": presentation.credential_ids
            })
            
            return {"status": "SUBMITTED", "response": response}
        else:
            return {"status": "USER_DECLINED"}
```

---

## 4. Data Models & Semantics

### 4.1 Core Data Entities

**Worker Data Model:**

```typescript
interface Worker {
  "@context": string[];
  "@type": "Worker";
  id: string; // DID
  personalIdentifier?: PersonalIdentifier;
  taxNumber?: TaxNumber;
  workPermits: WorkPermit[];
  qualifications: ProfessionalQualification[];
  safetyTraining: SafetyTraining[];
  employmentHistory: EmploymentRelationship[];
  socialSecurityCoverage?: SocialSecurityCoverage;
}

interface PersonalIdentifier {
  "@type": "PersonalIdentifier";
  country: string;
  identifierType: "PID" | "eID";
  credentialId: string;
}

interface TaxNumber {
  "@type": "TaxNumber";
  number: string;
  status: "Active" | "Inactive" | "Suspended";
  validFrom: string; // ISO 8601
  validUntil?: string;
  issuingAuthority: string;
  credentialId: string;
}

interface WorkPermit {
  "@type": "WorkPermit";
  country: string;
  permitType: "EU_Citizen" | "Work_Visa" | "Residence_Permit";
  validFrom: string;
  validUntil?: string;
  credentialId: string;
}

interface ProfessionalQualification {
  "@type": "ProfessionalQualification";
  qualificationType: string;
  level: string; // EQF level
  issuingBody: string;
  issuedDate: string;
  expiryDate?: string;
  credentialId: string;
}

interface SafetyTraining {
  "@type": "SafetyTraining";
  courseType: "ConstructionSafety" | "ElectricalSafety" | "HeightWork" | "other";
  provider: string;
  completionDate: string;
  expiryDate?: string;
  credentialId: string;
}

interface EmploymentRelationship {
  "@type": "EmploymentRelationship";
  employer: string; // DID or name
  position: string;
  startDate: string;
  endDate?: string;
  contractType: "Permanent" | "Fixed_Term" | "Temporary";
  credentialId?: string;
}

interface SocialSecurityCoverage {
  "@type": "SocialSecurityCoverage";
  coverageType: "A1_Certificate" | "E101_Form";
  coverageCountry: string;
  validFrom: string;
  validUntil: string;
  credentialId: string;
}
```

**Employer Data Model:**

```typescript
interface ConstructionEmployer {
  "@context": string[];
  "@type": "ConstructionEmployer";
  id: string; // DID
  legalName: string;
  registrationNumber: string;
  vatNumber: string;
  employerID: string;
  complianceStatus: ComplianceStatus;
  insuranceCoverage: InsuranceCoverage[];
  mandates: Mandate[];
}

interface ComplianceStatus {
  "@type": "ComplianceStatus";
  overallStatus: "FULL_COMPLIANCE" | "MINOR_CONCERNS" | "MAJOR_CONCERNS" | "NON_COMPLIANT";
  lastChecked: string;
  validUntil: string;
  checks: ComplianceCheck[];
  credentialId: string;
}

interface ComplianceCheck {
  checkType: string;
  status: "PASS" | "FAIL" | "WARNING";
  details?: string;
}

interface InsuranceCoverage {
  "@type": "InsuranceCoverage";
  policyType: "Liability" | "WorkersCompensation" | "Professional";
  policyNumber: string;
  provider: string;
  coverageAmount: number;
  currency: string;
  validFrom: string;
  validUntil: string;
  credentialId: string;
}

interface Mandate {
  "@type": "Mandate";
  grantedTo: string; // DID of person
  scope: "FullRepresentation" | "HRMatters" | "FinancialMatters" | "LimitedRepresentation";
  validFrom: string;
  validUntil?: string;
  credentialId: string;
}
```

---

### 4.2 Credential Templates

**Tax Number Credential Template:**

```json
{
  "@context": [
    "https://www.w3.org/ns/credentials/v2",
    "https://vastuu.fi/contexts/worker/v1"
  ],
  "id": "urn:uuid:{GENERATED}",
  "type": ["VerifiableCredential", "TaxNumberCredential"],
  "issuer": {
    "id": "did:ebsi:zVastuuGroupIssuer",
    "name": "Vastuu Group Oy"
  },
  "validFrom": "{CURRENT_DATE}",
  "validUntil": "{EXPIRY_DATE}",
  "credentialSubject": {
    "id": "{WORKER_DID}",
    "type": "Worker",
    "taxNumber": "{TAX_NUMBER}",
    "taxNumberStatus": "{STATUS}",
    "issuedBy": "Finnish Tax Administration"
  },
  "credentialSchema": {
    "id": "https://vastuu.fi/schemas/tax-number-v1.json",
    "type": "JsonSchema"
  },
  "proof": {
    "type": "DataIntegrityProof",
    "cryptosuite": "ecdsa-rdfc-2019",
    "created": "{CURRENT_TIMESTAMP}",
    "proofPurpose": "assertionMethod",
    "verificationMethod": "did:ebsi:zVastuuGroupIssuer#key-1",
    "proofValue": "{GENERATED_SIGNATURE}"
  }
}
```

---

## 5. Integration Architecture

### 5.1 Source System Integrations

**Tax Administration (Verohallinto) Integration:**

```
┌─────────────────┐              ┌────────────────────┐
│ Vastuu Issuer   │              │ Verohallinto API   │
└────────┬────────┘              └────────┬───────────┘
         │                                │
         │ 1. Authenticate (OAuth 2.0)   │
         ├───────────────────────────────►│
         │                                │
         │ 2. Request tax number data    │
         ├───────────────────────────────►│
         │   GET /taxpayer/{ssn}          │
         │                                │
         │◄───────────────────────────────┤
         │ 3. Response: tax data          │
         │   {taxNumber, status, validFrom}│
         │                                │
         │ 4. Build credential            │
         ├──────►[Credential Builder]     │
         │                                │
```

**API Details:**
- **Endpoint:** `https://api.vero.fi/taxpayer/v1`
- **Authentication:** OAuth 2.0 (client credentials grant)
- **Rate Limit:** 100 requests/minute
- **Data Freshness:** Real-time + daily batch sync
- **Fallback:** Cached data if API unavailable

**Integration Code (TypeScript):**

```typescript
class TaxAdministrationClient {
  private apiUrl = 'https://api.vero.fi/taxpayer/v1';
  private accessToken: string | null = null;

  async authenticate(): Promise<void> {
    const response = await fetch('https://api.vero.fi/oauth/token', {
      method: 'POST',
      headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
      body: new URLSearchParams({
        grant_type: 'client_credentials',
        client_id: process.env.VERO_CLIENT_ID!,
        client_secret: process.env.VERO_CLIENT_SECRET!,
        scope: 'taxpayer:read'
      })
    });
    
    const data = await response.json();
    this.accessToken = data.access_token;
  }

  async getTaxNumberData(ssn: string): Promise<TaxNumberData> {
    if (!this.accessToken) {
      await this.authenticate();
    }

    const response = await fetch(`${this.apiUrl}/taxpayer/${ssn}`, {
      headers: {
        'Authorization': `Bearer ${this.accessToken}`,
        'Accept': 'application/json'
      }
    });

    if (!response.ok) {
      throw new Error(`Tax API error: ${response.status}`);
    }

    const data = await response.json();
    
    return {
      taxNumber: data.taxNumber,
      status: data.status,
      validFrom: data.validFrom,
      validUntil: data.validUntil
    };
  }
}
```

---

**Kela Integration (A1 Certificates):**

```typescript
class KelaClient {
  private apiUrl = 'https://api.kela.fi/social-security/v1';

  async getA1Status(ssn: string, postingDetails: PostingDetails): Promise<A1Data> {
    // Authenticate via Suomi.fi e-Authorizations
    const authToken = await this.authenticateWithSuomiFi();

    const response = await fetch(`${this.apiUrl}/a1-certificate`, {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${authToken}`,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        personalId: ssn,
        postingCountry: postingDetails.country,
        postingDuration: postingDetails.durationMonths,
        employerBusinessId: postingDetails.employerBusinessId
      })
    });

    const data = await response.json();
    
    return {
      a1Number: data.certificateNumber,
      coverageCountry: data.coverageCountry,
      validFrom: data.validFrom,
      validUntil: data.validUntil,
      status: data.status
    };
  }
}
```

---

### 5.2 Wallet Integration APIs

**OpenID4VCI Endpoint Configuration:**

```json
{
  "issuer": "https://issuer.vastuu.fi",
  "credential_issuer": "https://issuer.vastuu.fi",
  "credential_endpoint": "https://issuer.vastuu.fi/credential",
  "authorization_server": "https://issuer.vastuu.fi",
  "token_endpoint": "https://issuer.vastuu.fi/token",
  "jwks_uri": "https://issuer.vastuu.fi/.well-known/jwks.json",
  "grant_types_supported": [
    "authorization_code",
    "urn:ietf:params:oauth:grant-type:pre-authorized_code"
  ],
  "credentials_supported": [
    {
      "format": "jwt_vc_json-ld",
      "id": "TaxNumberCredential",
      "cryptographic_binding_methods_supported": ["did"],
      "cryptographic_suites_supported": ["ES256"],
      "display": [
        {
          "name": "Tax Number Credential",
          "locale": "en-US",
          "background_color": "#003f5c",
          "text_color": "#FFFFFF"
        },
        {
          "name": "Veronumeron todistus",
          "locale": "fi-FI",
          "background_color": "#003f5c",
          "text_color": "#FFFFFF"
        }
      ],
      "credentialSubject": {
        "taxNumber": {
          "mandatory": true,
          "display": [
            {"name": "Tax Number", "locale": "en-US"},
            {"name": "Veronumero", "locale": "fi-FI"}
          ]
        },
        "taxNumberStatus": {
          "mandatory": true,
          "display": [
            {"name": "Status", "locale": "en-US"},
            {"name": "Tila", "locale": "fi-FI"}
          ]
        }
      }
    }
  ]
}
```

---

## 6. Security Architecture

### 6.1 Threat Model

**Identified Threats:**

| Threat | Impact | Mitigation |
|--------|--------|------------|
| Credential theft from wallet | High | Biometric auth + encryption + device binding |
| Man-in-the-middle attack | High | TLS 1.3, certificate pinning |
| Issuer key compromise | Critical | HSM storage, key rotation, revocation |
| Replay attack | Medium | Nonce, timestamp validation |
| Phishing (fake verifier) | Medium | Verifier DID verification, user education |
| Unauthorized issuance | High | Policy engine, audit logging |
| Privacy violation | High | Selective disclosure, minimal data |

---

### 6.2 Authentication & Authorization

**Worker Authentication:**
```
User → Wallet (Biometric/PIN) → DID Auth → Issuer/Verifier
```

**Employer Authentication:**
```
User → EBW Wallet (Multi-factor) → Mandate Check → Action Authorization
```

**Issuer Authentication:**
```
Source System → OAuth 2.0 → Vastuu Issuer → Policy Check → Credential Issuance
```

---

### 6.3 Data Protection

**Encryption at Rest:**
- Wallet credentials: AES-256 (iOS Keychain / Android Keystore)
- Database: PostgreSQL encryption (pgcrypto)
- Backups: Encrypted with separate keys

**Encryption in Transit:**
- TLS 1.3 minimum
- Certificate pinning for critical APIs
- Perfect forward secrecy (ECDHE)

**Privacy by Design:**
- Selective disclosure (ZKP where applicable)
- Minimal data collection
- Data minimization in presentations
- User consent required for every disclosure

---

## 7. Deployment Architecture

### 7.1 Cloud Infrastructure

```
┌───────────────────────────────────────────────────┐
│              Azure / AWS Cloud                    │
│                                                   │
│  ┌─────────────────────────────────────────────┐  │
│  │        Load Balancer (Azure LB / ALB)       │  │
│  └────────────┬────────────────────────────────┘  │
│               │                                   │
│  ┌────────────▼────────────────────────────────┐  │
│  │     API Gateway (Azure APIM / API GW)       │  │
│  │  - Rate limiting                            │  │
│  │  - Authentication                           │  │
│  │  - Request routing                          │  │
│  └────────────┬────────────────────────────────┘  │
│               │                                   │
│  ┌────────────▼────────────────────────────────┐  │
│  │   Kubernetes Cluster (AKS / EKS)            │  │
│  │  ┌──────────────┐  ┌──────────────────────┐│  │
│  │  │ Issuer       │  │ Verifier Services    ││  │
│  │  │ Services     │  │                      ││  │
│  │  │ (3 replicas) │  │ (2 replicas)         ││  │
│  │  └──────────────┘  └──────────────────────┘│  │
│  │  ┌──────────────┐  ┌──────────────────────┐│  │
│  │  │ Agent        │  │ Admin Services       ││  │
│  │  │ Platform     │  │                      ││  │
│  │  │ (2 replicas) │  │ (1 replica)          ││  │
│  │  └──────────────┘  └──────────────────────┘│  │
│  └─────────────────────────────────────────────┘  │
│                                                   │
│  ┌─────────────────────────────────────────────┐  │
│  │         Data Layer                          │  │
│  │  ┌──────────────┐  ┌──────────────────────┐│  │
│  │  │ PostgreSQL   │  │ Redis Cache          ││  │
│  │  │ (Primary +   │  │ (Cluster)            ││  │
│  │  │  Replica)    │  │                      ││  │
│  │  └──────────────┘  └──────────────────────┘│  │
│  │  ┌──────────────┐  ┌──────────────────────┐│  │
│  │  │ MongoDB      │  │ Object Storage       ││  │
│  │  │ (Replica Set)│  │ (Azure Blob / S3)    ││  │
│  │  └──────────────┘  └──────────────────────┘│  │
│  └─────────────────────────────────────────────┘  │
│                                                   │
│  ┌─────────────────────────────────────────────┐  │
│  │      Security Services                      │  │
│  │  ┌──────────────┐  ┌──────────────────────┐│  │
│  │  │ Azure Key    │  │ HSM                  ││  │
│  │  │ Vault /      │  │ (Hardware Security   ││  │
│  │  │ AWS KMS      │  │  Module)             ││  │
│  │  └──────────────┘  └──────────────────────┘│  │
│  └─────────────────────────────────────────────┘  │
└───────────────────────────────────────────────────┘
```

---

### 7.2 Scalability & High Availability

**Horizontal Scaling:**
- Auto-scaling based on CPU/memory metrics
- Minimum 3 replicas for critical services
- Load balancing across availability zones

**Database Replication:**
- PostgreSQL: Primary + 2 read replicas
- MongoDB: 3-node replica set
- Redis: Redis Cluster (6 nodes)

**Disaster Recovery:**
- Daily automated backups
- Cross-region backup replication
- RTO (Recovery Time Objective): 4 hours
- RPO (Recovery Point Objective): 1 hour

---

## 8. AI Agent Architecture

### 8.1 Agent Core Components

```typescript
class BaseAgent {
  protected wallet: WalletAPI;
  protected semanticReasoner: SPARQLEngine;
  protected llm: LLMProvider; // GPT-4 / Claude
  protected policyEngine: PolicyEngine;

  abstract async processIntent(intent: Intent): Promise<Action>;

  protected async querySemantics(sparql: string): Promise<any> {
    return this.semanticReasoner.query(sparql);
  }

  protected async checkPolicy(action: Action): Promise<boolean> {
    return this.policyEngine.evaluate(action);
  }

  protected async log(event: AgentEvent): Promise<void> {
    await this.auditLog.record(event);
  }
}

class WorkerAgent extends BaseAgent {
  async processIntent(intent: Intent): Promise<Action> {
    switch (intent.type) {
      case 'FIND_JOBS':
        return this.findSuitableJobs(intent.parameters);
      case 'APPLY_JOB':
        return this.applyToJob(intent.parameters.jobId);
      case 'REVIEW_OFFER':
        return this.reviewOffer(intent.parameters.offerId);
      default:
        throw new Error(`Unknown intent: ${intent.type}`);
    }
  }

  private async findSuitableJobs(params: any): Promise<Action> {
    // 1. Get worker's credentials from wallet
    const credentials = await this.wallet.listCredentials();

    // 2. Build semantic profile
    const profile = await this.buildSemanticProfile(credentials);

    // 3. Query job listings
    const jobs = await this.queryJobs(params.location, params.role);

    // 4. Match requirements
    const matches = [];
    for (const job of jobs) {
      const matchScore = await this.matchRequirements(profile, job.requirements);
      if (matchScore.qualified) {
        matches.push({ job, score: matchScore.score });
      }
    }

    // 5. Rank and return
    matches.sort((a, b) => b.score - a.score);
    
    return {
      type: 'JOBS_FOUND',
      data: matches.slice(0, 10)
    };
  }

  private async matchRequirements(
    profile: WorkerProfile,
    requirements: JobRequirements
  ): Promise<MatchResult> {
    let score = 0;
    const gaps = [];

    for (const req of requirements.mandatory) {
      const hasCredential = profile.credentials.some(cred => 
        this.credentialMatches(cred, req)
      );
      
      if (hasCredential) {
        score += 10;
      } else {
        gaps.push(req);
      }
    }

    for (const req of requirements.preferred) {
      const hasCredential = profile.credentials.some(cred =>
        this.credentialMatches(cred, req)
      );
      
      if (hasCredential) {
        score += 5;
      }
    }

    return {
      qualified: gaps.length === 0,
      score,
      gaps
    };
  }
}

class EmployerAgent extends BaseAgent {
  async processIntent(intent: Intent): Promise<Action> {
    switch (intent.type) {
      case 'SCREEN_APPLICANTS':
        return this.screenApplicants(intent.parameters.jobId);
      case 'MAKE_OFFER':
        return this.makeOffer(intent.parameters);
      case 'ONBOARD_EMPLOYEE':
        return this.onboardEmployee(intent.parameters.employeeId);
      default:
        throw new Error(`Unknown intent: ${intent.type}`);
    }
  }

  private async screenApplicants(jobId: string): Promise<Action> {
    // 1. Get job requirements
    const job = await this.getJob(jobId);

    // 2. Get applications
    const applications = await this.getApplications(jobId);

    // 3. Verify each application
    const results = [];
    for (const app of applications) {
      const verification = await this.verifyApplication(app, job.requirements);
      results.push({
        applicantId: app.applicantId,
        status: verification.status,
        gaps: verification.gaps,
        score: verification.score
      });
    }

    // 4. Classify applicants
    const fullyQualified = results.filter(r => r.status === 'FULLY_QUALIFIED');
    const partiallyQualified = results.filter(r => r.status === 'PARTIALLY_QUALIFIED');
    const notQualified = results.filter(r => r.status === 'NOT_QUALIFIED');

    return {
      type: 'SCREENING_COMPLETE',
      data: {
        fullyQualified,
        partiallyQualified,
        notQualified
      }
    };
  }

  private async verifyApplication(
    application: JobApplication,
    requirements: JobRequirements
  ): Promise<VerificationResult> {
    const presentation = application.credentialPresentation;

    // 1. Verify signatures
    const signaturesValid = await this.verifySignatures(presentation);
    if (!signaturesValid) {
      return { status: 'INVALID', reason: 'Signature verification failed' };
    }

    // 2. Validate SHACL constraints
    const shaclValid = await this.validateSHACL(presentation);
    if (!shaclValid) {
      return { status: 'INVALID', reason: 'SHACL validation failed' };
    }

    // 3. Check revocation status
    const notRevoked = await this.checkRevocation(presentation);
    if (!notRevoked) {
      return { status: 'INVALID', reason: 'Credential revoked' };
    }

    // 4. Match requirements
    const match = await this.matchRequirements(presentation, requirements);

    if (match.gaps.length === 0) {
      return { status: 'FULLY_QUALIFIED', score: match.score };
    } else if (match.gaps.length <= 2) {
      return { 
        status: 'PARTIALLY_QUALIFIED', 
        score: match.score,
        gaps: match.gaps
      };
    } else {
      return { 
        status: 'NOT_QUALIFIED',
        gaps: match.gaps
      };
    }
  }
}
```

---

### 8.2 Agent Communication Protocol

**DIDComm Message Example:**

```json
{
  "type": "https://didcomm.org/job-application/1.0/application",
  "id": "abc-123-xyz-789",
  "from": "did:example:worker-agent-abc",
  "to": ["did:example:employer-agent-xyz"],
  "created_time": 1704110400,
  "expires_time": 1704196800,
  "body": {
    "jobId": "job-2024-electrician-001",
    "applicantDID": "did:ebsi:zWorker123Maria",
    "presentationExchange": {
      "presentation_submission": {
        "id": "submission-001",
        "definition_id": "job-requirements-electrician",
        "descriptor_map": [
          {
            "id": "tax_number",
            "format": "jwt_vc_json-ld",
            "path": "$.verifiableCredential[0]"
          },
          {
            "id": "safety_training",
            "format": "jwt_vc_json-ld",
            "path": "$.verifiableCredential[1]"
          }
        ]
      },
      "verifiablePresentation": {
        "@context": [...],
        "type": "VerifiablePresentation",
        "verifiableCredential": [...]
      }
    }
  },
  "attachments": [
    {
      "id": "resume",
      "media_type": "application/pdf",
      "data": {
        "base64": "..."
      }
    }
  ]
}
```

---

## Summary: Technical Architecture Highlights

### Key Technologies
- **Semantic Layer:** RDF/OWL/SHACL/SKOS
- **Credential Format:** W3C VC 2.0 (JSON-LD)
- **DID Method:** did:ebsi (European Blockchain Services Infrastructure)
- **Protocols:** OpenID4VCI, OpenID4VP, DIDComm
- **Trust:** eIDAS 2.0 (QES/QEAA)
- **Runtime:** TypeScript + Node.js
- **Database:** PostgreSQL, Redis, MongoDB
- **Deployment:** Kubernetes (Azure/AWS)
- **AI/ML:** GPT-4 / Claude for agent intelligence

### Architecture Principles
1. **Decentralized by design** - No central data warehouse
2. **Semantic interoperability** - OWL/SHACL foundation
3. **Privacy by default** - Selective disclosure
4. **Trust framework** - eIDAS compliance
5. **Agent-orchestrated** - AI automation
6. **Scalable & resilient** - Cloud-native architecture

---

**For implementation roadmap, see [ROADMAP.md](ROADMAP.md)**  
**For use cases, see [use-cases/](use-cases/)**
