# Implementation Roadmap

**Vastuu Group eIDAS 2.0 Transformation - 18-Month Plan**

---

## Timeline Overview

```
2024 Q2-Q3: Foundation & Semantics (Phase 1-2)
2024 Q4-2025 Q1: Core Infrastructure (Phase 3-4)
2025 Q2-Q3: Production Deployment (Phase 5-6)
```

---

## Phase 1: Semantic Modeling (3-4 months)

**Duration:** May 2024 - August 2024

### Objectives
- Complete semantic vocabulary definition
- Map existing data to OWL/SHACL models
- Harmonize with EU vocabularies
- Create JSON-LD contexts

### Deliverables

| # | Deliverable | Owner | Status |
|---|-------------|-------|--------|
| 1.1 | Current data inventory | Data team | 📋 Planned |
| 1.2 | OWL ontology (vocabulary.ttl) | Semantic architect | 📋 Planned |
| 1.3 | SHACL shapes (shapes.ttl) | Semantic architect | 📋 Planned |
| 1.4 | SKOS concept schemes | Semantic architect | 📋 Planned |
| 1.5 | JSON-LD contexts | Developer | 📋 Planned |
| 1.6 | EU vocabulary alignment | Semantic architect | 📋 Planned |
| 1.7 | Mapping documentation | Technical writer | 📋 Planned |

### Milestones
- **M1.1** (Week 4): Current state analysis complete
- **M1.2** (Week 8): Draft OWL ontology
- **M1.3** (Week 12): SHACL shapes validated
- **M1.4** (Week 16): All deliverables complete

### Budget
- **Personnel:** 2 semantic architects, 1 developer (4 months)
- **Tools:** Protégé, TopBraid, SHACL validation tools
- **External:** Consultation with EU vocabulary experts
- **Total:** €120,000 - €150,000

---

## Phase 2: VC Model Definition (2-3 months)

**Duration:** July 2024 - September 2024  
*(Overlaps with Phase 1)*

### Objectives
- Design W3C VC schemas for all attestations
- Create credential templates
- Define validation rules
- Build test suite

### Deliverables

| # | Deliverable | Owner | Status |
|---|-------------|-------|--------|
| 2.1 | Credential type catalog | Product owner | 📋 Planned |
| 2.2 | JSON Schema definitions | Developer | 📋 Planned |
| 2.3 | Credential templates | Developer | 📋 Planned |
| 2.4 | Validation test suite | QA engineer | 📋 Planned |
| 2.5 | Example credentials | Developer | 📋 Planned |
| 2.6 | Documentation | Technical writer | 📋 Planned |

### Key Credential Types
1. WorkerTaxNumberCredential
2. EmploymentRelationshipCredential
3. SocialSecurityCoverageCredential (A1)
4. ProfessionalQualificationCredential
5. SafetyTrainingCredential
6. ComplianceStatusCredential
7. CompanyIdentityCredential
8. VATRegistrationCredential
9. InsuranceCoverageCredential
10. MandateCredential

### Milestones
- **M2.1** (Week 4): Credential catalog complete
- **M2.2** (Week 8): Schemas and templates ready
- **M2.3** (Week 12): Test suite complete

### Budget
- **Personnel:** 2 developers, 1 QA engineer (3 months)
- **Tools:** JSON Schema validators, test frameworks
- **Total:** €90,000 - €110,000

---

## Phase 3: Issuer Service (4-6 months)

**Duration:** September 2024 - February 2025

### Objectives
- Build credential issuance infrastructure
- Integrate with source systems
- Implement signing service
- Deploy to staging environment

### Deliverables

| # | Deliverable | Owner | Status |
|---|-------------|-------|--------|
| 3.1 | Issuer service API | Backend team | 📋 Planned |
| 3.2 | Tax Authority integration | Integration developer | 📋 Planned |
| 3.3 | Kela integration | Integration developer | 📋 Planned |
| 3.4 | Employer data integration | Integration developer | 📋 Planned |
| 3.5 | Signing service (HSM) | Security engineer | 📋 Planned |
| 3.6 | DID registry | Backend developer | 📋 Planned |
| 3.7 | Revocation registry | Backend developer | 📋 Planned |
| 3.8 | Admin dashboard | Frontend developer | 📋 Planned |
| 3.9 | Audit logging system | Backend developer | 📋 Planned |
| 3.10 | API documentation | Technical writer | 📋 Planned |

### Technical Stack
- **Backend:** Node.js 20+ / TypeScript
- **Database:** PostgreSQL 15, Redis, MongoDB
- **DID Method:** did:ebsi
- **Signing:** HSM + eIDAS QTSP
- **Deployment:** Docker + Kubernetes

### Milestones
- **M3.1** (Month 2): Core API functional
- **M3.2** (Month 3): Tax Authority integration complete
- **M3.3** (Month 4): All integrations complete
- **M3.4** (Month 5): Signing service operational
- **M3.5** (Month 6): Staging deployment complete

### Budget
- **Personnel:** 3 backend developers, 2 integration developers, 1 security engineer, 1 DevOps (6 months)
- **Infrastructure:** Cloud services (Azure/AWS)
- **HSM:** Hardware Security Module purchase/lease
- **QTSP Integration:** eIDAS qualified service provider
- **Total:** €400,000 - €500,000

---

## Phase 4: Wallet Integration (3-4 months)

**Duration:** December 2024 - March 2025  
*(Overlaps with Phase 3)*

### Objectives
- Develop/integrate EUDI Wallet for workers
- Develop/integrate EBW Wallet for employers
- Implement OpenID4VCI/VP protocols
- Build mobile applications

### Deliverables

| # | Deliverable | Owner | Status |
|---|-------------|-------|--------|
| 4.1 | EUDI Wallet SDK | Mobile team | 📋 Planned |
| 4.2 | EUDI Wallet mobile app (iOS) | iOS developer | 📋 Planned |
| 4.3 | EUDI Wallet mobile app (Android) | Android developer | 📋 Planned |
| 4.4 | EBW Wallet web app | Frontend team | 📋 Planned |
| 4.5 | EBW Wallet mobile app | Mobile team | 📋 Planned |
| 4.6 | Wallet SDK documentation | Technical writer | 📋 Planned |
| 4.7 | User guides | UX writer | 📋 Planned |

### Technology
- **Mobile:** React Native (cross-platform)
- **Web:** Next.js 14 + React 18
- **Security:** Biometric auth, encrypted storage
- **Protocols:** OpenID4VCI, OpenID4VP

### Milestones
- **M4.1** (Month 2): Wallet SDK functional
- **M4.2** (Month 3): Mobile apps beta
- **M4.3** (Month 4): Production-ready wallets

### Budget
- **Personnel:** 2 mobile developers, 2 frontend developers, 1 UX designer (4 months)
- **App Store fees:** Apple + Google Play
- **Security audit:** Mobile app security review
- **Total:** €250,000 - €300,000

---

## Phase 5: Verification Services (3-4 months)

**Duration:** February 2025 - May 2025  
*(Overlaps with Phase 4)*

### Objectives
- Build construction site verifier app
- Create client verification APIs
- Implement compliance dashboard
- Deploy verifier infrastructure

### Deliverables

| # | Deliverable | Owner | Status |
|---|-------------|-------|--------|
| 5.1 | Verifier mobile app | Mobile team | 📋 Planned |
| 5.2 | Verifier web portal | Frontend team | 📋 Planned |
| 5.3 | Verification API | Backend team | 📋 Planned |
| 5.4 | Compliance dashboard | Frontend team | 📋 Planned |
| 5.5 | Offline verification support | Mobile developer | 📋 Planned |
| 5.6 | Integration guides | Technical writer | 📋 Planned |

### Features
- QR code scanning
- Signature validation
- SHACL constraint checking
- Revocation status checks
- Offline capability (cached credentials + revocation lists)
- Multi-language support (FI/EN/SE)

### Milestones
- **M5.1** (Month 2): Verifier app functional
- **M5.2** (Month 3): API complete
- **M5.3** (Month 4): Production deployment

### Budget
- **Personnel:** 2 mobile developers, 2 backend developers, 1 frontend developer (4 months)
- **Total:** €200,000 - €250,000

---

## Phase 6: EU Interoperability & Production Launch (6-12 months)

**Duration:** April 2025 - December 2025

### Objectives
- Obtain eIDAS QTSP certification
- Establish cross-border credential mapping
- Build acceptance network
- Launch production service
- Migrate existing users

### Deliverables

| # | Deliverable | Owner | Status |
|---|-------------|-------|--------|
| 6.1 | QTSP certification | Security team | 📋 Planned |
| 6.2 | Cross-border mapping rules | Semantic architect | 📋 Planned |
| 6.3 | EBSI integration | Integration developer | 📋 Planned |
| 6.4 | EU wallet provider agreements | Business development | 📋 Planned |
| 6.5 | Migration tools | Backend team | 📋 Planned |
| 6.6 | User training materials | Training team | 📋 Planned |
| 6.7 | Production deployment | DevOps team | 📋 Planned |
| 6.8 | Monitoring & alerting | DevOps engineer | 📋 Planned |

### Key Activities

#### QTSP Certification (6 months)
- Submit application to Finnish regulatory authority
- Security audit
- Compliance documentation
- Certification review
- Certificate issuance

#### Cross-Border Integration (3 months)
- Map Finnish credentials to EU equivalents
- Integrate with EBSI (European Blockchain Services Infrastructure)
- Bilateral agreements with Swedish, German wallet providers
- Test cross-border scenarios

#### Production Launch (3 months)
- Pilot with 5-10 construction companies
- Migrate 1,000 workers as initial wave
- Monitor performance and issues
- Full production rollout

### Milestones
- **M6.1** (Month 3): QTSP application submitted
- **M6.2** (Month 6): Cross-border mapping complete
- **M6.3** (Month 9): QTSP certification obtained
- **M6.4** (Month 10): Pilot launch
- **M6.5** (Month 12): Production launch

### Budget
- **Personnel:** Full team (6-12 months)
- **QTSP Certification:** €100,000 - €150,000
- **Legal & Compliance:** €50,000 - €75,000
- **Marketing & Training:** €75,000 - €100,000
- **Total:** €650,000 - €850,000

---

## Phase 7: AI Agent Development (Optional - Post-Launch)

**Duration:** Q1 2026 - Q4 2026

### Objectives
- Build AI agent platform
- Develop worker AI agent
- Develop employer AI agent
- Integrate with wallets and issuer

### Deliverables

| # | Deliverable | Owner | Status |
|---|-------------|-------|--------|
| 7.1 | Agent platform architecture | AI architect | 📋 Future |
| 7.2 | Worker agent (job search) | AI team | 📋 Future |
| 7.3 | Employer agent (screening) | AI team | 📋 Future |
| 7.4 | DIDComm integration | Backend developer | 📋 Future |
| 7.5 | LLM integration (GPT-4/Claude) | AI engineer | 📋 Future |
| 7.6 | Semantic reasoner | AI engineer | 📋 Future |
| 7.7 | Policy engine | Backend developer | 📋 Future |
| 7.8 | Agent UI/UX | UX team | 📋 Future |

### Technology
- **AI/ML:** GPT-4 API, Claude API
- **Semantic Reasoning:** SPARQL engine (Apache Jena)
- **Agent Communication:** DIDComm v2
- **Backend:** Python (for ML) + TypeScript (for integration)

### Budget
- **Personnel:** 2 AI engineers, 2 backend developers, 1 UX designer (12 months)
- **LLM API costs:** Variable (usage-based)
- **Total:** €500,000 - €700,000

---

## Total Budget Summary

| Phase | Duration | Cost (€) |
|-------|----------|----------|
| Phase 1: Semantic Modeling | 4 months | 120,000 - 150,000 |
| Phase 2: VC Model Definition | 3 months | 90,000 - 110,000 |
| Phase 3: Issuer Service | 6 months | 400,000 - 500,000 |
| Phase 4: Wallet Integration | 4 months | 250,000 - 300,000 |
| Phase 5: Verification Services | 4 months | 200,000 - 250,000 |
| Phase 6: EU Interoperability & Launch | 12 months | 650,000 - 850,000 |
| **Total (Phases 1-6)** | **18 months** | **1,710,000 - 2,160,000** |
| Phase 7: AI Agents (Optional) | 12 months | 500,000 - 700,000 |
| **Grand Total (with AI)** | **30 months** | **2,210,000 - 2,860,000** |

---

## Risk Management

### High-Priority Risks

| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|------------|
| eIDAS QTSP certification delay | Medium | High | Early application, engage consultants |
| Source system integration failures | Medium | High | Fallback to manual processes initially |
| Wallet adoption low | Medium | High | Strong user onboarding, marketing |
| Cross-border acceptance limited | Low | Medium | Focus on Nordic countries first |
| Security breach | Low | Critical | Security audits, penetration testing |
| Regulatory changes | Medium | Medium | Monitor EU policy developments |

### Contingency Plans

1. **If QTSP delayed:** Use non-qualified signatures temporarily
2. **If source integration fails:** Manual credential issuance workflow
3. **If wallet adoption slow:** Hybrid model (wallet + legacy PDF)
4. **If budget overrun:** Descope AI agents (Phase 7)

---

## Success Metrics

### Technical KPIs
- **Credential issuance time:** < 30 seconds
- **Verification time:** < 2 seconds
- **System uptime:** 99.9%
- **API response time:** < 200ms (p95)

### Business KPIs
- **Wallet adoption:** 10,000 workers by end of Year 1
- **Employer adoption:** 200 companies by end of Year 1
- **Credential issuance:** 50,000 credentials in Year 1
- **Cross-border usage:** 5% of verifications from other EU countries

### User Experience KPIs
- **App store rating:** > 4.0 stars
- **User satisfaction:** > 80% positive feedback
- **Support tickets:** < 5% of active users per month

---

## Dependencies

### External Dependencies
1. **EBSI Infrastructure:** EU blockchain services availability
2. **eIDAS QTSP:** Certification timeline
3. **Source Systems:** Tax Authority, Kela API availability
4. **EU Wallet Standards:** Final ARF (Architecture Reference Framework) specification
5. **EUDI Wallet Ecosystem:** Other wallet providers

### Internal Dependencies
1. **Stakeholder Buy-in:** Executive approval, budget allocation
2. **Data Governance:** Privacy impact assessment, GDPR compliance
3. **HR Capacity:** Hiring/training technical team
4. **Infrastructure:** Cloud contracts, HSM procurement

---

## Next Steps (Immediate Actions)

### Month 1 (Now)
1. ✅ **Approve roadmap** - Executive decision
2. ✅ **Allocate budget** - Finance approval
3. ✅ **Form core team** - Hire semantic architect, lead developer
4. ✅ **Kickoff Phase 1** - Begin current state analysis

### Month 2
1. **Data inventory** - Map existing worker/employer data
2. **Semantic research** - Review EU vocabularies (Core, NCBV, EBW)
3. **Technology selection** - Finalize tech stack
4. **Partner engagement** - Contact QTSP candidates

### Month 3
1. **Draft OWL ontology** - First version of vocabulary
2. **Proof of concept** - Simple credential issuance demo
3. **Stakeholder demos** - Show progress to management
4. **Hire additional team** - Ramp up for Phase 3

---

**Repository:** https://github.com/jgmikael/vastuu-group-eidas-transformation  
**Owner:** Mikael  
**Last Updated:** 2024-04-20
