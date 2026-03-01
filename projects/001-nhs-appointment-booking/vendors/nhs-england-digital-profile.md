# Vendor Profile: NHS England Digital (NHS App Platform)

## Document Control

| Field | Value |
|-------|-------|
| **Document ID** | nhs-england-digital-profile |
| **Document Type** | Vendor Profile |
| **Project** | NHS Digital Appointment Booking Service (Project 001) |
| **Classification** | OFFICIAL |
| **Status** | DRAFT |
| **Version** | 1.0 |
| **Created Date** | 2026-03-01 |
| **Last Modified** | 2026-03-01 |
| **Review Cycle** | On-Demand |
| **Next Review Date** | 2026-09-01 |
| **Owner** | Enterprise Architect, NHS Digital |
| **Reviewed By** | PENDING |
| **Approved By** | PENDING |
| **Distribution** | Project Team, Architecture Team, Procurement |
| **Source Research** | ARC-001-RSCH-v2.0 |
| **Confidence** | high |

## Revision History

| Version | Date | Author | Changes | Approved By | Approval Date |
|---------|------|--------|---------|-------------|---------------|
| 1.0 | 2026-03-01 | ArcKit AI | Initial creation from `/arckit:research` agent | PENDING | PENDING |

---

## Overview

NHS England Digital (formerly NHS Digital, merged into NHS England in 2023) is the national digital, data and technology organisation for health and social care in England. It builds and operates the critical NHS national digital infrastructure including NHS login, the NHS App, NHS Notify, NHS Spine, and the GP Connect APIs. The NHS App is the strategic patient-facing digital platform operated by NHS England Digital, with 39 million registered users as of December 2025 and 62.3 million logins in November 2025 alone. NHS England Digital operates under the Secretary of State for Health and Social Care and its platforms are free to integrate with for NHS-commissioned services.

## Products & Services

**NHS App**: Native iOS (Swift, iOS 15+) and Android (Kotlin, Android 8+) mobile application plus browser-based web access. Current version 4.54 (February 2026). Features: GP appointment booking, hospital appointment management, repeat prescriptions, GP health records, NHS 111 online, messaging/notifications inbox, organ donation registration. Migrated from Xamarin cross-platform to native iOS/Android effective February 2025.

**NHS Login**: Citizens identity service using OIDC/OAuth 2.0 with P5 (medium — email/phone) and P9 (high — biometric/documentary) verification levels. 30 million+ verified citizens registered. Mandatory for all citizen-facing NHS digital services (TC-003).

**NHS Notify**: Multi-channel notification service for NHS-to-patient communications. Channels: NHS App push notification (free, 88% opt-in rate), SMS (£0.0233/message beyond 250,000 free/year), email (free), letters (£0.65/item). Mandated by Secretary of State Service Directions February 2025. NHS App push notifications are free and represent the primary channel strategy.

**NHS Spine**: National infrastructure for NHS patient demographic data (Personal Demographics Service), prescriptions (EPS), and clinical messaging. Access requires ASID certificates and TLS-MA (mutual TLS).

**GP Connect APIs**: NHS FHIR APIs for accessing GP practice clinical systems. Supports appointment slot retrieval, booking, and cancellation.

**NHS BaRS**: Booking and Referral Standard — FHIR R4-based API for cross-service appointment booking and referrals. Current core version 1.3.1. Replaces legacy NHS Booking Standard.

**NHS Developer Portal**: API catalogue and developer documentation at digital.nhs.uk/developer. Note: developer.nhs.uk and associated FHIR servers being decommissioned 2 March 2026 — all references must use digital.nhs.uk.

## Pricing Model

All NHS England Digital platforms are free for NHS-commissioned services:
- NHS App integration: Free (web integration approach; NHS England onboarding approval required via app.onboarding@nhs.net)
- NHS Login: Free (integration costs are engineering effort only — estimated 4-6 person-weeks)
- NHS Notify: Email free; NHS App push free; SMS at volume pricing (£0.0233/message); letters £0.65/item
- NHS Spine / GP Connect: Free for NHS services; requires ASID certificate procurement (£25,000 Year 1 estimated cost)
- NHS BaRS API: Free access; mutual TLS certification costs apply

## UK Government Presence

- G-Cloud listed: yes (NHS services are eligible users; NHS England Digital is a public body)
- DOS listed: yes (NHS England Digital engages suppliers via DOS frameworks)
- UK data centres: yes — all NHS England Digital services are hosted in UK data centres (AWS eu-west-2 London confirmed for NHS App, NHS Login)
- NHS DSPT: Inherently compliant — NHS England Digital operates under NHS DSPT as a covered entity
- Data residency: All platforms maintain patient-identifiable data within UK borders (NHS App, NHS Login, NHS Notify, NHS Spine)

## Strengths

- 39 million registered users with no acquisition cost — the single largest NHS digital citizen platform
- NHS App push notification opt-in rate of 88% among active users — highest-reach zero-cost notification channel for NHS appointment reminders
- Mandated platforms: NHS Login (TC-003), NHS Notify (Secretary of State Directions Feb 2025), NHS App (default communication channel by 2029)
- All data UK-resident; GDPR-compliant; Caldicott Principles embedded in platform design
- Free for NHS-commissioned services — dramatically reduces notification cost at national scale
- Deep link support (Android App Links, iOS Associated Domains) enables one-click actions from push notifications
- NHS App JS API v2 provides calendar integration (`addToCalendar()`), back button handling, overlay browser — critical for appointment UX
- NHS App design system built to WCAG 2.2 AA — accessibility built-in for 39 million users
- NHS App GP booking journey overhauled October 2025 after 15 rounds of research (438 users, including accessibility testing) — proven user-centred design
- Web integration model allows independent deployment without App Store submission cycles
- Broad device coverage: iOS 15+, Android 8+, plus web browser for older devices and desktop access
- NHS Notify Service Directions 2025 provide regulatory certainty for communications strategy through 2028-29

## Weaknesses

- NHS App onboarding process (app.onboarding@nhs.net) adds 4-6 weeks elapsed time — must start in Discovery phase
- WebView limitations in web integration: no conventional file downloads; no browser printing (mitigated by JS API v2 `downloadFromBytes()`)
- iOS 15 / Android 8 minimum OS threshold (effective February 2025) excludes oldest device users — mitigated by web browser access pathway
- NHS login proxy service API (for carer/parent booking) development timeline not publicly confirmed
- NHS App JavaScript API v2 capabilities limited compared to full native integration — cannot access all device capabilities
- NHS login P5 users cannot retrieve NHS Number — must prompt P9 upgrade for booking eligibility (creates friction for some users)
- developer.nhs.uk FHIR servers being decommissioned 2 March 2026 — all integration must use digital.nhs.uk endpoints
- No published external SLA for NHS App or NHS Login uptime (assumed 99.9%+ by NHS Digital policy)
- NHS Notify SMS costs can be significant at national scale (100M+ reminders/year); push-first strategy essential to manage costs

## Projects Referenced In

- ARC-001: NHS Digital Appointment Booking Service (2026) — NHS App is the primary citizen-facing channel; NHS Login is mandatory authentication; NHS Notify is mandated notification platform

---

**Generated by**: ArcKit `/arckit:research` agent
**Generated on**: 2026-03-01
**ArcKit Version**: 2.2.0
**Project**: NHS Digital Appointment Booking Service (Project 001)
**Model**: Claude Sonnet 4.6
