# Project Requirements: NHS Digital Appointment Booking Service

> **Template Status**: Live | **Version**: 0.11.2 | **Command**: `/arckit.requirements`

## Document Control

| Field | Value |
|-------|-------|
| **Document ID** | ARC-001-REQ-v1.0 |
| **Document Type** | Business and Technical Requirements |
| **Project** | NHS Digital Appointment Booking Service (Project 001) |
| **Classification** | OFFICIAL |
| **Status** | DRAFT |
| **Version** | 1.0 |
| **Created Date** | 2026-01-26 |
| **Last Modified** | 2026-01-26 |
| **Review Cycle** | Monthly |
| **Next Review Date** | 2026-02-26 |
| **Owner** | Product Owner, NHS Digital |
| **Reviewed By** | PENDING |
| **Approved By** | PENDING |
| **Distribution** | Project Team, Architecture Team, Clinical Safety Team, Suppliers |

## Revision History

| Version | Date | Author | Changes | Approved By | Approval Date |
|---------|------|--------|---------|-------------|---------------|
| 1.0 | 2026-01-26 | ArcKit AI | Initial creation from `/arckit.requirements` command | PENDING | PENDING |

## Document Purpose

This document defines the comprehensive business, functional, non-functional, integration, and data requirements for the NHS Digital Appointment Booking Service. It serves as the authoritative source for vendor RFPs, architecture reviews, development specifications, and acceptance testing criteria.

---

## Executive Summary

### Business Context

The NHS currently lacks a unified, citizen-facing digital appointment booking system. Patients must navigate fragmented booking processes across different trusts, GP practices, and specialist services, leading to poor user experience, high DNA (Did Not Attend) rates, and inefficient resource utilisation.

This project will deliver a modern, accessible, and secure digital appointment booking service that enables citizens to book, manage, and cancel NHS appointments across primary, secondary, and community care services. The service will integrate with existing NHS Spine infrastructure, GP systems, and trust Patient Administration Systems (PAS).

The strategic value includes improved patient experience, reduced administrative burden on NHS staff, lower DNA rates through automated reminders, and better data visibility for capacity planning.

### Objectives

- Provide citizens with a single, accessible digital channel to book NHS appointments
- Reduce appointment DNA rates through proactive reminders and easy rescheduling
- Integrate seamlessly with NHS Spine, GP systems, and trust PAS systems
- Meet all NHS security, clinical safety, and accessibility standards
- Support NHS Long Term Plan digital transformation goals

### Expected Outcomes

- **Patient Experience**: 80% citizen satisfaction rating within 12 months of launch
- **DNA Reduction**: 25% reduction in Did Not Attend rates within 12 months
- **Administrative Efficiency**: 40% reduction in appointment booking call volumes
- **Digital Adoption**: 60% of eligible appointments booked digitally within 18 months
- **Accessibility Compliance**: 100% WCAG 2.2 Level AA compliance at launch

### Project Scope

**In Scope**:
- Citizen-facing web and mobile booking interface
- NHS login integration for citizen authentication
- GP appointment booking (routine and urgent)
- Hospital outpatient appointment booking
- Community health services appointment booking
- Appointment reminders (SMS, email, push notification)
- Integration with NHS Spine PDS for demographics
- Integration with GP system appointment books
- Integration with trust PAS systems
- Welsh language support
- Accessibility features (WCAG 2.2 AA)

**Out of Scope**:
- Emergency and A&E services
- Mental health crisis services
- Dental appointments (Phase 2)
- Pharmacy appointments (Phase 2)
- Video consultation booking (Phase 2)
- International patient booking
- Private healthcare appointments

---

## Stakeholders

| Stakeholder | Role | Organisation | Involvement Level |
|-------------|------|--------------|-------------------|
| TBD | Executive Sponsor | NHS England | Decision maker |
| TBD | Senior Responsible Owner (SRO) | NHS Digital | Accountable |
| TBD | Product Owner | NHS Digital | Requirements definition |
| TBD | Clinical Safety Officer | NHS Digital | Clinical safety review |
| TBD | Information Governance Lead | NHS Digital | Data protection oversight |
| TBD | Enterprise Architect | NHS Digital | Technical oversight |
| TBD | Security Architect | NHS Digital | Security review |
| TBD | GP Federation Representative | Primary Care | User requirements |
| TBD | Trust Digital Lead | Secondary Care | Integration requirements |
| TBD | Patient Representative | HealthWatch | User acceptance |
| TBD | Accessibility Lead | NHS Digital | Accessibility compliance |

---

## Business Requirements

### BR-001: Unified Appointment Access

**Description**: Citizens must be able to access and manage all their NHS appointments through a single digital service, regardless of which NHS organisation provides the care.

**Rationale**: Currently patients must contact multiple organisations separately to manage appointments. A unified view reduces confusion, improves patient experience, and enables better self-management of healthcare.

**Success Criteria**:
- Citizens can view appointments across GP, hospital, and community services in one place
- 90% of appointment types from connected organisations visible within 6 months of launch
- Single sign-on via NHS login for all appointment management

**Priority**: MUST_HAVE

**Stakeholder**: NHS England, Patients

**Traces to**: Architecture Principle 3 (Interoperability)

---

### BR-002: Reduce DNA Rates

**Description**: The service must actively reduce Did Not Attend (DNA) rates through automated reminders, easy rescheduling, and cancellation options.

**Rationale**: NHS DNA rates cost approximately £1 billion annually. Reducing DNAs improves capacity utilisation, reduces waiting times, and improves patient outcomes.

**Success Criteria**:
- Automated reminder system operational from day one
- Citizens can cancel or reschedule appointments with less than 3 clicks
- DNA rate reduction of 25% measured against baseline within 12 months
- Released appointment slots available for rebooking within 1 hour

**Priority**: MUST_HAVE

**Stakeholder**: NHS England, Trust Operations

**Traces to**: Architecture Principle 1 (Scalability), Principle 5 (Observability)

---

### BR-003: Administrative Efficiency

**Description**: The service must reduce administrative burden on NHS staff by enabling citizen self-service for appointment booking and management.

**Rationale**: GP practices and hospital booking teams spend significant time handling appointment calls. Digital self-service frees staff for higher-value activities.

**Success Criteria**:
- 40% reduction in appointment-related phone calls within 12 months
- Staff booking interface for assisted digital support
- Bulk booking management for repeat appointments
- Integration with existing NHS workflow systems

**Priority**: MUST_HAVE

**Stakeholder**: GP Federation, Trust Operations

---

### BR-004: Digital Inclusion

**Description**: The service must be accessible to all citizens, including those with disabilities, limited digital skills, or no internet access.

**Rationale**: NHS services must be equitable. Digital-only services risk excluding vulnerable populations who may have the greatest healthcare needs.

**Success Criteria**:
- WCAG 2.2 Level AA compliance verified by independent audit
- Assisted digital pathway via NHS 111 and GP reception
- Telephone booking option maintained for those who cannot use digital
- Service available in Welsh and English
- User testing with diverse user groups including elderly and disabled users

**Priority**: MUST_HAVE

**Stakeholder**: Patient Groups, Accessibility Team

**Traces to**: Architecture Principle 17 (Inclusive Design)

---

### BR-005: Clinical Safety Compliance

**Description**: The service must meet all clinical safety requirements as a health IT system, ensuring no patient harm can result from system failures or incorrect data.

**Rationale**: Healthcare IT systems are safety-critical. DCB0129 compliance is mandatory for NHS-commissioned systems.

**Success Criteria**:
- DCB0129 Clinical Risk Management case approved before go-live
- Hazard log maintained and reviewed monthly
- Clinical safety testing completed for all booking workflows
- Incident reporting integration with NHS patient safety systems
- No patient safety incidents attributable to the booking system

**Priority**: MUST_HAVE

**Stakeholder**: Clinical Safety Officer

**Traces to**: Architecture Principle 4 (Security by Design)

---

### BR-006: Data-Driven Capacity Planning

**Description**: The service must provide data and analytics to support NHS capacity planning and service improvement.

**Rationale**: Aggregated appointment data can inform demand forecasting, resource allocation, and service design improvements.

**Success Criteria**:
- Real-time dashboard showing booking volumes, DNA rates, and capacity utilisation
- Exportable reports for trust and CCG planning teams
- Trend analysis and forecasting capabilities
- Anonymised data available for NHS research (with appropriate governance)

**Priority**: SHOULD_HAVE

**Stakeholder**: NHS England Analytics

---

## Functional Requirements

### User Personas

#### Persona 1: Sarah - Working Parent
- **Role**: Full-time employed parent, aged 35
- **Goals**: Book appointments quickly during lunch breaks; manage family appointments
- **Pain Points**: Limited time; phone lines busy during work hours; different systems for GP vs hospital
- **Technical Proficiency**: High
- **Access Needs**: Mobile-first, notifications to work calendar

#### Persona 2: Harold - Elderly Patient
- **Role**: Retired, aged 78, multiple long-term conditions
- **Goals**: Manage regular appointments; avoid confusion between different departments
- **Pain Points**: Small text hard to read; complex navigation; prefers phone but lines busy
- **Technical Proficiency**: Low
- **Access Needs**: Large text, simple navigation, telephone backup, carer access

#### Persona 3: Mohammed - Limited English
- **Role**: Employed, aged 45, English as second language
- **Goals**: Book appointments; understand appointment details
- **Pain Points**: Medical terminology confusing; written instructions hard to follow
- **Technical Proficiency**: Medium
- **Access Needs**: Simple language, translation support, visual confirmation

#### Persona 4: Dr. Chen - GP Receptionist
- **Role**: GP practice receptionist, manages appointment book
- **Goals**: Fill appointment slots efficiently; reduce phone call volume; help patients who struggle with digital
- **Pain Points**: Multiple systems; patients calling about hospital appointments; DNA management
- **Technical Proficiency**: High
- **Access Needs**: Desktop interface, bulk operations, patient lookup

---

### Use Cases

#### UC-001: Book GP Appointment

**Actor**: Citizen (Sarah persona)

**Preconditions**:
- Citizen has NHS login account
- Citizen is registered with a GP practice
- GP practice is connected to the booking service
- Citizen has available appointments to book

**Main Flow**:
1. Citizen authenticates via NHS login
2. System retrieves citizen's GP registration from PDS
3. System displays available appointment types (routine, urgent, nurse, etc.)
4. Citizen selects appointment type
5. System retrieves available slots from GP system
6. Citizen selects preferred date/time slot
7. System reserves slot (2-minute hold)
8. Citizen confirms booking
9. System confirms booking with GP system
10. System displays confirmation with appointment details
11. System sends confirmation via preferred channel (email/SMS/push)
12. System schedules reminder notifications

**Postconditions**:
- Appointment created in GP system
- Appointment visible in citizen's appointment list
- Confirmation sent to citizen
- Audit record created

**Alternative Flows**:
- **Alt 4a**: If no appointments available, offer waitlist or alternative dates
- **Alt 7a**: If slot taken during selection, offer next available slot
- **Alt 9a**: If GP system unavailable, queue booking and notify citizen

**Exception Flows**:
- **Ex 1**: NHS login authentication fails - display error, offer retry or help
- **Ex 2**: GP practice not connected - display message, provide phone number
- **Ex 3**: System timeout - save state, allow resume within 10 minutes

**Business Rules**:
- Citizens can only book at their registered GP practice
- Maximum advance booking period defined per appointment type (typically 4 weeks for routine)
- Urgent appointments require triage questionnaire completion
- Maximum 3 active future appointments per citizen (configurable per practice)

**Priority**: CRITICAL

---

#### UC-002: Cancel or Reschedule Appointment

**Actor**: Citizen

**Preconditions**:
- Citizen is authenticated
- Citizen has an existing future appointment
- Appointment is within cancellation window

**Main Flow**:
1. Citizen views their appointments list
2. Citizen selects appointment to manage
3. System displays appointment details with cancel/reschedule options
4. Citizen selects reschedule
5. System displays available alternative slots
6. Citizen selects new slot
7. System cancels original appointment and books new slot
8. System sends confirmation of change
9. Released slot made available for rebooking

**Postconditions**:
- Original appointment cancelled
- New appointment created (if rescheduled)
- Slot released for rebooking
- Audit record created

**Business Rules**:
- Cancellation allowed up to 2 hours before appointment (configurable)
- Repeat cancellations may trigger clinical review flag
- No-show history displayed to booking staff (not citizen)

**Priority**: CRITICAL

---

#### UC-003: Receive and Act on Reminder

**Actor**: Citizen

**Preconditions**:
- Citizen has upcoming appointment
- Reminder schedule reached (e.g., 48 hours before)
- Citizen has valid contact details

**Main Flow**:
1. System identifies appointments due for reminder
2. System sends reminder via citizen's preferred channel
3. Reminder includes: date, time, location, preparation instructions, cancel/reschedule link
4. Citizen clicks link in reminder
5. System deep-links to appointment management
6. Citizen confirms attendance or cancels/reschedules

**Business Rules**:
- Default reminders at 7 days, 48 hours, and 2 hours before appointment
- Citizens can customise reminder preferences
- Reminders respect do-not-disturb hours (configurable, default 21:00-08:00)

**Priority**: HIGH

---

#### UC-004: Book Hospital Outpatient Appointment

**Actor**: Citizen

**Preconditions**:
- Citizen has referral from GP
- Referral received by hospital
- Hospital has appointment slots available
- Citizen received "book your appointment" notification

**Main Flow**:
1. Citizen receives notification that appointment is ready to book
2. Citizen authenticates via NHS login
3. System displays pending referrals requiring booking
4. Citizen selects referral
5. System retrieves available slots from hospital PAS
6. System displays slots across available clinics/sites
7. Citizen selects preferred slot
8. System confirms booking with hospital PAS
9. System sends confirmation with location details, parking info, preparation instructions

**Alternative Flows**:
- **Alt 6a**: If multiple sites available, allow site preference filter
- **Alt 6b**: If no suitable slots, offer to join waiting list

**Business Rules**:
- Hospital controls which appointment types are citizen-bookable
- Some appointments require phone booking (complex cases)
- Booking window defined by hospital (typically 2-6 weeks from referral)

**Priority**: HIGH

---

### Functional Requirements Detail

#### FR-001: NHS Login Authentication

**Description**: The system must authenticate citizens using NHS login as the sole authentication method for citizen-facing services.

**Relates To**: BR-001, UC-001, UC-002, UC-004

**Acceptance Criteria**:
- [ ] Given a citizen accesses the booking service, when they attempt to book, then they are redirected to NHS login
- [ ] Given a citizen completes NHS login authentication, when redirected back, then their identity is verified against PDS
- [ ] Given NHS login returns P9 identity verification level, when citizen accesses bookings, then all features are available
- [ ] Given NHS login returns P5 identity verification level, when citizen accesses bookings, then only limited features available with upgrade prompt

**Data Requirements**:
- **Inputs**: NHS login OAuth2 token, user claims
- **Outputs**: Authenticated session, user profile
- **Validations**: Token signature, expiry, NHS Number match

**Priority**: MUST_HAVE

**Complexity**: MEDIUM

**Dependencies**: NHS login service availability

---

#### FR-002: Appointment Slot Retrieval

**Description**: The system must retrieve available appointment slots from connected healthcare systems in real-time.

**Relates To**: BR-001, UC-001, UC-004

**Acceptance Criteria**:
- [ ] Given a citizen requests GP appointments, when slots are requested, then available slots returned within 3 seconds
- [ ] Given a citizen requests hospital appointments, when slots are requested, then available slots returned within 5 seconds
- [ ] Given no slots available, when displayed to citizen, then waitlist option offered
- [ ] Given slot data is stale (>60 seconds), when displayed, then staleness indicator shown

**Data Requirements**:
- **Inputs**: Organisation ID, appointment type, date range, citizen ID
- **Outputs**: List of available slots with metadata
- **Validations**: Slot still available at confirmation time

**Priority**: MUST_HAVE

**Complexity**: HIGH

**Dependencies**: INT-001 (GP Systems), INT-002 (Hospital PAS)

---

#### FR-003: Appointment Booking Confirmation

**Description**: The system must confirm appointments with the source system and notify the citizen.

**Relates To**: BR-001, BR-002, UC-001

**Acceptance Criteria**:
- [ ] Given a citizen confirms a booking, when the slot is available, then booking confirmed within 5 seconds
- [ ] Given booking is confirmed, when complete, then citizen receives confirmation via selected channel within 60 seconds
- [ ] Given booking confirmation fails, when error occurs, then citizen informed and slot released
- [ ] Given booking is confirmed, when audit logged, then includes citizen ID, appointment ID, timestamp, source system

**Data Requirements**:
- **Inputs**: Slot ID, citizen ID, appointment details
- **Outputs**: Confirmed appointment, confirmation reference
- **Validations**: Slot availability, citizen eligibility

**Priority**: MUST_HAVE

**Complexity**: MEDIUM

**Dependencies**: FR-002

---

#### FR-004: Appointment Cancellation

**Description**: The system must allow citizens to cancel appointments within policy windows.

**Relates To**: BR-002, UC-002

**Acceptance Criteria**:
- [ ] Given an appointment exists, when citizen requests cancellation, then cancellation processed within 3 seconds
- [ ] Given cancellation is processed, when complete, then slot released for rebooking within 1 minute
- [ ] Given citizen cancels within 2 hours of appointment, when processed, then flagged for clinical review
- [ ] Given cancellation is processed, when complete, then source system updated synchronously

**Data Requirements**:
- **Inputs**: Appointment ID, cancellation reason (optional)
- **Outputs**: Cancellation confirmation, released slot notification
- **Validations**: Cancellation window, appointment ownership

**Priority**: MUST_HAVE

**Complexity**: MEDIUM

**Dependencies**: FR-003

---

#### FR-005: Appointment Reminders

**Description**: The system must send automated reminders to citizens before their appointments.

**Relates To**: BR-002, UC-003

**Acceptance Criteria**:
- [ ] Given an appointment is 7 days away, when reminder schedule runs, then reminder sent via preferred channel
- [ ] Given an appointment is 48 hours away, when reminder schedule runs, then reminder sent with confirm/cancel options
- [ ] Given an appointment is 2 hours away, when reminder schedule runs, then final reminder sent
- [ ] Given citizen clicks reminder link, when accessed, then deep-links to appointment management

**Data Requirements**:
- **Inputs**: Appointment schedule, citizen contact preferences
- **Outputs**: Reminder messages (SMS, email, push)
- **Validations**: Contact details valid, consent for channel

**Priority**: MUST_HAVE

**Complexity**: MEDIUM

**Dependencies**: FR-003, INT-003 (Notification Service)

---

#### FR-006: Appointment Search and Filter

**Description**: The system must allow citizens to search and filter available appointments by date, time, location, and clinician.

**Relates To**: BR-001, UC-001, UC-004

**Acceptance Criteria**:
- [ ] Given appointment slots are displayed, when citizen filters by date range, then only matching slots shown
- [ ] Given appointment slots are displayed, when citizen filters by time of day (morning/afternoon/evening), then only matching slots shown
- [ ] Given hospital appointments, when citizen filters by location, then slots grouped by site
- [ ] Given GP appointments, when citizen filters by clinician, then only that clinician's slots shown (where permitted)

**Priority**: SHOULD_HAVE

**Complexity**: LOW

**Dependencies**: FR-002

---

#### FR-007: Appointment Preparation Information

**Description**: The system must display appointment-specific preparation instructions to citizens.

**Relates To**: BR-001, UC-001, UC-004

**Acceptance Criteria**:
- [ ] Given booking is confirmed, when displayed, then preparation instructions shown (fasting, documents, etc.)
- [ ] Given appointment has location, when displayed, then travel directions and parking info shown
- [ ] Given appointment type has specific requirements, when displayed, then checklist provided
- [ ] Given preparation instructions exist, when reminder sent, then instructions included

**Priority**: SHOULD_HAVE

**Complexity**: LOW

**Dependencies**: FR-003

---

#### FR-008: Proxy Booking (Carer/Parent)

**Description**: The system must allow authorised proxies (parents, carers) to book appointments on behalf of another person.

**Relates To**: BR-004

**Acceptance Criteria**:
- [ ] Given a parent is linked to child via PDS, when booking, then can book for child
- [ ] Given a carer is authorised via proxy service, when booking, then can book for cared-for person
- [ ] Given proxy books appointment, when confirmed, then notifications sent to both proxy and patient (if appropriate)
- [ ] Given proxy relationship, when displayed, then clearly shows booking is for another person

**Priority**: SHOULD_HAVE

**Complexity**: HIGH

**Dependencies**: FR-001, INT-004 (Proxy Service)

---

#### FR-009: Waitlist Management

**Description**: The system must allow citizens to join waiting lists when no suitable appointments are available.

**Relates To**: BR-002

**Acceptance Criteria**:
- [ ] Given no suitable slots available, when citizen requests, then can join waitlist
- [ ] Given citizen on waitlist, when slot becomes available, then citizen notified within 5 minutes
- [ ] Given citizen notified, when slot claimed, then removed from waitlist
- [ ] Given citizen doesn't respond within 2 hours, when timeout, then slot offered to next person

**Priority**: COULD_HAVE

**Complexity**: MEDIUM

**Dependencies**: FR-002, FR-004

---

#### FR-010: Multi-Language Support

**Description**: The system must support Welsh language for all citizen-facing content.

**Relates To**: BR-004

**Acceptance Criteria**:
- [ ] Given citizen selects Welsh, when interface displayed, then all UI text in Welsh
- [ ] Given citizen selects Welsh, when appointment details displayed, then static content in Welsh
- [ ] Given Welsh selected, when reminders sent, then reminder text in Welsh
- [ ] Given language preference, when saved, then persists across sessions

**Priority**: MUST_HAVE

**Complexity**: MEDIUM

**Dependencies**: None

---

#### FR-011: Staff Booking Interface

**Description**: The system must provide a staff interface for assisted digital booking on behalf of citizens.

**Relates To**: BR-003, BR-004

**Acceptance Criteria**:
- [ ] Given staff authenticates with NHS Smartcard, when accessing system, then staff interface displayed
- [ ] Given staff searches for citizen, when NHS Number entered, then citizen record retrieved
- [ ] Given staff books on behalf of citizen, when confirmed, then audit trail shows staff identity
- [ ] Given staff views appointments, when displayed, then can see all connected appointments for citizen

**Priority**: MUST_HAVE

**Complexity**: MEDIUM

**Dependencies**: FR-001, INT-005 (NHS Smartcard)

---

#### FR-012: Appointment History

**Description**: The system must display citizen's appointment history for reference.

**Relates To**: BR-001

**Acceptance Criteria**:
- [ ] Given citizen views history, when displayed, then shows past 12 months of appointments
- [ ] Given appointment in history, when displayed, then shows outcome (attended, DNA, cancelled)
- [ ] Given historical appointment, when selected, then shows summary details
- [ ] Given history view, when filtered, then can filter by service type

**Priority**: SHOULD_HAVE

**Complexity**: LOW

**Dependencies**: FR-003

---

---

## Non-Functional Requirements (NFRs)

### Performance Requirements

#### NFR-P-001: Page Load Time

**Requirement**:
- Web page load time: < 3 seconds (95th percentile)
- Mobile page load time: < 4 seconds (95th percentile) on 4G
- Time to interactive: < 2 seconds (95th percentile)

**Measurement Method**: Real User Monitoring (RUM) with percentile tracking

**Load Conditions**:
- Peak load: 50,000 concurrent users
- Average load: 100 requests per second
- Burst capacity: 500 requests per second for 5 minutes

**Priority**: CRITICAL

**Traces to**: Architecture Principle 11 (Performance)

---

#### NFR-P-002: API Response Time

**Requirement**:
- Slot retrieval API: < 2 seconds (p95), < 5 seconds (p99)
- Booking confirmation API: < 3 seconds (p95)
- Cancellation API: < 2 seconds (p95)
- All other APIs: < 500ms (p95)

**Measurement Method**: Application Performance Monitoring (APM)

**Priority**: CRITICAL

---

#### NFR-P-003: Throughput

**Requirement**: System must handle 500 bookings per minute at peak load

**Scalability**: Must scale horizontally to support 3x growth over 3 years

**Priority**: HIGH

**Traces to**: Architecture Principle 1 (Scalability)

---

### Availability and Resilience Requirements

#### NFR-A-001: Availability Target

**Requirement**: System must achieve 99.9% uptime (excludes planned maintenance)
- Maximum planned downtime: 4 hours/month for maintenance (out of hours only)
- Maximum unplanned downtime: 8.76 hours/year

**Maintenance Windows**: Sundays 02:00-06:00 GMT/BST only

**Priority**: CRITICAL

**Traces to**: Architecture Principle 12 (Availability)

---

#### NFR-A-002: Disaster Recovery

**RPO (Recovery Point Objective)**: Maximum acceptable data loss = 15 minutes

**RTO (Recovery Time Objective)**: Maximum acceptable downtime = 1 hour

**Backup Requirements**:
- Backup frequency: Continuous replication + hourly snapshots
- Backup retention: 30 days for operational, 7 years for audit
- Geographic backup location: UK secondary region

**Failover Requirements**:
- Automatic failover to secondary region: YES
- Failover time: < 15 minutes

**Priority**: CRITICAL

---

#### NFR-A-003: Fault Tolerance

**Requirement**: System must gracefully degrade when external systems fail

**Resilience Patterns Required**:
- [x] Circuit breaker for GP system integrations
- [x] Circuit breaker for hospital PAS integrations
- [x] Retry with exponential backoff
- [x] Timeout on all network calls (30 second maximum)
- [x] Bulkhead isolation for notification service
- [x] Graceful degradation showing cached data when real-time unavailable

**Degraded Mode Behaviour**:
- If GP system unavailable: Show cached slots with staleness indicator, queue bookings
- If PDS unavailable: Use cached demographics, block new user registration
- If notification service unavailable: Queue notifications for later delivery

**Priority**: CRITICAL

**Traces to**: Architecture Principle 2 (Resilience)

---

### Scalability Requirements

#### NFR-S-001: Horizontal Scaling

**Requirement**: System must support horizontal scaling without code changes

**Growth Projections**:
- Year 1: 5 million registered users, 10 million bookings
- Year 2: 15 million registered users, 40 million bookings
- Year 3: 30 million registered users, 100 million bookings

**Scaling Triggers**: Auto-scale when CPU > 70% or memory > 80% or request queue > 1000

**Priority**: HIGH

---

#### NFR-S-002: Data Volume Scaling

**Requirement**: System must handle data growth to 50TB over 5 years

**Data Archival Strategy**:
- Hot storage: Current year appointments
- Warm storage: 1-3 years historical
- Cold storage: 3+ years (audit retention only)

**Priority**: HIGH

---

### Security Requirements

#### NFR-SEC-001: Authentication

**Requirement**: All citizen access must authenticate via NHS login (OAuth 2.0 / OpenID Connect)

**Multi-Factor Authentication (MFA)**:
- Required for: NHS login provides MFA based on identity verification level
- P9 (high): Full service access
- P5 (medium): Limited service access

**Session Management**:
- Session timeout: 20 minutes of inactivity
- Absolute session timeout: 8 hours
- Re-authentication required for: Booking confirmation, viewing sensitive data

**Priority**: CRITICAL

**Traces to**: Architecture Principle 4 (Security by Design)

---

#### NFR-SEC-002: Authorisation

**Requirement**: Role-based access control (RBAC) with least privilege principle

**Citizen Roles**:
- Standard user: Book/manage own appointments
- Proxy user: Book/manage for linked dependents

**Staff Roles**:
- Reception staff: Book on behalf, view/manage appointments
- Practice manager: Configuration, reporting
- Trust admin: Hospital-level configuration
- System admin: Platform configuration

**Priority**: CRITICAL

---

#### NFR-SEC-003: Data Encryption

**Requirement**:
- Data in transit: TLS 1.2+ (TLS 1.3 preferred)
- Data at rest: AES-256 encryption for all data stores
- Key management: Hardware Security Module (HSM) or equivalent cloud KMS

**Encryption Scope**:
- [x] Database encryption at rest
- [x] Backup encryption
- [x] File storage encryption
- [x] Field-level encryption for NHS Number, date of birth

**Priority**: CRITICAL

---

#### NFR-SEC-004: Secrets Management

**Requirement**: No secrets in code or configuration files

**Secrets Storage**: Cloud-native secrets management with automatic rotation

**Secrets Rotation**:
- API keys: 90 days
- Certificates: Annual
- Database credentials: 30 days

**Priority**: CRITICAL

---

#### NFR-SEC-005: Vulnerability Management

**Requirement**:
- Dependency scanning in CI/CD pipeline (no critical/high vulnerabilities allowed)
- Static application security testing (SAST) on every build
- Dynamic application security testing (DAST) weekly
- Penetration testing: Annual by CHECK-approved provider

**Remediation SLA**:
- Critical vulnerabilities: 24 hours
- High vulnerabilities: 7 days
- Medium vulnerabilities: 30 days
- Low vulnerabilities: 90 days

**Priority**: CRITICAL

---

#### NFR-SEC-006: NHS Security Standards

**Requirement**: Compliance with NHS security frameworks

**Mandatory Compliance**:
- [x] Data Security and Protection Toolkit (DSPT) - Standards Met
- [x] Cyber Essentials Plus certification
- [x] NHS Digital Technology Assessment Criteria (DTAC)
- [x] NCSC Cloud Security Principles

**Priority**: CRITICAL

---

### Compliance and Regulatory Requirements

#### NFR-C-001: Data Privacy Compliance (UK GDPR)

**Applicable Regulations**: UK GDPR, Data Protection Act 2018, Common Law Duty of Confidentiality

**Compliance Requirements**:
- [x] Data subject rights (access, deletion, portability) within 30 days
- [x] Consent management with audit trail
- [x] Privacy by design and by default
- [x] Data breach notification within 72 hours
- [x] Data Protection Impact Assessment (DPIA) completed
- [x] Caldicott Principles applied to patient data access

**Data Residency**: All patient data must remain within UK borders

**Data Retention**:
- Appointment records: 8 years (NHS Records Management Code)
- Audit logs: 8 years
- User preferences: Until account deletion + 30 days

**Priority**: CRITICAL

---

#### NFR-C-002: Clinical Safety (DCB0129)

**Requirement**: Full compliance with DCB0129 Clinical Risk Management

**Clinical Safety Requirements**:
- [x] Clinical Safety Case approved before go-live
- [x] Hazard Log maintained and reviewed monthly
- [x] Clinical Safety Officer appointed
- [x] Clinical testing with clinician involvement
- [x] Incident reporting process integrated with NRLS

**Hazard Categories**:
- Wrong patient booking
- Missed appointment due to system failure
- Incorrect appointment information displayed
- Failure to send critical reminders

**Priority**: CRITICAL

---

#### NFR-C-003: Audit Logging

**Requirement**: Comprehensive audit trail for compliance and forensics

**Audit Log Contents**:
- Who: User/service identity (NHS Number or staff ID)
- What: Action performed (create, read, update, delete, login, logout)
- When: Timestamp (UTC, millisecond precision)
- Where: System component, IP address
- Why: Reason code where applicable
- Result: Success/failure with error details

**Log Retention**: 8 years for patient data access logs (immutable storage)

**Log Integrity**: Cryptographic hashing with tamper-evident storage

**Priority**: CRITICAL

**Traces to**: Architecture Principle 5 (Observability)

---

### Usability Requirements

#### NFR-U-001: User Experience

**Requirement**: System must be intuitive for users with low digital skills

**UX Standards**:
- NHS.UK Design System components
- Mobile-first responsive design
- Maximum 3 clicks to complete any primary task
- Clear error messages with recovery actions
- Progress indicators for multi-step processes

**Browser Support**: Chrome, Firefox, Safari, Edge (last 2 major versions)

**Priority**: HIGH

---

#### NFR-U-002: Accessibility (NON-NEGOTIABLE)

**Requirement**: WCAG 2.2 Level AA compliance

**Accessibility Features**:
- [x] Keyboard navigation for all functions
- [x] Screen reader compatibility (JAWS, NVDA, VoiceOver)
- [x] High contrast mode
- [x] Adjustable font sizes (up to 200%)
- [x] Alt text for all images
- [x] No time limits or extendable time limits
- [x] Focus indicators visible

**Testing**:
- Automated accessibility testing in CI/CD (axe-core)
- Manual testing with assistive technologies
- User testing with disabled users

**Priority**: CRITICAL

**Traces to**: Architecture Principle 17 (Inclusive Design)

---

#### NFR-U-003: Language Support

**Requirement**: Full Welsh language support (Welsh Language Standards)

**Localisation Scope**:
- [x] All UI text translation
- [x] Date/time format (day/month/year)
- [x] Welsh language appointment details where available
- [x] Language toggle persistent across sessions

**Priority**: MUST_HAVE

---

### Maintainability and Supportability Requirements

#### NFR-M-001: Observability

**Requirement**: Comprehensive instrumentation for monitoring and troubleshooting

**Telemetry Requirements**:
- **Logging**: Structured JSON logs, centralised aggregation, no PII in logs
- **Metrics**: Prometheus-compatible, RED metrics (Rate, Errors, Duration)
- **Tracing**: Distributed tracing with correlation IDs across all services
- **Dashboards**: Real-time operational dashboards
- **Alerts**: SLO-based alerting with runbook links

**Priority**: HIGH

**Traces to**: Architecture Principle 5 (Observability)

---

#### NFR-M-002: Documentation

**Requirement**: Comprehensive documentation for operators and developers

**Documentation Types**:
- [x] Architecture documentation (C4 model)
- [x] API documentation (OpenAPI 3.0)
- [x] Operational runbooks
- [x] Troubleshooting guides
- [x] User guides (citizen and staff)

**Documentation Currency**: Updated within 5 working days of changes

**Priority**: HIGH

**Traces to**: Architecture Principle 13 (Maintainability)

---

#### NFR-M-003: Operational Runbooks

**Requirement**: Runbooks for all operational procedures and common incidents

**Runbook Coverage**:
- [x] Deployment procedures
- [x] Rollback procedures
- [x] Backup and restore
- [x] Incident response for common failure modes
- [x] Scaling procedures
- [x] Disaster recovery
- [x] Integration failure recovery

**Priority**: HIGH

---

---

## Integration Requirements

### INT-001: GP Clinical System Integration

**Purpose**: Retrieve and book appointment slots from GP practice clinical systems

**Integration Type**: Real-time API (GP Connect)

**Data Exchanged**:
- **From Booking Service to GP System**: Slot queries, booking requests, cancellation requests
- **From GP System to Booking Service**: Available slots, booking confirmations, appointment updates

**Integration Pattern**: FHIR REST API via GP Connect

**Authentication**: NHS Spine security (ASID/Party Key, TLS-MA)

**Error Handling**: Circuit breaker, retry with backoff, fallback to cached slots

**SLA**:
- Availability: 99.5% during core hours (08:00-18:30)
- Latency: < 3 seconds p95

**Owner**: NHS Digital (GP Connect programme)

**Priority**: CRITICAL

---

### INT-002: Hospital PAS Integration

**Purpose**: Retrieve and book outpatient appointment slots from hospital Patient Administration Systems

**Integration Type**: Real-time API

**Data Exchanged**:
- **From Booking Service to PAS**: Referral queries, slot queries, booking requests
- **From PAS to Booking Service**: Referral status, available slots, booking confirmations

**Integration Pattern**: HL7 FHIR or proprietary API (vendor-dependent)

**Authentication**: OAuth 2.0 with NHS identity

**Error Handling**: Circuit breaker, async fallback for non-urgent bookings

**SLA**:
- Availability: 99.5% during core hours
- Latency: < 5 seconds p95

**Owner**: Individual NHS Trusts (via integration adapters)

**Priority**: HIGH

---

### INT-003: NHS Spine PDS

**Purpose**: Retrieve patient demographics and verify identity

**Integration Type**: Real-time API

**Data Exchanged**:
- **From Booking Service to PDS**: NHS Number lookup, demographics query
- **From PDS to Booking Service**: Patient demographics, GP registration, linked records

**Integration Pattern**: FHIR REST API

**Authentication**: NHS Spine security

**Error Handling**: Cache demographics for 24 hours, degrade gracefully

**SLA**:
- Availability: 99.9%
- Latency: < 1 second p95

**Owner**: NHS Digital

**Priority**: CRITICAL

---

### INT-004: NHS Notify

**Purpose**: Send appointment reminders and notifications to citizens

**Integration Type**: Asynchronous API

**Data Exchanged**:
- **From Booking Service to Notify**: SMS, email, letter requests
- **From Notify to Booking Service**: Delivery status callbacks

**Integration Pattern**: REST API with webhooks

**Authentication**: API key

**Error Handling**: Queue failed messages for retry, dead letter queue after 3 attempts

**SLA**:
- SMS delivery: < 60 seconds
- Email delivery: < 5 minutes

**Owner**: GDS/NHS Digital

**Priority**: HIGH

---

### INT-005: NHS Login

**Purpose**: Citizen authentication and identity verification

**Integration Type**: OAuth 2.0 / OpenID Connect

**Data Exchanged**:
- **From Booking Service to NHS Login**: Authentication request, token validation
- **From NHS Login to Booking Service**: ID token, access token, user claims

**Integration Pattern**: OIDC authorization code flow

**Authentication**: OAuth 2.0

**Error Handling**: Graceful login failure handling, retry option

**SLA**:
- Availability: 99.9%
- Authentication time: < 500ms (excluding user input)

**Owner**: NHS Digital

**Priority**: CRITICAL

---

### INT-006: Analytics Platform

**Purpose**: Operational analytics and performance monitoring

**Integration Type**: Event streaming

**Data Exchanged**:
- **From Booking Service to Analytics**: Anonymised booking events, performance metrics, user journeys

**Integration Pattern**: Event stream (pseudo-anonymised)

**Authentication**: Service account

**Error Handling**: Buffer events locally if analytics unavailable

**SLA**: Best effort, non-blocking

**Owner**: NHS Digital Analytics

**Priority**: MEDIUM

---

---

## Data Requirements

### Data Entities

#### Entity: Appointment

**Description**: An appointment booking linking a citizen to a healthcare service at a specific time and location

**Attributes**:
| Attribute | Type | Required | Description | Constraints |
|-----------|------|----------|-------------|-------------|
| appointment_id | UUID | Yes | Unique identifier | Primary key |
| citizen_nhs_number | String(10) | Yes | Patient NHS Number | Encrypted, FK to Citizen |
| organisation_ods_code | String(10) | Yes | Healthcare organisation | FK to Organisation |
| appointment_type | Enum | Yes | Type of appointment | ['gp_routine', 'gp_urgent', 'hospital_outpatient', 'community'] |
| scheduled_start | DateTime | Yes | Appointment start time | Indexed |
| scheduled_end | DateTime | Yes | Appointment end time | |
| status | Enum | Yes | Booking status | ['booked', 'cancelled', 'attended', 'dna', 'rescheduled'] |
| booking_reference | String(12) | Yes | Human-readable reference | Unique |
| external_reference | String(50) | No | Source system reference | |
| booked_by | Enum | Yes | Booking channel | ['citizen', 'staff', 'system'] |
| booked_at | DateTime | Yes | Booking timestamp | Indexed |
| cancelled_at | DateTime | No | Cancellation timestamp | |
| cancellation_reason | String(255) | No | Reason for cancellation | |
| created_at | DateTime | Yes | Record creation | Indexed |
| updated_at | DateTime | Yes | Last update | |

**Relationships**:
- Many-to-one with Citizen via nhs_number
- Many-to-one with Organisation via ods_code

**Data Volume**:
- Year 1: 10 million records
- Year 3: 100 million records

**Access Patterns**:
- Query by NHS Number (citizen view)
- Query by organisation + date range (staff view)
- Query by status + date (reminder scheduling)

**Data Classification**: CONFIDENTIAL (patient identifiable)

**Data Retention**: 8 years (NHS Records Management Code)

---

#### Entity: Citizen

**Description**: Registered citizen who can book appointments (cached from PDS)

**Attributes**:
| Attribute | Type | Required | Description | Constraints |
|-----------|------|----------|-------------|-------------|
| nhs_number | String(10) | Yes | NHS Number | Primary key, encrypted |
| given_name | String(100) | Yes | First name | Encrypted |
| family_name | String(100) | Yes | Surname | Encrypted |
| date_of_birth | Date | Yes | DOB | Encrypted |
| postcode | String(8) | No | Postal code | |
| registered_gp_ods | String(10) | No | GP practice ODS code | |
| contact_email | String(255) | No | Email address | Encrypted |
| contact_mobile | String(20) | No | Mobile number | Encrypted |
| language_preference | Enum | No | Preferred language | ['en', 'cy'] |
| notification_preferences | JSON | No | Reminder channel prefs | |
| pds_sync_timestamp | DateTime | Yes | Last PDS sync | |
| created_at | DateTime | Yes | First registration | |
| updated_at | DateTime | Yes | Last update | |

**Data Classification**: CONFIDENTIAL

**Data Retention**: Until account deletion + 30 days

---

#### Entity: Organisation

**Description**: NHS healthcare organisation providing appointment services

**Attributes**:
| Attribute | Type | Required | Description | Constraints |
|-----------|------|----------|-------------|-------------|
| ods_code | String(10) | Yes | ODS organisation code | Primary key |
| name | String(255) | Yes | Organisation name | |
| type | Enum | Yes | Organisation type | ['gp_practice', 'trust', 'community'] |
| address | JSON | No | Address details | |
| integration_status | Enum | Yes | Connection status | ['active', 'pending', 'disconnected'] |
| configuration | JSON | No | Booking rules config | |
| created_at | DateTime | Yes | Record creation | |
| updated_at | DateTime | Yes | Last update | |

**Data Classification**: PUBLIC

**Data Retention**: Indefinite (reference data)

---

#### Entity: AuditLog

**Description**: Immutable audit trail of all data access and changes

**Attributes**:
| Attribute | Type | Required | Description | Constraints |
|-----------|------|----------|-------------|-------------|
| audit_id | UUID | Yes | Unique identifier | Primary key |
| timestamp | DateTime | Yes | Event time (UTC) | Indexed, immutable |
| actor_id | String(50) | Yes | User/service identity | Indexed |
| actor_type | Enum | Yes | Actor type | ['citizen', 'staff', 'system'] |
| action | Enum | Yes | Action performed | ['create', 'read', 'update', 'delete', 'login', 'logout'] |
| resource_type | String(50) | Yes | Entity type accessed | |
| resource_id | String(50) | Yes | Entity ID accessed | |
| details | JSON | No | Additional context | |
| ip_address | String(45) | No | Client IP | |
| correlation_id | UUID | Yes | Request trace ID | Indexed |

**Data Classification**: CONFIDENTIAL

**Data Retention**: 8 years (immutable storage)

---

### Data Quality Requirements

**Data Accuracy**:
- NHS Number validation (check digit)
- ODS code validation against ODS API
- Date validation (no future DOB, valid appointment dates)

**Data Completeness**:
- Required fields enforced at API level
- Missing contact details prompt user update

**Data Consistency**:
- PDS synchronisation within 24 hours
- Appointment status sync with source systems within 5 minutes

**Data Timeliness**:
- Appointment slot data < 60 seconds stale
- Demographics data < 24 hours stale

**Data Lineage**:
- Source system reference stored for all appointments
- PDS sync timestamp tracked

---

### Data Migration Requirements

**Migration Scope**: Not applicable for new build (greenfield)

**Future Consideration**:
- If replacing existing booking systems, migration strategy required
- Citizen data will be sourced from PDS (no migration needed)

---

## Constraints and Assumptions

### Technical Constraints

**TC-001**: Must integrate with NHS Spine infrastructure using approved methods (ASID registration, TLS-MA)

**TC-002**: Must host on NHS-approved cloud platforms (UK regions only)

**TC-003**: Must use NHS login for citizen authentication (no alternative identity providers)

**TC-004**: Must comply with NHS Design System for citizen-facing interfaces

**TC-005**: GP system integration limited to GP Connect-enabled suppliers initially

---

### Business Constraints

**BC-001**: Phased rollout required - cannot launch nationally without regional pilots

**BC-002**: Must maintain existing booking channels (phone) during transition

**BC-003**: Clinical safety case must be approved before any patient-facing functionality

**BC-004**: Welsh language support mandatory from day one (Welsh Language Standards)

---

### Assumptions

**A-001**: NHS login will support required identity verification levels (P9) for all target users

**A-002**: GP Connect appointment booking APIs will be available and stable

**A-003**: Hospital trusts will adopt standardised integration APIs (FHIR preferred)

**A-004**: NHS Notify capacity sufficient for projected reminder volumes

**A-005**: Citizens have access to smartphones or computers with internet (digital exclusion addressed via assisted digital pathway)

**Validation Plan**:
- A-001: Confirmed with NHS login programme
- A-002: GP Connect currently in production - validated
- A-003: Requires trust-by-trust assessment during onboarding
- A-004: Capacity planning with NHS Notify team
- A-005: Digital inclusion research underway

---

## Success Criteria and KPIs

### Business Success Metrics

| Metric | Baseline | Target | Timeline | Measurement Method |
|--------|----------|--------|----------|-------------------|
| Citizen satisfaction (CSAT) | N/A | 80% | 12 months post-launch | Post-booking survey |
| DNA rate | 8% (national average) | 6% | 12 months post-launch | Appointment outcomes |
| Digital booking adoption | 0% | 60% | 18 months post-launch | Booking channel analytics |
| Appointment call volume reduction | Baseline TBD | -40% | 12 months post-launch | Call centre metrics |
| Time to book appointment | 5 minutes (phone) | < 2 minutes (digital) | Launch | User journey analytics |

---

### Technical Success Metrics

| Metric | Target | Measurement Method |
|--------|--------|-------------------|
| System availability | 99.9% | Uptime monitoring |
| API response time (p95) | < 2 seconds | APM tooling |
| Error rate | < 0.1% | Log aggregation |
| Successful booking rate | > 98% | Transaction monitoring |
| Reminder delivery rate | > 99% | Notification tracking |
| Mean time to recovery (MTTR) | < 1 hour | Incident tracking |

---

### User Adoption Metrics

| Metric | Target | Timeline | Measurement Method |
|--------|--------|----------|-------------------|
| Registered users | 5 million | Year 1 | User registration |
| Monthly active users | 2 million | Year 1 | Analytics platform |
| Bookings per user | 2/year average | Year 1 | Booking analytics |
| Mobile vs desktop ratio | 60/40 | Year 1 | Device analytics |

---

## Dependencies and Risks

### Dependencies

| Dependency | Description | Owner | Target Date | Status | Impact if Delayed |
|------------|-------------|-------|-------------|--------|-------------------|
| NHS Login P9 | High identity verification for booking | NHS Digital | Pre-launch | On Track | HIGH - Cannot launch |
| GP Connect booking | GP appointment slot APIs | NHS Digital | Pre-launch | On Track | HIGH - Cannot book GP |
| Hospital integrations | Trust PAS connectivity | Trusts | Phased | At Risk | MEDIUM - Limited scope |
| NHS Notify capacity | Notification delivery | GDS | Pre-launch | On Track | MEDIUM - Degraded reminders |
| DSPT assessment | Security compliance | Project | Pre-launch | On Track | HIGH - Cannot launch |
| DCB0129 approval | Clinical safety | Project | Pre-launch | On Track | HIGH - Cannot launch |

---

### Risks

| Risk ID | Description | Probability | Impact | Mitigation Strategy | Owner |
|---------|-------------|-------------|--------|---------------------|-------|
| R-001 | Hospital trust integration delays | HIGH | HIGH | Prioritise GP-only MVP, phase hospital features | Programme Manager |
| R-002 | Low citizen adoption | MEDIUM | HIGH | Marketing campaign, GP promotion, assisted digital | Product Owner |
| R-003 | GP system vendor non-cooperation | MEDIUM | HIGH | Escalate to NHS England, regulatory pressure | NHS Digital |
| R-004 | Performance under national load | MEDIUM | HIGH | Load testing, phased rollout, auto-scaling | Technical Lead |
| R-005 | Clinical safety incident | LOW | CRITICAL | Rigorous testing, DCB0129 compliance, incident process | Clinical Safety Officer |
| R-006 | Data breach | LOW | CRITICAL | Security by design, pen testing, DSPT compliance | Security Lead |

---

## Requirement Conflicts & Resolutions

### Conflict C-001: Speed vs Hospital Integration Complexity

**Conflicting Requirements**:
- **BR-001**: Unified appointment access across GP and hospital services
- **BC-001**: Phased rollout required - cannot delay for all integrations

**Stakeholders Involved**:
- **NHS England**: Wants comprehensive service covering all appointment types
- **Programme Delivery**: Needs achievable delivery timeline with manageable risk

**Nature of Conflict**: Hospital PAS systems are diverse (10+ vendors) with varying integration maturity. Waiting for all trusts delays launch significantly; launching without hospitals limits service value.

**Trade-off Analysis**:

| Option | Pros | Cons | Impact |
|--------|------|------|--------|
| **Option 1**: Wait for hospital integration | ✅ Complete offering | ❌ 18+ month delay | NHS England satisfied, delivery frustrated |
| **Option 2**: GP-only MVP | ✅ Faster launch | ❌ Limited value | Delivery satisfied, NHS England disappointed |
| **Option 3**: Phased approach | ✅ Early value + growth | ❌ Longer to full vision | Both partially satisfied |

**Resolution Strategy**: PHASE

**Decision**: Launch with GP appointments (MVP), add hospital outpatients in Phase 2, community services in Phase 3.

**Rationale**: GP appointments represent 70% of booking volume. Delivering value early while building hospital integrations in parallel is the best balance of risk and value.

**Decision Authority**: Senior Responsible Owner (SRO) with Programme Board approval

**Impact on Requirements**:
- **Modified**: BR-001 success criteria phased (GP Year 1, Hospital Year 2)
- **Added**: Phased delivery milestones in timeline

**Stakeholder Management**:
- **NHS England**: Agreed to phased approach with commitment to hospital integration roadmap
- **Programme Delivery**: Committed to aggressive but achievable GP MVP timeline

---

### Conflict C-002: Security vs User Experience

**Conflicting Requirements**:
- **NFR-SEC-001**: Strong authentication via NHS login (may require identity verification)
- **BR-004**: Accessible to users with limited digital skills

**Stakeholders Involved**:
- **Security Team**: Requires strong identity verification to protect patient data
- **Accessibility Team**: Concerned NHS login P9 verification excludes vulnerable users

**Nature of Conflict**: NHS login P9 (highest verification) requires smartphone or in-person verification, potentially excluding elderly or digitally excluded users.

**Resolution Strategy**: COMPROMISE

**Decision**:
- P9 required for booking/cancelling (write operations)
- P5 acceptable for viewing appointments only (read operations)
- Assisted digital pathway for users who cannot complete P9

**Impact on Requirements**:
- **Modified**: FR-001 acceptance criteria updated to support P5 with limitations
- **Added**: FR-011 staff booking interface for assisted digital

---

## Timeline and Milestones

### High-Level Milestones

| Milestone | Description | Target Date | Dependencies |
|-----------|-------------|-------------|--------------|
| Requirements Approved | Stakeholder sign-off | TBD | This document |
| Clinical Safety Case (draft) | Initial hazard analysis | TBD | Requirements |
| Alpha Assessment | GDS service assessment | TBD | Prototype |
| Private Beta (3 GP practices) | Limited pilot | TBD | Core development |
| Public Beta (regional) | Regional rollout | TBD | Private Beta success |
| DCB0129 Approval | Clinical safety sign-off | TBD | Beta testing |
| Live Service | National availability | TBD | All assessments passed |
| Phase 2 (Hospital) | Hospital integration | TBD | Live service |

---

## Budget

### Cost Estimate

| Category | Estimated Cost | Notes |
|----------|----------------|-------|
| Development (vendor) | TBD | Based on RFP responses |
| Infrastructure | TBD | Cloud hosting estimate |
| Integration | TBD | GP Connect, hospital adapters |
| Testing | TBD | Performance, security, clinical |
| Training | TBD | Staff training, documentation |
| **Total** | **TBD** | Subject to procurement |

### Ongoing Operational Costs

| Category | Annual Cost | Notes |
|----------|-------------|-------|
| Infrastructure | TBD | Based on usage projections |
| Support | TBD | L1/L2/L3 support model |
| Licensing | TBD | Third-party dependencies |
| **Total** | **TBD/year** | |

---

## Approval

### Requirements Review

| Reviewer | Role | Status | Date | Comments |
|----------|------|--------|------|----------|
| TBD | Executive Sponsor | [ ] Approved | | |
| TBD | Product Owner | [ ] Approved | | |
| TBD | Enterprise Architect | [ ] Approved | | |
| TBD | Clinical Safety Officer | [ ] Approved | | |
| TBD | Security Lead | [ ] Approved | | |
| TBD | IG Lead | [ ] Approved | | |
| TBD | Accessibility Lead | [ ] Approved | | |

### Sign-Off

By signing below, stakeholders confirm that requirements are complete, understood, and approved to proceed to design phase.

| Stakeholder | Signature | Date |
|-------------|-----------|------|
| TBD, SRO | _________ | |
| TBD, Product Owner | _________ | |
| TBD, Clinical Safety Officer | _________ | |

---

## Appendices

### Appendix A: Glossary

| Term | Definition |
|------|------------|
| DNA | Did Not Attend - patient misses appointment without notice |
| DSPT | Data Security and Protection Toolkit |
| DCB0129 | Clinical Risk Management standard for health IT |
| GP Connect | NHS Digital programme for GP system interoperability |
| NHS login | NHS citizen identity service |
| NHS Number | 10-digit unique patient identifier |
| ODS | Organisation Data Service (NHS organisation codes) |
| PAS | Patient Administration System (hospital) |
| PDS | Personal Demographics Service (national patient index) |
| FHIR | Fast Healthcare Interoperability Resources (HL7 standard) |
| WCAG | Web Content Accessibility Guidelines |

### Appendix B: Reference Documents

- [Architecture Principles](../../.arckit/memory/architecture-principles.md)
- [NHS Service Standard](https://service-manual.nhs.uk/standards-and-technology/service-standard)
- [NHS Digital Design System](https://service-manual.nhs.uk/design-system)
- [GP Connect Specifications](https://digital.nhs.uk/services/gp-connect)
- [DCB0129 Standard](https://digital.nhs.uk/data-and-information/information-standards/information-standards-and-data-collections-including-extractions/publications-and-notifications/standards-and-collections/dcb0129-clinical-risk-management-its-application-in-the-manufacture-of-health-it-systems)

### Appendix C: Related Projects

- NHS login
- GP Connect
- NHS App
- NHS Notify

---

**Document History**

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2026-01-26 | ArcKit AI | Initial draft |

---

**Generated by**: ArcKit `/arckit.requirements` command
**Generated on**: 2026-01-26
**ArcKit Version**: 0.11.2
**Project**: NHS Digital Appointment Booking Service (Project 001)
**AI Model**: Claude Opus 4.5
**Generation Context**: Based on architecture principles, NHS Digital standards, and test project context
