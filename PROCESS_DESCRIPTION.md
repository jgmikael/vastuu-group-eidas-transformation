# Process Description - Vastuu Group eIDAS 2.0 Transformation

**Complete process flows for wallet-native trusted worker services and AI agent automation**

---

## Table of Contents

1. [Transformation Process](#1-transformation-process)
2. [Worker and Employer Agent Processes](#2-worker-and-employer-agent-processes)
3. [Credential Issuance Process](#3-credential-issuance-process)
4. [Job Search and Hiring Process](#4-job-search-and-hiring-process)
5. [Construction Site Access Process](#5-construction-site-access-process)
6. [Cross-Border Worker Posting Process](#6-cross-border-worker-posting-process)

---

## 1. Transformation Process

### 1.1 Current Baseline Architecture

**How "Luotettava työntekijä" Currently Works:**

```
[Worker] ──► [Vastuu Group Central Registry] ◄── [Employer]
                      │
                      ├── Worker Database
                      ├── Employer Database
                      ├── Compliance Checks
                      └── PDF/API Reports
                             │
                             ▼
                   [Client/Main Contractor]
```

**Characteristics:**
- Centralized data storage
- Service-specific authentication
- PDF-based documentation
- Manual verification workflows
- Limited cross-border support
- Data ownership with service, not users

**Problems:**
1. **Data silos** - Information locked in service
2. **No portability** - Workers can't easily take their data elsewhere
3. **Duplicate entry** - Same information entered multiple times
4. **Limited interoperability** - Doesn't integrate with EU-wide systems
5. **Manual overhead** - Document checking requires human intervention

---

### 1.2 Target State: Wallet-Native Infrastructure

**The New Model:**

```
[Public Registers] ──► [Vastuu Group Issuer]
   (Tax Auth)              │
   (Income Reg)            ▼
   (Kela)         Verifiable Credentials (W3C VC)
                           │
        ┌──────────────────┴──────────────────┐
        ▼                                     ▼
[EUDI Wallet - Worker]              [EBW Wallet - Company]
        │                                     │
        └──────────► Presentation ◄───────────┘
                           │
                           ▼
                 [Contractor / Authority]
                   (Verifier)
```

**Key Changes:**
1. **Decentralized identity** - PID/EBWOID instead of service accounts
2. **User-held data** - Credentials in wallets, not central database
3. **Verifiable claims** - Cryptographically signed attestations
4. **Selective disclosure** - Share only what's needed
5. **Trust network** - eIDAS trust framework
6. **Agent-orchestrated** - AI agents automate processes

---

### 1.3 Six-Phase Transformation Roadmap

#### **Phase 1: Semantic Modeling (3-4 months)**

**Objective:** Map existing data structures to standardized semantic models

**Activities:**
1. **Inventory current attributes:**
   - Worker: tax number, employment status, qualifications, safety training, A1 certificates
   - Employer: company identity, VAT status, compliance status, insurance coverage

2. **Create OWL ontology:**
   ```turtle
   @prefix vg: <https://vastuu.fi/vocabulary/> .
   @prefix ebw: <https://ebw.europa.eu/vocabulary/> .
   
   vg:Worker a owl:Class ;
       rdfs:subClassOf ebw:NaturalPersonRepresentative ;
       rdfs:label "Construction Worker"@en .
   
   vg:hasWorkPermit a owl:ObjectProperty ;
       rdfs:domain vg:Worker ;
       rdfs:range vg:WorkPermit .
   ```

3. **Define SHACL constraints:**
   ```turtle
   vg:WorkerShape a sh:NodeShape ;
       sh:targetClass vg:Worker ;
       sh:property [
           sh:path vg:taxNumber ;
           sh:minCount 1 ;
           sh:pattern "^FI[0-9]{8}$" ;
       ] .
   ```

4. **Harmonize with EU vocabularies:**
   - EU Core Person
   - EU Core Business
   - NCBV (Nordic Core Business Vocabulary)
   - EBW Vocabulary

**Deliverables:**
- `vocabulary.ttl` - Complete OWL ontology
- `shapes.ttl` - SHACL validation shapes
- `mapping.md` - Current-to-semantic mapping documentation

---

#### **Phase 2: VC Model Definition (2-3 months)**

**Objective:** Design W3C Verifiable Credential schemas for all attestations

**Key Credential Types:**

**2.1 Worker Credentials**

| Credential Type | Issuer | Attributes | Validity |
|----------------|--------|------------|----------|
| `WorkerTaxNumberCredential` | Tax Authority | taxNumber, validFrom, validUntil | Annual |
| `EmploymentRelationshipCredential` | Employer | employerID, position, startDate, contractType | Duration of employment |
| `SocialSecurityCoverageCredential` | Kela | A1Status, coverageCountry, validUntil | Posted period |
| `ProfessionalQualificationCredential` | Training Provider | qualificationType, level, issuedDate | Permanent/renewal |
| `SafetyTrainingCredential` | Training Provider | courseType, completionDate, expiryDate | 3-5 years |
| `ComplianceStatusCredential` | Vastuu Group | overallStatus, lastChecked, concerns | Real-time |

**2.2 Employer Credentials**

| Credential Type | Issuer | Attributes | Validity |
|----------------|--------|------------|----------|
| `CompanyIdentityCredential` | Trade Register | legalName, registrationNumber, businessID | Permanent |
| `VATRegistrationCredential` | Tax Authority | vatNumber, status, validFrom | Annual verification |
| `EmployerRegisterCredential` | Employer Register | employerID, status, employees | Continuous |
| `InsuranceCoverageCredential` | Insurance Provider | policyNumber, coverageType, validUntil | Policy period |
| `TilaajavastuurCredential` | Vastuu Group | complianceLevel, lastAudit, validUntil | Periodic |

**Example JSON-LD VC Schema:**
```json
{
  "@context": [
    "https://www.w3.org/ns/credentials/v2",
    "https://vastuu.fi/contexts/v1"
  ],
  "id": "urn:uuid:3f7a6e2b-4c1d-4e8f-9a3b-1c2d3e4f5a6b",
  "type": ["VerifiableCredential", "WorkerTaxNumberCredential"],
  "issuer": {
    "id": "did:ebsi:zVerohallintoFI",
    "name": "Finnish Tax Administration"
  },
  "validFrom": "2024-01-01T00:00:00Z",
  "validUntil": "2024-12-31T23:59:59Z",
  "credentialSubject": {
    "id": "did:ebsi:zWorker123",
    "type": "Worker",
    "taxNumber": "FI12345678",
    "taxNumberStatus": "Active"
  },
  "proof": {
    "type": "DataIntegrityProof",
    "cryptosuite": "ecdsa-rdfc-2019",
    "created": "2024-01-01T10:00:00Z",
    "proofPurpose": "assertionMethod",
    "verificationMethod": "did:ebsi:zVerohallintoFI#key-1",
    "proofValue": "z3FXQw..."
  }
}
```

**Deliverables:**
- Credential schema definitions (JSON Schema + JSON-LD)
- JSON-LD contexts for all credential types
- Example credentials (test data)
- Validation test suite

---

#### **Phase 3: Issuer Service (4-6 months)**

**Objective:** Build VC issuance infrastructure

**3.1 Architecture Components:**

```
[Source Systems] ──► [Integration Layer] ──► [VC Issuer Core] ──► [Wallet APIs]
     │                     │                       │                    │
Tax Authority         Data Validators      Credential Builder      EUDI Wallet
Income Register       Policy Engine        Signing Service         EBW Wallet
Kela                  Consent Manager      DID Registry            Mobile Apps
Employers             Audit Logger         Revocation Registry
```

**3.2 Integration Points:**

1. **Tax Administration (Verohallinto):**
   - API: Tax number validation
   - Frequency: Real-time + nightly batch
   - Protocol: REST API + OAuth 2.0

2. **Income Register (Tulorekisteri):**
   - API: Employment relationship data
   - Frequency: Monthly + event-driven
   - Protocol: REST API + Suomi.fi authentication

3. **Kela:**
   - API: Social security coverage (A1 status)
   - Frequency: On-demand
   - Protocol: Suomi.fi e-Authorizations

4. **Employers:**
   - API: Employment contracts, terminations
   - Frequency: Event-driven
   - Protocol: OpenID Connect + VC issuance protocol

**3.3 Issuance Flow:**

```
1. Worker requests credential ──► Issuer validates sources
                                        │
2. Issuer checks policy ◄───────────────┘
   (Is worker entitled?)
                                        │
3. Build credential ◄───────────────────┘
   (JSON-LD + semantics)
                                        │
4. Sign with eIDAS QES ◄────────────────┘
   (Qualified electronic signature)
                                        │
5. Deliver to wallet ◄──────────────────┘
   (OpenID4VCI protocol)
```

**3.4 Technical Stack:**
- **Backend:** TypeScript + Node.js
- **Database:** PostgreSQL (audit trail) + Redis (cache)
- **DID Method:** `did:ebsi` (European Blockchain Services Infrastructure)
- **Signing:** eIDAS-compliant QES provider
- **Wallet Protocol:** OpenID for Verifiable Credential Issuance (OpenID4VCI)

**Deliverables:**
- Issuer service API
- Integration adapters for source systems
- Admin dashboard
- Revocation registry
- Audit logging system

---

#### **Phase 4: Wallet Integration (3-4 months)**

**Objective:** Enable wallet holders to receive and present credentials

**4.1 EUDI Wallet Integration (Workers):**

```
[Vastuu Group Issuer] ──► OpenID4VCI ──► [EUDI Wallet]
                                              │
                                              ├── Credential Storage
                                              ├── Selective Disclosure
                                              └── Presentation (OpenID4VP)
                                                      │
                                                      ▼
                                              [Verifier/Employer]
```

**Key Features:**
- QR code-based credential offer
- Mobile app push notifications
- Biometric authentication
- Encrypted local storage
- Selective disclosure UI

**4.2 EBW Wallet Integration (Employers):**

```
[Vastuu Group Issuer] ──► OpenID4VCI ──► [EBW Wallet]
                                              │
                                              ├── Company Credentials
                                              ├── Mandate Management
                                              └── Agent Access (API)
```

**Key Features:**
- Multi-user access control
- Role-based presentation
- Bulk credential management
- API for AI agents

**4.3 Wallet SDK:**
- React Native components
- Web components (for desktop)
- QR scanner
- Biometric integrations
- Secure storage wrappers

**Deliverables:**
- Wallet SDK (iOS, Android, Web)
- Integration documentation
- Test wallets
- Demo applications

---

#### **Phase 5: Verification Services (3-4 months)**

**Objective:** Enable construction sites and clients to verify workers

**5.1 Construction Site Verifier App:**

```
┌─────────────────────────┐
│  Verifier Mobile App    │
│  ┌──────────────────┐   │
│  │  QR Scanner      │   │      Worker shows QR
│  └──────────────────┘   │ ◄──── from EUDI Wallet
│         │               │
│         ▼               │
│  ┌──────────────────┐   │
│  │ Verification     │   │       Check signatures
│  │ Engine           │   │       Validate SHACL
│  └──────────────────┘   │       Check revocation
│         │               │
│         ▼               │
│  ┌──────────────────┐   │
│  │ Access Decision  │   │       ✅ or ❌
│  └──────────────────┘   │
└─────────────────────────┘
```

**Verification Checks:**
1. **Cryptographic:** Signature validation (eIDAS trust chain)
2. **Semantic:** SHACL constraint validation
3. **Status:** Revocation check
4. **Context:** Role-specific requirements
5. **Freshness:** Validity period check

**5.2 Client API (for larger organizations):**

```http
POST /verify/presentation
Content-Type: application/json

{
  "presentation": {
    "@context": [...],
    "type": "VerifiablePresentation",
    "verifiableCredential": [...]
  },
  "requiredClaims": {
    "taxNumber": "required",
    "safetyTraining": "required",
    "a1Status": "optional"
  }
}

Response:
{
  "verified": true,
  "claims": {
    "taxNumber": { "value": "FI12345678", "status": "valid" },
    "safetyTraining": { "status": "valid", "expiryDate": "2026-12-31" }
  },
  "warnings": [],
  "decision": "ALLOW_ACCESS"
}
```

**Deliverables:**
- Verifier mobile app (iOS + Android)
- Verifier web portal
- Verification API
- Compliance reporting dashboard

---

#### **Phase 6: EU Interoperability (6-12 months)**

**Objective:** Enable cross-border acceptance and eIDAS trust framework integration

**6.1 eIDAS Trust Anchors:**
- Register Vastuu Group as Qualified Trust Service Provider (QTSP)
- Obtain eIDAS certificates
- Integrate with EU Trust Lists

**6.2 Cross-Border Credential Mapping:**

| Finnish Credential | EU Equivalent | Standards |
|-------------------|---------------|-----------|
| `TaxNumberCredential` | `TaxIdentificationCredential` | EU Core Person |
| `A1Credential` | `SocialSecurityCoverageCredential` | EU Social Security Regulation |
| `SafetyTrainingCredential` | `ProfessionalQualificationCredential` | EQF (European Qualifications Framework) |

**6.3 Acceptance Network:**
- Bilateral agreements with other EU wallet providers
- Integration with European Data Spaces
- Participation in EBSI (European Blockchain Services Infrastructure)

**Deliverables:**
- QTSP certification
- Cross-border credential profiles
- Integration with EU wallet providers
- Compliance documentation

---

## 2. Worker and Employer Agent Processes

### 2.1 Core Architecture

```
┌─────────────────────┐                    ┌─────────────────────┐
│  Worker's AI Agent  │                    │ Employer's AI Agent │
│  ┌──────────────┐   │                    │  ┌──────────────┐   │
│  │ Profile      │   │                    │  │ Role         │   │
│  │ Builder      │   │                    │  │ Modeler      │   │
│  └──────────────┘   │                    │  └──────────────┘   │
│  ┌──────────────┐   │                    │  ┌──────────────┐   │
│  │ Job          │   │                    │  │ Credential   │   │
│  │ Matcher      │   │                    │  │ Verifier     │   │
│  └──────────────┘   │                    │  └──────────────┘   │
│  ┌──────────────┐   │                    │  ┌──────────────┐   │
│  │ Application  │   │ ◄────────────────► │  │ Offer        │   │
│  │ Builder      │   │   Presentation     │  │ Generator    │   │
│  └──────────────┘   │     Exchange       │  └──────────────┘   │
│  ┌──────────────┐   │                    │  ┌──────────────┐   │
│  │ Evidence     │   │                    │  │ Onboarding   │   │
│  │ Manager      │   │                    │  │ Orchestrator │   │
│  └──────────────┘   │                    │  └──────────────┘   │
└─────────────────────┘                    └─────────────────────┘
         │                                           │
         └───────────► [EUDI Wallet] ◄──────────────┘
                       [EBW Wallet]
```

---

### 2.2 Worker's AI Agent Detailed Functions

#### **Function 1: Profile Building from Verified Facts**

**Input:** Credentials in worker's EUDI Wallet

**Process:**
```
1. Agent scans wallet ──► Identifies credentials
                              │
2. Extract attributes ◄───────┘
   (qualification, tax number, experience, etc.)
                              │
3. Build semantic profile ◄───┘
   (using vg:Worker ontology)
                              │
4. Identify gaps ◄─────────────┘
   (required for target roles)
                              │
5. Suggest credential acquisition ◄┘
```

**Example Output:**
```json
{
  "workerProfile": {
    "identity": "verified_PID",
    "qualifications": [
      { "type": "SafetyTraining", "status": "valid", "expiry": "2026-12-31" },
      { "type": "ElectricalQualification", "status": "valid" }
    ],
    "employment": {
      "status": "active",
      "employer": "Construction Co Ltd",
      "role": "Electrician"
    },
    "gaps": [
      {
        "credential": "A1Certificate",
        "reason": "Required for cross-border posting",
        "howToObtain": "Apply via Kela using your EUDI Wallet"
      }
    ]
  }
}
```

---

#### **Function 2: Suitable Job Searching**

**Smart Matching Algorithm:**

```python
def find_suitable_jobs(worker_profile, job_listings):
    matches = []
    
    for job in job_listings:
        score = 0
        gaps = []
        
        # Check mandatory qualifications
        for req in job.mandatory_requirements:
            if worker_has_credential(worker_profile, req):
                score += 10
            else:
                gaps.append(req)
        
        # Check location/work permit
        if check_work_permit(worker_profile, job.location):
            score += 5
        
        # Check language requirements
        if check_language(worker_profile, job.required_language):
            score += 3
        
        # Compliance checks
        if check_tax_status(worker_profile):
            score += 2
        
        if len(gaps) == 0:
            matches.append({
                "job": job,
                "score": score,
                "status": "fully_qualified"
            })
        elif len(gaps) <= 2:
            matches.append({
                "job": job,
                "score": score,
                "status": "partially_qualified",
                "missing": gaps
            })
    
    return sorted(matches, key=lambda x: x['score'], reverse=True)
```

**Agent Behavior:**
- Filters out jobs where mandatory credentials are missing
- Prioritizes jobs with high match scores
- Suggests acquiring missing credentials for good matches
- Considers location, language, and compliance requirements

---

#### **Function 3: Application Composition**

**Selective Disclosure in Action:**

```
Job requires:
├── Valid tax number (yes/no)
├── Safety training completion (yes/no + expiry date)
├── 2+ years experience (yes/no)
└── Work permit for Finland (yes/no)

Agent composes presentation:
├── TaxNumberCredential (status only, not full number)
├── SafetyTrainingCredential (completion + expiry)
├── EmploymentRelationshipCredential (duration only)
└── WorkPermitCredential (validity confirmation)

❌ NOT included:
- Full personal details
- Salary history
- Other irrelevant qualifications
- Medical information
```

**Example Presentation Request:**
```json
{
  "presentationDefinition": {
    "id": "job-application-electrician-001",
    "input_descriptors": [
      {
        "id": "tax_number",
        "name": "Valid Tax Number",
        "constraints": {
          "fields": [
            {
              "path": ["$.credentialSubject.taxNumberStatus"],
              "filter": { "const": "Active" }
            }
          ]
        }
      },
      {
        "id": "safety_training",
        "name": "Safety Training",
        "constraints": {
          "fields": [
            {
              "path": ["$.credentialSubject.courseType"],
              "filter": { "const": "ConstructionSafety" }
            },
            {
              "path": ["$.credentialSubject.expiryDate"],
              "filter": { "type": "string", "format": "date" }
            }
          ]
        }
      }
    ]
  }
}
```

---

#### **Function 4: Negotiation and Approvals**

**Human-in-the-Loop Workflow:**

```
┌─────────────────────────────┐
│  Agent receives offer       │
│  ┌──────────────────────┐   │
│  │ Analyze terms        │   │
│  │ - Salary             │   │
│  │ - Work hours         │   │
│  │ - Contract type      │   │
│  │ - Required consents  │   │
│  └──────────────────────┘   │
│          │                  │
│          ▼                  │
│  ┌──────────────────────┐   │
│  │ Flag concerns        │   │
│  │ - Below market rate? │   │
│  │ - Unclear terms?     │   │
│  │ - Missing benefits?  │   │
│  └──────────────────────┘   │
│          │                  │
│          ▼                  │
│  ┌──────────────────────┐   │
│  │ Present to worker    │   │ ──► Worker reviews
│  │ with explanation     │   │     and decides
│  └──────────────────────┘   │
│          │                  │
│  ┌──────────────────────┐   │
│  │ Await user decision  │   │ ◄── Approve / Reject
│  └──────────────────────┘   │
│          │                  │
│          ▼                  │
│  ┌──────────────────────┐   │
│  │ Execute action       │   │
│  │ - Sign contract      │   │
│  │ - Request changes    │   │
│  │ - Decline offer      │   │
│  └──────────────────────┘   │
└─────────────────────────────┘
```

**Critical Principle:**
> **Agent automates preparation and verification; human makes final decisions on legally binding actions.**

---

#### **Function 5: Post-Employment Evidence Management**

**New Process Evidence Types:**

| Evidence | Issuer | When Created | Stored Where |
|----------|--------|--------------|--------------|
| `JobApplicationEvidence` | Worker's Agent | Application sent | Worker's wallet |
| `ConsentEvidence` | Worker | Data sharing consent | Worker's wallet |
| `EmploymentOfferCredential` | Employer's Agent | Offer made | Both wallets |
| `ContractSignatureEvidence` | Both parties | Contract signed | Both wallets |
| `OnboardingCompletionCredential` | Employer | Onboarding done | Worker's wallet |
| `SiteAccessCredential` | Site operator | Access granted | Worker's wallet |
| `WorkCommencementEvidence` | Employer | First day | Both wallets |

**Benefit:**
- Continuous, reusable digital evidence trail
- No need to request "proof of employment" letters
- Automatic reference generation
- Compliance audit trail

---

### 2.3 Employer's AI Agent Detailed Functions

#### **Function 1: Role Requirements Modeling**

**From Job Description to Semantic Requirements:**

```
Input (natural language):
"Seeking experienced electrician for industrial site.
Must have valid electrical qualification, safety training,
and ability to work in Finland. Construction experience preferred."

Agent output (semantic model):
{
  "roleRequirements": {
    "mandatory": [
      {
        "credential": "ProfessionalQualificationCredential",
        "constraints": {
          "qualificationType": "ElectricalInstallation",
          "level": "Journeyman"
        }
      },
      {
        "credential": "SafetyTrainingCredential",
        "constraints": {
          "courseType": "ConstructionSafety",
          "status": "valid"
        }
      },
      {
        "credential": "WorkPermitCredential",
        "constraints": {
          "country": "FI",
          "status": "valid"
        }
      },
      {
        "credential": "TaxNumberCredential",
        "constraints": {
          "taxNumberStatus": "Active"
        }
      }
    ],
    "preferred": [
      {
        "credential": "EmploymentRelationshipCredential",
        "constraints": {
          "industry": "Construction",
          "duration": { "min": "P2Y" }  // 2+ years
        }
      }
    ]
  }
}
```

---

#### **Function 2: Automated Candidate Screening**

**Three-Tier Classification:**

```
For each applicant:

1. Extract presented credentials ──► Parse JSON-LD
                                         │
2. Verify signatures ◄───────────────────┘
   (Check eIDAS trust chain)
                                         │
3. Validate against SHACL ◄──────────────┘
   (Semantic constraints)
                                         │
4. Check revocation status ◄─────────────┘
   (Query revocation registry)
                                         │
5. Match requirements ◄───────────────────┘
   (Compare with roleRequirements)
                                         │
6. Classify ◄─────────────────────────────┘

Classifications:
├── FULLY_QUALIFIED: All mandatory + most preferred
├── PARTIALLY_QUALIFIED: Missing 1-2 credentials (acquirable)
└── NOT_QUALIFIED: Missing critical credentials
```

**Automated Actions:**
- **Fully qualified:** Add to interview shortlist
- **Partially qualified:** Send credential request (see Function 3)
- **Not qualified:** Send automated rejection with explanation

---

#### **Function 3: Evidence Request Orchestration**

**Structured Presentation Request:**

```json
{
  "requestType": "CredentialRequest",
  "requestId": "req-20240115-001",
  "requestedBy": {
    "did": "did:ebw:zConstructionCoLtd",
    "name": "Construction Co Ltd",
    "purpose": "Employment verification for role ELC-2024-001"
  },
  "requestedCredentials": [
    {
      "type": "SocialSecurityCoverageCredential",
      "reason": "Required for cross-border posting",
      "alternatives": ["A1Certificate", "E101Form"],
      "deadline": "2024-01-20T23:59:59Z"
    },
    {
      "type": "AdvancedElectricalQualification",
      "reason": "Preferred for industrial installations",
      "optional": true
    }
  ],
  "consent": {
    "purpose": "Employment application evaluation",
    "retention": "30 days after hiring decision",
    "recipients": ["HR Department", "Safety Officer"]
  }
}
```

Worker's agent receives this and:
1. Checks if credentials are in wallet
2. If yes → Presents them
3. If no → Suggests how to obtain
4. If optional → Asks worker if they want to share alternative evidence

---

#### **Function 4: Offer and Contract Generation**

**Automated Contract Preparation:**

```
1. Template selection ──► Choose contract type
                             │
2. Data population ◄─────────┘
   (From verified credentials)
   - Worker name (from PID)
   - Tax number (from TaxNumberCredential)
   - Role (from job listing)
   - Start date (negotiated)
   - Salary (negotiated)
                             │
3. Compliance check ◄────────┘
   (Ensure employer has right to hire)
   - Check EBW Wallet for:
     • EmployerRegisterCredential
     • InsuranceCoverageCredential
     • TilaajavastuurCredential
                             │
4. Mandate verification ◄────┘
   (Check signer has authority)
   - Verify HR manager has MandateCredential
   - Check scope includes "hiring authority"
                             │
5. Generate offer ◄───────────┘
   (Signed EmploymentOfferCredential)
                             │
6. Deliver to worker's agent ◄┘
```

**Example Offer Credential:**
```json
{
  "@context": [...],
  "type": ["VerifiableCredential", "EmploymentOfferCredential"],
  "issuer": {
    "id": "did:ebw:zConstructionCoLtd",
    "name": "Construction Co Ltd"
  },
  "validFrom": "2024-01-15T10:00:00Z",
  "validUntil": "2024-01-30T23:59:59Z",
  "credentialSubject": {
    "offerTo": "did:ebsi:zWorker123",
    "role": "Electrician",
    "startDate": "2024-02-01",
    "contractType": "Permanent",
    "grossMonthlySalary": 3500,
    "currency": "EUR",
    "workHoursPerWeek": 37.5,
    "probationPeriod": "P3M"
  },
  "proof": {
    "type": "DataIntegrityProof",
    "created": "2024-01-15T10:00:00Z",
    "proofPurpose": "assertionMethod",
    "verificationMethod": "did:ebw:zConstructionCoLtd#hr-manager-key",
    "proofValue": "z3FXQw..."
  }
}
```

---

#### **Function 5: Onboarding Automation**

**Multi-Step Orchestration:**

```
[Contract Signed] ──► Trigger onboarding workflow
                              │
1. Perehdytys (Induction) ◄───┘
   ├── Send training materials
   ├── Schedule safety briefing
   ├── Track completion
   └── Issue OnboardingCompletionCredential
                              │
2. Site Registration ◄────────┘
   ├── Register in site system
   ├── Assign equipment
   ├── Create site access credential
   └── Issue SiteAccessCredential
                              │
3. System Access ◄─────────────┘
   ├── Create user accounts
   ├── Assign roles/permissions
   └── Send login credentials
                              │
4. Compliance Notifications ◄─┘
   ├── Notify Tax Authority (if required)
   ├── Notify client/main contractor
   ├── Update Vastuu Group registry
   └── Issue WorkCommencementEvidence
```

**Deliverable Evidence:**
- `OnboardingCompletionCredential` (worker keeps in wallet)
- `SiteAccessCredential` (used for daily entry)
- `WorkCommencementEvidence` (for compliance audit)

---

## 3. Credential Issuance Process

### 3.1 Worker Tax Number Credential Issuance

**Detailed Flow:**

```
┌─────────────────────┐
│  Worker (via EUDI)  │
│                     │
│ 1. Request tax      │
│    number           │
│    credential       │
└──────┬──────────────┘
       │
       ▼
┌─────────────────────────────────┐
│  Vastuu Group Issuer            │
│                                 │
│ 2. Authenticate worker          │
│    (PID verification)           │
│       │                         │
│       ▼                         │
│ 3. Query Tax Authority API      │
│    GET /taxpayer/{pid}          │
│       │                         │
│       ▼                         │
│ ┌───────────────────────────┐   │
│ │ Tax Authority Response    │   │
│ │ {                         │   │
│ │   "taxNumber": "FI123...", │   │
│ │   "status": "Active",     │   │
│ │   "validFrom": "2024-01-01"│   │
│ │ }                         │   │
│ └───────────────────────────┘   │
│       │                         │
│       ▼                         │
│ 4. Build VC                     │
│    (JSON-LD + semantics)        │
│       │                         │
│       ▼                         │
│ 5. Sign with eIDAS QES          │
│    (Vastuu Group's key)         │
│       │                         │
│       ▼                         │
│ 6. Register in audit log        │
│       │                         │
│       ▼                         │
│ 7. Deliver via OpenID4VCI       │
└──────┬──────────────────────────┘
       │
       ▼
┌─────────────────────┐
│  Worker's EUDI      │
│  Wallet             │
│                     │
│ 8. Store credential │
│ 9. Show notification│
└─────────────────────┘
```

**OpenID4VCI Protocol Steps:**

```http
// Step 1: Credential Offer
GET /credential-offer?credential_offer_uri=https://vastuu.fi/offers/abc123

Response:
{
  "credential_issuer": "https://vastuu.fi",
  "credentials": ["TaxNumberCredential"],
  "grants": {
    "authorization_code": {
      "issuer_state": "abc123"
    }
  }
}

// Step 2: Authorization
POST /authorize
{
  "response_type": "code",
  "client_id": "eudi-wallet",
  "redirect_uri": "eudi://callback",
  "scope": "credential_issuance",
  "state": "xyz789"
}

// Step 3: Token Exchange
POST /token
{
  "grant_type": "authorization_code",
  "code": "auth_code_123",
  "redirect_uri": "eudi://callback"
}

Response:
{
  "access_token": "eyJhbGc...",
  "token_type": "Bearer",
  "expires_in": 3600
}

// Step 4: Credential Request
POST /credential
Authorization: Bearer eyJhbGc...
{
  "format": "jwt_vc_json-ld",
  "credential_definition": {
    "type": ["VerifiableCredential", "TaxNumberCredential"]
  },
  "proof": {
    "proof_type": "jwt",
    "jwt": "eyJhbGc..." // DID-bound proof of possession
  }
}

Response:
{
  "format": "jwt_vc_json-ld",
  "credential": "eyJhbGc..." // The actual signed VC
}
```

---

### 3.2 Employer Compliance Status Credential Issuance

**Periodic Automated Issuance:**

```
Every month:
1. Vastuu Group compliance agent runs checks
   ├── VAT status (from Tax Authority)
   ├── Employer register status
   ├── Insurance coverage validation
   ├── Outstanding violations check
   └── Payment status verification
              │
2. Aggregate results ◄┘
   ├── Calculate compliance score
   ├── Identify concerns
   └── Determine overall status
              │
3. Issue credential ◄─┘
   ├── Create ComplianceStatusCredential
   ├── Set expiry to next month
   └── Sign and deliver to EBW Wallet
              │
4. Notify employer ◄──┘
   ├── Email notification
   ├── Dashboard update
   └── API event (for employer's agent)
```

**Compliance Status Levels:**
- **FULL_COMPLIANCE** - All checks passed
- **MINOR_CONCERNS** - 1-2 administrative issues
- **MAJOR_CONCERNS** - Significant violations
- **NON_COMPLIANT** - Critical failures

---

## 4. Job Search and Hiring Process

### 4.1 End-to-End Process Flow

```
Phase 1: Preparation
├── Worker: Profile readiness
│   └── Agent inventories credentials, identifies gaps
└── Employer: Role modeling
    └── Agent defines requirements, publishes job

Phase 2: Matching
├── Worker agent searches suitable jobs
├── Compares requirements vs. credentials
└── Short-lists best matches

Phase 3: Application
├── Worker agent composes selective disclosure
├── Sends presentation to employer's agent
└── Employer agent receives and validates

Phase 4: Screening
├── Employer agent verifies credentials
├── Checks SHACL constraints
├── Classifies applicant (fully/partially/not qualified)
└── Requests additional evidence if needed

Phase 5: Interview
├── Agents coordinate scheduling
├── Human conducts interview
├── Agent logs interview evidence
└── Employer makes decision

Phase 6: Offer
├── Employer agent generates offer credential
├── Worker agent presents to worker
├── Worker reviews and decides
└── If accepted → proceed to contract

Phase 7: Contract
├── Employer agent generates contract
├── Both parties sign (eIDAS signatures)
├── Contract evidence created
└── Stored in both wallets

Phase 8: Onboarding
├── Employer agent orchestrates onboarding
├── Induction training
├── Site registration
├── Access credentials issued
└── OnboardingCompletionCredential created

Phase 9: Work Commencement
├── Site verifier checks credentials
├── Validates access permission
├── Logs entry
└── Work begins
```

**Timeline Example:**
```
Day 0: Worker agent finds job, applies (5 minutes)
Day 1: Employer agent screens applicant (automated)
Day 2: Interview invitation sent (automated)
Day 5: Interview conducted (human)
Day 6: Decision made, offer sent (automated)
Day 7: Worker accepts (human decision)
Day 8: Contract signed (automated + human approval)
Day 9-14: Onboarding period
Day 15: Work commences
```

---

### 4.2 Construction Worker Example Scenario

**Context:** Maria Virtanen seeks electrician role in Helsinki construction project

**Maria's Credentials (in EUDI Wallet):**
✅ PID (Finnish identity)
✅ WorkerTaxNumberCredential (valid)
✅ SafetyTrainingCredential (expires 2026-12-31)
✅ ElectricalQualificationCredential (journeyman level)
✅ EmploymentRelationshipCredential (2 years experience, previous employer)
❌ A1Certificate (not needed - working in Finland)

**Employer Requirements:**
- Finnish work permit
- Valid tax number
- Electrical qualification (journeyman or higher)
- Safety training (construction)
- 1+ years experience (preferred)

**Process:**

```
Step 1: Job Discovery
Maria's agent:
├── Scans job listings
├── Finds "Electrician - Helsinki Metro Project"
├── Matches requirements: 100% (all mandatory met)
└── Suggests application

Step 2: Application
Maria's agent:
├── Composes presentation with only required info
├── Includes:
│   ├── Identity confirmation (yes/no)
│   ├── Tax number status (valid)
│   ├── Electrical qualification (level + issuer)
│   ├── Safety training (valid until 2026)
│   └── Experience (2+ years in construction)
└── Sends to employer's agent

Step 3: Screening
Employer's agent:
├── Verifies all signatures ✓
├── Checks tax number with Tax Authority ✓
├── Validates qualification with issuer registry ✓
├── Confirms safety training not expired ✓
├── Classification: FULLY_QUALIFIED
└── Adds Maria to interview shortlist

Step 4: Interview
HR manager:
├── Reviews verified profile
├── Conducts technical interview
├── Makes hiring decision: HIRE
└── Agent generates offer

Step 5: Offer
Employer's agent:
├── Creates EmploymentOfferCredential
│   ├── Role: Electrician
│   ├── Salary: €3,500/month
│   ├── Start: 2024-02-01
│   └── Type: Permanent
├── Signs with company's EBW Wallet
└── Sends to Maria's agent

Step 6: Acceptance
Maria's agent:
├── Presents offer to Maria
├── Flags for review (legally binding)
├── Maria reviews terms
├── Maria accepts
└── Agent sends signed acceptance

Step 7: Contract
Both agents:
├── Generate contract document
├── Both parties sign (eIDAS QES)
├── ContractSignatureEvidence created
└── Stored in both wallets

Step 8: Onboarding
Employer's agent:
├── Sends induction materials
├── Schedules safety briefing (Feb 1, 08:00)
├── Registers Maria in site system
├── Generates SiteAccessCredential
└── Notifies Maria's agent

Step 9: First Day
Maria arrives at site:
├── Shows QR from EUDI Wallet
├── Site verifier scans QR
├── Verifies:
│   ├── Identity (PID) ✓
│   ├── Employment (ContractEvidence) ✓
│   ├── Safety training (valid) ✓
│   ├── Site access (credential) ✓
│   └── Induction (completed) ✓
└── Access granted → Work begins
```

**Total Time:** 10 days (vs. 3-4 weeks traditional process)

**Manual Steps:** 2 (interview, acceptance decision)

**Automated Steps:** 18

---

## 5. Construction Site Access Process

### 5.1 Daily Access Verification

**Standard Flow (Returning Worker):**

```
┌─────────────────────┐
│  Worker arrives     │
│  at site entrance   │
└──────┬──────────────┘
       │
       ▼
┌─────────────────────┐
│ 1. Opens EUDI       │
│    Wallet           │
│ 2. Selects "Site    │
│    Access"          │
│ 3. Shows QR code    │
└──────┬──────────────┘
       │
       ▼
┌──────────────────────────────┐
│  Site Verifier Tablet        │
│                              │
│ 4. Scans QR code             │
│       │                      │
│       ▼                      │
│ 5. Requests presentation     │
│    (OpenID4VP)               │
│    ┌─────────────────────┐   │
│    │ Required claims:    │   │
│    │ - Identity (PID)    │   │
│    │ - Employment        │   │
│    │ - Safety training   │   │
│    │ - Site access       │   │
│    └─────────────────────┘   │
└──────┬───────────────────────┘
       │
       ▼
┌─────────────────────┐
│  Wallet responds    │
│  with presentation  │
└──────┬──────────────┘
       │
       ▼
┌──────────────────────────────┐
│  Verifier validates          │
│  ┌───────────────────────┐   │
│  │ Check 1: Signatures   │   │
│  │ ├─ PID signed by FI   │   │
│  │ │  gov (eIDAS) ✓      │   │
│  │ ├─ Employment by      │   │
│  │ │  employer ✓         │   │
│  │ └─ Site access by     │   │
│  │    site operator ✓    │   │
│  └───────────────────────┘   │
│  ┌───────────────────────┐   │
│  │ Check 2: Validity     │   │
│  │ ├─ Not expired ✓      │   │
│  │ ├─ Not revoked ✓      │   │
│  │ └─ Dates valid ✓      │   │
│  └───────────────────────┘   │
│  ┌───────────────────────┐   │
│  │ Check 3: SHACL        │   │
│  │ ├─ Identity complete ✓│   │
│  │ ├─ Employment active ✓│   │
│  │ └─ Safety training    │   │
│  │    not expired ✓      │   │
│  └───────────────────────┘   │
│  ┌───────────────────────┐   │
│  │ Check 4: Revocation   │   │
│  │ └─ Query registry ✓   │   │
│  └───────────────────────┘   │
│       │                      │
│       ▼                      │
│  ┌───────────────────────┐   │
│  │ Decision: ALLOW       │   │
│  └───────────────────────┘   │
└──────┬───────────────────────┘
       │
       ▼
┌─────────────────────┐
│ 6. Access granted   │
│ 7. Log entry        │
│ 8. Open gate/door   │
└─────────────────────┘
```

**Verification Time:** ~2 seconds

**Offline Capability:** Yes (with cached credential + revocation list)

---

### 5.2 First-Day Access (New Worker)

**Additional Checks:**

```
Standard checks (above)
        +
Onboarding verification:
├── OnboardingCompletionCredential present?
├── Induction completed within last 7 days?
├── SiteAccessCredential issued?
└── Work commencement date is today or earlier?
        │
        ▼
If all pass:
├── Grant access
├── Create WorkCommencementEvidence
├── Store in audit log
└── Notify employer's agent

If any fail:
├── Deny access
├── Show reason on screen
├── Suggest remediation
└── Notify supervisor
```

---

### 5.3 Contractor Chain Verification

**Multi-Party Credential Check:**

```
Main Contractor site requires:
1. Worker credentials (as above)
2. Employer's compliance credentials
   ├── EBW Wallet holder
   ├── ComplianceStatusCredential (from Vastuu Group)
   ├── InsuranceCoverageCredential
   └── TilaajavastuurCredential
3. Subcontractor relationship
   ├── SubcontractorAgreementCredential
   └── Main contractor's approval

Verification process:
├── Worker presents identity + employment
│   └── Employment credential includes employer DID
├── Verifier queries employer's EBW Wallet (if authorized)
│   └── Retrieves compliance credentials
├── Verifier checks subcontractor relationship
│   └── Confirms chain: Main ← Sub ← Worker
└── If all valid → Access granted
```

---

## 6. Cross-Border Worker Posting Process

### 6.1 Scenario: Finnish Worker Posted to Sweden

**Context:** Maria Virtanen (Finnish electrician) posted by Finnish employer to Swedish construction project for 3 months

**Required Additional Credentials:**
- A1 Certificate (social security coverage)
- Posted worker notification (Swedish authorities)
- Proof of insurance coverage in Sweden

**Process Flow:**

```
Phase 1: A1 Certificate Acquisition
Maria's agent:
├── Detects posting need (from job acceptance)
├── Requests A1Certificate from Kela
│   └── Provides:
│       ├── PID
│       ├── EmploymentRelationshipCredential
│       ├── Posting details (Sweden, 3 months)
│       └── Employer confirmation
├── Kela validates and issues SocialSecurityCoverageCredential
└── Maria stores in EUDI Wallet

Phase 2: Swedish Notification
Employer's agent:
├── Prepares posted worker notification
│   └── Includes:
│       ├── Worker identity (from PID)
│       ├── A1 status
│       ├── Employer details (from EBW Wallet)
│       ├── Work location (Swedish site)
│       └── Duration (3 months)
├── Submits to Swedish Work Environment Authority
│   └── Via eDelivery/AS4 protocol
│       ├── Signed with eIDAS eSeal
│       └── Includes all credential proofs
└── Receives acknowledgment
    └── PostedWorkerRegistrationCredential

Phase 3: Insurance Coverage
Employer's agent:
├── Requests insurance extension from provider
├── Insurance provider issues:
│   └── InternationalInsuranceCoverageCredential
│       ├── Coverage: Sweden
│       ├── Duration: Posting period
│       └── Policy details
└── Delivered to employer's EBW Wallet

Phase 4: Site Access in Sweden
Maria arrives at Swedish site:
├── Shows EUDI Wallet QR
├── Swedish verifier requests:
│   ├── Identity (PID) ✓
│   ├── A1 Certificate ✓
│   ├── Posted worker registration ✓
│   ├── Safety training ✓
│   ├── Professional qualification ✓
│   └── Insurance coverage ✓
├── All credentials verified
│   └── Trust: eIDAS cross-border framework
└── Access granted

Phase 5: Compliance Monitoring
During 3-month posting:
├── Employer's agent monitors:
│   ├── A1 validity
│   ├── Insurance coverage
│   ├── Swedish working time regulations
│   └── Posting duration limits
├── Automated alerts if:
│   ├── Credentials near expiry
│   ├── Duration approaching limit
│   └── Compliance issues detected
└── Generates periodic compliance reports
```

**Key Benefits:**
- No paper A1 forms
- Automated authority notifications
- Real-time compliance monitoring
- Seamless cross-border verification
- Audit trail for all parties

---

### 6.2 Multi-Country Credential Mapping

**Semantic Interoperability in Action:**

```
Finnish Credential          Swedish Equivalent          Trust Framework
────────────────────       ───────────────────         ───────────────
SafetyTrainingCredential   Arbetsmiljöutbildning       EQF Level mapping
(FI-specific course)       (SE-specific recognition)   + eIDAS trust

ElectricalQualification    Behörighetsbevis El         EU Professional
(FI regulatory body)       (SE Elsäkerhetsverket)      Qualifications Directive

TaxNumberCredential        Skatteregistrering          EU Tax ID mapping
(FI Verohallinto)          (SE Skatteverket)           + Once-Only principle

A1Certificate              EU social security          EU Regulation 883/2004
(Kela)                     coverage proof              + EESSI network
```

**How Mapping Works:**

```
1. Swedish verifier requests "Arbetsmiljöutbildning"
2. Maria's wallet recognizes equivalent requirement
3. Agent checks semantic mapping:
   ├── SafetyTrainingCredential (FI)
   │   └── courseType: "ConstructionSafety"
   │   └── level: EQF Level 3
   ├── Maps to:
   │   └── Arbetsmiljöutbildning (SE)
   │       └── Same EQF level
   │       └── Mutual recognition agreement
4. Presents FI credential with semantic proof
5. Swedish verifier validates:
   ├── eIDAS signature trust ✓
   ├── Semantic equivalence ✓
   ├── Mutual recognition ✓
   └── Accepts credential
```

---

## Summary: Process Transformation Impact

### Before (Traditional Model)

**Worker's Experience:**
- Fill out application forms (45 minutes)
- Attach CV, certificates (find files, scan, upload)
- Wait for manual screening (3-7 days)
- Interview (scheduled by phone/email)
- Wait for offer (2-5 days)
- Sign paper contract (in person or by mail)
- Attend induction (scheduled separately)
- Access site (show paper documents)
- **Total time to first day:** 3-4 weeks

**Employer's Experience:**
- Post job listing
- Receive 50-100 applications
- Manually screen CVs (8-16 hours)
- Verify documents (call issuers, check PDFs)
- Schedule interviews manually
- Make offers by email/letter
- Process paperwork (HR systems)
- Arrange induction
- Issue access cards
- **Total HR time per hire:** 20-30 hours

### After (Wallet + Agent Model)

**Worker's Experience:**
- Agent finds suitable jobs (automated)
- Review match and apply (2 minutes)
- Automated screening (instant)
- Interview invitation (automated scheduling)
- Review offer via agent (5 minutes)
- Sign contract digitally (1 minute)
- Complete e-induction (agent-coordinated)
- Access site with QR (2 seconds)
- **Total time to first day:** 7-10 days

**Employer's Experience:**
- Define role semantically (agent-assisted, 15 minutes)
- Agent screens applicants automatically
- Review qualified candidates only (2-4 hours)
- Interviews (unchanged)
- Agent generates offer automatically
- Contract signed digitally (minutes)
- Agent orchestrates onboarding
- Agent issues access credentials
- **Total HR time per hire:** 4-6 hours

### Efficiency Gains

| Metric | Traditional | Wallet + Agent | Improvement |
|--------|-------------|----------------|-------------|
| Time to hire | 3-4 weeks | 7-10 days | **60-70% faster** |
| HR time per hire | 20-30 hours | 4-6 hours | **75-80% reduction** |
| Document handling | Manual verification | Automated | **95% reduction** |
| Compliance errors | 5-10% | <1% | **90% reduction** |
| Cross-border overhead | High (weeks) | Low (days) | **80% reduction** |
| Worker data entry | Repeated per job | Once (wallet) | **100% elimination** |

---

**End of Process Description Document**

*For technical architecture details, see [TECHNICAL_ARCHITECTURE.md](TECHNICAL_ARCHITECTURE.md)*
