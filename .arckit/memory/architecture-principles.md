# Enterprise Architecture Principles

## Document Information

| Field | Value |
|-------|-------|
| **Document ID** | ARC-GLOBAL-PRIN-v1.0 |
| **Project** | NHS Digital Appointment Service (Global) |
| **Document Type** | Enterprise Architecture Principles |
| **Classification** | OFFICIAL |
| **Version** | 1.0 |
| **Status** | DRAFT |
| **Date** | 2026-01-22 |
| **Owner** | Enterprise Architecture Team |

## Revision History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2026-01-22 | ArcKit AI | Initial creation from `/arckit.principles` command |

---

## Executive Summary

This document establishes the immutable principles governing all technology architecture decisions within the organisation. These principles ensure consistency, security, scalability, and alignment with business strategy across all projects and initiatives.

Given the NHS context, these principles incorporate healthcare-specific considerations including NHS Digital standards, clinical safety requirements, patient data protection, and accessibility mandates.

**Scope**: All technology projects, systems, and initiatives
**Authority**: Enterprise Architecture Review Board
**Compliance**: Mandatory unless exception approved by CTO/CIO

**Philosophy**: These principles are **technology-agnostic** - they describe WHAT qualities the architecture must have, not HOW to implement them with specific products. Technology selection happens during research and design phases guided by these principles.

---

## I. Strategic Principles

### 1. Scalability and Elasticity

**Principle Statement**:
All systems MUST be designed to scale horizontally to meet demand, with the ability to dynamically adjust capacity based on load.

**Rationale**:
Healthcare demand is unpredictable and variable—appointment booking surges during flu season, pandemic responses require rapid scaling, and national rollouts must handle millions of concurrent users. Systems must handle both growth and traffic spikes without manual intervention or architectural changes.

**Implications**:
- Design for stateless components that can be replicated
- Avoid hard-coded limits or fixed capacity assumptions
- Plan for distributed deployment across multiple compute nodes
- Use load balancing to distribute traffic across instances
- Implement auto-scaling based on demand metrics
- Consider geographic distribution for national services

**Validation Gates**:
- [ ] System can scale horizontally (add more instances)
- [ ] No single points of failure that limit scaling
- [ ] Load testing demonstrates capacity growth with added resources
- [ ] Scaling metrics and triggers defined
- [ ] Cost model accounts for variable capacity
- [ ] Peak load scenarios modelled (e.g., vaccination campaigns, national announcements)

**Example Scenarios**:
| Scenario | Good Practice | Violation |
|----------|---------------|-----------|
| Appointment booking surge | Stateless services auto-scale based on queue depth | Monolithic application with fixed server allocation |
| New region rollout | Horizontal scaling adds capacity without code changes | Requires architecture changes to support additional load |
| Seasonal variation | Automatic capacity reduction during off-peak periods | Fixed infrastructure regardless of demand |

**Common Violations**:
- Storing session state in local memory
- Hard-coded connection pool limits
- Single database instance without read replicas
- Fixed infrastructure sizing based on average load

---

### 2. Resilience and Fault Tolerance

**Principle Statement**:
All systems MUST gracefully degrade when dependencies fail and recover automatically without data loss or manual intervention.

**Rationale**:
Healthcare systems are safety-critical. Failures are inevitable in distributed systems, but patient care cannot stop. The architecture must assume failures will occur and design for resilience rather than perfect reliability.

**Implications**:
- Implement circuit breakers for external dependencies
- Use timeouts on all network calls
- Retry with exponential backoff for transient failures
- Graceful degradation when non-critical services fail
- Automated health checks and recovery
- Avoid cascading failures through bulkhead isolation
- Offline-capable modes for critical clinical workflows

**Validation Gates**:
- [ ] Failure modes identified and mitigated
- [ ] Chaos engineering or fault injection testing performed
- [ ] Recovery Time Objective (RTO) and Recovery Point Objective (RPO) defined
- [ ] Automated failover tested
- [ ] Degraded mode behaviour documented
- [ ] Clinical safety implications of failures assessed (DCB0129/DCB0160)

**Example Scenarios**:
| Scenario | Good Practice | Violation |
|----------|---------------|-----------|
| Third-party API timeout | Circuit breaker opens, cached data served with staleness indicator | System hangs waiting for response |
| Database failover | Automatic promotion of replica, connections retry transparently | Manual DBA intervention required |
| Network partition | Queued operations retry when connectivity restored | Data loss or corrupted state |

**Common Violations**:
- Missing timeouts on external calls
- No circuit breaker implementation
- Synchronous dependencies on non-critical services
- No fallback behaviour defined

---

### 3. Interoperability and Integration

**Principle Statement**:
All systems MUST expose functionality through well-defined, versioned interfaces using industry-standard protocols. Direct database access across system boundaries is prohibited.

**Rationale**:
The NHS ecosystem requires integration across trusts, GP systems, national services, and third-party providers. Loose coupling through standard interfaces enables independent evolution, technology diversity, and system composability.

**Healthcare Integration Standards**:
- HL7 FHIR for clinical data exchange
- NHS Data Dictionary for coding standards
- GP Connect and NHS APIs for primary care integration
- NHS login for citizen authentication

**Implications**:
- Use standardised protocols (HTTP REST, GraphQL, message queuing, event streaming)
- Version all interfaces with backward compatibility strategy
- Publish interface specifications (API contracts, event schemas)
- No direct database access across system boundaries
- Asynchronous communication for non-real-time interactions
- NHS API standards compliance for national services

**Validation Gates**:
- [ ] Interface specifications published (OpenAPI, AsyncAPI, FHIR profiles)
- [ ] Versioning strategy defined
- [ ] Authentication and authorisation model documented
- [ ] Error handling and retry behaviour specified
- [ ] No direct database coupling across systems
- [ ] NHS interoperability standards compliance assessed

**Example Scenarios**:
| Scenario | Good Practice | Violation |
|----------|---------------|-----------|
| Patient data sharing | FHIR API with versioned resources and OAuth2 | Direct database queries between systems |
| GP integration | GP Connect APIs with standard consent model | Custom point-to-point integration |
| Appointment sync | Event-driven updates with idempotent handlers | Polling with timestamp comparison |

**Common Violations**:
- Shared databases between services
- Undocumented or unversioned APIs
- Custom integration protocols
- Tight coupling to specific vendor systems

---

### 4. Security by Design (NON-NEGOTIABLE)

**Principle Statement**:
All architectures MUST implement defence-in-depth security with zero-trust principles. Security is NOT a feature to be added later—it is a foundational requirement.

**Rationale**:
Patient data is among the most sensitive information categories. The threat landscape requires assuming breach, eliminating implicit trust, and continuously verifying all access requests. NHS systems must meet stringent regulatory and compliance requirements.

**Zero Trust Pillars**:
1. **Identity-Based Access**: No network-based trust; every request authenticated
2. **Least Privilege**: Grant minimum necessary permissions, time-boxed where possible
3. **Encryption Everywhere**: Data encrypted in transit and at rest
4. **Continuous Verification**: Monitor, log, and analyse all access patterns

**Mandatory Controls**:
- [ ] Multi-factor authentication for all staff and clinical access
- [ ] NHS login integration for citizen-facing services
- [ ] Service-to-service authentication (mutual TLS, signed tokens, or equivalent)
- [ ] Secrets management via secure vault (never in code or config files)
- [ ] Network segmentation with minimal trust zones
- [ ] Encryption at rest for all data stores (AES-256 or equivalent)
- [ ] Encrypted transport for all network communication (TLS 1.2+ or equivalent)
- [ ] Structured logging of all authentication/authorisation events
- [ ] Regular security testing (penetration testing, vulnerability scanning)

**NHS Compliance Frameworks**:
- Data Security and Protection Toolkit (DSPT) - Annual compliance required
- Cyber Essentials Plus - Mandatory for NHS contracts
- NHS Digital Technology Assessment Criteria (DTAC)
- DCB0129 Clinical Risk Management - For health IT systems

**Data Protection**:
- UK GDPR and Data Protection Act 2018
- Caldicott Principles for patient data
- Common Law Duty of Confidentiality
- NHS Confidentiality Code of Practice

**Exceptions**:
- NONE. Security principles are non-negotiable.
- Specific control implementations may vary with compensating controls approved by SIRO.

**Validation Gates**:
- [ ] Threat model completed and reviewed
- [ ] Security controls mapped to requirements
- [ ] DSPT compliance verified
- [ ] Penetration testing performed annually
- [ ] Clinical safety case (DCB0129) completed for health IT
- [ ] Data Protection Impact Assessment (DPIA) completed

---

### 5. Observability and Operational Excellence

**Principle Statement**:
All systems MUST emit structured telemetry (logs, metrics, traces) enabling real-time monitoring, troubleshooting, and capacity planning.

**Rationale**:
We cannot operate what we cannot observe. Instrumentation is a first-class architectural requirement, not an afterthought. For healthcare systems, rapid incident detection and resolution is critical for patient safety.

**Telemetry Requirements**:
- **Logging**: Structured logs with correlation IDs (excluding PII in logs)
- **Metrics**: Request volume, latency percentiles (p50, p95, p99), error rates
- **Tracing**: Distributed trace context for request flows
- **Alerting**: Service Level Objective (SLO)-based alerting with actionable runbooks

**Required Instrumentation**:
- Request volume, latency distribution, error rate
- Resource utilisation (CPU, memory, I/O, network)
- Business metrics (appointments booked, cancellation rate, DNA rate)
- Security events (auth failures, policy violations, suspicious patterns)
- Clinical safety events (near misses, system errors affecting care)

**Log Retention** (NHS Requirements):
- **Audit logs**: 8 years minimum (NHS Records Management Code)
- **Security logs**: 7 years (regulatory requirement)
- **Application logs**: 90 days minimum for troubleshooting
- **Metrics**: 2 years with aggregation for trend analysis

**Validation Gates**:
- [ ] Logging, metrics, tracing instrumented
- [ ] PII excluded from logs (pseudonymisation where needed)
- [ ] Dashboards and alerts configured
- [ ] Service Level Objectives (SLOs) and Service Level Indicators (SLIs) defined
- [ ] Runbooks created for common failure scenarios
- [ ] Capacity planning metrics tracked

---

## II. Data Principles

### 6. Data Sovereignty and Governance

**Principle Statement**:
Data classification, residency, retention, and access controls MUST comply with regulatory requirements and NHS data governance policies.

**Data Classification Tiers** (NHS Model):
1. **Public**: Published information, open data (NHS organisation info, service locations)
2. **Internal**: Staff-only information (operational data, non-patient information)
3. **Confidential**: Patient Identifiable Data (PID), staff records, business sensitive
4. **Restricted**: Special category data (mental health, sexual health, genetic data)

**Data Residency**:
- Patient data MUST remain within UK borders (UK GDPR)
- NHS Cloud Security Guidance for approved hosting
- Assured suppliers only for health data processing
- Cross-border transfers prohibited without legal basis and explicit approval

**Data Retention** (NHS Requirements):
- Patient records: Minimum 8 years (25 years for children's records)
- Audit trails: 8 years minimum
- Automatic deletion after retention period with audit log
- Legal hold process for investigations and litigation

**Validation Gates**:
- [ ] Data classification performed for all data stores
- [ ] Data Processing Impact Assessment (DPIA) completed
- [ ] UK hosting confirmed for patient data
- [ ] Retention policies configured with automated deletion
- [ ] Access controls enforce least privilege and Caldicott principles
- [ ] Data sharing agreements in place for external parties

---

### 7. Data Quality and Lineage

**Principle Statement**:
Data pipelines MUST maintain data quality standards and provide end-to-end lineage for auditability and troubleshooting.

**Quality Standards**:
- **Completeness**: No unexpected nulls in required fields (NHS Number, date of birth)
- **Consistency**: Cross-system data reconciliation (PDS synchronisation)
- **Accuracy**: Validation rules enforced at source (NHS Number check digit)
- **Timeliness**: Freshness SLAs defined and monitored

**NHS Data Standards**:
- NHS Data Dictionary for clinical coding
- SNOMED CT for clinical terminology
- dm+d for medicines
- NHS Number as primary patient identifier

**Lineage Requirements**:
- Source-to-target mapping documented for all data flows
- Transformation logic version-controlled and reviewable
- Data quality metrics tracked per pipeline
- Impact analysis capability for schema changes

**Validation Gates**:
- [ ] NHS Data Dictionary compliance verified
- [ ] Data quality rules defined and automated
- [ ] Lineage metadata captured and queryable
- [ ] Data contracts between producers and consumers
- [ ] Schema evolution strategy documented

---

### 8. Single Source of Truth

**Principle Statement**:
Every data domain MUST have a single authoritative source. Derived copies must be clearly labelled and synchronised.

**Rationale**:
Multiple authoritative sources create inconsistency, reconciliation overhead, and data integrity issues. For patient data, inconsistency can impact clinical decisions.

**NHS Authoritative Sources**:
- **Patient Demographics**: Personal Demographics Service (PDS)
- **Organisation Data**: Organisation Data Service (ODS)
- **GP Registration**: National Health Application and Infrastructure Services (NHAIS)
- **Medicines**: Dictionary of Medicines and Devices (dm+d)

**Implications**:
- Identify the system of record for each data domain
- Derived/cached copies are read-only and clearly labelled as such
- Synchronisation strategy defined for all derived copies
- Avoid bidirectional synchronisation (creates split-brain scenarios)
- PDS synchronisation for patient demographics

**Validation Gates**:
- [ ] System of record identified for each data entity
- [ ] PDS used as source for patient demographics
- [ ] Derived copies documented with sync frequency
- [ ] No bidirectional sync without conflict resolution strategy
- [ ] Master data management strategy for shared reference data

---

## III. Integration Principles

### 9. Loose Coupling

**Principle Statement**:
Systems MUST be loosely coupled through published interfaces, avoiding shared databases, file systems, or tight runtime dependencies.

**Rationale**:
Loose coupling enables independent deployment, technology diversity, team autonomy, and system evolution without breaking dependencies. Critical for the complex NHS supplier ecosystem.

**Implications**:
- Communicate through published APIs or asynchronous events
- No direct database access across system boundaries
- Each system manages its own data lifecycle
- Shared libraries kept minimal (favour duplication over coupling)
- Avoid distributed transactions across systems
- Vendor-neutral integration patterns

**Validation Gates**:
- [ ] Systems communicate via APIs or events, not database
- [ ] No shared mutable state
- [ ] Each system has independent data store
- [ ] Deployment of one system doesn't require deployment of another
- [ ] Interface changes versioned with backward compatibility
- [ ] No vendor lock-in through proprietary integration

---

### 10. Asynchronous Communication

**Principle Statement**:
Systems SHOULD use asynchronous communication for non-real-time interactions to improve resilience and decoupling.

**Rationale**:
Asynchronous patterns reduce temporal coupling, improve fault tolerance, and enable better scalability. Essential for reliable integration with multiple external systems.

**When to Use Async**:
- Appointment reminders and notifications
- Batch data synchronisation (PDS updates, GP feeds)
- Audit event publication
- Long-running operations (report generation, bulk operations)
- Integration with unreliable or slow external systems

**When Synchronous is Acceptable**:
- Real-time appointment availability checks
- Patient identity verification
- Immediate booking confirmation
- Query operations (read-only, idempotent)

**Validation Gates**:
- [ ] Async patterns used for non-real-time flows
- [ ] Message durability and delivery guarantees defined
- [ ] Event schemas versioned and published
- [ ] Dead letter queues and error handling configured
- [ ] Idempotent message handling implemented

---

## IV. Quality Attributes

### 11. Performance and Efficiency

**Principle Statement**:
All systems MUST meet defined performance targets under expected load with efficient use of computational resources.

**Performance Targets** (NHS Service Standard):
- **Page Load**: < 3 seconds for citizen-facing pages
- **API Response**: p95 < 500ms for interactive operations
- **Booking Confirmation**: < 5 seconds end-to-end
- **Search Results**: < 2 seconds for appointment search
- **Concurrent Users**: Support anticipated peak load with 50% headroom

**Implications**:
- Performance requirements defined before implementation
- Load testing performed before production deployment
- Performance monitoring continuous, not just point-in-time
- Optimise hot paths identified through profiling
- Caching strategies for expensive operations
- Efficient data access patterns

**Validation Gates**:
- [ ] Performance requirements defined with measurable targets
- [ ] Load testing performed at expected capacity + 50% headroom
- [ ] Performance metrics monitored in production
- [ ] Capacity planning model defined
- [ ] Performance regression testing in CI/CD pipeline

---

### 12. Availability and Reliability

**Principle Statement**:
All systems MUST meet defined availability targets with automated recovery and minimal data loss.

**Availability Targets** (by Service Tier):
| Tier | Uptime SLA | RTO | RPO | Example |
|------|------------|-----|-----|---------|
| Platinum | 99.99% | 15 min | 0 | Emergency booking |
| Gold | 99.95% | 1 hour | 15 min | Routine appointment booking |
| Silver | 99.9% | 4 hours | 1 hour | Reporting, analytics |
| Bronze | 99.5% | 24 hours | 24 hours | Batch processing |

**High Availability Patterns**:
- Redundancy across availability zones / data centres
- Automated health checks and failover
- Active-active or active-passive configurations
- Regular disaster recovery testing (minimum quarterly)

**Validation Gates**:
- [ ] Availability SLA defined and agreed with stakeholders
- [ ] RTO and RPO requirements documented
- [ ] Redundancy strategy implemented
- [ ] Failover tested regularly (minimum quarterly)
- [ ] Backup and restore procedures validated
- [ ] Business continuity plan documented

---

### 13. Maintainability and Evolvability

**Principle Statement**:
All systems MUST be designed for change, with clear separation of concerns, modular architecture, and comprehensive documentation.

**Rationale**:
Software spends most of its lifetime in maintenance. NHS contracts typically span 5-10 years with ongoing evolution requirements. Design decisions should optimise for understandability and modifiability.

**Implications**:
- Modular architecture with clear boundaries
- Separation of concerns (business logic, data access, presentation)
- Code is self-documenting with meaningful names
- Architecture Decision Records (ADRs) for significant choices
- Automated testing to enable confident refactoring
- Handover documentation for operational teams

**Validation Gates**:
- [ ] Architecture documentation exists and is current
- [ ] Module boundaries clear with defined responsibilities
- [ ] Automated test coverage enables safe refactoring
- [ ] Architecture Decision Records (ADRs) document key choices
- [ ] Operational runbooks complete
- [ ] Knowledge transfer completed before go-live

---

## V. Development Practices

### 14. Infrastructure as Code

**Principle Statement**:
All infrastructure MUST be defined as code, version-controlled, and deployed through automated pipelines.

**Rationale**:
Manual infrastructure changes create drift, inconsistency, and undocumented state. Infrastructure as Code (IaC) enables repeatability, auditability, and disaster recovery. Essential for NHS DSPT compliance evidence.

**Implications**:
- All infrastructure defined in declarative code
- Infrastructure changes go through code review
- Environments are reproducible from code
- No manual changes to production infrastructure
- Infrastructure versioned alongside application code
- Secrets never stored in infrastructure code

**Validation Gates**:
- [ ] Infrastructure defined as code
- [ ] Infrastructure code version-controlled
- [ ] Automated deployment pipeline for infrastructure
- [ ] No manual infrastructure changes in production
- [ ] Disaster recovery tested from code

---

### 15. Automated Testing

**Principle Statement**:
All code changes MUST be validated through automated testing before deployment to production.

**Test Pyramid**:
- **Unit Tests**: Fast, isolated, high coverage (70-80% of tests)
- **Integration Tests**: Test component interactions (15-20% of tests)
- **End-to-End Tests**: Critical user journeys (5-10% of tests)

**Required Test Types**:
- Functional tests (does it work correctly?)
- Performance tests (is it fast enough?)
- Security tests (is it secure? OWASP top 10)
- Accessibility tests (is it usable by everyone? WCAG 2.2 AA)
- Resilience tests (does it handle failures?)

**Validation Gates**:
- [ ] Automated tests exist and pass before merge
- [ ] Test coverage meets defined thresholds (minimum 80%)
- [ ] Critical paths have end-to-end tests
- [ ] Performance tests run regularly
- [ ] Accessibility testing automated where possible

---

### 16. Continuous Integration and Deployment

**Principle Statement**:
All code changes MUST go through automated build, test, and deployment pipelines with quality gates at each stage.

**Pipeline Stages**:
1. **Source Control**: All changes committed to version control (Git)
2. **Build**: Automated compilation and packaging
3. **Test**: Automated test execution (unit, integration, security)
4. **Security Scan**: Dependency and code vulnerability scanning
5. **Quality Gate**: Code quality, coverage, security thresholds
6. **Deployment**: Automated deployment to environments

**Quality Gates**:
- All tests must pass
- No critical/high security vulnerabilities
- Code review approval required
- Minimum test coverage threshold met
- Accessibility checks passed

**Deployment Safety**:
- Blue-green or canary deployments for production
- Automated rollback capability
- Feature flags for controlled rollout
- Change approval for production deployments

**Validation Gates**:
- [ ] Automated CI/CD pipeline exists
- [ ] Pipeline includes security scanning
- [ ] Deployment is automated and repeatable
- [ ] Rollback capability tested
- [ ] Change management process integrated

---

## VI. Accessibility Principles

### 17. Inclusive Design (NON-NEGOTIABLE)

**Principle Statement**:
All citizen-facing services MUST meet WCAG 2.2 Level AA accessibility standards and follow inclusive design principles.

**Rationale**:
The Public Sector Bodies (Websites and Mobile Applications) Accessibility Regulations 2018 mandate accessibility. More importantly, NHS services must be usable by everyone, including those with disabilities, older adults, and users with temporary impairments.

**Accessibility Requirements**:
- WCAG 2.2 Level AA compliance (legal minimum)
- GOV.UK Design System components (tested for accessibility)
- Screen reader compatibility
- Keyboard-only navigation
- Sufficient colour contrast
- Clear, plain English content

**Testing Requirements**:
- Automated accessibility testing in CI/CD
- Manual testing with assistive technologies
- User testing with people with disabilities
- Regular accessibility audits

**Validation Gates**:
- [ ] WCAG 2.2 AA compliance verified
- [ ] Automated accessibility testing in pipeline
- [ ] Screen reader testing completed
- [ ] Keyboard navigation tested
- [ ] Accessibility statement published
- [ ] User research includes users with access needs

---

## VII. Exception Process

### Requesting Architecture Exceptions

Principles are mandatory unless a documented exception is approved by the Enterprise Architecture Review Board.

**Valid Exception Reasons**:
- Technical constraints that prevent compliance (with evidence)
- Regulatory or legal requirements that conflict
- Transitional state during migration (with remediation plan)
- Pilot/proof-of-concept with defined end date
- Legacy system integration with documented constraints

**Exception Request Requirements**:
- [ ] Justification with business/technical rationale
- [ ] Alternative approach and compensating controls
- [ ] Risk assessment and mitigation plan
- [ ] Expiration date (exceptions are time-bound, maximum 12 months)
- [ ] Remediation plan to achieve compliance
- [ ] Clinical safety assessment if health IT (DCB0129)

**Approval Process**:
1. Submit exception request to Enterprise Architecture team
2. Clinical safety review if applicable
3. Review by architecture review board
4. SIRO approval for security-related exceptions
5. CTO/CIO approval for exceptions to critical principles
6. Document exception in project architecture documentation
7. Regular review of exceptions (quarterly)

**Non-Negotiable Principles** (No Exceptions Permitted):
- Security by Design
- Inclusive Design (Accessibility)
- Data Sovereignty (UK residency for patient data)
- Clinical Safety Compliance

---

## VIII. Governance and Compliance

### Architecture Review Gates

All projects must pass architecture reviews at key milestones:

**Discovery/Alpha**:
- [ ] Architecture principles understood and accepted
- [ ] High-level approach aligns with principles
- [ ] No obvious principle violations
- [ ] Clinical safety requirements identified (DCB0129)

**Beta/Design**:
- [ ] Detailed architecture documented
- [ ] Compliance with each principle validated
- [ ] Exceptions requested and approved
- [ ] Security and data principles validated
- [ ] Clinical safety case draft complete
- [ ] Accessibility assessment complete

**Pre-Production**:
- [ ] Implementation matches approved architecture
- [ ] All validation gates passed
- [ ] Operational readiness verified
- [ ] DSPT compliance confirmed
- [ ] Clinical safety case approved
- [ ] Accessibility audit passed

### Enforcement

- Architecture reviews are **mandatory** for all projects
- Principle violations must be remediated before production deployment
- Approved exceptions are time-bound and reviewed quarterly
- Clinical safety violations require immediate escalation
- Retrospective reviews for compliance on live systems

---

## IX. Appendix

### Principle Summary Checklist

| Principle | Category | Criticality | Validation |
|-----------|----------|-------------|------------|
| Scalability and Elasticity | Strategic | HIGH | Load testing, scaling metrics |
| Resilience and Fault Tolerance | Strategic | CRITICAL | Chaos testing, RTO/RPO |
| Interoperability and Integration | Strategic | HIGH | API specs, versioning, FHIR |
| Security by Design | Strategic | CRITICAL | DSPT, pen testing, threat model |
| Observability | Strategic | HIGH | Metrics, logs, traces |
| Data Sovereignty | Data | CRITICAL | UK residency, DPIA |
| Data Quality | Data | HIGH | NHS Data Dictionary compliance |
| Single Source of Truth | Data | HIGH | PDS synchronisation |
| Loose Coupling | Integration | HIGH | Deployment independence |
| Asynchronous Communication | Integration | MEDIUM | Async patterns used |
| Performance | Quality | HIGH | Load testing, SLA monitoring |
| Availability | Quality | CRITICAL | SLA monitoring, DR testing |
| Maintainability | Quality | MEDIUM | Documentation, tests, ADRs |
| Infrastructure as Code | DevOps | HIGH | IaC coverage, no manual changes |
| Automated Testing | DevOps | HIGH | 80% coverage, security tests |
| CI/CD | DevOps | HIGH | Pipeline exists, quality gates |
| Inclusive Design | Accessibility | CRITICAL | WCAG 2.2 AA, user testing |

### NHS-Specific Compliance Summary

| Requirement | Principle Mapping | Validation Method |
|-------------|-------------------|-------------------|
| DSPT Compliance | Security by Design | Annual assessment |
| DCB0129 Clinical Safety | Resilience, Data Quality | Clinical safety case |
| UK GDPR | Data Sovereignty, Security | DPIA, data flow mapping |
| Accessibility Regulations | Inclusive Design | WCAG audit, user testing |
| NHS Digital Standards | Interoperability, Data Quality | DTAC assessment |
| Cyber Essentials Plus | Security by Design | Annual certification |

---

**Generated by**: ArcKit `/arckit.principles` command
**Generated on**: 2026-01-22
**ArcKit Version**: 0.8.2
**Project**: NHS Digital Appointment Service (Global)
**AI Model**: Claude Opus 4.5
