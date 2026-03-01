# Tech Note: NHS App Integration for Appointment Booking Services

## Document Control

| Field | Value |
|-------|-------|
| **Document ID** | nhs-app-integration |
| **Document Type** | Tech Note |
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
| **Distribution** | Project Team, Development Team, Architecture Team |
| **Source Research** | ARC-001-RSCH-v2.0 |

## Revision History

| Version | Date | Author | Changes | Approved By | Approval Date |
|---------|------|--------|---------|-------------|---------------|
| 1.0 | 2026-03-01 | ArcKit AI | Initial creation from `/arckit:research` agent | PENDING | PENDING |

---

## Summary

The NHS App is the primary citizen-facing digital health platform in England, operated by NHS England Digital, with 39 million registered users (December 2025) and 62.3 million logins in November 2025. Current app version is 4.54 (released February 2026). The NHS App migrated from its legacy Xamarin cross-platform codebase to native iOS (Swift, iOS 15+) and Android (Kotlin, Android 8+) implementations effective 3 February 2025. Third-party NHS services integrate via web integration using NHS login SSO JWT tokens and the JavaScript API v2, with the app surfacing the service as an embedded WebView. NHS England designates the NHS App as the default patient communication channel by 2029, with all NHS communications migrating to NHS Notify by end of 2028-29. For appointment booking services, the NHS App is the mandatory integration target to achieve BR-001 (unified appointment access) and BR-002 (DNA reduction through push notification reminders with 88% opt-in rate among active users).

## Key Findings

**Current Version and Statistics**:
- Current version: 4.54 (19 February 2026)
- Registered users: 39 million (December 2025 — record)
- Monthly logins: 62.3 million (November 2025, +43% year-on-year)
- Secondary care appointments managed: 6.6 million (November 2025)
- Repeat prescriptions ordered: 6.3 million (November 2025)
- Messages target: 270 million through NHS App by start of 2026
- Push notification opt-in: 88% of active users as of early 2025
- Sources: [NHS England](https://www.england.nhs.uk/2025/12/record-numbers-using-nhs-app-to-manage-health/), [Digital Health](https://www.digitalhealth.net/2025/12/record-39-million-people-registered-for-the-nhs-app/), [NHS App statistics](https://digital.nhs.uk/data-and-information/publications/statistical/nhs-app-statistics/january-2026)

**Device and Platform Support (as of February 2025)**:
- iOS native app: iOS 15 and above (raised from iOS 12 on 3 February 2025)
- Android native app: Android 8 (Oreo) and above, device with front-facing camera required (raised from Android 6 on 3 February 2025)
- Web browser: Chrome, Firefox, Edge, Safari, Android WebView (any current version)
- Web access: Available at https://www.nhsapp.service.nhs.uk/login without app installation
- Source: [NHS App supported operating systems](https://www.nhs.uk/nhs-app/setting-up/supported-operating-systems-and-browsers/)

**Technology Architecture (post-February 2025)**:
- iOS: Native Swift/UIKit (migrated from Xamarin — Microsoft ended Xamarin support 1 May 2024)
- Android: Native Kotlin/Jetpack Compose (migrated from Xamarin)
- Embedded web services: Vue.js 3 (version 3.1.3 confirmed in release notes)
- Infrastructure: AWS eu-west-2 London (confirmed — NHS login, NHS App, EPS all on AWS)
- Authentication: NHS login OIDC (P5 medium, P9 high identity levels)
- Push notifications: APNS (iOS) + FCM (Android), delivered via NHS Notify
- Source: [NHS App release notes](https://www.nhs.uk/nhs-app/about/nhs-app-release-notes/), NHS trust migration communications

**Web Integration Architecture**:
- Integration model: Embedded WebView (Android WebView, iOS WKWebView)
- SSO mechanism: `assertedLoginIdentity` JWT query parameter (camelCase in URL) converted to `asserted_login_identity` (snake_case) for NHS login `/authorize` call with `prompt=none`
- User agent format: `nhsapp-android/{version}` or `nhsapp-ios/{version}` (detect via `isOpenInNHSApp()` JS function — do not parse user agent strings directly)
- Deep linking — Android: Android App Links (`/.well-known/assetlinks.json`)
- Deep linking — iOS: Associated Domains (`/.well-known/apple-app-site-association`)
- JavaScript API v1 is deprecated — all new integrations must use v2
- JS API must be loaded inline (not bundled) — NHS requirement to prevent recompile/redeploy on API changes
- Source: [NHS App web integration guidance](https://nhsconnect.github.io/nhsapp-developer-documentation/web-integration-guidance/)

**JavaScript API v2 Methods (appointment booking relevance)**:
- `isOpenInNHSApp()` — detect whether running inside NHS App (conditional styling)
- `setBackCallback()` — override native back button (prevent accidental navigation)
- `openBrowser()` — open URL in overlay browser (external links without leaving app)
- `goToPage()` — navigate to NHS App pages (post-booking return to appointment list)
- `addToCalendar()` — add appointment to device calendar (meets FR-005 acceptance criteria)
- `downloadFromBytes()` — file download workaround for WebView limitations (appointment letters)
- Source: [NHS App JS API developer documentation](https://nhsconnect.github.io/nhsapp-developer-documentation/js-api-specification/)

**WebView Limitations to Design Around**:
- Conventional file download in WebView: Not supported — use `downloadFromBytes()` JS API v2 method
- Browser printing: Not available, no plans to implement
- These limitations affect appointment letter and referral document handling

**GP Appointment Booking UX Redesign (October 2025)**:
- 15 rounds of research with 438 users (including visual/physical accessibility needs)
- 85-participant usability testing of original design
- Key findings: multiple dropdowns on single page caused confusion; users abandoned to phone instead; screen readers struggled with dynamic list updates
- Changes: single-question-per-page flow; radio buttons replace dropdowns; check-your-appointment-details page added; clear success banner on confirmation; calendar add option on confirmation; improved cancellation journey; session names (GP jargon) removed
- Clinical approval secured; no clinical risk identified
- All new appointment booking service UI should align with this one-question-per-page pattern
- Source: [NHS App design history Oct 2025](http://design-history.nhsapp.service.nhs.uk/gp-services/2025/10/appointment-booking-updates/)

**NHS App Appointment Booking Scope (November 2025)**:
- GP appointments: Full booking, viewing, and cancellation (available for 6 years)
- Hospital/secondary care: Viewing and management of appointments — 6.6 million managed Nov 2025
- Patient-initiated follow-up (PIFU): Pilot at Rotherham NHS FT; expanding to all PIFU pathways; estimated 8,000 pathways by end 2026
- Integration standard: GP Connect (GP appointments); NHS BaRS (hospital/community/PIFU)

**2026 Roadmap Items (NHS England, January 2026)**:
- Cleaner, more consistent GP appointment booking with clearer no-slot guidance
- PIFU request expansion to 8,000 pathways by end 2026
- Waiting list information improvement (secondary care)
- Proxy access extending to secondary care appointment management (carer/parent)
- Unified messaging inbox consolidating GP, hospital, and national messages
- Push notification opt-in rate improvement
- iOS, Android, and web surface consistency refresh
- Prescription reminders to support medicines adherence
- Source: [HTN NHS App roadmap Jan 2026](https://htn.co.uk/2026/01/29/nhs-app-roadmap-highlights-pilots-integrated-services-appointments-prescriptions-messaging/), [6B Health roadmap review](https://6b.health/insight/reviewing-the-nhs-app-year-development-roadmap-what-is-coming-next/)

**NHS Notify Push Notification Strategy**:
- NHS App push notifications are free and reach 88% of active users
- NHS Notify Service Directions 2025 (February) mandate NHS Notify for all NHS patient communications
- NHS England target: NHS App as default communication channel by 2029; full migration from SMS/letters by end 2028-29
- Channel priority: NHS App push (free, 88% reach) > SMS (£0.0233/message) > Email (free) > Letter (£0.65)
- Integration: REST API with API key authentication; webhooks for delivery status callbacks
- Source: [NHS Notify Service Directions summary](https://www.england.nhs.uk/long-read/summary-of-nhs-notify-service-directions-2025-issued-to-nhs-england-by-the-secretary-of-state-for-health-and-social-care-in-february-2025/)

**Critical Decommission Risk**:
- developer.nhs.uk website, associated domains, and FHIR servers will be decommissioned on 2 March 2026
- All NHS BaRS, GP Connect, and API integrations must reference digital.nhs.uk endpoints exclusively
- Impact: Any code or documentation pointing to developer.nhs.uk will break after this date

**Onboarding Process**:
- Contact: app.onboarding@nhs.net
- Elapsed time: 4-6 weeks
- Cost: Free — managed by NHS England
- Must start in Discovery phase due to onboarding lead time
- Sandbox environment available for development and testing before production access

## Relevance to Projects

- **ARC-001 NHS Digital Appointment Booking Service**: NHS App is the primary citizen-facing integration target. Category 9 of ARC-001-RSCH-v2.0. The web integration approach (10 person-weeks development) is the recommended integration strategy, giving access to 39 million users at no platform cost. Push notification via NHS App (88% opt-in, free) is the primary appointment reminder channel, critical to achieving BR-002 DNA reduction target.

---

**Generated by**: ArcKit `/arckit:research` agent
**Generated on**: 2026-03-01
**ArcKit Version**: 2.2.0
**Project**: NHS Digital Appointment Booking Service (Project 001)
**Model**: Claude Sonnet 4.6
