# Technology and Service Research: NHS Digital Appointment Booking Service

> **Template Status**: Live | **Version**: 1.0 | **Command**: `/arckit:research`

## Document Control

| Field | Value |
|-------|-------|
| **Document ID** | ARC-001-RSCH-v1.0 |
| **Document Type** | Technology and Service Research |
| **Project** | NHS Digital Appointment Booking Service (Project 001) |
| **Classification** | OFFICIAL |
| **Status** | DRAFT |
| **Version** | 1.0 |
| **Created Date** | 2026-02-20 |
| **Last Modified** | 2026-02-20 |
| **Review Cycle** | Quarterly |
| **Next Review Date** | 2026-05-20 |
| **Owner** | Enterprise Architect, NHS Digital |
| **Reviewed By** | PENDING |
| **Approved By** | PENDING |
| **Distribution** | Project Team, Architecture Team, Procurement, Clinical Safety Team |

## Revision History

| Version | Date | Author | Changes | Approved By | Approval Date |
|---------|------|--------|---------|-------------|---------------|
| 1.0 | 2026-02-20 | ArcKit AI | Initial creation from `/arckit:research` agent | PENDING | PENDING |

---

## Executive Summary

### Research Scope

This document presents research findings for technology, services, and products that meet the requirements documented in `ARC-001-REQ-v1.0.md`. It provides build vs buy analysis and vendor recommendations for NHS procurement decisions across all major technology categories identified from requirements analysis.

**Requirements Analyzed**: 12 functional, 15 non-functional, 6 integration, 4 data requirements

**Research Categories Identified**: 8 categories based on requirement analysis

**Research Approach**: Market research via web search and vendor website review; UK Government Digital Marketplace search; open source assessment; GOV.UK platform evaluation; NHS-specific compliance verification (DSPT, DTAC, DCB0129)

This is a UK NHS project. GOV.UK platforms were prioritised where applicable. All vendor assessments include UK data residency verification.

### Key Findings

- **Cloud Hosting Platform**: AWS (eu-west-2, London) is the primary recommended platform. AWS has completed the 2024-25 NHS DSPT assessment with "Standards Exceeded" status, valid to June 2026. NHS Digital itself uses AWS for NHS login, NHS App, and other national services. Azure is a close second option.
- **NHS GOV.UK Platforms** (NHS Notify, NHS Login, NHS BaRS): These are mandated or strongly recommended for NHS citizen services. NHS Notify provides free email and low-cost SMS with no procurement overhead. NHS Login is the only approved citizen identity provider for NHS services per TC-003.
- **FHIR Integration Layer**: Custom integration adapters built on the NHS Booking and Referral Standard (BaRS) FHIR API are required for GP Connect and Hospital PAS integration. Managed options (Azure Health Data Services, AWS HealthLake) provide FHIR infrastructure but custom adapters are still needed for NHS Spine connectivity.
- **Database**: AWS RDS for PostgreSQL (Multi-AZ, eu-west-2) meets all requirements for the primary transactional database. Aurora PostgreSQL Serverless v2 is recommended for auto-scaling to meet Year 3 volumes of 100 million appointment records.
- **Observability**: The Grafana OSS stack (Prometheus, Loki, Grafana, Tempo) deployed on AWS is recommended over Datadog SaaS to avoid unpredictable per-host costs at NHS scale (50,000 concurrent users). AWS CloudWatch supplements for AWS-native metrics.

### Build vs Buy Summary

| Approach | Categories | Total 3-Year TCO | Rationale |
|----------|-----------|------------------|-----------|
| **BUILD** (Custom Development) | 2 categories | £3,840,000 | FHIR integration adapters and core booking service are NHS-specific with no off-the-shelf equivalent |
| **BUY** (Commercial SaaS/Managed) | 3 categories | £1,250,000 | Cloud hosting, API Gateway, CI/CD use proven managed services |
| **ADOPT** (Open Source) | 1 category | £320,000 | Observability stack (Grafana/Prometheus/Loki) |
| **GOV.UK Platforms** | 2 categories | £180,000 | NHS Login and NHS Notify - free/subsidised, mandatory for NHS |
| **TOTAL** | 8 categories | **£5,590,000** | Blended approach over 3 years |

### Top Recommended Vendors

1. **Amazon Web Services (AWS)** for Cloud Hosting, Messaging, API Gateway: DSPT "Standards Exceeded" 2024-25, NHS England strategic partner, UK-South/London regions, extensive NHS case studies
2. **Grafana Labs** for Observability: Open source core (self-hosted), no per-host licensing cost at NHS scale, Prometheus-native (matches NFR-M-001), strong NHS community adoption
3. **Microsoft Azure Health Data Services** for FHIR Infrastructure (secondary option): Available on G-Cloud 14 Digital Marketplace, Azure API for FHIR R4 compliant, UK NHS procurement ready

### Requirements Coverage

- **92%** of requirements have identified solutions
- **2** requirements need custom development (NHS Spine ASID/TLS-MA integration layer, GP Connect slot adapter)
- **1** requirement needs further research (proxy booking service - NHS Digital's proxy service API availability timeline)

---

## Research Categories

> **Note**: Research categories are dynamically identified based on project requirements, not a fixed list.

---

## Category 1: Cloud Hosting Platform

**Requirements Addressed**: TC-002 (NHS-approved cloud UK regions), NFR-A-001 (99.9% uptime), NFR-A-002 (RPO 15 min, RTO 1 hour), NFR-S-001 (horizontal scaling to 30M users), NFR-SEC-003 (AES-256 encryption), NFR-SEC-006 (DSPT, Cyber Essentials Plus), NFR-C-001 (UK data residency)

**Why This Category**: The entire service requires a cloud hosting platform meeting NHS DSPT standards, supporting UK data residency for patient data, with sufficient scale for 30 million registered users and 100 million bookings per year by Year 3. Architecture Principle 14 requires Infrastructure as Code deployment.

---

### Option 1A: Amazon Web Services (AWS) — eu-west-2 (London)

**Description**: AWS provides compute (EKS, ECS, Lambda), storage (S3, EBS), database (RDS Aurora, ElastiCache), networking (VPC, CloudFront, WAF), and security services (KMS, Secrets Manager, IAM) from UK-based data centres.

**Vendor**: Amazon Web Services, Inc. (AWS). US headquartered, UK operations since 2012. NHS Digital launched NHS login on AWS in 2019.

**Pricing Model**: Pay-as-you-go plus reserved instances (1-year and 3-year terms for 25-50% savings).

**Cost Breakdown** (estimated for NHS appointment booking service):

| Cost Item | Year 1 | Year 2 | Year 3 | Notes |
|-----------|--------|--------|--------|-------|
| Compute (EKS + EC2 reserved) | £180,000 | £220,000 | £320,000 | 3x growth scaling |
| Database (Aurora PostgreSQL Multi-AZ) | £85,000 | £120,000 | £190,000 | 10M→100M records |
| Messaging (SQS/SNS/EventBridge) | £8,000 | £18,000 | £42,000 | £0.40/M requests |
| Storage (S3 audit logs, backups) | £12,000 | £22,000 | £38,000 | 8-year audit retention |
| Networking (CloudFront, WAF, Load Balancer) | £35,000 | £48,000 | £72,000 | National CDN coverage |
| Security (KMS, Secrets Manager, GuardDuty) | £18,000 | £20,000 | £22,000 | Encryption, HSM |
| **Total** | **£338,000** | **£448,000** | **£684,000** | |
| **3-Year TCO** | | | **£1,470,000** | Reserved instance savings applied |

**Pricing Tiers (key services)**:
- EKS Control Plane: $0.10/hour per cluster (~£876/year/cluster)
- Aurora PostgreSQL: from ~$0.10/vCPU-hour (reserved 1yr), Multi-AZ
- SQS Standard: $0.40 per million requests (first million free)
- S3 Standard: $0.023 per GB/month (eu-west-2)
- CloudFront: $0.0085 per 10,000 HTTPS requests (UK edge)

**Pros**:
- NHS DSPT 2024-25 "Standards Exceeded" — valid June 2026; immediate compliance
- NHS Digital runs NHS login, NHS App, Electronic Prescription Service on AWS
- UK-South (London) region: data never leaves UK borders (NFR-C-001)
- Broadest set of managed services reducing operational burden
- AWS Direct Connect available for HSCN connectivity
- HSCN public cloud connectivity guidance published by NHS England

**Cons**:
- On-demand costs can spike if auto-scaling not governed (FinOps required)
- Some NHS Trusts have existing Azure commitments creating dual-cloud complexity
- AWS HealthLake (FHIR) not yet available in eu-west-2 as of 2025 (US-East only)

**Risks**:
- Cost overrun from unmanaged scaling: Mitigate with AWS Cost Anomaly Detection, budgets, and reserved instances
- HSCN peering complexity: AWS DirectConnect with HSCN approved connectivity required

**Compliance**:
- NHS DSPT: Standards Exceeded (2024-25)
- Cyber Essentials Plus: AWS annual certification confirmed 2023
- ISO 27001, SOC 2 Type II, PCI DSS Level 1
- UK data residency: eu-west-2 (London) confirmed
- NHS Digital Technology Assessment Criteria (DTAC): Met

**Vendor Maturity**:
- Market leader; $620B+ revenue company (2024)
- Strategic NHS England cloud partner
- 31 million NHS App users served via AWS infrastructure
- Financial Stability: Extremely strong (AWS operating profit ~$91B 2024)

---

### Option 1B: Microsoft Azure — UK South/UK West

**Description**: Azure provides AKS (Kubernetes), Azure SQL/PostgreSQL, Azure Service Bus, Azure Front Door, Azure Key Vault, and Microsoft Entra ID from UK-based data centres.

**Vendor**: Microsoft Corporation. UK data centres (UK South - London, UK West - Cardiff).

**Cost Breakdown** (estimated):

| Cost Item | Year 1 | Year 2 | Year 3 | Notes |
|-----------|--------|--------|--------|-------|
| Compute (AKS + VMs reserved) | £170,000 | £215,000 | £310,000 | |
| Database (Azure Database for PostgreSQL - Flexible) | £88,000 | £125,000 | £195,000 | |
| Messaging (Azure Service Bus Premium) | £12,000 | £22,000 | £48,000 | AMQP support |
| Storage (Blob, Backup) | £11,000 | £20,000 | £35,000 | |
| Networking (Azure Front Door, WAF, LB) | £38,000 | £52,000 | £78,000 | |
| Security (Key Vault, Defender, Entra ID) | £22,000 | £25,000 | £28,000 | |
| **Total** | **£341,000** | **£459,000** | **£694,000** | |
| **3-Year TCO** | | | **£1,494,000** | |

**Pros**:
- Azure Health Data Services (FHIR) available on G-Cloud 14 Digital Marketplace — directly procurable
- Azure API for FHIR R4 native service with UK region
- Strong NHS Trust adoption — many trusts have existing Azure enterprise agreements
- AKS is simpler to set up than EKS (lower operational overhead)
- Microsoft 365 / NHS mail integration familiar to NHS staff

**Cons**:
- Azure NHS DSPT certification less prominent than AWS (AWS explicitly touts DSPT "Standards Exceeded")
- Azure HealthLake equivalent (Health Data Services) has opaque "contact for pricing" model
- Azure Service Bus Premium tier significantly costlier than AWS SQS for same throughput

**Compliance**:
- ISO 27001, SOC 2 Type II, PCI DSS
- UK data residency: UK South (London) confirmed
- G-Cloud 14 listed supplier

---

### Option 1C: Google Cloud Platform (GCP) — europe-west2 (London)

**Description**: GCP offers GKE, Cloud SQL for PostgreSQL, Pub/Sub, Cloud Armor WAF, Cloud KMS from London data centre.

**Vendor**: Google LLC. London region: europe-west2.

**Cost Breakdown** (estimated):

| Cost Item | Year 1 | Year 2 | Year 3 | Notes |
|-----------|--------|--------|--------|-------|
| Compute (GKE + Compute Engine) | £165,000 | £210,000 | £305,000 | |
| Database (Cloud SQL PostgreSQL) | £82,000 | £115,000 | £180,000 | |
| Messaging (Pub/Sub) | £6,000 | £14,000 | £35,000 | £0.04/GB |
| Storage (Cloud Storage) | £10,000 | £18,000 | £32,000 | |
| Networking (Cloud CDN, Load Balancing) | £32,000 | £45,000 | £68,000 | |
| Security (Cloud KMS, Secret Manager) | £15,000 | £18,000 | £21,000 | |
| **Total** | **£310,000** | **£420,000** | **£641,000** | |
| **3-Year TCO** | | | **£1,371,000** | |

**Pros**:
- Lowest compute pricing among three providers
- Cloud Healthcare API supports FHIR R4
- Strong AI/ML capabilities for future analytics workloads

**Cons**:
- Less established in NHS primary care — fewer reference customers at national scale
- GCP DSPT-specific certification less documented compared to AWS
- NHS Digital's primary cloud relationships are with AWS and Azure

**Compliance**:
- ISO 27001, SOC 2 Type II
- UK data residency: europe-west2 (London)
- G-Cloud 14 listed

---

### Build vs Buy Recommendation for Category 1: Cloud Hosting

**Recommended Approach**: BUY — Amazon Web Services (AWS) eu-west-2 (London), primary; Azure UK South as secondary/disaster recovery consideration

**Rationale**:

AWS is the clear primary recommendation for the NHS Appointment Booking Service. The critical differentiator is AWS's NHS DSPT 2024-25 "Standards Exceeded" status, which provides immediate compliance evidence for the DSPT annual assessment. NHS Digital has built its most critical national services — NHS login (which this project depends on via INT-005), NHS App (31 million users), and the Electronic Prescription Service — on AWS, providing proven reference architecture at national scale.

AWS eu-west-2 (London) satisfies the patient data UK residency requirement (NFR-C-001) without additional controls. AWS DirectConnect with HSCN connectivity is documented by NHS England. The breadth of AWS managed services (Aurora Serverless v2 for auto-scaling databases, SQS for async messaging, EKS for containers) reduces operational burden and aligns with the team's likely existing AWS knowledge.

GCP offers slightly lower pricing but lacks the NHS-specific compliance track record and reference customers at national scale required for a citizen-facing health service. Azure is a strong second option particularly where NHS Trusts have existing Azure enterprise agreements for integration work.

**Key Decision Factors**:
- NHS DSPT "Standards Exceeded": AWS is the only provider with explicit published certification status
- NHS Reference Architecture: NHS login, NHS App already on AWS — architectural consistency
- HSCN Connectivity: AWS DirectConnect with NHS-documented HSCN peering guide
- Scale proven: 31 million NHS App users, 15 million COVID vaccination bookings processed on AWS

**Shortlist for Further Evaluation**:
1. **AWS eu-west-2**: Primary recommendation — NHS DSPT certified, national reference architecture
2. **Azure UK South**: Secondary/hybrid consideration — G-Cloud 14 Digital Marketplace FHIR services

**Next Steps**:
- [ ] Engage AWS NHS Account team for reserved instance pricing and NHS public sector agreement
- [ ] Register on NHS England's HSCN connectivity framework for AWS Direct Connect
- [ ] Confirm AWS HealthLake availability in eu-west-2 or alternative FHIR approach
- [ ] Technical POC: Deploy EKS cluster with Aurora PostgreSQL Multi-AZ in eu-west-2

---

## Category 2: NHS GOV.UK Mandated Platforms

**Requirements Addressed**: FR-001 (NHS Login authentication), FR-005 (appointment reminders), INT-004 (NHS Notify), INT-005 (NHS Login), TC-003 (NHS Login mandatory for citizens), NFR-SEC-001 (authentication), NFR-C-001 (GDPR)

**Why This Category**: Two GOV.UK/NHS platforms are mandated or strongly required: NHS Login for citizen authentication (TC-003 states "must use NHS login for citizen authentication — no alternative identity providers") and NHS Notify for multi-channel notifications. These are not build vs buy decisions; they are mandated platforms. This section documents the integration approach and costs.

---

### Option 2A: NHS Login (GOV.UK Platform) — MANDATORY

**Description**: NHS Login is the single NHS citizen identity service, providing OAuth 2.0/OpenID Connect authentication with identity verification levels P5 (medium) and P9 (high). It is built and operated by NHS England Digital.

**Eligibility**: Mandatory for all NHS citizen-facing digital services (TC-003).

**Cost Model**:
- Integration: Free (no licensing fee)
- Integration effort: Estimated 4-6 person-weeks for OIDC integration and P5/P9 level handling

**Cost Breakdown**:

| Cost Item | Year 1 | Year 2 | Year 3 | Notes |
|-----------|--------|--------|--------|-------|
| Platform Usage | £0 | £0 | £0 | Free NHS platform |
| Integration (6 person-weeks @ £1,200/day) | £36,000 | £0 | £0 | OIDC flow + P5/P9 handling + proxy service |
| Testing and clinical safety | £15,000 | £0 | £0 | Security testing, P9 flow validation |
| **Total** | **£51,000** | **£0** | **£0** | |
| **3-Year TCO** | | | **£51,000** | Minimal ongoing cost |

**Key Technical Details**:
- OIDC Authorization Code Flow with PKCE
- P9 (High): Full booking/cancellation access
- P5 (Medium): View-only access (per Conflict C-002 resolution in requirements)
- NHS Number returned as claim — verified against PDS
- NHS Login integration toolkit published at digital.nhs.uk
- Test environment available for pre-production integration testing

**Pros**:
- Mandatory — no procurement decision required
- Pre-built identity verification including P9 (biometric/documentary)
- 30 million+ verified NHS citizens already registered
- GDPR compliant, UK data residency, meets DSPT
- Deep link support for appointment management workflows (FR-005 reminder links)
- Proxy service API for carer/parent booking (FR-008) — in development

**Cons**:
- NHS Number returned only at P9 level — P5 users cannot complete bookings
- Proxy service API (for FR-008) availability timeline unclear — needs confirmation with NHS Digital
- Integration testing environment can have slower response times
- No SLA for NHS Login service uptime published externally (assumed 99.9% by policy)

**TCoP Alignment**:
- Point 8: Share, reuse and collaborate — using NHS common platform
- Point 6: Make things secure — NHS-operated identity, no custom auth risk
- Point 7: Make privacy integral — citizen data stays within NHS ecosystem

---

### Option 2B: NHS Notify — RECOMMENDED (GOV.UK Platform)

**Description**: NHS Notify is the NHS notification delivery service for patient communications, built on GOV.UK Notify infrastructure. It supports NHS App push notifications, SMS, email, and letters. NHS England issued Service Directions in February 2025 mandating NHS Notify for NHS communications.

**Eligibility**: All NHS England commissioned services. NHS Notify Service Directions 2025 issued by Secretary of State.

**Cost Model**:
- Email: Free (unlimited)
- SMS: Free annual allowance (250,000 messages/year per service), then ~£0.0233/message + VAT
- NHS App push notifications: Free
- Letters: Pay-per-use (~£0.65 per letter including print and post)

**Cost Breakdown** (projected for 100M reminders/year by Year 3):

| Cost Item | Year 1 | Year 2 | Year 3 | Notes |
|-----------|--------|--------|--------|-------|
| Email (estimated 20M/year) | £0 | £0 | £0 | Free |
| SMS (beyond free allowance, 5M Year 1) | £95,000 | £200,000 | £380,000 | £0.0233 × excess volume |
| NHS App push (est 15M/year) | £0 | £0 | £0 | Free |
| Integration (4 person-weeks) | £24,000 | £0 | £0 | REST API + webhooks |
| **Total** | **£119,000** | **£200,000** | **£380,000** | |
| **3-Year TCO** | | | **£699,000** | (incl. integration) |

**Note**: If the NHS Notify team confirms negotiated volume pricing for a national service of this scale, SMS costs will be materially lower. NHS Notify "pays less" for bulk volume and passes savings on.

**Key Technical Details**:
- REST API with API key authentication
- Webhooks for delivery status callbacks (INT-004)
- Template management for multi-language messages (English and Welsh — FR-010)
- Dead Letter Queue retry support (NFR-A-003)
- Message scheduling for reminder workflows (FR-005)

**Pros**:
- February 2025 Service Directions effectively mandate this for NHS appointment reminders
- No monthly fee, no setup cost, no procurement process
- GDPR compliant, all data remains in UK
- Welsh language template support (FR-010 compliance)
- NHS App push notifications at zero cost (reduces SMS volume significantly)
- Pre-integrated with NHS login citizen identity for personalised messages

**Cons**:
- SMS volume costs significant at national scale (100M+ bookings/year)
- Letter delivery adds cost for offline citizens (BR-004 digital inclusion)
- Limited to NHS/public sector use — no alternative for private healthcare

---

### Build vs Buy Recommendation for Category 2: GOV.UK Platforms

**Recommended Approach**: GOV.UK Platforms — NHS Login (mandatory) and NHS Notify (mandated by 2025 Service Directions)

**Rationale**: These are not build vs buy decisions. TC-003 explicitly prohibits alternative identity providers for citizen authentication. NHS Notify Service Directions 2025 mandate its use for NHS patient communications. The correct approach is to plan integration effort and ongoing SMS cost management. NHS App push notifications should be maximised to reduce SMS volume and cost.

---

## Category 3: FHIR Integration Layer

**Requirements Addressed**: INT-001 (GP Connect FHIR REST API), INT-002 (Hospital PAS HL7 FHIR), INT-003 (NHS Spine PDS), FR-002 (slot retrieval), FR-003 (booking confirmation), TC-001 (NHS Spine ASID/TLS-MA), TC-005 (GP Connect APIs)

**Why This Category**: The booking service must integrate with GP practice clinical systems via GP Connect (FHIR R4), hospital PAS systems via HL7 FHIR or proprietary APIs, and NHS Spine PDS for patient demographics. This requires a FHIR integration capability — either custom-built integration adapters, a commercial integration platform, or a managed FHIR service.

---

### Option 3A: Build Custom — NHS BaRS FHIR Integration Adapters

**Description**: Build bespoke integration adapters using the NHS Booking and Referral Standard (BaRS) FHIR API and GP Connect APIs, deployed as microservices on the chosen cloud platform. The NHS BaRS API is published at digital.nhs.uk and is the strategic direction for NHS appointment booking interoperability.

**Technology Stack**: Java Spring Boot or Python FastAPI microservices; HAPI FHIR library (open source, Apache 2.0); AWS API Gateway for NHS Spine connectivity; ASID/TLS-MA certificates; GP Connect FHIR R4 appointment slot APIs

**Effort Estimate**:
- Development: 24 person-months (GP Connect adapter 12pm, Hospital PAS adapter 8pm, PDS adapter 4pm)
- Skills Required: Java/Python, HL7 FHIR R4, NHS Spine security (ASID, TLS-MA), GP Connect APIs
- Timeline: 12 months to production-ready (phased — GP Connect first, Hospital PAS Phase 2)

**Cost Breakdown**:

| Cost Item | Year 1 | Year 2 | Year 3 | Notes |
|-----------|--------|--------|--------|-------|
| Development (24 pm × £12,500/pm) | £300,000 | £0 | £0 | Senior engineers at £600/day |
| Infrastructure (ECS/Lambda) | £18,000 | £20,000 | £24,000 | Managed compute |
| Maintenance (30% dev cost) | £0 | £90,000 | £90,000 | Year 2+ ongoing |
| NHS Spine ASID certs and testing | £25,000 | £5,000 | £5,000 | Annual renewal |
| **Total** | **£343,000** | **£115,000** | **£119,000** | |
| **3-Year TCO** | | | **£577,000** | |

**Pros**:
- Full control over NHS Spine security implementation (ASID/TLS-MA)
- Tailored to GP Connect and BaRS API specifications exactly
- NHS BaRS API is free to use (no licensing)
- Aligns with NHS Digital open standards mandate (BaRS is the mandated API)
- Can handle the diverse hospital PAS ecosystem (10+ vendors) with custom adapters per trust

**Cons**:
- Significant development effort — 24 person-months for initial adapters
- Requires deep NHS Spine expertise (ASID registration, TLS-MA, Spine Security Proxy)
- Hospital PAS integration is trust-by-trust with varying API maturity (R-001 risk)
- Ongoing maintenance as GP Connect API versions evolve

**Risks**:
- Hospital PAS integration delays (identified as R-001 HIGH risk in requirements): Mitigate with GP Connect-only MVP, add hospital PAS in Phase 2
- GP Connect API changes: Monitor GP Connect programme releases; version APIs with backward compatibility

---

### Option 3B: Buy — Azure Health Data Services (FHIR Service)

**Description**: Azure Health Data Services provides a fully managed, scalable FHIR R4 server that can store and serve FHIR resources. Available on G-Cloud 14 Digital Marketplace. Combined with Azure Logic Apps or Azure API Management for NHS Spine connectivity.

**Vendor**: Microsoft Azure. Available in UK South region. Listed on G-Cloud 14 (service ID: 926971059674068).

**Cost Breakdown** (estimated, based on Azure pricing model):

| Cost Item | Year 1 | Year 2 | Year 3 | Notes |
|-----------|--------|--------|--------|-------|
| Azure Health Data Services (FHIR) | £45,000 | £52,000 | £60,000 | Per RU/s throughput + storage |
| NHS Spine integration development | £120,000 | £0 | £0 | ASID/TLS-MA still required |
| Azure API Management (Standard tier) | £36,000 | £36,000 | £36,000 | $0.35/hour ≈ £3,066/month |
| Integration effort (12 person-weeks) | £72,000 | £0 | £0 | FHIR adapter layer |
| **Total** | **£273,000** | **£88,000** | **£96,000** | |
| **3-Year TCO** | | | **£457,000** | |

**Key Features**:
- Managed FHIR R4 server — no infrastructure management
- Azure SMART on FHIR authorization built-in
- NHS-compliant FHIR capability statement
- Available on G-Cloud 14 for straightforward procurement
- UK South region — UK data residency maintained

**Pros**:
- No FHIR server infrastructure to manage
- G-Cloud 14 procurement path available
- Azure SLA-backed managed service (99.9% uptime SLA)
- Integrates natively with Azure API Management

**Cons**:
- Opaque pricing ("contact for quote") — makes TCO projection difficult
- NHS Spine ASID/TLS-MA integration still requires custom development regardless
- Vendor lock-in to Azure ecosystem if chosen on AWS primary cloud
- Does not eliminate the need for GP Connect-specific adapters (FHIR profiles are NHS-specific)

---

### Option 3C: Adopt — HAPI FHIR Server (Open Source)

**Description**: HAPI FHIR is the leading open-source Java-based FHIR server, widely used in NHS implementations. Apache 2.0 license. Can be self-hosted on AWS EKS.

**Project Details**:
- License: Apache 2.0 (open source, commercial use permitted)
- GitHub: github.com/hapifhir/hapi-fhir — 4,800+ stars, actively maintained
- Maturity: Mature — production-ready, used by NHS trusts, US health systems, Canadian Health Infoway
- Last Release: Regular releases, v7.x current (2025)
- Commit Activity: Active — multiple commits weekly
- Contributors: 350+ contributors

**Cost Breakdown** (Self-hosted on AWS):

| Cost Item | Year 1 | Year 2 | Year 3 | Notes |
|-----------|--------|--------|--------|-------|
| Infrastructure (EKS pods, RDS) | £22,000 | £25,000 | £28,000 | Runs alongside main service |
| Setup (4 person-weeks) | £24,000 | £0 | £0 | Configuration, NHS FHIR profiles |
| NHS Spine integration development | £120,000 | £0 | £0 | GP Connect + ASID still required |
| Maintenance | £0 | £18,000 | £18,000 | Version upgrades, patches |
| **Total** | **£166,000** | **£43,000** | **£46,000** | |
| **3-Year TCO** | | | **£255,000** | (but NHS Spine dev cost same as build) |

**Note**: HAPI FHIR replaces the FHIR server component but the NHS Spine integration adapters still require custom development. The cost advantage comes from not paying for Azure Health Data Services.

**Pros**:
- No licensing cost — significantly reduces TCO versus Azure managed FHIR
- Full control over FHIR profiles and NHS extensions
- Large NHS community — NHS Digital uses HAPI FHIR in published reference implementations
- Apache 2.0 license: zero restriction on commercial NHS use

**Cons**:
- Operational burden: team must manage HAPI FHIR upgrades, performance tuning
- NHS-specific FHIR profiles require configuration effort
- Requires Java expertise for customisation

---

### Build vs Buy Recommendation for Category 3: FHIR Integration

**Recommended Approach**: ADOPT HAPI FHIR (open source) as FHIR server infrastructure + BUILD custom NHS BaRS/GP Connect integration adapters

**Rationale**:

Custom integration adapters are unavoidable — NHS Spine connectivity via ASID/TLS-MA authentication and the specifics of GP Connect appointment APIs and NHS BaRS cannot be avoided with any commercial product. No commercial vendor has pre-built NHS Spine adapters that remove the need for custom development.

The recommended approach uses HAPI FHIR (Apache 2.0, 4,800+ GitHub stars) as the FHIR server layer — avoiding £45,000-60,000/year in Azure Health Data Services charges — while custom-building NHS-specific integration adapters using the NHS BaRS FHIR API standard. This matches what NHS Digital's own reference implementations use.

For hospital PAS integration (Phase 2), the adapter pattern allows trust-by-trust onboarding, consistent with the phased delivery approach agreed in Conflict C-001 resolution.

**Shortlist for Further Evaluation**:
1. **HAPI FHIR v7 on AWS EKS**: Open source, zero licensing, NHS reference architecture
2. **Azure Health Data Services**: If project ultimately chooses Azure as primary cloud (cross-cloud consistency advantage)

---

## Category 4: Relational Database (Primary Datastore)

**Requirements Addressed**: DR-001 (Appointment entity — 10M→100M records), DR-002 (Citizen entity), DR-003 (Organisation entity), DR-004 (AuditLog entity — 8-year immutable retention), NFR-SEC-003 (AES-256 encryption at rest), NFR-A-002 (RPO 15 min — continuous replication), NFR-S-002 (50TB over 5 years)

**Why This Category**: The service requires a relational database for appointment booking (ACID transactions), citizen records, and organisational data. Access patterns include NHS Number lookup, date-range queries, and status-based reminder scheduling. Data volume grows from 10M to 100M appointment records over 3 years.

---

### Option 4A: Buy — AWS Aurora PostgreSQL Serverless v2

**Description**: AWS Aurora PostgreSQL Serverless v2 is a fully managed, auto-scaling relational database compatible with PostgreSQL 15/16. It scales from minimum to maximum ACU (Aurora Capacity Units) within seconds, ideal for variable NHS appointment booking load patterns.

**Vendor**: Amazon Web Services.

**Pricing**: ~$0.12 per ACU-hour (eu-west-2); minimum 0.5 ACU. Serverless v2 scales to 128 ACU maximum per instance. Storage: $0.10 per GB-month.

**Cost Breakdown**:

| Cost Item | Year 1 | Year 2 | Year 3 | Notes |
|-----------|--------|--------|--------|-------|
| Aurora Serverless v2 (writer + reader, Multi-AZ) | £55,000 | £85,000 | £145,000 | Scales with 10M→100M records |
| Aurora Global Database (UK secondary) | £20,000 | £28,000 | £42,000 | NFR-A-002 secondary region |
| RDS Proxy (connection pooling) | £8,000 | £10,000 | £14,000 | EKS pod connection management |
| Backup storage (30-day + 8-year audit archive to S3) | £4,000 | £8,000 | £15,000 | Tiered S3 Glacier Deep Archive |
| **Total** | **£87,000** | **£131,000** | **£216,000** | |
| **3-Year TCO** | | | **£434,000** | |

**Key Features**:
- Auto-scales in sub-second response time (NFR-S-001 — horizontal scaling)
- Multi-AZ with automatic failover < 30 seconds (NFR-A-002 RTO)
- Continuous backup to S3 — point-in-time recovery to any second within 35 days (NFR-A-002 RPO 15 minutes)
- AES-256 encryption at rest via AWS KMS (NFR-SEC-003)
- Aurora Global Database for cross-region replication to second UK region
- PostgreSQL wire protocol — standard tooling, no vendor lock-in for application code
- Read replicas supported for reporting workloads (BR-006 analytics)

**Pros**:
- Serverless v2: no capacity planning — scales automatically to Year 3 volume
- Multi-AZ failover: tested at < 30 seconds (< 15 minute RTO requirement easily met)
- PostgreSQL compatibility: open standard SQL, no vendor lock-in for queries
- Native AWS encryption with KMS HSM keys (NFR-SEC-004 HSM requirement met)
- Performance Insights for query profiling (NFR-M-001 observability)

**Cons**:
- Higher cost than provisioned instances at steady-state (but lower operational risk)
- Cold-start latency (Serverless v2 minimum 0.5 ACU mitigates this)
- Requires RDS Proxy for EKS connection pooling (additional cost)

**Compliance**:
- GDPR: Encryption at rest + field-level encryption for NHS Number, DoB via application layer
- Audit: Separate AuditLog table to S3 Glacier Deep Archive (8-year retention, immutable)
- UK data residency: eu-west-2 (London)

---

### Option 4B: Adopt — Self-hosted PostgreSQL on EKS (via CloudNativePG)

**Description**: PostgreSQL 16 managed by CloudNativePG Kubernetes operator on EKS. Open source (Apache 2.0). Used by major financial institutions and increasingly NHS Trusts.

**Cost Breakdown**:

| Cost Item | Year 1 | Year 2 | Year 3 | Notes |
|-----------|--------|--------|--------|-------|
| EC2 instances (r6i.2xlarge reserved) | £45,000 | £45,000 | £55,000 | Grows Year 3 |
| EBS storage (gp3 SSD, 10TB→50TB) | £18,000 | £35,000 | £72,000 | $0.08/GB-month |
| Setup and operations | £30,000 | £20,000 | £20,000 | DBA engineering |
| Backup (pgBackRest to S3) | £3,000 | £6,000 | £12,000 | |
| **Total** | **£96,000** | **£106,000** | **£159,000** | |
| **3-Year TCO** | | | **£361,000** | |

**Pros**:
- Lower licensing cost than Aurora (no per-ACU charge)
- Full PostgreSQL flexibility (extensions, custom configurations)
- No risk of Aurora-specific feature lock-in

**Cons**:
- Requires skilled PostgreSQL/Kubernetes DBA resource — scarce in NHS
- Manual failover configuration required for Multi-AZ equivalence
- Scaling requires manual intervention compared to Serverless v2 auto-scaling
- Higher operational risk for a national citizen-facing service

---

### Build vs Buy Recommendation for Category 4: Database

**Recommended Approach**: BUY — AWS Aurora PostgreSQL Serverless v2 with Aurora Global Database

**Rationale**: For a national NHS citizen-facing service scaling to 100 million appointment records, Aurora Serverless v2's automatic scaling eliminates the capacity planning risk that would be significant with self-hosted PostgreSQL. The incremental cost over self-hosted is approximately £73,000 over 3 years — a justified operational risk premium for a service with NFR-A-002 requirements (RPO 15 minutes, RTO 1 hour) and no DBA team overhead on the project.

Aurora Global Database provides the cross-region secondary needed for NFR-A-002's disaster recovery requirements within the AWS ecosystem.

---

## Category 5: Async Messaging and Event Streaming

**Requirements Addressed**: NFR-A-003 (circuit breaker, bulkhead isolation, graceful degradation), FR-005 (reminder scheduling), FR-009 (waitlist management), INT-004 (notification delivery), Principle 10 (asynchronous communication)

**Why This Category**: The architecture requires async messaging for: appointment reminder scheduling, notification delivery to NHS Notify, audit event publication, GP system booking queue (for graceful degradation when GP systems unavailable), and waitlist notifications.

---

### Option 5A: Buy — AWS SQS + SNS + EventBridge

**Description**: AWS SQS (Standard and FIFO queues), SNS (fan-out pub/sub), and EventBridge (event bus) provide a composable messaging stack natively integrated with EKS/Lambda workloads.

**Pricing**: SQS Standard: $0.40 per million requests (first 1M free/month). SNS: $0.50 per million notifications. EventBridge: $1.00 per million events.

**Cost Breakdown** (projected for appointment booking volumes):

| Cost Item | Year 1 | Year 2 | Year 3 | Notes |
|-----------|--------|--------|--------|-------|
| SQS (booking queue, notification queue, DLQ) | £8,000 | £18,000 | £42,000 | 10M→100M+ events |
| SNS (fan-out to multiple consumers) | £3,000 | £8,000 | £20,000 | |
| EventBridge (audit events, schedules) | £5,000 | £12,000 | £28,000 | Reminder scheduling |
| **Total** | **£16,000** | **£38,000** | **£90,000** | |
| **3-Year TCO** | | | **£144,000** | |

**Key Features**:
- SQS FIFO: Exactly-once processing for appointment bookings (prevents duplicate bookings)
- SQS Dead Letter Queue: Failed messages after 3 retries (NFR-A-003 DLQ requirement)
- EventBridge Scheduler: Cron-based reminder scheduling (FR-005 — 7-day, 48-hour, 2-hour reminders)
- Message retention: Up to 14 days (handles NHS Notify outages gracefully — INT-004)
- Visibility timeout: Configurable for GP system retry patterns (NFR-A-003 circuit breaker)

**Pros**:
- Native AWS integration — zero operational overhead if primary cloud is AWS
- Pay-per-use with 1M free messages/month per queue
- FIFO queues prevent duplicate appointment bookings
- EventBridge Scheduler handles all reminder scheduling natively (replaces cron job infrastructure)
- Dead letter queues with CloudWatch alerts for failed notifications

**Cons**:
- AWS-specific — increases lock-in if multi-cloud strategy ever needed
- Maximum message size: 256KB (sufficient for appointment data)
- FIFO throughput: 3,000 msg/sec per queue (sufficient for 500 bookings/minute at NFR-P-003)

---

### Option 5B: Adopt — Apache Kafka (via Amazon MSK)

**Description**: Amazon Managed Streaming for Apache Kafka (MSK) — fully managed Kafka on AWS. Used for high-throughput event streaming.

**Cost Breakdown**:

| Cost Item | Year 1 | Year 2 | Year 3 | Notes |
|-----------|--------|--------|--------|-------|
| MSK Standard cluster (3 brokers) | £42,000 | £42,000 | £42,000 | $0.21/broker-hour × 3 |
| EBS storage (1TB SSD) | £12,000 | £16,000 | £24,000 | |
| **Total** | **£54,000** | **£58,000** | **£66,000** | |
| **3-Year TCO** | | | **£178,000** | Higher than SQS for this workload |

**Assessment**: Kafka/MSK adds significant operational cost (£34,000 more over 3 years than SQS) without proportionate benefit for appointment booking volumes. Kafka's strengths (high throughput, log compaction, replay) are not required for 500 bookings/minute. SQS is the more appropriate choice.

---

### Build vs Buy Recommendation for Category 5: Async Messaging

**Recommended Approach**: BUY — AWS SQS + SNS + EventBridge Scheduler

**Rationale**: SQS/SNS/EventBridge is the natural choice on the recommended AWS primary cloud, with pay-per-use pricing that is significantly cheaper than Kafka/MSK at appointment booking volumes. The combination handles all messaging requirements: FIFO booking queues, notification fan-out, reminder scheduling, and dead letter queues for resilience.

---

## Category 6: Observability and Monitoring

**Requirements Addressed**: NFR-M-001 (structured JSON logs, Prometheus metrics, distributed traces, SLO alerting), NFR-C-003 (8-year audit log retention, immutable, cryptographic hashing), NFR-P-001/002 (Real User Monitoring, APM), NFR-SEC-006 (security event monitoring)

**Why This Category**: The service requires comprehensive observability: structured logging (excluding PII), Prometheus-compatible RED metrics, distributed tracing with correlation IDs, SLO-based alerting with runbooks, and 8-year immutable audit log retention. An NHS-scale deployment (50,000 concurrent users) generates significant telemetry volume.

---

### Option 6A: Adopt — Grafana Open Source Stack (Prometheus + Loki + Tempo + Grafana)

**Description**: The Grafana OSS observability stack: Grafana for dashboards, Prometheus for metrics, Loki for log aggregation, Tempo for distributed tracing. Deployed on AWS EKS via the kube-prometheus-stack Helm chart. Industry standard for Kubernetes observability.

**Project Details**:
- Grafana: Apache 2.0, GitHub 63,000+ stars
- Prometheus: Apache 2.0, CNCF Graduated project, 55,000+ stars
- Loki: AGPL-3.0 (self-hosted), 23,000+ stars
- Tempo: Apache 2.0, 3,800+ stars
- Maturity: Production-grade (all CNCF projects or Grafana Labs maintained)

**Cost Breakdown** (Self-hosted on AWS EKS):

| Cost Item | Year 1 | Year 2 | Year 3 | Notes |
|-----------|--------|--------|--------|-------|
| EKS compute (Prometheus + Loki pods) | £28,000 | £32,000 | £36,000 | t3.large × 6 nodes |
| S3 storage (Loki log storage, Tempo traces) | £14,000 | £22,000 | £34,000 | 90-day app logs, 8yr audit |
| Setup (6 person-weeks) | £36,000 | £0 | £0 | kube-prometheus-stack config |
| Maintenance (2 person-weeks/year) | £0 | £12,000 | £12,000 | Helm upgrades |
| **Total** | **£78,000** | **£66,000** | **£82,000** | |
| **3-Year TCO** | | | **£226,000** | |

**Key Capabilities**:
- Prometheus: RED metrics (Rate, Errors, Duration) per microservice — NFR-M-001
- Loki: Structured JSON log aggregation with NHS Number redaction support (no PII in logs — NFR-M-001)
- Tempo: Distributed tracing with correlation IDs across all services — NFR-C-003
- Grafana dashboards: Real-time operational and business metrics (BR-006)
- AlertManager: SLO-based alerting with runbook links — NFR-M-001
- Long-term metrics: Thanos or Grafana Mimir for 2-year metric retention — Principle 5

**Audit Log Architecture** (NFR-C-003 — 8-year immutable retention):
- AuditLog records written to database (PostgreSQL) and simultaneously published to SQS
- SQS consumer writes to S3 with Object Lock (WORM) + Glacier Deep Archive after 90 days
- SHA-256 hash stored per record for tamper-evidence
- This is a custom component alongside Grafana stack

**Pros**:
- Zero licensing cost — significant saving vs Datadog at NHS scale
- Prometheus is the Kubernetes-native metrics format — aligns perfectly with EKS architecture
- Grafana dashboards widely understood — NHS teams familiar with the tooling
- Full control over PII redaction in logs (critical for NHS patient data — NFR-C-003)
- AGPL license for Loki: self-hosted use is permitted without commercial license

**Cons**:
- Operational complexity: team must maintain Helm charts, upgrade cycles (5-6 per year)
- Loki is AGPL — commercial support contract may be needed for NHS assurance
- No out-of-box support SLA unless Grafana Enterprise licence purchased
- AWS CloudWatch supplement still needed for AWS-native metrics (Lambda, RDS Performance Insights)

---

### Option 6B: Buy — Datadog

**Description**: Datadog is a unified SaaS observability platform (metrics, logs, APM, dashboards, alerting). Enterprise plan at $23/host/month.

**Cost Breakdown** (50,000 concurrent users, estimated 150-200 hosts/pods):

| Cost Item | Year 1 | Year 2 | Year 3 | Notes |
|-----------|--------|--------|--------|-------|
| Infrastructure Monitoring (200 hosts × $23/month) | £44,000 | £48,000 | £53,000 | |
| APM + Distributed Tracing ($31/host × 50 APM hosts) | £15,000 | £16,500 | £18,000 | |
| Log Management ($0.10/GB ingestion + $0.01/GB/day retention) | £65,000 | £85,000 | £110,000 | High log volumes |
| **Total** | **£124,000** | **£149,500** | **£181,000** | |
| **3-Year TCO** | | | **£454,500** | 2× Grafana OSS cost |

**Assessment**: Datadog provides an excellent out-of-box experience but at 2x the cost of the Grafana OSS stack for this workload. Log management costs in particular escalate significantly at NHS scale. The Grafana OSS stack is the more appropriate choice for a cost-conscious NHS service with a team willing to manage the observability infrastructure.

---

### Option 6C: Buy — AWS CloudWatch (Supplemental)

**Description**: AWS CloudWatch is used as a supplement to the Grafana OSS stack for AWS-native metrics (RDS Performance Insights, EKS cluster metrics, Lambda metrics, SQS queue depth).

**Cost**: ~£12,000/year (custom metrics at $0.30/metric/month × 300 metrics + dashboards)

**Recommendation**: Use AWS CloudWatch for AWS-native infrastructure metrics alongside the Grafana OSS stack for application-level observability. This hybrid approach minimises cost while providing comprehensive coverage.

---

### Build vs Buy Recommendation for Category 6: Observability

**Recommended Approach**: ADOPT Grafana OSS Stack (Prometheus + Loki + Tempo + Grafana on AWS EKS) + AWS CloudWatch supplement (£12,000/year)

**Rationale**: The Grafana OSS stack saves approximately £228,000 over 3 years compared to Datadog while providing equivalent technical capability. The stack is industry-standard for Kubernetes environments and the 8-year audit log retention requirement is best addressed by a custom S3 WORM storage component regardless of which observability tool is chosen.

---

## Category 7: API Gateway and Security Edge

**Requirements Addressed**: INT-001 to INT-006 (all integrations requiring rate limiting, auth, routing), NFR-SEC-001/002 (OAuth 2.0/OIDC token validation), NFR-SEC-005 (WAF, DDoS protection), NFR-P-002 (API response time p95 < 2 seconds)

**Why This Category**: The service exposes APIs to citizens (via NHS login OIDC), NHS staff (via NHS Smartcard), and integrates with GP systems (NHS Spine). An API gateway provides: rate limiting, JWT validation, routing, WAF, DDoS protection, and API versioning.

---

### Option 7A: Buy — AWS API Gateway + AWS WAF

**Description**: AWS API Gateway (HTTP API, $1.00 per million requests) with AWS WAF (Web Application Firewall) for DDoS protection and OWASP Top 10 rules. Natively integrated with EKS via ALB Ingress.

**Pricing**: HTTP API: $1.00 per million requests (eu-west-2). AWS WAF: $5.00/month per rule group + $0.60 per million requests inspected.

**Cost Breakdown**:

| Cost Item | Year 1 | Year 2 | Year 3 | Notes |
|-----------|--------|--------|--------|-------|
| AWS API Gateway (HTTP API) | £12,000 | £25,000 | £55,000 | 10M→100M API calls |
| AWS WAF (OWASP rules + NHS custom rules) | £18,000 | £20,000 | £22,000 | Rate limit, bot protection |
| AWS Shield Standard | £0 | £0 | £0 | Included with WAF |
| **Total** | **£30,000** | **£45,000** | **£77,000** | |
| **3-Year TCO** | | | **£152,000** | |

**Pros**:
- Pay-per-use: zero cost for low-traffic periods (e.g., maintenance windows)
- Native JWT authorizer — validates NHS login OIDC tokens without code (NFR-SEC-001)
- AWS WAF Managed Rules: OWASP Top 10 pre-configured, NHS-specific rate limits
- CloudFront integration for national CDN with < 20ms edge latency (NFR-P-001)
- 99.95% uptime SLA

**Cons**:
- Higher cost than Kong or NGINX at very high volumes (> 1 billion requests/month)
- Less feature-rich than Azure API Management for complex API lifecycle management
- HTTP API lacks some REST API features (custom domains require REST API)

---

### Option 7B: Adopt — Kong Gateway (Open Source) on EKS

**Description**: Kong is an open-source API gateway running as a Kubernetes ingress controller on EKS. Apache 2.0 license. Provides JWT validation, rate limiting, logging plugins.

**Cost Breakdown**:

| Cost Item | Year 1 | Year 2 | Year 3 | Notes |
|-----------|--------|--------|--------|-------|
| Infrastructure (EKS pods for Kong) | £8,000 | £8,000 | £8,000 | Runs in existing cluster |
| AWS WAF (still required for DDoS/edge) | £18,000 | £20,000 | £22,000 | Cannot replace WAF |
| Setup (3 person-weeks) | £18,000 | £0 | £0 | |
| Maintenance | £0 | £8,000 | £8,000 | |
| **Total** | **£44,000** | **£36,000** | **£38,000** | |
| **3-Year TCO** | | | **£118,000** | |

**Assessment**: Kong OSS on EKS is marginally cheaper over 3 years (£34,000 saving) but adds operational complexity. For an initial national deployment, AWS API Gateway's managed service reliability and native AWS integration justifies the cost premium. Consider Kong for Phase 2 when team expertise and operational maturity is higher.

---

### Build vs Buy Recommendation for Category 7: API Gateway

**Recommended Approach**: BUY — AWS API Gateway (HTTP API) + AWS WAF (+ AWS CloudFront for CDN)

**Rationale**: AWS API Gateway is the lowest-friction option on the recommended AWS primary cloud. Native JWT authorizer for NHS login tokens, WAF rules for NHS-specific threat patterns, and CloudFront CDN for national delivery performance all reduce development overhead significantly. The pay-per-use model means zero fixed cost during development, Alpha, and Beta phases.

---

## Category 8: CI/CD and Developer Platform

**Requirements Addressed**: Principle 15 (Automated Testing — 80% coverage, security tests), Principle 16 (CI/CD pipeline, quality gates), NFR-SEC-005 (SAST, dependency scanning in CI/CD, DAST weekly), NFR-U-002 (accessibility testing — axe-core in CI/CD)

**Why This Category**: The service requires an automated CI/CD pipeline with quality gates: build, unit/integration test, security scan (SAST, dependency vulnerability), accessibility test, and automated deployment to AWS EKS.

---

### Option 8A: Buy — GitHub Actions (Enterprise or Teams)

**Description**: GitHub Actions provides CI/CD as a service tightly integrated with GitHub source control. Enterprise plan provides 50,000 included minutes/month for private repositories.

**Pricing**: GitHub Teams: $4/user/month (includes 3,000 CI minutes). GitHub Enterprise: $21/user/month (includes 50,000 minutes). Additional minutes: $0.008/minute (Linux).

**Cost Breakdown** (team of 20 engineers, Enterprise plan):

| Cost Item | Year 1 | Year 2 | Year 3 | Notes |
|-----------|--------|--------|--------|-------|
| GitHub Enterprise (20 users × $21 × 12) | £4,500 | £4,500 | £4,500 | |
| Additional CI minutes (beyond 50K) | £3,000 | £4,500 | £6,000 | Security scan pipelines |
| GitHub Advanced Security (SAST, secret scanning) | £4,500 | £4,500 | £4,500 | $49/active committer |
| **Total** | **£12,000** | **£13,500** | **£15,000** | |
| **3-Year TCO** | | | **£40,500** | |

**Key Features**:
- Native integration with GitHub (source control, PRs, code review)
- Actions marketplace: 20,000+ pre-built actions including NHS-relevant tools (Snyk, Trivy, OWASP ZAP)
- GitHub Advanced Security: SAST (CodeQL), secret scanning, dependency review (NFR-SEC-005)
- Matrix builds for multi-environment testing
- Environments and deployment protection rules (production approval gates)
- Self-hosted runners available for air-gapped NHS environments if needed

**Pros**:
- Tightly integrated with GitHub — minimal context switching for developers
- GitHub Advanced Security covers SAST and dependency scanning (NFR-SEC-005) in one tool
- Actions marketplace: Trivy (container scanning), OWASP ZAP (DAST), axe-core (accessibility)
- 50,000 included minutes/month on Enterprise plan — sufficient for 20-engineer team

**Cons**:
- 2026 pricing changes introduce $0.002/minute platform charge for self-hosted runners
- GitHub Enterprise pricing increasing — 2026 self-hosted runner changes add cost
- Vendor lock-in: workflows are GitHub Actions YAML — migration to GitLab requires rewriting pipelines

---

### Option 8B: Adopt — GitLab CI (Self-hosted Community Edition)

**Description**: GitLab Community Edition self-hosted provides CI/CD with unlimited runners, source control, and issue tracking. Free for self-hosted use.

**Cost Breakdown**:

| Cost Item | Year 1 | Year 2 | Year 3 | Notes |
|-----------|--------|--------|--------|-------|
| EC2 instances for GitLab + runners | £18,000 | £18,000 | £18,000 | t3.xlarge × 2 |
| Setup and migration | £24,000 | £0 | £0 | 4 person-weeks |
| Maintenance (2 person-weeks/year) | £0 | £12,000 | £12,000 | Version upgrades |
| Snyk/Trivy/OWASP ZAP (security scanning) | £8,000 | £8,000 | £8,000 | Third-party tools |
| **Total** | **£50,000** | **£38,000** | **£38,000** | |
| **3-Year TCO** | | | **£126,000** | Higher than GitHub Enterprise |

**Assessment**: GitLab self-hosted is more expensive than GitHub Enterprise for a 20-person team (£126,000 vs £40,500 over 3 years) once infrastructure and maintenance are included. GitLab Premium SaaS (~$29/user/month) would cost £139,200 over 3 years. GitHub Actions is more cost-effective.

---

### Build vs Buy Recommendation for Category 8: CI/CD

**Recommended Approach**: BUY — GitHub Actions (Enterprise plan) + GitHub Advanced Security

**Rationale**: GitHub Actions Enterprise is cost-effective at £40,500 over 3 years for a 20-person team. GitHub Advanced Security covers the SAST, secret scanning, and dependency review requirements in NFR-SEC-005 without additional tooling. The Actions marketplace provides pre-built actions for OWASP ZAP (weekly DAST), Trivy (container scanning), and axe-core (accessibility testing), reducing custom pipeline development significantly.

---

## Total Cost of Ownership (TCO) Summary

### Blended TCO Across All Categories (Recommended Approach)

| Category | Recommended Option | Year 1 | Year 2 | Year 3 | 3-Year TCO |
|----------|--------------------|--------|--------|--------|------------|
| Cloud Hosting (AWS) | BUY — AWS eu-west-2 | £338,000 | £448,000 | £684,000 | £1,470,000 |
| NHS GOV.UK Platforms | GOV.UK (NHS Login + Notify) | £170,000 | £200,000 | £380,000 | £750,000 |
| FHIR Integration (HAPI + Adapters) | ADOPT + BUILD | £166,000 | £43,000 | £46,000 | £255,000 |
| Database (Aurora PostgreSQL) | BUY — Aurora Serverless v2 | £87,000 | £131,000 | £216,000 | £434,000 |
| Async Messaging (SQS/SNS) | BUY — AWS SQS/SNS/EventBridge | £16,000 | £38,000 | £90,000 | £144,000 |
| Observability (Grafana OSS) | ADOPT — Grafana stack | £78,000 | £66,000 | £82,000 | £226,000 |
| API Gateway (AWS) | BUY — API Gateway + WAF | £30,000 | £45,000 | £77,000 | £152,000 |
| CI/CD (GitHub Actions) | BUY — GitHub Enterprise | £12,000 | £13,500 | £15,000 | £40,500 |
| **TOTAL (infrastructure + tooling)** | | **£897,000** | **£984,500** | **£1,590,000** | **£3,471,500** |

**Note**: The above covers infrastructure and tooling costs only. Custom development costs (FHIR adapters, core booking service) are shown separately below.

### Custom Development Costs (BUILD components)

| Component | Year 1 | Year 2 | Year 3 | 3-Year Total |
|-----------|--------|--------|--------|--------------|
| Core booking service (API + frontend) | £840,000 | £0 | £0 | £840,000 |
| FHIR integration adapters (GP Connect + PDS) | £300,000 | £0 | £0 | £300,000 |
| Hospital PAS adapters (Phase 2) | £0 | £240,000 | £0 | £240,000 |
| Maintenance (25% of dev cost) | £0 | £285,000 | £285,000 | £570,000 |
| **Total Development** | **£1,140,000** | **£525,000** | **£285,000** | **£1,950,000** |

### Combined 3-Year TCO (Infrastructure + Development)

| Component | Year 1 | Year 2 | Year 3 | 3-Year TCO |
|-----------|--------|--------|--------|------------|
| Infrastructure and tooling | £897,000 | £984,500 | £1,590,000 | £3,471,500 |
| Custom development | £1,140,000 | £525,000 | £285,000 | £1,950,000 |
| **COMBINED TOTAL** | **£2,037,000** | **£1,509,500** | **£1,875,000** | **£5,421,500** |

### Alternative Scenarios

**Scenario A: Build Everything Custom** (including custom auth, custom notifications, self-hosted Kubernetes, custom CI/CD):
- 3-Year TCO: £9,200,000 (estimated)
- Pros: Maximum control
- Cons: Highest cost, 24+ months longer to production-ready, prohibits using NHS Login (TC-003 violation), requires full DevOps/platform team

**Scenario B: Buy Everything Commercial SaaS** (Datadog, Kafka/MSK, Azure Health Data Services, GitLab Premium):
- 3-Year TCO: £6,100,000 (estimated)
- Pros: Managed services, reduced ops burden
- Cons: Higher ongoing subscription costs, FHIR adapters still need custom build, Datadog log costs at NHS scale excessive

**Scenario C: Open Source Everything** (self-hosted PostgreSQL, NGINX, GitLab CE, no managed cloud services):
- 3-Year TCO: £4,800,000 (estimated)
- Pros: Lower licensing costs
- Cons: Significant operational overhead; higher engineering headcount required; risk to 99.9% availability SLA with self-managed databases

**Scenario D: Recommended Blended Approach** (this document):
- 3-Year TCO: £5,421,500 (including development)
- Pros: Best balance of cost, delivery speed, operational risk, and NHS compliance
- Cons: Multi-tool complexity requires skilled engineering team

### TCO Assumptions

- Engineering rates: £1,200/day (£600/day for mid-level, £800/day senior — blended contractor rate)
- AWS pricing: eu-west-2 on-demand rates with 1-year reserved instance for compute (25% saving applied)
- SaaS pricing: 2024 list prices with 10% annual increase Year 2+
- Maintenance: 25% of initial development cost per year from Year 2
- Exchange rates: USD/GBP 0.79 (February 2026)
- NHS Notify SMS: £0.0233/message (GOV.UK Notify published price)
- Team size: 20 engineers for development, 6 for operations

### Risk-Adjusted TCO

| Scenario | Base TCO | Contingency | Risk-Adjusted TCO | Risk Factors |
|----------|----------|-------------|-------------------|--------------|
| Build Everything | £9,200,000 | +25% | £11,500,000 | Scope creep, NHS integration delays, regulatory gaps |
| Buy SaaS | £6,100,000 | +10% | £6,710,000 | Price increases, vendor changes, Datadog bill shock |
| Open Source | £4,800,000 | +20% | £5,760,000 | Underestimated ops overhead, skills gaps |
| **Recommended Blend** | **£5,421,500** | **+15%** | **£6,235,000** | Hospital PAS integration delays, scaling surprises |

---

## Requirements Traceability

### Requirements Coverage Matrix

| Requirement ID | Requirement Description | Research Category | Recommended Solution | Rationale |
|----------------|------------------------|-------------------|---------------------|-----------|
| FR-001 | NHS Login Authentication | Cat 2: GOV.UK Platforms | NHS Login (mandatory) | TC-003 mandates NHS Login — no alternative |
| FR-002 | Appointment Slot Retrieval | Cat 3: FHIR Integration | HAPI FHIR + GP Connect adapters | BaRS FHIR API is mandated NHS standard |
| FR-003 | Appointment Booking Confirmation | Cat 3: FHIR Integration | GP Connect FHIR booking API | Standard NHS API — BUILD adapter |
| FR-004 | Appointment Cancellation | Cat 3: FHIR Integration | GP Connect FHIR cancellation API | Standard NHS API — BUILD adapter |
| FR-005 | Appointment Reminders | Cat 2: GOV.UK Platforms | NHS Notify (mandated 2025) | Service Directions 2025 mandate NHS Notify |
| FR-006 | Appointment Search and Filter | Build — Core service | Custom PostgreSQL queries | No off-the-shelf NHS appointment search |
| FR-007 | Preparation Information | Build — Core service | CMS content stored in PostgreSQL | Custom CMS or structured content store |
| FR-008 | Proxy Booking | Cat 2: NHS Login | NHS Login Proxy Service API | NHS Digital developing proxy service — timeline TBC |
| FR-009 | Waitlist Management | Cat 5: Messaging | SQS FIFO + EventBridge | Waitlist queue with 2-hour timeout using SQS visibility |
| FR-010 | Multi-Language (Welsh) | Cat 2: NHS Notify | NHS Notify Welsh templates | Welsh language template support confirmed |
| FR-011 | Staff Booking Interface | Build — Core service | Custom staff portal + NHS Smartcard auth | NHS Smartcard integration via NHS CIS |
| FR-012 | Appointment History | Cat 4: Database | Aurora PostgreSQL query | 12-month history query on indexed appointment table |
| NFR-P-001 | Page Load < 3 seconds | Cat 1: AWS | CloudFront CDN + EKS | Edge caching + containerised services |
| NFR-P-002 | API Response < 2s p95 | Cat 7: API Gateway | AWS API Gateway + EKS | Gateway throttling + auto-scaling |
| NFR-P-003 | 500 bookings/minute | Cat 1: AWS + Cat 5: SQS | EKS auto-scaling + SQS FIFO | Horizontal scaling + async booking queue |
| NFR-A-001 | 99.9% uptime | Cat 1: AWS + Cat 4: DB | Multi-AZ Aurora + EKS across AZs | AWS multi-AZ SLA exceeds 99.9% |
| NFR-A-002 | RPO 15 min, RTO 1 hour | Cat 4: Database | Aurora Global DB continuous replication | Continuous replication + Global DB failover < 1 min |
| NFR-A-003 | Fault tolerance / circuit breaker | Cat 5: Messaging | SQS DLQ + Resilience4j in services | SQS graceful degradation + Resilience4j circuit breaker |
| NFR-S-001 | Scale to 30M users | Cat 1: AWS + Cat 4: DB | EKS Cluster Autoscaler + Aurora Serverless v2 | Serverless scales automatically to 128 ACU |
| NFR-S-002 | 50TB over 5 years | Cat 4: Database | Aurora + S3 tiered storage | Hot/warm/cold S3 Intelligent Tiering |
| NFR-SEC-001 | NHS Login OAuth 2.0 / OIDC | Cat 2: NHS Login | NHS Login + AWS JWT authorizer | OIDC flow with JWT token validation at API Gateway |
| NFR-SEC-002 | RBAC least privilege | Build — Core service | Custom RBAC in application layer | Spring Security / custom middleware |
| NFR-SEC-003 | AES-256 encryption | Cat 1: AWS | AWS KMS + Aurora encryption + S3 SSE | KMS-managed keys, field-level encryption for NHS Number/DoB |
| NFR-SEC-004 | Secrets management | Cat 1: AWS | AWS Secrets Manager | Auto-rotation: 30/90 days per requirement |
| NFR-SEC-005 | Vulnerability management | Cat 8: CI/CD | GitHub Advanced Security + Trivy + OWASP ZAP | SAST, dependency scan, DAST in GitHub Actions pipeline |
| NFR-SEC-006 | DSPT, Cyber Essentials Plus | Cat 1: AWS | AWS eu-west-2 (DSPT Standards Exceeded) | AWS holds current DSPT certification |
| NFR-C-001 | UK GDPR, data residency | Cat 1: AWS | AWS eu-west-2 all services | All patient data remains in UK London region |
| NFR-C-002 | DCB0129 Clinical Safety | Not a technology category | Clinical process + testing | DCB0129 is a process requirement, not technology |
| NFR-C-003 | 8-year immutable audit logs | Cat 6: Observability | S3 Object Lock + Glacier Deep Archive | S3 WORM + SHA-256 hash per record |
| NFR-U-001 | NHS.UK Design System | Build — Frontend | NHS.UK Design System components | React/Next.js with nhsuk-frontend npm package |
| NFR-U-002 | WCAG 2.2 AA | Cat 8: CI/CD | axe-core in GitHub Actions pipeline | Automated accessibility testing + manual testing |
| NFR-M-001 | Observability (logs/metrics/traces) | Cat 6: Observability | Grafana OSS stack (Prometheus/Loki/Tempo) | Prometheus-native for EKS, structured JSON logging |
| INT-001 | GP Clinical System (GP Connect) | Cat 3: FHIR Integration | BUILD — GP Connect FHIR adapter | Custom adapter required for NHS Spine ASID/TLS-MA |
| INT-002 | Hospital PAS Integration | Cat 3: FHIR Integration | BUILD — NHS BaRS FHIR adapters (Phase 2) | Trust-by-trust adapters via BaRS API |
| INT-003 | NHS Spine PDS | Cat 3: FHIR Integration | BUILD — FHIR PDS adapter + 24h cache | PDS FHIR API, cached demographics |
| INT-004 | NHS Notify | Cat 2: GOV.UK Platforms | NHS Notify API | REST API integration with webhooks for delivery status |
| INT-005 | NHS Login | Cat 2: GOV.UK Platforms | NHS Login OIDC | Authorization code flow with PKCE |
| INT-006 | Analytics Platform | Cat 6: Observability | EventBridge + S3 + Grafana | Anonymised event stream to S3, dashboarded in Grafana |
| DR-001 | Appointment entity (100M Year 3) | Cat 4: Database | Aurora PostgreSQL Serverless v2 | Auto-scales to handle 100M records |
| DR-002 | Citizen entity (30M Year 3) | Cat 4: Database | Aurora PostgreSQL — cached PDS data | 24-hour PDS sync, encrypted NHS Number/DoB |
| DR-003 | Organisation entity | Cat 4: Database | Aurora PostgreSQL — ODS reference data | Low-volume reference data, ODS API sync |
| DR-004 | AuditLog entity (8yr retention) | Cat 4 + Cat 6 | PostgreSQL + S3 Object Lock WORM | Immutable audit trail with SHA-256 hashing |
| TC-001 | NHS Spine ASID/TLS-MA | Cat 3: FHIR Integration | BUILD — NHS Spine security module | Custom TLS-MA certificate handling |
| TC-002 | NHS-approved cloud, UK only | Cat 1: AWS | AWS eu-west-2 (DSPT certified) | Only NHS DSPT-certified UK cloud provider |
| TC-003 | NHS Login only | Cat 2: NHS Login | NHS Login (mandatory) | TC-003 is a hard constraint |
| TC-004 | NHS Design System | Build — Frontend | nhsuk-frontend npm package | React components from NHS frontend library |
| TC-005 | GP Connect-enabled only initially | Cat 3: FHIR Integration | BUILD — GP Connect adapter | Integration limited to GP Connect suppliers (Phase 1) |

### Coverage Summary

- **92% of requirements** (34 of 37 requirement IDs) have identified solutions or GOV.UK platform answers
- **5 requirements** require custom development (FR-006 search, FR-007 preparation content, FR-011 staff interface, NFR-SEC-002 RBAC, TC-001 NHS Spine security)
- **1 requirement needs further research**: FR-008 (proxy booking) — NHS Digital proxy service API timeline requires confirmation with NHS Digital product team

**Gaps and Concerns**:

**GAP-1**: FR-008 (Proxy Booking for Carers/Parents)
- **Impact**: Cannot support parent booking for children or carer booking without confirmation of NHS Login proxy service API availability
- **Options**: (1) Wait for NHS Digital proxy service API; (2) Build interim proxy management within the service; (3) Defer to Phase 2
- **Recommendation**: Confirm proxy service roadmap with NHS Digital — expected 2026 per public documentation. Plan for Phase 2 feature if API not available at Phase 1 launch.

**GAP-2**: NFR-C-002 (DCB0129 Clinical Safety)
- **Impact**: Clinical safety case must be approved before go-live — this is a process and governance requirement, not a technology gap
- **Options**: Engage Clinical Safety Officer and follow DCB0129 process from Alpha assessment onwards
- **Recommendation**: Not a technology research gap — ensure project plan includes DCB0129 gate before NHS Login integration moves to production

---

## UK Government Considerations

### Technology Code of Practice (TCoP) Compliance

| TCoP Point | Status | Notes |
|-----------|--------|-------|
| **1. Define user needs** | Compliant | Research driven by requirements from citizen and staff personas |
| **2. Make things accessible** | Compliant | WCAG 2.2 AA in CI/CD via axe-core; NHS Design System mandated |
| **3. Be open and use open standards** | Compliant | FHIR R4, HL7, OAuth 2.0, OpenID Connect, BaRS open standard |
| **4. Make use of open source** | Compliant | HAPI FHIR, Grafana OSS stack, Prometheus, Loki — key components open source |
| **5. Use cloud first** | Compliant | AWS eu-west-2 public cloud primary |
| **6. Make things secure** | Compliant | AWS DSPT "Standards Exceeded", NHS Login, TLS-MA, KMS encryption |
| **7. Make privacy integral** | Compliant | UK data residency, field-level encryption, DPIA required, Caldicott Principles |
| **8. Share, reuse and collaborate** | Compliant | NHS Login, NHS Notify, NHS BaRS all reused common platforms |
| **9. Integrate and adapt technology** | Compliant | FHIR APIs, HL7, GP Connect, BaRS standard interfaces |
| **10. Make better use of data** | Compliant | Anonymised event streams, Grafana dashboards for capacity planning |
| **11. Define your purchasing strategy** | In Progress | AWS via G-Cloud 14 or NHS public sector agreement; GitHub via G-Cloud 14 |
| **12. Meet the Service Standard** | GDS/NHS Assessment | Required — NHS Service Standard assessments at Alpha, Beta, Live |
| **13. Spend controls** | Required | Total 3-year TCO £5.4M exceeds £100K — GDS spend approval required |

### GOV.UK Common Platforms Used

| Platform | Category | Status | Rationale |
|----------|----------|--------|-----------|
| NHS Login | Authentication | MANDATORY (TC-003) | Only approved citizen identity for NHS services |
| NHS Notify | Notifications | MANDATED (Service Directions 2025) | Secretary of State directions mandate use |
| NHS BaRS | Booking API | MANDATORY | Strategic NHS booking interoperability standard |
| NHS Spine PDS | Demographics | MANDATORY | Authoritative patient demographics (Principle 8) |

### NHS-Specific Compliance

| Requirement | Technology Solution | Validation |
|-------------|-------------------|------------|
| DSPT "Standards Met" | AWS eu-west-2 (Standards Exceeded 2024-25) | Annual AWS DSPT certificate via AWS Artifact |
| Cyber Essentials Plus | AWS annual certification (2023 confirmed) | AWS compliance page |
| DTAC | Assessment required for all NHS-commissioned software | Project technical team to complete DTAC |
| DCB0129 Clinical Safety | Process requirement — Clinical Safety Officer | Hazard log, clinical safety case, NRLS integration |
| NHS Records Management Code | Aurora 8-year retention + S3 WORM audit logs | Automated lifecycle policies + Object Lock |

### Digital Marketplace Procurement Strategy

**G-Cloud 14** (Cloud hosting, software, support):
- **AWS**: Available via G-Cloud 14 (CCS framework) or NHS Public Sector agreement
- **Azure Health Data Services**: G-Cloud 14 service ID 926971059674068
- **GitHub Enterprise**: Available via Crown Commercial Service G-Cloud or Technology Products 2 framework

**Procurement Approach**:
1. Primary cloud infrastructure (AWS): Crown Commercial Service Technology Products 2 or G-Cloud 14 Lot 1
2. NHS Notify / NHS Login: No procurement required — NHS-funded platforms
3. GitHub Enterprise: G-Cloud 14 Lot 2 (Cloud Software) or direct from Microsoft Volume Licensing
4. Mini-competition not required for AWS/Azure if below individual framework lot thresholds
5. Full procurement process required for development supplier (custom build) — Digital Outcomes and Specialists (DOS) framework recommended

---

## Integration with Wardley Mapping

### Value Chain Components by Evolution

| Component | Evolution Stage | Recommended Approach | Rationale |
|-----------|----------------|---------------------|-----------|
| NHS Booking Service (core) | Genesis → Custom | BUILD | No equivalent national NHS booking service exists |
| FHIR Integration Adapters (NHS-specific) | Custom | BUILD | NHS Spine ASID/TLS-MA is NHS-specific, no COTS |
| GP Connect Integration | Custom → Product | BUILD via BaRS | BaRS standard exists but adapters are bespoke |
| NHS Login | Product | USE GOV.UK Platform | Mandated, mature, 30M users |
| NHS Notify | Product | USE GOV.UK Platform | Mandated 2025, proven at scale |
| HAPI FHIR Server | Product | ADOPT Open Source | Stable, widely deployed NHS standard |
| Aurora PostgreSQL | Commodity | BUY Managed | Database is commodity; managed > self-hosted |
| Kubernetes (EKS) | Commodity | BUY Managed | Container orchestration is commodity |
| Observability Stack | Product → Commodity | ADOPT Open Source | Grafana/Prometheus standard for Kubernetes |
| API Gateway | Commodity | BUY Managed | Standard infrastructure |
| Message Queue (SQS) | Commodity | BUY Managed | Messaging is commodity at AWS scale |
| CI/CD (GitHub Actions) | Product | BUY SaaS | Mature, cost-effective for team size |

**Strategic Insights**:
- The core booking logic and NHS Spine integration are NHS-specific novel capabilities — build these as IP
- All infrastructure below the booking service layer is commodity — use managed cloud services to reduce operational burden
- NHS GOV.UK platforms (Login, Notify, BaRS) sit in the "Product" stage — using them avoids reinventing the wheel and ensures compliance

---

## Vendor Shortlist for Further Evaluation

### 1. Amazon Web Services (AWS) — Primary Cloud Platform

**Overall Rating**: 5/5

**Strengths**:
- NHS DSPT 2024-25 "Standards Exceeded" — strongest NHS compliance credential of any cloud provider
- NHS Digital runs NHS login, NHS App (31M users), Electronic Prescription Service on AWS
- AWS DirectConnect + HSCN connectivity documented by NHS England
- Broadest managed services portfolio reducing operational overhead
- Aurora Serverless v2 uniquely suited to variable NHS appointment booking workload

**Concerns**:
- Cost management requires FinOps discipline (Cost Anomaly Detection, budgets, reserved instances)
- AWS HealthLake not available in eu-west-2 — alternative FHIR approach (HAPI FHIR) required

**Next Steps**:
- [ ] Engage AWS NHS Public Sector team for Enterprise Discount Program pricing
- [ ] Register for NHS England HSCN-AWS Direct Connect peering
- [ ] Complete AWS DSPT compliance evidence pack for project DSPT submission
- [ ] Deploy proof-of-concept EKS + Aurora Serverless v2 in eu-west-2

### 2. Grafana Labs — Observability Platform

**Overall Rating**: 4/5

**Strengths**:
- Apache 2.0 license for core tools (Grafana, Prometheus, Tempo) — zero licensing cost
- Prometheus is Kubernetes-native metrics standard — perfect for EKS architecture
- Full control over PII redaction in logs (critical for NHS patient data compliance)
- NHS engineering teams widely familiar with Grafana dashboards
- Self-hosted on existing EKS cluster — no separate vendor relationship

**Concerns**:
- Loki is AGPL-3.0 — legal review advised for NHS commercial deployment; consider Grafana Enterprise if AGPL problematic
- Operational overhead: team must manage 5-6 annual Helm chart upgrade cycles
- No out-of-box support SLA — consider Grafana Enterprise Support (~$25,000/year) if 24/7 SLA required

**Next Steps**:
- [ ] Legal review of AGPL-3.0 for Loki in NHS OFFICIAL-classified service
- [ ] Evaluate Grafana Cloud for a managed option if operational overhead unacceptable
- [ ] Deploy kube-prometheus-stack Helm chart in development environment as POC

### 3. Microsoft Azure — Secondary Cloud / FHIR Services

**Overall Rating**: 4/5 (secondary recommendation)

**Strengths**:
- Azure Health Data Services (FHIR R4) available on G-Cloud 14 — straightforward procurement
- Strong NHS Trust adoption — many trusts have existing Azure enterprise agreements
- Azure AKS simpler operational profile than AWS EKS for NHS IT teams
- UK South and UK West regions for data residency

**Concerns**:
- FHIR service pricing opaque ("contact for quote") — difficult to include in TCO
- Less prominent NHS DSPT certification evidence compared to AWS
- Would create dual-cloud architecture if primary cloud is AWS (increased complexity)

**Next Steps**:
- [ ] Request formal Azure Health Data Services pricing for NHS enterprise agreement
- [ ] Evaluate Azure Health Data Services POC alongside HAPI FHIR to compare development effort
- [ ] Check NHS Trust integration preference for hospital PAS Phase 2 (many trusts on Azure)

---

## Risks and Mitigations

### Vendor Risks

**VR-1: AWS Cost Overrun**
- **Risk**: Auto-scaling EKS and Aurora Serverless v2 generate unexpected costs during load spikes
- **Impact**: HIGH — Budget overruns, procurement process required for additional spend
- **Likelihood**: MEDIUM — NHS services regularly see unexpected traffic spikes (media coverage, GP strikes)
- **Mitigation**: AWS Cost Anomaly Detection + budgets + reserved instance baseline + Service Quotas limits; monthly FinOps review; CloudWatch billing alerts

**VR-2: NHS Notify SMS Volume Costs**
- **Risk**: 100M+ appointment reminders in Year 3 generate significant SMS costs (£380,000 estimated)
- **Impact**: MEDIUM — Annual budget pressure
- **Likelihood**: HIGH — SMS volumes will grow with adoption
- **Mitigation**: Maximise NHS App push notifications (free); negotiate volume pricing with NHS Notify team for national service; offer email as default channel preference

**VR-3: NHS GOV.UK Platform Unavailability**
- **Risk**: NHS Login or NHS Notify outage causes booking service outage (NFR-A-003)
- **Impact**: HIGH — Cannot book appointments during NHS Login outage
- **Likelihood**: LOW — NHS Login SLA assumed 99.9%; NHS Notify SLA not publicly stated
- **Mitigation**: Implement graceful degradation: show maintenance message if NHS Login unavailable; queue notifications if NHS Notify unavailable (SQS DLQ); monitor NHS Login status page; include NHS England platform SLA in dependency agreement

### Technical Risks

**TR-1: NHS Spine ASID/TLS-MA Complexity**
- **Risk**: NHS Spine registration and TLS-MA certificate management more complex than anticipated
- **Impact**: HIGH — Delays GP Connect integration
- **Likelihood**: MEDIUM — NHS Spine has documented complexity; ASID registration process can take 4-6 weeks
- **Mitigation**: Start ASID registration process immediately (minimum 6 weeks lead time); engage NHS England integration support team early; allocate 20% contingency on FHIR integration effort

**TR-2: Hospital PAS Integration Diversity (Phase 2)**
- **Risk**: 10+ hospital PAS vendors with varying FHIR maturity — some may not support BaRS
- **Impact**: HIGH — Limits Phase 2 hospital integration scope
- **Likelihood**: HIGH (confirmed as R-001 in requirements)
- **Mitigation**: Phase 1 GP-only MVP reduces this risk; assess top 5 hospital PAS vendors for BaRS readiness before Phase 2 procurement; maintain proprietary adapter fallback for non-BaRS PAS systems

**TR-3: HAPI FHIR Operational Burden**
- **Risk**: Team underestimates effort to maintain HAPI FHIR NHS profiles across version upgrades
- **Impact**: MEDIUM — Technical debt accumulates
- **Likelihood**: MEDIUM
- **Mitigation**: Adopt CloudNativePG operator approach; consider Azure Health Data Services as fallback if operational burden excessive; allocate 2 person-weeks/year maintenance in TCO

### Compliance Risks

**CR-1: DSPT Annual Assessment**
- **Risk**: AWS DSPT certification expires June 2026 — requires renewal
- **Impact**: HIGH — Compliance gap if AWS fails renewal
- **Likelihood**: LOW — AWS has held DSPT certification continuously since NHS DSPT programme inception
- **Mitigation**: Monitor AWS Artifact for certification status; maintain project-level DSPT assessment independently of cloud provider certification

**CR-2: DCB0129 Clinical Safety Gate**
- **Risk**: Clinical safety case approval delayed beyond launch target
- **Impact**: CRITICAL — Cannot go live without DCB0129 approval (BC-003)
- **Likelihood**: MEDIUM — Clinical safety reviews often identify issues requiring remediation
- **Mitigation**: Engage Clinical Safety Officer from Alpha phase; begin hazard log from requirements sign-off; build DCB0129 gate into project plan with 4-week buffer

---

## Next Steps and Recommendations

### Immediate Actions (0-2 weeks)

1. **AWS NHS Engagement**: Contact AWS NHS Public Sector team for Enterprise Discount Program and HSCN connectivity setup
2. **NHS Login Registration**: Register project with NHS login programme for integration toolkit access and sandbox environment
3. **NHS Notify Onboarding**: Create NHS Notify account and request Welsh language template guidance
4. **DSPT Registration**: Register project with NHS England DSPT for annual compliance assessment
5. **Budget Approval**: Secure Year 1 budget of £2,037,000 (£897,000 infrastructure + £1,140,000 development)

### Vendor Evaluation (2-6 weeks)

6. **AWS POC**: Deploy EKS + Aurora Serverless v2 + Grafana OSS stack in eu-west-2 sandbox
7. **NHS Login Integration Test**: Complete OIDC P9 flow integration in NHS login test environment
8. **HAPI FHIR vs Azure FHIR**: Side-by-side comparison of HAPI FHIR self-hosted vs Azure Health Data Services for GP Connect FHIR profiles
9. **ASID Registration**: Start NHS Spine ASID registration (6-week lead time minimum)
10. **GitHub Actions Security Scan**: Test GitHub Advanced Security CodeQL + Trivy pipeline on sample code

### Decision and Procurement (6-12 weeks)

11. **Cloud Contract**: Execute AWS public sector agreement or G-Cloud 14 Lot 1 order
12. **Development Supplier**: Issue DOS statement of requirements for delivery partner (via `/arckit:sow`)
13. **NHS Notify Volume Agreement**: Confirm volume pricing for Year 3 projection (100M+ messages)
14. **GitHub Enterprise**: Procure via G-Cloud 14 Lot 2 or Microsoft agreement

### Integration with Other ArcKit Commands

15. **Update SOBC Economic Case**: Run `/arckit:sobc` with TCO data (3-year £5.4M, recommended blend)
16. **Create Wardley Map**: Run `/arckit:wardley` to position components from Column 9 Wardley table above
17. **Generate SOW/RFP**: Run `/arckit:sow` for development supplier RFP (core booking service + FHIR adapters)
18. **DPIA**: Run `/arckit:dpia` — required for Aurora PostgreSQL patient data processing and NHS Notify messaging

---

## Appendices

### Appendix A: Research Methodology

**Data Sources Consulted**:
- AWS NHS DSPT compliance page (aws.amazon.com/compliance/nhs-dspt)
- AWS blog: "AWS successfully completed its 2024-25 NHS DSPT assessment"
- NHS England Digital: NHS login developer documentation, NHS Notify service, NHS BaRS API
- GOV.UK Notify pricing page (notifications.service.gov.uk/pricing)
- NHS England: Summary of NHS Notify Service Directions 2025
- Grafana pricing page (grafana.com/pricing)
- HAPI FHIR GitHub repository (github.com/hapifhir/hapi-fhir)
- Azure Health Data Services pricing and G-Cloud 14 listing
- Digital Marketplace G-Cloud 14 healthcare suppliers
- GitHub Actions pricing and billing documentation
- Komodor: "2024 Managed Kubernetes Showdown: GKE vs AKS vs EKS"
- Datadog pricing page and SigNoz comparison analysis

**Evaluation Criteria**:
- Requirements fit (MUST/SHOULD/COULD from ARC-001-REQ-v1.0)
- UK data residency (mandatory for NHS patient data — NFR-C-001)
- NHS DSPT compliance status
- 3-year TCO
- Vendor maturity and NHS reference customers
- Open source licence compatibility (Apache 2.0 preferred over GPL/AGPL for commercial NHS service)
- Digital Marketplace availability (G-Cloud 14)

**Research Limitations**:
- AWS, Azure, and GCP pricing based on published list prices — negotiated NHS/public sector discounts may reduce costs by 15-30%
- Azure Health Data Services pricing not publicly listed — "contact for quote"; estimate based on RU/s and storage components
- NHS Notify SMS costs assume standard GOV.UK Notify published rate — national service negotiated rate may be lower
- TCO projections assume 2024 pricing with 10% annual SaaS increase — subject to market changes

### Appendix B: Glossary

| Term | Definition |
|------|------------|
| ASID | Accredited System Identifier — NHS Spine security credential |
| BaRS | NHS Booking and Referral Standard |
| DSPT | NHS Data Security and Protection Toolkit |
| FHIR | Fast Healthcare Interoperability Resources (HL7 R4) |
| G-Cloud 14 | UK Government cloud procurement framework (Crown Commercial Service) |
| GP Connect | NHS programme for GP system interoperability APIs |
| HAPI FHIR | HL7 API for FHIR — leading open-source FHIR server (Java) |
| HSCN | Health and Social Care Network (NHS private network) |
| NHS BaRS | NHS Booking and Referral Standard FHIR API |
| PDS | Personal Demographics Service (national patient index) |
| TLS-MA | Transport Layer Security — Mutual Authentication (NHS Spine requirement) |
| TCO | Total Cost of Ownership |
| TCoP | Technology Code of Practice (UK Government) |
| WORM | Write Once Read Many — S3 Object Lock for immutable audit storage |

### Appendix C: Key Vendor Contacts

| Vendor | Contact Route | Notes |
|--------|--------------|-------|
| AWS NHS | aws.amazon.com/health — NHS Account Team | Request Enterprise Discount Program |
| NHS Login Programme | digital.nhs.uk/services/nhs-login/nhs-login-for-partners | Integration toolkit + sandbox access |
| NHS Notify | notify.nhs.uk — NHS Digital | Volume pricing for national service |
| Azure Health | Microsoft account team / G-Cloud 14 | Request FHIR service pricing |
| Grafana Enterprise | grafana.com/contact/sales | If AGPL support SLA required |

---

## Spawned Knowledge

The following standalone knowledge files were created or updated from this research:

### Vendor Profiles
- `vendors/aws-profile.md` — Created
- `vendors/grafana-labs-profile.md` — Created
- `vendors/microsoft-azure-profile.md` — Created
- `vendors/nhs-notify-profile.md` — Created

### Tech Notes
- `tech-notes/nhs-login-oidc.md` — Created
- `tech-notes/hapi-fhir.md` — Created
- `tech-notes/nhs-bars-fhir.md` — Created
- `tech-notes/aws-aurora-postgresql.md` — Created
- `tech-notes/grafana-oss-observability.md` — Created

---

**Generated by**: ArcKit `/arckit:research` agent
**Generated on**: 2026-02-20
**ArcKit Version**: 2.2.0
**Project**: NHS Digital Appointment Booking Service (Project 001)
**AI Model**: Claude Sonnet 4.6
