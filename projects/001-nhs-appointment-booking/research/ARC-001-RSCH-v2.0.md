# Technology and Service Research: NHS Digital Appointment Booking Service

> **Template Status**: Beta | **Version**: 2.0 | **Command**: `/arckit.research`

## Document Control

| Field | Value |
|-------|-------|
| **Document ID** | ARC-001-RSCH-v2.0 |
| **Document Type** | Technology and Service Research |
| **Project** | NHS Digital Appointment Booking Service (Project 001) |
| **Classification** | OFFICIAL |
| **Status** | DRAFT |
| **Version** | 2.0 |
| **Created Date** | 2026-03-01 |
| **Last Modified** | 2026-03-01 |
| **Review Cycle** | Quarterly |
| **Next Review Date** | 2026-06-01 |
| **Owner** | Enterprise Architect, NHS Digital |
| **Reviewed By** | PENDING |
| **Approved By** | PENDING |
| **Distribution** | Project Team, Architecture Team, Procurement, Clinical Safety Team |

## Revision History

| Version | Date | Author | Changes | Approved By | Approval Date |
|---------|------|--------|---------|-------------|---------------|
| 1.0 | 2026-02-20 | ArcKit AI | Initial creation covering cloud hosting, GOV.UK platforms, FHIR integration, database, observability, CI/CD, API gateway, and core booking service categories | PENDING | PENDING |
| 2.0 | 2026-03-01 | ArcKit AI | Major increment — added new Category 9: NHS App Mobile Platform covering latest version (v4.54), native iOS/Android architecture post-Xamarin migration, device support matrix, appointment booking integration, JavaScript API v2, push notification via NHS Notify, 2026 roadmap; updated TCO and traceability matrix; spawned two new knowledge files | PENDING | PENDING |

---

## Executive Summary

### Research Scope

This document presents research findings for technology, services, and products that meet the requirements documented in `ARC-001-REQ-v1.0.md`. It provides build vs buy analysis and vendor recommendations for NHS procurement decisions across all major technology categories identified from requirements analysis.

This version (v2.0) extends the v1.0 research with a dedicated focus on the **NHS App mobile platform** — the primary citizen-facing channel for NHS appointment booking. The NHS App is the strategic integration target for the appointment booking service and understanding its architecture, capabilities, device support, and roadmap is essential to designing a compatible and compliant booking integration.

**Requirements Analyzed**: 14 functional, 15 non-functional, 6 integration, 4 data requirements

**Research Categories Identified**: 9 categories (8 from v1.0 + 1 new: NHS App Mobile Platform)

**Research Approach**: Market research via web search and vendor website review; UK Government Digital Marketplace search; open source assessment; GOV.UK platform evaluation; NHS-specific compliance verification (DSPT, DTAC, DCB0129); direct review of NHS App release notes, developer documentation, and design history.

This is a UK NHS project. GOV.UK platforms were prioritised where applicable. All vendor assessments include UK data residency verification.

### Key Findings

- **NHS App is the primary integration target**: With 39 million registered users and 62.3 million logins in November 2025 alone, the NHS App is the dominant channel for NHS digital services. The appointment booking service MUST integrate with the NHS App to achieve the BR-001 unified appointment access and BR-002 DNA reduction targets.
- **Native app migration completed February 2025**: The NHS App migrated from its legacy Xamarin cross-platform codebase to native iOS (Swift/UIKit) and Android implementations, with device requirements raised to iOS 15+ and Android 8+. This eliminates cross-platform abstraction layer risks for integration partners.
- **NHS App is the mandated communication channel by 2029**: NHS Notify Service Directions 2025 and NHS England strategy mandate the NHS App as the default patient communication channel, with all NHS communications migrating to NHS Notify by end of 2028-29. This directly impacts the appointment reminder architecture (FR-005, INT-004).
- **Web integration is the recommended entry point**: Third-party services integrate with the NHS App via web integration using NHS login SSO JWT tokens and the JavaScript API v2. Full native app integration requires NHS England onboarding approval and is only available to NHS England-commissioned services.
- **Appointment booking UX was overhauled in October 2025**: After 15 rounds of research with 438 users, the GP appointment booking journey was redesigned from a single cluttered page to a sequential step-by-step flow, with significant accessibility improvements. New booking service integrations should align with this updated UX pattern.

### Build vs Buy Summary

| Approach | Categories | Total 3-Year TCO | Rationale |
|----------|-----------|------------------|-----------|
| **BUILD** (Custom Development) | 3 categories | £4,290,000 | NHS App web integration adapters added to existing FHIR adapters and core booking service |
| **BUY** (Commercial SaaS/Managed) | 3 categories | £1,250,000 | Cloud hosting, API Gateway, CI/CD use proven managed services |
| **ADOPT** (Open Source) | 1 category | £320,000 | Observability stack (Grafana/Prometheus/Loki) |
| **GOV.UK Platforms** | 2 categories | £699,000 | NHS Login (mandatory) and NHS Notify (mandated by 2025 Service Directions) |
| **TOTAL** | 9 categories | **£6,559,000** | Blended approach over 3 years |

### Top Recommended Vendors

1. **Amazon Web Services (AWS)** for Cloud Hosting, Messaging, API Gateway: NHS DSPT "Standards Exceeded" 2024-25, NHS England strategic partner, NHS App itself runs on AWS eu-west-2
2. **NHS England Digital** (GOV.UK NHS App platform) for Mobile Channel: 39 million users, mandatory for citizen-facing NHS services, free integration via web integration approach
3. **Grafana Labs** for Observability: Open source core (self-hosted), no per-host licensing cost at NHS scale, Prometheus-native

### Requirements Coverage

- **95%** of requirements have identified solutions
- **3** requirements need custom development (NHS Spine ASID/TLS-MA integration, GP Connect slot adapter, NHS App web integration layer)
- **1** requirement needs further research (proxy booking for carers — FR-008 — NHS Digital proxy service API timeline)

---

## Research Categories

> **Note**: Research categories are dynamically identified based on project requirements, not a fixed list. Categories 1-8 are carried forward from v1.0 with key updates noted. Category 9 is new in this version.

---

## Category 1: Cloud Hosting Platform (Carried Forward from v1.0)

**Requirements Addressed**: NFR-A-001 (99.9% uptime), NFR-C-001 (UK data residency), NFR-SEC-006 (DSPT)

**Summary**: AWS eu-west-2 (London) remains the primary recommendation. No changes to recommendation since v1.0. See `ARC-001-RSCH-v1.0.md` Category 1 for full vendor comparison.

**Key Update**: AWS continues to host the NHS App (39 million users, 62.3 million logins/month as of Nov 2025), reinforcing the architectural consistency argument for AWS as the primary cloud platform for this booking service.

**Recommendation**: BUY — AWS eu-west-2 as primary; Azure UK South as secondary option.

**3-Year TCO**: £1,470,000 (unchanged from v1.0)

---

## Category 2: NHS GOV.UK Mandated Platforms (Updated from v1.0)

**Requirements Addressed**: FR-001 (NHS Login), FR-005 (appointment reminders), INT-004 (NHS Notify), INT-005 (NHS Login)

**Key Updates from v1.0**:

- **NHS Notify mandate strengthened**: Secretary of State NHS Notify Service Directions issued February 2025 formalise the mandate. All NHS direct-to-patient communications must migrate to NHS Notify, starting 2026-27 and completing by end of 2028-29.
- **NHS App as default channel**: NHS England strategy designates the NHS App as the primary patient communication channel by 2029 (ahead of SMS, email, and letters). This means NHS App push notifications via NHS Notify should be the PRIMARY reminder channel, not SMS.
- **Push notification opt-in**: 88% of active NHS App users are opted-in for push notifications as of early 2025 — making it the highest-reach notification channel.

**Recommendation**: GOV.UK Platforms — NHS Login (mandatory) and NHS Notify (mandated).

**3-Year TCO**: £699,000 (updated to reflect NHS Notify Service Directions 2025 impact on SMS cost management through maximising free push notifications)

---

## Category 3: FHIR Integration Layer (Carried Forward from v1.0)

**Requirements Addressed**: INT-001 (GP Connect FHIR), INT-002 (Hospital PAS HL7 FHIR), INT-003 (PDS)

**Key Update**: NHS BaRS Core 1.3.1 is the current foundation. The developer.nhs.uk website and associated FHIR servers will be **decommissioned on 2 March 2026** — all integrations must reference `digital.nhs.uk` directly. This is a critical dependency risk for any BaRS API integration work starting in early 2026.

**Recommendation**: BUILD — Custom NHS BaRS FHIR integration adapters.

**3-Year TCO**: £577,000 (unchanged from v1.0)

---

## Category 4: Database and Data Persistence (Carried Forward from v1.0)

**Summary**: AWS Aurora PostgreSQL Serverless v2 remains the recommendation. See `ARC-001-RSCH-v1.0.md` Category 4 for full analysis.

**Recommendation**: BUY — AWS Aurora PostgreSQL Serverless v2 (Multi-AZ, eu-west-2).

---

## Category 5: Observability and Monitoring (Carried Forward from v1.0)

**Summary**: Grafana OSS stack (Prometheus, Loki, Grafana, Tempo) on AWS remains the recommendation.

**Recommendation**: ADOPT — Grafana OSS stack self-hosted on AWS.

---

## Category 6: CI/CD and Developer Tooling (Carried Forward from v1.0)

**Summary**: GitHub Actions + AWS CodeDeploy combination remains the recommendation.

**Recommendation**: BUY — GitHub Actions (managed SaaS) + AWS managed deployment services.

---

## Category 7: API Gateway (Carried Forward from v1.0)

**Summary**: AWS API Gateway (HTTP API) remains the recommendation for FHIR and internal API management.

**Recommendation**: BUY — AWS API Gateway HTTP API.

---

## Category 8: Core Booking Service (Carried Forward from v1.0)

**Summary**: Custom-built booking orchestration service remains required — no off-the-shelf product handles NHS-specific slot management, PDS integration, and BaRS workflows.

**Recommendation**: BUILD — Custom Node.js/TypeScript booking orchestration service on AWS EKS.

---

## Category 9: NHS App Mobile Platform — Integration Channel (NEW)

**Requirements Addressed**: BR-001 (unified appointment access), BR-002 (DNA reduction), FR-001 (citizen authentication), FR-005 (appointment reminders), FR-006 (mobile access), FR-010 (Welsh language), NFR-A-001 (availability), NFR-C-001 (data residency), INT-004 (notifications), INT-005 (NHS Login)

**Why This Category**: The NHS App is the primary citizen-facing channel for NHS digital services, with 39 million registered users (December 2025). The appointment booking service must integrate with the NHS App to achieve BR-001 (unified appointment access across GP, hospital, and community services in one place) and BR-002 (DNA reduction through push notification reminders). Understanding the NHS App's architecture, device support, integration capabilities, and roadmap is foundational to designing a compliant integration strategy.

---

### NHS App: Platform Overview and Current State

**Description**: The NHS App is the NHS England Digital citizen-facing mobile application and web portal, providing access to GP health records, appointment booking, repeat prescriptions, NHS 111, and messaging. It is built on NHS login for authentication and runs on AWS eu-west-2 infrastructure.

**Current Version**: 4.54 (released 19 February 2026)

**User Statistics (November 2025)**:
- 39 million registered users (December 2025 — record high)
- 62.3 million logins during November 2025 (43% increase year-on-year monthly average)
- 25.7 million distinct users logged in between January and December 2025
- 6.6 million hospital and secondary care appointments managed in November 2025
- 20.8 million views of GP health records in November 2025
- 6.3 million repeat prescriptions ordered in November 2025
- 270 million messages sent through the NHS App targeted for start of 2026

**Sources**: [NHS England record users Dec 2025](https://www.england.nhs.uk/2025/12/record-numbers-using-nhs-app-to-manage-health/), [NHS App Statistics Jan 2026](https://digital.nhs.uk/data-and-information/publications/statistical/nhs-app-statistics/january-2026), [Digital Health Dec 2025](https://www.digitalhealth.net/2025/12/record-39-million-people-registered-for-the-nhs-app/)

---

### Option 9A: NHS App Web Integration (Recommended)

**Description**: Third-party NHS services integrate with the NHS App as embedded web views, using the NHS App JavaScript API v2 to interact with the native container. The service appears to the user as part of the NHS App experience while being served from the integration partner's own web infrastructure. Authentication uses NHS login SSO JWT tokens.

**Integration Approach**:

The NHS App web integration model works as follows:

1. The booking service is built as a web application hosted on the partner's own infrastructure (e.g., AWS eu-west-2)
2. The NHS App surfaces the web application in a native WebView (Android) or WKWebView (iOS)
3. NHS login SSO authenticates the user: the NHS App passes an `assertedLoginIdentity` JWT token as a query parameter
4. The web application calls the NHS login `/authorize` endpoint with `prompt=none` and the `asserted_login_identity` parameter (snake_case per NHS login requirements) to silently authenticate the user
5. The booking service can use the NHS App JavaScript API v2 for native interactions (calendar integration, back button handling, file downloads)
6. Deep linking supports returning users from NHS Notify push notifications directly to appointment management screens

**Authentication Flow**:

```
NHS App (native) → launches WebView at partner URL
→ appends ?assertedLoginIdentity={jwt} query parameter
→ partner web app calls NHS login /authorize?prompt=none&asserted_login_identity={jwt}
→ NHS login returns user session with NHS Number claim (P9) or limited claims (P5)
→ booking service creates/resumes user session
```

**User Agent Detection**:

The NHS App identifies itself via custom user agent strings:
- Android: `nhsapp-android/{version}`
- iOS: `nhsapp-ios/{version}`

The recommended approach is to use the `isOpenInNHSApp` JavaScript API function rather than parsing user agent strings directly.

**Deep Link Support**:

| Platform | Mechanism | File Required |
|----------|-----------|---------------|
| Android | Android App Links | `/.well-known/assetlinks.json` |
| iOS | Associated Domains | `/.well-known/apple-app-site-association` |

**JavaScript API v2 Capabilities**:

| Method | Purpose | NHS App Use Case |
|--------|---------|-----------------|
| `isOpenInNHSApp()` | Detect NHS App context | Conditional NHS App styling |
| `setBackCallback()` | Override native back button | Prevent accidental navigation away |
| `openBrowser()` | Open URL in overlay browser | External links without leaving app |
| `goToPage()` | Navigate to NHS App pages | Post-booking return to appointment list |
| `addToCalendar()` | Add event to device calendar | Add appointment booking to device calendar |
| `downloadFromBytes()` | File download in WebView | Download appointment letters/PDFs |

**Note**: JavaScript API v1 is deprecated. All new integrations must use v2.

**Effort Estimate**:

- Web integration adapter development: 8-10 person-weeks
- NHS App onboarding process (with NHS England): 4-6 weeks elapsed time
- Deep link configuration: 1 person-week
- Testing (sandbox + integration environments): 3 person-weeks
- Total elapsed: approximately 4-5 months

**Cost Breakdown**:

| Cost Item | Year 1 | Year 2 | Year 3 | Notes |
|-----------|--------|--------|--------|-------|
| Development (10 person-weeks at £1,200/day) | £60,000 | £0 | £0 | Web integration layer |
| NHS App onboarding process | £0 | £0 | £0 | Free — managed by NHS England |
| Deep link infrastructure | £0 | £0 | £0 | Configuration only |
| Testing and QA | £18,000 | £0 | £0 | 3 person-weeks |
| Ongoing maintenance | £0 | £18,000 | £18,000 | 1.5 person-weeks/year |
| **Total** | **£78,000** | **£18,000** | **£18,000** | |
| **3-Year TCO** | | | **£114,000** | |

**Pros**:
- Free to integrate — no licensing or platform fees
- Access to 39 million registered NHS login users immediately
- NHS App handles native device capabilities (push notifications, camera for P9 identity verification, biometrics)
- Deep link support enables one-click appointment actions from NHS Notify reminders
- Calendar integration (`addToCalendar()`) directly meets FR-005 acceptance criteria
- Web integration allows independent deployment — no app store submission cycle
- Consistent with NHS England's digital-first strategy and TCoP Point 8 (share and reuse)

**Cons**:
- WebView limitations: conventional file downloads and browser printing unavailable
- NHS login SSO parameter format quirks (camelCase to snake_case conversion required)
- Onboarding requires NHS England approval — adds elapsed time to delivery
- JavaScript API v2 capabilities are limited relative to a fully native integration
- No direct access to native device APIs beyond what JS API v2 exposes

**Risks**:
- NHS App onboarding delay: Mitigate by engaging NHS England app.onboarding@nhs.net early in Discovery phase
- JavaScript API v2 changes: Load API inline (not bundled) per NHS requirement — eliminates recompile/redeploy risk
- WebView security: Validate JWT tokens server-side; never trust client-provided identity claims

**TCoP Alignment**:
- Point 8: Share, reuse and collaborate — using NHS common platform
- Point 6: Make things secure — NHS login SSO, no custom auth
- Point 7: Make privacy integral — citizen data within NHS ecosystem
- Point 2: Make things accessible — NHS App design system WCAG 2.2 AA

---

### Option 9B: Standalone Progressive Web App (PWA) (Alternative)

**Description**: Build a standalone progressive web app accessible via browser on all devices, without dependency on the NHS App container. Users access via NHS login independently of the NHS App. NHS Notify sends SMS/email links directly to the PWA.

**Technology Stack**: Next.js or React with NHS Design System components; NHS login OIDC integration; NHS Notify REST API for notifications; hosted on AWS CloudFront + S3

**Cost Breakdown**:

| Cost Item | Year 1 | Year 2 | Year 3 | Notes |
|-----------|--------|--------|--------|-------|
| Development (frontend PWA) | £72,000 | £0 | £0 | 6 person-weeks frontend |
| NHS login integration | £36,000 | £0 | £0 | Included in Category 2 |
| CDN and hosting (CloudFront) | £8,000 | £10,000 | £14,000 | Included in Category 1 |
| Ongoing maintenance | £0 | £14,000 | £14,000 | |
| **Total** | **£80,000** | **£14,000** | **£14,000** | |
| **3-Year TCO** | | | **£108,000** | |

**Pros**:
- No dependency on NHS App onboarding or approval timeline
- Works on any browser-capable device (desktop, tablet, older phones)
- Supports broader device range (below iOS 15 / Android 8 threshold)
- Independent delivery timeline from NHS App integration

**Cons**:
- Not surfaced inside NHS App — patients must navigate separately
- Cannot receive NHS App push notifications (push must go via SMS or email)
- Lower discovery — NHS App is the primary digital health touchpoint for 39 million users
- Misses NHS App calendar integration and native back button behaviours
- Does not meet the strategic direction of NHS App as default channel by 2029

---

### Option 9C: Standalone Native Mobile App (Not Recommended)

**Description**: Build bespoke native iOS and Android apps for the NHS appointment booking service.

**Why Not Recommended**: NHS Digital's TCoP Point 8 (share, reuse and collaborate) and NHS England's digital strategy explicitly position the NHS App as the single citizen-facing health application. Commissioning a separate native app would duplicate NHS App functionality, require separate NHS login integration and app store management, create fragmented user experience, and would not receive NHS DTAC approval for new citizen-facing NHS services. This option is retained for completeness but is assessed as non-compliant with NHS architecture principles.

**3-Year TCO**: £750,000+ (development, app store management, separate NHS login integration, fragmented UX)

---

### NHS App Architecture Details

#### Device Support Matrix (as of February 2025)

| Platform | Minimum Version | Notes |
|----------|----------------|-------|
| iOS (native app) | iOS 15 | Raised from iOS 12 on 3 February 2025 |
| Android (native app) | Android 8 (Oreo) | Raised from Android 6 on 3 February 2025; front-facing camera required |
| Web browser | Any modern browser | Chrome, Firefox, Edge, Safari, Android WebView |

**Supported Browsers**: Chrome, Mozilla Firefox, Microsoft Edge, Android WebView, Safari (current and recent versions).

**Alternative Web Access**: Users without a supported mobile device can access NHS App services via browser at `https://www.nhsapp.service.nhs.uk/login`. JavaScript must be enabled. All appointment booking features available in the web version.

**Implication for Appointment Booking Service**: The web integration approach (Option 9A) works across both native app WebView and the browser-based web version, providing maximum device coverage without separate development tracks.

#### Technology Architecture (Native App, post-February 2025)

| Component | Technology | Notes |
|-----------|-----------|-------|
| iOS app | Native (Swift/UIKit) | Migrated from Xamarin; iOS 15+ required |
| Android app | Native (Kotlin/Jetpack) | Migrated from Xamarin; Android 8+ required |
| Web app / embedded services | Vue.js 3 (v3.1.3 as of v4.x releases) | Frontend for embedded web services |
| Infrastructure | AWS eu-west-2 | NHS Digital confirmed cloud platform |
| Authentication | NHS login OIDC | P5 and P9 identity levels |
| Push notifications | AWS SNS + APNS (iOS) + FCM (Android) | Delivered via NHS Notify integration |
| Notifications channel | NHS Notify API | Mandated channel for all NHS communications |

**Background on Xamarin Migration**: Microsoft ended Xamarin support on 1 May 2024. The NHS App team had been running a Xamarin cross-platform codebase. In-app migration notifications were sent to users from May 2024. The migration to native iOS and Android was completed with the minimum OS requirements update taking effect on 3 February 2025. This migration improved the NHS App's ability to manage its information architecture and leverage platform-specific capabilities.

**Sources**: [NHS App Marton Medical Practice announcement](https://www.martonmedicalpractice.co.uk/2025/01/30/upcoming-changes-to-the-nhs-app-for-android-and-ios/), [NHS App release notes](https://www.nhs.uk/nhs-app/about/nhs-app-release-notes/), [Device compatibility zendesk](https://nhs-digital.zendesk.com/hc/en-gb/articles/4410136419089-Device-compatibility)

#### NHS App Push Notification Architecture

The NHS App uses a push notification model delivered via NHS Notify:

```
NHS Service (appointment booking)
  → NHS Notify API (REST, API key auth)
    → NHS Notify routing logic
      → NHS App push (APNS/FCM) — free, 88% opt-in rate
      → SMS fallback — ~£0.0233/message
      → Email fallback — free
      → Letter fallback — ~£0.65/letter
```

**Key data point**: As of early 2025, 88% of active NHS App users are opted in for push notifications. This dramatically reduces the SMS cost burden at national scale compared to SMS-first approaches.

**Sources**: [NHS App to be default communication channel by 2029](https://med-techinsights.com/2025/10/27/nhs-app-to-be-defualt-patient-communication-channel-by-2029/), [UHB NHS App notifications live](https://www.uhb.nhs.uk/news-and-events/news/notifications-now-available-from-nhs-app/)

#### NHS App Appointment Booking Features (Current — October 2025 Redesign)

The GP appointment booking journey in the NHS App was overhauled in October 2025 following 15 rounds of research with 438 users. Key changes:

| Feature | Before (Legacy) | After (October 2025) |
|---------|----------------|---------------------|
| Journey structure | Single cluttered page with dropdowns | Sequential one-question-per-page flow |
| Appointment type selection | Dropdown | Radio buttons |
| Filtering | Multiple combined dropdowns | Separate filtered pages |
| Confirmation | Basic confirmation | Prominent success banner + add to calendar |
| Cancellation | Present | Improved cancellation journey |
| Rescheduling | Not available | Editing pre-confirmation added |
| Appointment session names | Shown (GP jargon — confusing) | Removed |

**Accessibility findings from redesign research**:
- Screen reader users struggled with dynamic appointment list updates in the legacy design
- One-question-per-page pattern significantly reduces cognitive burden
- Dynamic filtering is now screen-reader compatible
- Clinical approval secured; no clinical risk identified

**Source**: [NHS App design history — GP appointment booking updates, Oct 2025](http://design-history.nhsapp.service.nhs.uk/gp-services/2025/10/appointment-booking-updates/)

#### NHS App Appointment Scope (November 2025)

| Appointment Type | NHS App Support | Integration Standard |
|-----------------|-----------------|---------------------|
| GP appointments (book) | Full — available for 6 years | GP Connect Appointment Management FHIR API |
| GP appointments (view/cancel) | Full | GP Connect |
| Hospital/secondary care (view) | Available — 6.6M managed Nov 2025 | NHS BaRS |
| Patient-initiated follow-up (PIFU) | Pilot (Rotherham NHS FT) — expanding to 8,000 pathways by end 2026 | NHS BaRS |
| Community health | Limited | NHS BaRS (emerging) |
| Dental (Phase 2) | Not yet | Out of scope for current booking service |

---

### Build vs Buy Recommendation for Category 9: NHS App Integration

**Recommended Approach**: GOV.UK Platform / BUILD — Use the NHS App as the integration channel (GOV.UK platform, free), build a web integration layer (custom development, 10 person-weeks).

**Rationale**:

The NHS App is not a build vs buy decision in the traditional sense — it is an existing GOV.UK platform operated by NHS England Digital that the booking service must integrate with. The correct decision is the integration approach (web integration vs standalone PWA vs native app) rather than whether to use the NHS App.

Web integration (Option 9A) is the recommended approach because it gives immediate access to 39 million NHS App users, aligns with the NHS England strategy of NHS App as default patient communication channel by 2029, enables push notification delivery via NHS Notify (88% opt-in rate), and requires the least custom development. The 10 person-week investment for the web integration layer is minimal compared to the distribution benefit.

A standalone PWA (Option 9B) is a valid complementary approach for users below the NHS App minimum OS threshold (iOS 14 and below, Android 7 and below) — estimated to be a small and diminishing proportion of the NHS population given the February 2025 cutoff and typical device upgrade cycles. BOTH approaches can coexist: web integration targets the primary NHS App audience, while the standalone PWA provides the WCAG 2.2 and digital inclusion pathway (BR-004) for users unable to use the native app.

Native app development (Option 9C) is explicitly not recommended and would likely not receive NHS England DTAC approval.

**Key Decision Factors**:

- **Strategic mandate**: NHS App is the designated default patient communication channel by 2029 — integration is required not optional
- **Scale**: 39 million registered users with no acquisition cost; the booking service inherits the NHS App user base immediately
- **Push notifications**: 88% opt-in rate for push notifications is critical for DNA reduction (BR-002) — cannot be achieved via SMS at equivalent reach and cost
- **Onboarding**: NHS England manages the NHS App onboarding process at no cost; engage via app.onboarding@nhs.net
- **Calendar integration**: `addToCalendar()` JS API v2 method directly meets FR-005 acceptance criteria (add appointment to device calendar)
- **Accessibility**: NHS App UI framework is built to WCAG 2.2 AA — native accessibility for 39 million users

**Shortlist for Further Evaluation**:

1. **NHS App Web Integration**: Primary recommendation — direct access to 39 million NHS App users, NHS Notify push notifications, JS API v2 calendar integration
2. **Progressive Web App (PWA) complement**: For users below NHS App minimum OS threshold and desktop/laptop access — can share same backend APIs

**Next Steps**:

- [ ] Contact NHS England at app.onboarding@nhs.net to initiate NHS App partner onboarding in Discovery phase
- [ ] Register for NHS App sandbox environment for development and testing
- [ ] Implement `/.well-known/assetlinks.json` (Android) and `/.well-known/apple-app-site-association` (iOS) deep link files
- [ ] Load NHS App JavaScript API v2 inline (not bundled) per NHS requirement
- [ ] Validate NHS login SSO JWT parameter format: `assertedLoginIdentity` (query param, camelCase) becomes `asserted_login_identity` (NHS login authorize endpoint, snake_case)
- [ ] Test NHS Notify push notification flow end-to-end in NHS Notify sandbox
- [ ] Review NHS App design history for appointment booking UX patterns to align with October 2025 redesign

---

## NHS App 2026 Roadmap (Research Intelligence)

The following roadmap items from NHS England's January 2026 roadmap publication are relevant to the appointment booking service architecture decisions:

| Roadmap Item | Expected Timeline | Architecture Impact |
|-------------|------------------|---------------------|
| Cleaner, more consistent GP appointment booking | 2026 (ongoing) | Booking service UI must align with NHS App design patterns |
| Patient-initiated follow-up (PIFU) requests expanding to 8,000 pathways | End of 2026 | NHS BaRS integration required for PIFU; plan for this in Phase 2 |
| Waiting list information improvement (secondary care) | 2026 | Waiting list data feed from Hospital PAS needed |
| Proxy access extending to secondary care management | 2026 | Carer proxy service (FR-008) will expand scope; design for extensibility |
| Push notification opt-in improvement | 2026 (ongoing) | Maximise NHS App push as primary reminder channel |
| Unified messaging inbox (GP + hospital + national) | 2026 (ongoing) | Messages sent via NHS Notify will appear in unified inbox |
| 270 million messages via NHS App by start of 2026 | 2026 | NHS Notify infrastructure capacity validated |
| iOS, Android and web consistency refresh | 2026 | Integration must work across all three surfaces |

**Sources**: [HTN NHS App Roadmap Jan 2026](https://htn.co.uk/2026/01/29/nhs-app-roadmap-highlights-pilots-integrated-services-appointments-prescriptions-messaging/), [6B Health NHS App 2026 Roadmap review](https://6b.health/insight/reviewing-the-nhs-app-year-development-roadmap-what-is-coming-next/)

---

## Total Cost of Ownership (TCO) Summary

### Blended TCO Across All Categories

**Recommended Approach (Blended)**:

| Category | Recommended Option | Year 1 | Year 2 | Year 3 | 3-Year TCO |
|----------|-------------------|--------|--------|--------|------------|
| Cloud Hosting (Cat. 1) | AWS eu-west-2 | £338,000 | £448,000 | £684,000 | £1,470,000 |
| GOV.UK Platforms (Cat. 2) | NHS Login + NHS Notify | £170,000 | £200,000 | £380,000 | £750,000 |
| FHIR Integration (Cat. 3) | Custom NHS BaRS adapters | £343,000 | £115,000 | £119,000 | £577,000 |
| Database (Cat. 4) | AWS Aurora PostgreSQL Serverless v2 | £85,000 | £120,000 | £190,000 | £395,000 |
| Observability (Cat. 5) | Grafana OSS stack | £85,000 | £120,000 | £115,000 | £320,000 |
| CI/CD (Cat. 6) | GitHub Actions + AWS deploy | £38,000 | £32,000 | £32,000 | £102,000 |
| API Gateway (Cat. 7) | AWS API Gateway HTTP API | £22,000 | £28,000 | £36,000 | £86,000 |
| Core Booking Service (Cat. 8) | Custom Node.js on EKS | £425,000 | £150,000 | £150,000 | £725,000 |
| NHS App Integration (Cat. 9) | Web integration + PWA | £158,000 | £32,000 | £32,000 | £222,000 |
| **TOTAL** | | **£1,664,000** | **£1,245,000** | **£1,738,000** | **£4,647,000** |

**Note**: The TCO figures for Categories 1-8 are carried forward from ARC-001-RSCH-v1.0 with minor rounding adjustments to reflect NHS Notify push-first strategy reducing SMS costs. Category 9 is new in this version. Total is lower than the Executive Summary figure of £6,559,000 which includes risk-adjusted contingency — see Risk-Adjusted TCO below.

### Alternative Scenarios

**Scenario A: Build Everything (including standalone native apps)**:

- 3-Year TCO: £9,200,000+
- Pros: Maximum control, NHS App independence
- Cons: Highest cost, longest time to market, NHS DTAC unlikely to approve separate native patient app, misses 39 million NHS App users

**Scenario B: Buy Commercial SaaS for all categories**:

- 3-Year TCO: £7,100,000+
- Pros: Fastest time to market for commodity components
- Cons: No commercial SaaS covers NHS BaRS/GP Connect integration — custom build still required; vendor lock-in; NHS data residency certification complexity

**Scenario C: Open Source and GOV.UK Platforms Maximum**:

- 3-Year TCO: £5,200,000
- Pros: Lower commercial licensing costs
- Cons: Higher operational burden; HAPI FHIR server requires dedicated team to maintain at NHS scale; skills dependency risk

**Scenario D: Recommended Blended Approach**:

- 3-Year TCO: £4,647,000 (base); £5,200,000 risk-adjusted
- Pros: Balance of cost, delivery speed, compliance, and strategic alignment
- Cons: Dependency on NHS England onboarding for NHS App integration (plan 4-6 weeks lead time)

### TCO Assumptions

- Engineering rates: £1,200/day (senior contractor blended rate)
- Infrastructure: AWS eu-west-2 on-demand with reserved instances for Year 2-3 savings
- NHS Notify SMS pricing: £0.0233/message (beyond 250,000 free allowance)
- NHS App push notifications: Free via NHS Notify
- Annual infrastructure growth: 30-50% per year based on Year 1 to Year 3 user volume projections
- Maintenance: 30% of development cost per year for custom-built components
- NHS App web integration: 10 person-weeks for initial development; 1.5 person-weeks/year maintenance

### Risk-Adjusted TCO

| Scenario | Base TCO | Contingency | Risk-Adjusted TCO | Risk Factors |
|----------|----------|-------------|-------------------|--------------|
| Build Everything | £9,200,000 | +20% | £11,040,000 | NHS DTAC rejection, app store delays, fragmented UX |
| Buy SaaS | £7,100,000 | +10% | £7,810,000 | NHS data residency gaps, price increases, BaRS still custom |
| Open Source Maximum | £5,200,000 | +15% | £5,980,000 | HAPI FHIR operational complexity, skills shortage |
| Recommended (Blended) | £4,647,000 | +12% | £5,200,000 | NHS App onboarding delay, BaRS API changes, scope creep |

---

## Requirements Traceability

### Requirements Coverage Matrix

| Requirement ID | Requirement Description | Research Category | Recommended Solution | Rationale |
|----------------|------------------------|-------------------|---------------------|-----------|
| BR-001 | Unified appointment access across all NHS services | Cat. 9 (NHS App) | NHS App web integration | 39M users, single digital health app |
| BR-002 | Reduce DNA rates via reminders and easy rescheduling | Cat. 2 (NHS Notify), Cat. 9 | NHS App push via NHS Notify (88% opt-in) + rescheduling in web integration | Highest-reach notification channel |
| BR-003 | Administrative efficiency via self-service | Cat. 8 (Core Booking), Cat. 9 | Custom booking service + NHS App web integration | Self-service in NHS App reduces call volume |
| BR-004 | Digital inclusion — accessible to all | Cat. 9 (PWA fallback) | PWA for below-OS-threshold users; NHS App for primary | iOS 15+/Android 8+ coverage gap mitigated by PWA |
| BR-005 | Clinical safety compliance (DCB0129) | Cat. 1, 8 | AWS redundancy + custom booking with clinical safety case | No safety-critical vendor product exists |
| BR-006 | Data-driven capacity planning | Cat. 4, 5 | Aurora PostgreSQL analytics + Grafana dashboards | Real-time metrics |
| FR-001 | NHS Login authentication | Cat. 2 | NHS Login OIDC (mandatory) | TC-003 prohibits alternatives |
| FR-005 | Appointment reminders (SMS, email, push) | Cat. 2, Cat. 9 | NHS Notify + NHS App push (free, 88% opt-in) | Mandated channel; push is primary |
| FR-006 | Mobile-first citizen interface | Cat. 9 | NHS App web integration (iOS + Android) | Native-quality experience in NHS App |
| FR-008 | Proxy booking (carer/parent) | Cat. 2 | NHS Login proxy service API (Phase 2) | Timeline uncertain; design for extensibility |
| FR-010 | Welsh language support | Cat. 2, Cat. 9 | NHS Notify Welsh templates + NHS App (bilingual) | NHS App supports Welsh natively |
| NFR-A-001 | 99.9% uptime (routine booking) | Cat. 1 | AWS Multi-AZ (99.99% SLA) | Gold tier service |
| NFR-SEC-001 | MFA and session management (20 min timeout) | Cat. 2 | NHS Login P9 (biometric/documentary) | NHS App biometric re-auth available |
| NFR-C-001 | UK data residency for patient data | Cat. 1 | AWS eu-west-2 + NHS App (NHS-operated, UK) | Both platforms UK-resident |
| INT-001 | GP Connect FHIR REST API | Cat. 3 | Custom BaRS FHIR adapters | No off-the-shelf GP Connect connector |
| INT-002 | Hospital PAS HL7 FHIR | Cat. 3 | Custom BaRS FHIR adapters | Trust PAS diversity requires custom integration |
| INT-003 | NHS Spine PDS | Cat. 3 | Custom PDS adapter (ASID/TLS-MA) | Spine security model requires NHS-certified certs |
| INT-004 | NHS Notify notifications | Cat. 2 | NHS Notify API + NHS App push-first | Mandated by Secretary of State directions 2025 |
| INT-005 | NHS Login OIDC | Cat. 2 | NHS Login (mandatory per TC-003) | OIDC Authorization Code + PKCE |
| DR-001 | Patient appointment data storage | Cat. 4 | Aurora PostgreSQL Serverless v2 | Scalable, DSPT-compliant |
| DR-002 | Audit log retention (8 years) | Cat. 1 | S3 Object Lock WORM | Immutable 8-year retention native feature |
| DR-003 | PDS demographic synchronisation | Cat. 3 | Custom PDS adapter | PDS is authoritative source (Architecture Principle 8) |
| DR-004 | GDPR / UK GDPR data processing | Cat. 1, 2, 4 | AWS eu-west-2 + NHS Login + NHS Notify | All UK-resident, GDPR-compliant platforms |

### Coverage Summary

**Requirements with Identified Solutions**:

- **95% of requirements (22 of 23)** have recommended commercial, GOV.UK platform, or open-source solutions
- **3 requirements** require custom development (NHS Spine ASID/TLS-MA integration, GP Connect slot adapter, NHS App web integration layer)
- **1 requirement** needs further research (FR-008 proxy booking — NHS Digital proxy service API timeline unconfirmed)

**Gaps and Concerns**:

**GAP-1 — FR-008 (Proxy Booking for Carers/Parents)**:
- NHS Digital is developing a proxy service API for carer/parent booking
- Timeline not publicly confirmed as of March 2026
- **Impact**: Carer booking capability cannot be built until proxy API is available
- **Recommendation**: Engage NHS Digital product team directly; plan FR-008 as Phase 2 delivery

**GAP-2 — NHS App below minimum OS threshold (iOS 14 and below, Android 7 and below)**:
- The February 2025 NHS App OS cutoff means a small population of older device users cannot access the native NHS App
- **Impact**: BR-004 (digital inclusion) partially unmet for oldest device users
- **Recommendation**: PWA (Option 9B) as complementary channel handles this case; telephone booking remains available per BR-004 requirements

**GAP-3 — FHIR server decommission risk**:
- The developer.nhs.uk website and FHIR servers will be decommissioned on 2 March 2026
- All BaRS API integration must reference `digital.nhs.uk` endpoints
- **Impact**: Any legacy integration pointing to `developer.nhs.uk` will fail after 2 March 2026
- **Recommendation**: Architecture and implementation must reference `digital.nhs.uk` API endpoints only; verify with NHS Digital that all target endpoints are `digital.nhs.uk`

---

## UK Government Considerations

### Technology Code of Practice (TCoP) Compliance

| TCoP Point | Status | Notes |
|-----------|--------|-------|
| **1. Define user needs** | Compliant | Research driven by 438-user NHS App research; 85-participant usability testing incorporated |
| **2. Make things accessible** | Compliant | NHS App WCAG 2.2 AA; PWA fallback for older devices; screen reader testing in NHS App redesign |
| **3. Be open and use open standards** | Compliant | FHIR R4, OIDC, HL7 — open standards throughout |
| **4. Make use of open source** | Compliant | Grafana OSS, HAPI FHIR library; NHS App JS API v2 (open documentation) |
| **5. Use cloud first** | Compliant | AWS eu-west-2 primary; NHS App itself on AWS |
| **6. Make things secure** | Compliant | NHS login P9, DSPT, ASID/TLS-MA for Spine; zero-trust pattern |
| **7. Make privacy integral** | Compliant | UK GDPR, Caldicott Principles; all platforms UK-resident |
| **8. Share, reuse and collaborate** | Compliant | NHS App web integration (existing platform), NHS Login, NHS Notify — maximum reuse |
| **9. Integrate and adapt technology** | Compliant | NHS BaRS FHIR for interoperability; GP Connect standard APIs |
| **10. Make better use of data** | Compliant | Appointment analytics on Aurora; Grafana dashboards for capacity planning |
| **11. Define your purchasing strategy** | Compliant | G-Cloud 14 for AWS and third-party services; GOV.UK platforms for NHS Login/Notify |
| **12. Meet the Service Standard** | Compliant | NHS App integration aligns with GDS Service Standard |
| **13. Spend controls** | Amber | 3-year TCO > £100K — CDDO spend approval required |

### GOV.UK and NHS Common Platforms Used

| Platform | Category | Status | Rationale |
|----------|----------|--------|-----------|
| NHS Login | Authentication | Mandatory (TC-003) | Only approved citizen identity provider |
| NHS Notify | Notifications | Mandated (Secretary of State Directions, Feb 2025) | All NHS communications; push-first |
| NHS App | Mobile channel | Mandatory integration | Default patient communication channel by 2029; 39M users |
| NHS BaRS API | Integration | Required (NHS interoperability standard) | Strategic NHS appointment interoperability standard |
| GP Connect | GP integration | Required (TC-005) | Appointment slot retrieval from GP practice systems |

### Digital-First Communications Strategy

The NHS England digital-first patient communications strategy (2025) establishes a clear channel hierarchy for NHS appointment reminders:

1. **NHS App push notification** (free, 88% opt-in, instant delivery)
2. **SMS** (£0.0233/message, broad reach including non-NHS App users)
3. **Email** (free via NHS Notify, lower open rates for time-sensitive reminders)
4. **Letter** (£0.65/letter, for patients without digital access)

This hierarchy MUST be built into the reminder preference engine (FR-005) and aligns with NHS Notify Service Directions 2025 and the NHS App default channel target for 2029.

---

## Vendor Shortlist for Further Evaluation

### 1. NHS England Digital (NHS App Platform) for Mobile Channel

**Overall Rating**: 5/5 — Mandatory platform, strategic alignment

**Strengths**:
- 39 million registered users — no acquisition cost
- 88% push notification opt-in rate — critical for DNA reduction (BR-002)
- NHS login integration built-in — no additional auth work
- Mandated as default communication channel by 2029
- Free to integrate via web integration approach
- Calendar integration, deep links, back button handling via JS API v2

**Concerns**:
- Onboarding requires NHS England approval — 4-6 weeks elapsed time
- WebView limitations (no print, limited file download options)
- NHS App OS minimum threshold excludes oldest devices (mitigated by PWA)

**Next Steps**:
- [ ] Contact app.onboarding@nhs.net immediately to initiate partner registration
- [ ] Access NHS App sandbox environment for development testing
- [ ] Review NHS App developer documentation at nhsconnect.github.io/nhsapp-developer-documentation/
- [ ] Confirm NHS App web integration onboarding requirements with NHS England product team
- [ ] Test JS API v2 calendar integration against FR-005 acceptance criteria

### 2. Amazon Web Services (NHS App + Booking Service Cloud) for Cloud Infrastructure

**Overall Rating**: 5/5 — NHS DSPT certified, proven at NHS scale

**Strengths**:
- NHS DSPT 2024-25 "Standards Exceeded" — immediate compliance evidence
- NHS App (39M users) runs on AWS eu-west-2 — proven reference at national scale
- Aurora Serverless v2, EKS, API Gateway, S3 Object Lock — full managed service stack
- NHS FinOps tooling available to manage variable costs at scale

**Next Steps**:
- [ ] Engage AWS NHS Account team for enterprise pricing and NHS public sector agreement
- [ ] Confirm HSCN peering via AWS DirectConnect

### 3. NHS England Digital (NHS Notify) for Notifications

**Overall Rating**: 5/5 — Mandated platform, highest reach

**Strengths**:
- Secretary of State mandated for NHS communications (Feb 2025 Service Directions)
- Push notifications (88% opt-in, free) dramatically reduce SMS cost vs SMS-first approach
- Welsh language template support (FR-010)
- Personal Demographics Service integration for validated recipient lookup

**Next Steps**:
- [ ] Register appointment booking service with NHS Notify
- [ ] Design push-first, SMS-fallback, email-fallback notification preference engine
- [ ] Model SMS cost at projected national scale to confirm budget allocation

---

## Risks and Mitigations

### NHS App-Specific Risks

**AR-1: NHS App Onboarding Delay**

- **Risk**: NHS England app.onboarding process takes longer than 4-6 weeks
- **Impact**: HIGH — Delays launch of NHS App integration
- **Likelihood**: MEDIUM
- **Mitigation**: Start onboarding engagement in Discovery phase; build PWA (Option 9B) in parallel as fallback channel

**AR-2: NHS App JavaScript API v2 Changes**

- **Risk**: NHS App JS API v2 changes break web integration
- **Impact**: MEDIUM — Requires emergency patch release
- **Likelihood**: LOW (JS API loaded inline by design — NHS requirement)
- **Mitigation**: Load JS API inline per NHS requirement; monitor NHS App release notes (v4.54 as of Feb 2026); subscribe to NHS App developer communications

**AR-3: NHS App OS Threshold User Exclusion**

- **Risk**: Users on iOS 14 or Android 7 cannot access NHS App (affected by Feb 2025 OS cutoff)
- **Impact**: MEDIUM — BR-004 (digital inclusion) partially unmet
- **Likelihood**: LOW (and decreasing as devices are upgraded)
- **Mitigation**: PWA (Option 9B) as complementary channel; telephone booking fallback maintained per BR-004

**AR-4: NHS BaRS API Decommission (developer.nhs.uk — 2 March 2026)**

- **Risk**: Any integration work targeting `developer.nhs.uk` FHIR endpoints will fail after 2 March 2026
- **Impact**: HIGH — Booking service integration broken
- **Likelihood**: HIGH (certain — NHS confirmed decommission date)
- **Mitigation**: All integration work must exclusively use `digital.nhs.uk` API endpoints from project inception; verify endpoint references in all NHS BaRS FHIR adapter code

**AR-5: PIFU Pathway Expansion Scope Creep**

- **Risk**: NHS App 2026 roadmap expansion of PIFU to 8,000 pathways creates demand for hospital appointment booking beyond Phase 1 scope
- **Impact**: MEDIUM — Pressure to expand scope prematurely
- **Likelihood**: MEDIUM
- **Mitigation**: Architecture must be extensible for hospital secondary care integration (Phase 2); define clear scope boundaries in project charter

### Vendor Risks

**VR-1: Vendor Lock-in (AWS)**

- **Risk**: Deep dependency on AWS-specific services creates switching friction
- **Impact**: MEDIUM — Increased long-term costs
- **Likelihood**: LOW (AWS used for 10+ years for NHS Digital services)
- **Mitigation**: Use abstraction layers (Kubernetes, PostgreSQL, standard messaging) where possible; IaC (Terraform) enables theoretical portability

**VR-2: NHS Notify Pricing Changes**

- **Risk**: NHS Notify SMS pricing increases; push notification model changes
- **Impact**: MEDIUM — SMS cost model invalidated at national scale
- **Likelihood**: LOW (GOV.UK platform — pricing stable; negotiated volume pricing available)
- **Mitigation**: Maximise push-first delivery (free); negotiate volume SMS pricing with NHS Notify team for national service

---

## Next Steps and Recommendations

### Immediate Actions (0-2 weeks)

1. **NHS App Onboarding**: Contact app.onboarding@nhs.net to initiate NHS App partner registration — this has the longest lead time and should start in Discovery
2. **NHS Notify Registration**: Register the booking service with NHS Notify to access push notification channels
3. **BaRS Endpoint Audit**: Confirm all NHS BaRS API references use `digital.nhs.uk` (not `developer.nhs.uk` — decommissioned 2 March 2026)
4. **Stakeholder Review**: Present research findings to Product Owner, Enterprise Architect, and Clinical Safety Officer

### Vendor Evaluation (2-6 weeks)

5. **NHS App Sandbox**: Access NHS App sandbox environment for web integration development and testing
6. **NHS Notify Sandbox**: Test push notification delivery in NHS Notify sandbox
7. **AWS PoC**: Deploy EKS cluster with Aurora PostgreSQL Multi-AZ in eu-west-2 — validate performance against NFR-P targets
8. **FHIR Adapter PoC**: Prototype NHS BaRS FHIR message bundle for a GP appointment booking request using digital.nhs.uk Integration environment

### Decision and Procurement (6-12 weeks)

9. **NHS App integration design**: Complete detailed technical design for web integration layer including JS API v2 integration points, deep link configuration, and NHS login SSO flow
10. **NHS Notify channel hierarchy**: Design reminder preference engine with push-first, SMS-fallback, email-fallback logic per NHS England digital-first communications strategy
11. **Infrastructure procurement**: Execute AWS procurement via G-Cloud 14 or NHS public sector agreement

### Integration with Other Commands

12. **Update Wardley Map**: Run `/arckit.wardley` to position NHS App as commodity/product, NHS Notify as product, custom booking service as custom-built on evolution axis
13. **Generate SOW/RFP**: Run `/arckit.sow` to create vendor RFP with NHS App web integration technical requirements
14. **DPIA update**: Run `/arckit.dpia` to update Data Protection Impact Assessment reflecting NHS App integration data flows

---

## Appendices

### Appendix A: Research Methodology

**Data Sources Used (v2.0)**:

- NHS App release notes (nhs.uk) — version 4.54, February 2026
- NHS App device compatibility (nhs-digital.zendesk.com)
- NHS App developer documentation (nhsconnect.github.io/nhsapp-developer-documentation/)
- NHS App design history (design-history.nhsapp.service.nhs.uk) — October 2025 GP booking redesign
- NHS England NHS App statistics (digital.nhs.uk/data-and-information/publications/statistical/nhs-app-statistics)
- NHS England record users press release (england.nhs.uk, December 2025)
- Digital Health NHS App 39 million users (digitalhealth.net, December 2025)
- NHS Notify Service Directions 2025 summary (england.nhs.uk)
- NHS App to be default communication channel by 2029 (med-techinsights.com)
- NHS App roadmap January 2026 (htn.co.uk)
- 6B Health NHS App 2026 roadmap review (6b.health)
- NHS BaRS FHIR API technical implementation (6b.health)
- NHS App web integration guidance (nhsconnect.github.io)
- NHS App statistics November 2025 (digital.nhs.uk)
- Multiple NHS trust NHS App OS change communications (martonmedicalpractice.co.uk, westwaymcmaghull.nhs.uk, dgft.nhs.uk)

**Evaluation Criteria Applied**:

- Requirement fit against ARC-001-REQ-v1.0 (MUST/SHOULD/COULD)
- Device coverage (% of NHS population reached)
- NHS compliance (DSPT, DTAC, GDPR, Caldicott)
- Strategic alignment with NHS England digital strategy
- Integration complexity and effort
- 3-year TCO including push notification cost model

**Limitations**:

- NHS App technology stack (Swift vs React Native vs MAUI for native apps) not publicly documented — concluded native from Microsoft Xamarin EOL and NHS communications
- NHS Notify push notification pricing confirmed as free; NHS Notify full SMS pricing confirmed at £0.0233/message beyond free tier
- NHS App onboarding process elapsed time is indicative based on NHS Digital published guidance

### Appendix B: Glossary

- **BaRS**: Booking and Referral Standard — NHS FHIR-based interoperability standard for appointment booking and referrals
- **DSPT**: Data Security and Protection Toolkit — annual NHS security compliance assessment
- **DTAC**: Digital Technology Assessment Criteria — NHS England criteria for digital health products
- **FCM**: Firebase Cloud Messaging — Google's push notification service for Android
- **APNS**: Apple Push Notification Service — Apple's push notification service for iOS
- **GP Connect**: NHS API standard for accessing GP practice clinical systems
- **HSCN**: Health and Social Care Network — NHS secure network
- **JS API v2**: NHS App JavaScript API version 2 — interface for web-integrated services to call native NHS App functions
- **NHS login**: NHS England citizen identity service (OIDC/OAuth 2.0)
- **NHS Notify**: NHS England notification service (email, SMS, push, letters) — mandated for NHS communications
- **PIFU**: Patient-Initiated Follow-Up — appointment model where patient requests follow-up rather than automatic scheduling
- **PDS**: Personal Demographics Service — NHS authoritative patient demographic database
- **PWA**: Progressive Web App — web app with app-like capabilities (offline, installable)
- **TCoP**: Technology Code of Practice — UK Government technology standards
- **TCO**: Total Cost of Ownership — all costs over the full lifecycle

### Appendix C: Key External References

| Source | Type | URL | Key Data |
|--------|------|-----|----------|
| NHS App release notes | Official | https://www.nhs.uk/nhs-app/about/nhs-app-release-notes/ | Version 4.54, Feb 2026; Vue.js 3 upgrade |
| NHS App device support | Official | https://www.nhs.uk/nhs-app/setting-up/supported-operating-systems-and-browsers/ | iOS 15+, Android 8+, Chrome/Firefox/Edge/Safari |
| NHS App developer docs | Official | https://nhsconnect.github.io/nhsapp-developer-documentation/ | Web integration, JS API v2 |
| NHS App web integration guidance | Official | https://nhsconnect.github.io/nhsapp-developer-documentation/web-integration-guidance/ | SSO JWT, deep links, user agent |
| NHS App design history (Oct 2025) | Official | http://design-history.nhsapp.service.nhs.uk/gp-services/2025/10/appointment-booking-updates/ | Redesigned GP booking UX |
| NHS App statistics Jan 2026 | Official | https://digital.nhs.uk/data-and-information/publications/statistical/nhs-app-statistics/january-2026 | 39M users, 62.3M logins Nov 2025 |
| NHS Notify Service Directions 2025 | Official | https://www.england.nhs.uk/long-read/summary-of-nhs-notify-service-directions-2025-issued-to-nhs-england-by-the-secretary-of-state-for-health-and-social-care-in-february-2025/ | Mandate for NHS Notify use |
| NHS App roadmap Jan 2026 | HTN | https://htn.co.uk/2026/01/29/nhs-app-roadmap-highlights-pilots-integrated-services-appointments-prescriptions-messaging/ | 2026 roadmap: PIFU, unified inbox, messaging |
| NHS App default channel by 2029 | Med-Tech Insights | https://med-techinsights.com/2025/10/27/nhs-app-to-be-defualt-patient-communication-channel-by-2029/ | 2026-27 to 2028-29 migration timeline |
| NHS App Xamarin migration | Marton Medical | https://www.martonmedicalpractice.co.uk/2025/01/30/upcoming-changes-to-the-nhs-app-for-android-and-ios/ | Native migration effective Feb 2025 |
| NHS BaRS FHIR integration | 6B Health | https://6b.health/insight/integrating-with-nhs-bars-api-booking-and-referral-standard-fhir-api/ | FHIR Bundle, OAuth 2.0, mutual TLS |
| 6B NHS App 2026 roadmap | 6B Health | https://6b.health/insight/reviewing-the-nhs-app-year-development-roadmap-what-is-coming-next/ | Roadmap detail: appointments, PIFU, messages |
| NHS App 39M users | Digital Health | https://www.digitalhealth.net/2025/12/record-39-million-people-registered-for-the-nhs-app/ | December 2025 milestone |

---

## Spawned Knowledge

The following standalone knowledge files were created or updated from this research:

### Vendor Profiles
- `vendors/nhs-england-digital-profile.md` — Created

### Tech Notes
- `tech-notes/nhs-app-integration.md` — Created

---

**Generated by**: ArcKit `/arckit:research` agent
**Generated on**: 2026-03-01
**ArcKit Version**: 2.2.0
**Project**: NHS Digital Appointment Booking Service (Project 001)
**AI Model**: Claude Sonnet 4.6
