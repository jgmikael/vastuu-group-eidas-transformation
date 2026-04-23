# Use Case: Construction Worker Access to Site

**Scenario:** Maria Virtanen, a Finnish electrician, starts work at a Helsinki construction project

---

## Actors

- **Maria Virtanen** - Worker (Electrician)
- **Construction Co Ltd** - Employer
- **Helsinki Metro Extension Project** - Construction site operator (main contractor)
- **Vastuu Group** - Credential issuer
- **Finnish Tax Administration** - Tax number issuer
- **Kela** - Social security (when cross-border posting)

---

## Preconditions

1. Maria has EUDI Wallet installed on her mobile phone
2. Construction Co Ltd has EBW Wallet (company identity)
3. Helsinki Metro site uses Vastuu Group verification system
4. Maria has been hired by Construction Co Ltd

---

## Flow: First Day at New Construction Site

### Phase 1: Credential Acquisition (Before Starting Work)

**Step 1: Maria Receives Employment Contract**
- Construction Co Ltd offers Maria a permanent electrician position
- Employment offer sent as `EmploymentOfferCredential` to Maria's EUDI Wallet
- Maria reviews and accepts via wallet

**Step 2: Tax Number Credential Issuance**
- Maria requests tax number credential from Vastuu Group issuer
- Vastuu Group queries Finnish Tax Administration API
- Tax Administration confirms: "FI12345678, Status: Active"
- Vastuu Group issues `TaxNumberCredential` to Maria's wallet
- **Credential contains:**
  - Tax number: FI12345678
  - Status: Active
  - Valid until: 2024-12-31
  - Issuer: Vastuu Group (trusted by Tax Administration)

**Step 3: Safety Training Credential**
- Maria completed construction safety training 6 months ago
- Training provider issued `SafetyTrainingCredential` to her wallet
- **Credential contains:**
  - Course type: ConstructionSafety
  - Completion date: 2023-10-15
  - Expiry date: 2026-10-15
  - Issuer: Finnish Safety Training Center

**Step 4: Professional Qualification Credential**
- Maria's electrical qualification from 2018
- Issued by Finnish qualification authority
- **Credential contains:**
  - Qualification type: ElectricalInstallation
  - Level: Journeyman (EQF Level 4)
  - Issued: 2018-06-15
  - No expiry (permanent qualification)

---

### Phase 2: Site Induction (Day Before Starting)

**Step 5: Employer Registers Maria with Site Operator**
- Construction Co Ltd's HR system (integrated with EBW Wallet) sends notification to Helsinki Metro site
- Notification includes:
  - Maria's DID (identifier)
  - Employment relationship confirmation
  - Start date: 2024-02-01
  - Required credentials list (tax number, safety training, electrical qualification)

**Step 6: Site Operator Creates Access Credential**
- Helsinki Metro site verifier system checks Maria's credentials (via verifiable presentation request)
- Maria's wallet responds with:
  - ✅ TaxNumberCredential (status: valid)
  - ✅ SafetyTrainingCredential (not expired)
  - ✅ ElectricalQualificationCredential (valid)
- Site operator issues `SiteAccessCredential` to Maria's wallet
- **Credential contains:**
  - Site: Helsinki Metro Extension
  - Access zones: Electrical installation areas
  - Valid from: 2024-02-01
  - Valid until: 2024-06-30 (renewable)

**Step 7: Site Induction Completion**
- Maria attends 2-hour site-specific induction
- Induction instructor marks completion in site system
- Site operator issues `OnboardingCompletionCredential` to Maria's wallet
- **Credential contains:**
  - Site: Helsinki Metro Extension
  - Induction date: 2024-01-31
  - Topics covered: Site layout, emergency procedures, specific risks
  - Valid for: This project only

---

### Phase 3: Daily Site Access (Typical Morning)

**Step 8: Maria Arrives at Site (Day 1 - 06:45)**
- Maria approaches site entrance with security gate
- Security guard has tablet with Vastuu Group verifier app

**Step 9: Presentation Request**
- Maria opens EUDI Wallet
- Taps "Site Access"
- Wallet displays QR code

**Step 10: Verification**
- Security guard scans QR code
- Verifier app requests presentation:
  ```
  Required credentials:
  - Identity (PID)
  - Employment relationship
  - Site access credential
  - Safety training
  - Onboarding completion
  ```

**Step 11: Maria Approves Sharing**
- Wallet shows: "Helsinki Metro Extension requests verification"
- Maria sees exactly what will be shared (selective disclosure):
  - ✅ Identity confirmed (yes/no) - name NOT shared
  - ✅ Employed by Construction Co Ltd (yes/no)
  - ✅ Site access valid (yes/no)
  - ✅ Safety training current (yes/no)
  - ✅ Induction completed (yes/no)
- Maria taps "Share"

**Step 12: Instant Verification**
- Verifier validates in 2 seconds:
  1. Cryptographic signatures ✓ (all issuers trusted)
  2. SHACL constraints ✓ (data structure valid)
  3. Revocation status ✓ (none revoked)
  4. Temporal validity ✓ (all credentials current)
  5. Site-specific rules ✓ (access credential matches this site)

**Step 13: Access Granted**
- Verifier displays: ✅ "Access Granted - Maria Virtanen, Electrician"
- Gate opens
- Entry logged (timestamp + Maria's DID)
- Maria enters site

**Total time: ~5 seconds** (vs. 2-3 minutes with manual document check)

---

### Phase 4: On-Site Verification (Supervisor Check)

**Step 14: Electrical Work Area Access (08:00)**
- Maria needs to access restricted electrical equipment room
- Area supervisor uses verifier app to check credentials
- Requests specific verification:
  ```
  Required:
  - Electrical qualification (journeyman or higher)
  - Site access (electrical zones)
  ```

**Step 15: Qualified Access Confirmation**
- Maria's wallet presents only relevant credentials
- Supervisor sees:
  - ✅ Electrical qualification: Journeyman level
  - ✅ Site access: Includes electrical installation zones
- Access approved instantly

---

### Phase 5: Compliance Audit (Spot Check by Authorities)

**Step 16: Labour Inspector Visit (10:30)**
- Regional labour inspector arrives for random compliance check
- Inspector wants to verify:
  - Workers have valid employment relationships
  - Tax numbers are current
  - Safety training requirements met

**Step 17: Maria's Credentials Checked**
- Inspector uses official verifier app (trusted by Finnish authorities)
- Requests:
  ```
  - Employment status
  - Tax number validity
  - Safety training
  ```
- Maria's wallet presents credentials
- Inspector verifies:
  - ✅ Employment: Active (Construction Co Ltd)
  - ✅ Tax number: Valid (FI12345678, Active)
  - ✅ Safety training: Valid until 2026-10-15

**Step 18: Compliance Confirmed**
- Inspector records: "Maria Virtanen - Compliant"
- No paperwork exchanged
- Audit evidence automatically logged in inspector's system

---

## Benefits Demonstrated

### For Maria (Worker):
- ✅ **No paper documents** - Everything in one wallet
- ✅ **Privacy control** - Selective disclosure (shares only what's needed)
- ✅ **Fast access** - 5 seconds vs. 2-3 minutes
- ✅ **Portable credentials** - Can use at any site
- ✅ **No repeated data entry** - One-time issuance, multiple uses

### For Construction Co Ltd (Employer):
- ✅ **Automated compliance** - Real-time credential verification
- ✅ **Reduced HR burden** - No manual document collection
- ✅ **Audit trail** - All verifications logged
- ✅ **Fast onboarding** - Maria ready to work in hours, not days

### For Helsinki Metro Site (Client/Main Contractor):
- ✅ **Security** - Cryptographic verification (can't forge credentials)
- ✅ **Compliance** - Instant confirmation of all requirements
- ✅ **Efficiency** - Guards spend less time checking documents
- ✅ **Audit** - Complete access logs with credential verification proof

### For Authorities (Labour Inspectors):
- ✅ **Instant verification** - No need to call employers/tax office
- ✅ **Tamper-proof** - Cryptographic signatures prevent fraud
- ✅ **Complete data** - All required info in standardized format
- ✅ **Audit trail** - Who was verified, when, and what was checked

---

## Alternative Scenarios

### Scenario A: Expired Safety Training
**What if Maria's safety training had expired?**

- Step 10 verification would fail:
  - ❌ Safety training expired 2024-01-15
- Verifier displays: "Access Denied - Safety training expired"
- Maria notified: "Renew safety training before accessing site"
- Maria can:
  1. Book refresher course
  2. Complete training
  3. Receive updated credential
  4. Return to site next day

**Key point:** System prevents non-compliant access automatically

---

### Scenario B: Revoked Credential
**What if Construction Co Ltd terminated Maria's employment?**

- Employer revokes `EmploymentRelationshipCredential`
- Revocation recorded in status list
- Next verification attempt:
  - ❌ Employment credential revoked
- Site access denied
- Maria cannot access site (even with SiteAccessCredential)

**Key point:** Real-time revocation prevents former employees from accessing sites

---

### Scenario C: Cross-Border Worker (Posted to Sweden)
**What if Maria is posted to Swedish construction project?**

Additional credentials needed:
- `SocialSecurityCoverageCredential` (A1 certificate from Kela)
- `PostedWorkerRegistrationCredential` (from Swedish authorities)

Flow:
1. Maria's AI agent detects posting requirement
2. Agent requests A1 certificate from Kela (automated)
3. Employer's agent submits posted worker notification to Sweden (automated)
4. Swedish site verifier recognizes Finnish credentials via eIDAS trust
5. Verification successful (cross-border interoperability)

**Key point:** Same wallet, same credentials, works across EU borders

---

## Technical Details

### Credentials Involved

| Credential | Issuer | Format | Validity |
|------------|--------|--------|----------|
| TaxNumberCredential | Vastuu Group | W3C VC JSON-LD | Annual |
| SafetyTrainingCredential | Training Center | W3C VC JSON-LD | 3 years |
| ElectricalQualificationCredential | Qualification Authority | W3C VC JSON-LD | Permanent |
| EmploymentRelationshipCredential | Construction Co Ltd | W3C VC JSON-LD | Duration of employment |
| SiteAccessCredential | Helsinki Metro Site | W3C VC JSON-LD | Project duration |
| OnboardingCompletionCredential | Helsinki Metro Site | W3C VC JSON-LD | Project duration |

### Verification Checks

```typescript
interface VerificationChecks {
  signatureValidation: boolean; // eIDAS trust chain
  shaclValidation: boolean; // Semantic constraints
  revocationCheck: boolean; // Not revoked
  temporalValidation: boolean; // Currently valid
  siteSpecificRules: boolean; // Site access zones match
}
```

### Trust Framework

```
Root: eIDAS Trust List (EU-level)
  ├─► Finnish Tax Administration (QTSP)
  │     └─► Vastuu Group (Issuer)
  │           └─► TaxNumberCredential
  ├─► Training Center (Qualified)
  │     └─► SafetyTrainingCredential
  └─► Construction Co Ltd (EBW Wallet)
        └─► EmploymentRelationshipCredential
```

---

## Success Metrics (After 6 Months)

- **Workers using wallets:** 1,500 at Helsinki Metro project
- **Daily verifications:** ~3,000 (workers arriving + spot checks)
- **Average verification time:** 4 seconds (vs. 2.5 minutes before)
- **Compliance violations:** Reduced by 85%
- **Document fraud attempts:** 0 (vs. 2-3 per month before)
- **Worker satisfaction:** 92% positive feedback
- **Site manager satisfaction:** 95% positive feedback

---

**Related Documents:**
- [PROCESS_DESCRIPTION.md](../PROCESS_DESCRIPTION.md) - Complete process flows
- [TECHNICAL_ARCHITECTURE.md](../TECHNICAL_ARCHITECTURE.md) - System architecture
- [ROADMAP.md](../ROADMAP.md) - Implementation plan
