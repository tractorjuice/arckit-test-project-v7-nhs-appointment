# NHS Digital Appointment Service

An ArcKit-managed enterprise architecture governance project for an NHS appointment booking system with NHS Spine integration and UK GDPR safeguards.

Live documentation site: <https://tractorjuice.github.io/arckit-test-project-v7-nhs-appointment/>

## Overview

This repository demonstrates the use of [ArcKit](https://github.com/tractorjuice/arc-kit) to generate, govern, and publish the full set of enterprise architecture artifacts for a UK Government digital service. All artifacts follow NHS Digital standards (NHS Data Dictionary, HL7 FHIR, NHS login, PDS, DSPT, DCB0129) and UK Government frameworks (GDS Service Standard, TCoP, Secure by Design, Green Book).

## Current Project State

- **Project**: `001-nhs-appointment-booking`
- **Artifacts**: 13 architecture documents (requirements, plan, research, traceability, Wardley map, cloud research for AWS/Azure/GCP, gap analysis)
- **Vendor profiles**: 5 (AWS, Azure, Grafana Labs, NHS England Digital, NHS Notify)
- **Tech notes**: 6 (Aurora PostgreSQL, Grafana OSS, HAPI FHIR, NHS App integration, NHS BaRS FHIR, NHS login OIDC)
- **Global**: Architecture principles (v1.0, v1.1)

Run `/arckit:health` for a live governance health scan and `/arckit:pages` to regenerate the documentation site.

## Using ArcKit

ArcKit is distributed as a Claude Code plugin providing 80+ slash commands grouped into the following workflow stages:

### Discovery & Planning
`/arckit:principles`, `/arckit:stakeholders`, `/arckit:risk`, `/arckit:sobc`, `/arckit:requirements`, `/arckit:data-model`, `/arckit:plan`, `/arckit:roadmap`, `/arckit:strategy`

### Research
`/arckit:research`, `/arckit:datascout`, `/arckit:aws-research`, `/arckit:azure-research`, `/arckit:gcp-research`, `/arckit:gov-reuse`, `/arckit:gov-code-search`, `/arckit:gov-landscape`, `/arckit:grants`

### Architecture & Decisions
`/arckit:adr`, `/arckit:diagram`, `/arckit:wardley` (+ value-chain, climate, doctrine, gameplay), `/arckit:hld-review`, `/arckit:dld-review`, `/arckit:platform-design`, `/arckit:data-mesh-contract`

### Governance & Traceability
`/arckit:traceability`, `/arckit:analyze`, `/arckit:conformance`, `/arckit:impact`, `/arckit:principles-compliance`, `/arckit:maturity-model`, `/arckit:health`

### UK Government Compliance
`/arckit:tcop`, `/arckit:service-assessment`, `/arckit:secure`, `/arckit:mod-secure`, `/arckit:ai-playbook`, `/arckit:dpia`, `/arckit:atrs`, `/arckit:jsp-936`

### EU Compliance (community)
`/arckit:eu-rgpd`, `/arckit:eu-nis2`, `/arckit:eu-ai-act`, `/arckit:eu-dora`, `/arckit:eu-cra`, `/arckit:eu-dsa`, `/arckit:eu-data-act`

### French Government Compliance (community)
`/arckit:fr-rgpd`, `/arckit:fr-anssi`, `/arckit:fr-anssi-carto`, `/arckit:fr-ebios`, `/arckit:fr-dinum`, `/arckit:fr-secnumcloud`, `/arckit:fr-dr`, `/arckit:fr-pssi`, `/arckit:fr-algorithme-public`, `/arckit:fr-marche-public`, `/arckit:fr-code-reuse`

### Procurement
`/arckit:sow`, `/arckit:evaluate`, `/arckit:score`, `/arckit:dos`, `/arckit:gcloud-search`, `/arckit:gcloud-clarify`

### Operations
`/arckit:devops`, `/arckit:mlops`, `/arckit:finops`, `/arckit:operationalize`, `/arckit:servicenow`

### Delivery & Reporting
`/arckit:backlog`, `/arckit:trello`, `/arckit:story`, `/arckit:glossary`, `/arckit:presentation`, `/arckit:pages`, `/arckit:search`, `/arckit:framework`

## Project Structure

```
.
├── .arckit/
│   ├── memory/                # Global architecture artifacts
│   ├── templates/             # Document templates
│   └── scripts/bash/          # Helper scripts
├── .claude/                   # Claude Code settings and hooks
├── projects/
│   ├── 000-global/
│   │   ├── ARC-000-PRIN-v1.0.md
│   │   ├── ARC-000-PRIN-v1.1.md
│   │   ├── policies/          # Organisation-wide policies
│   │   └── external/          # Enterprise reference documents
│   └── 001-nhs-appointment-booking/
│       ├── ARC-001-REQ-v1.0.md
│       ├── ARC-001-PLAN-v1.0.md
│       ├── ARC-001-RSCH-v1.0.md
│       ├── ARC-001-TRAC-v1.0.md, v1.1.md
│       ├── ARC-001-GAPS-v1.0.md
│       ├── research/          # Cloud & technology research
│       ├── wardley-maps/      # Strategic maps
│       ├── vendors/           # Vendor profiles and submissions
│       ├── tech-notes/        # Technical reference notes
│       └── external/          # External documents
└── docs/                      # Generated GitHub Pages site
    ├── index.html             # Governance dashboard
    ├── manifest.json          # Document index
    ├── health.json            # Health scan results
    ├── llms.txt               # LLM/agent discovery index
    └── guides/                # Command usage guides
```

## Requirement ID Conventions

- `BR-xxx` — Business Requirements
- `FR-xxx` — Functional Requirements
- `NFR-xxx` — Non-Functional (NFR-P performance, NFR-SEC security, NFR-A availability)
- `INT-xxx` — Integration Requirements
- `DR-xxx` — Data Requirements

Traceability chain: Stakeholders → Goals → Requirements → Data Model → Components → User Stories.

## NHS-Specific Standards

- NHS Data Dictionary (clinical coding)
- HL7 FHIR (clinical data exchange)
- NHS login (citizen authentication via OIDC)
- PDS — Personal Demographics Service
- DSPT — Data Security and Protection Toolkit
- DCB0129 — Clinical Risk Management for health IT

## Getting Started

1. Launch Claude Code: `claude`
2. Run `/arckit:start` for guided onboarding
3. Run `/arckit:health` to view current governance status
4. Run `/arckit:pages` to regenerate the documentation site

## Documentation

- [ArcKit Project](https://github.com/tractorjuice/arc-kit)
- [Command Guides](docs/guides/)
- [Live Documentation Site](https://tractorjuice.github.io/arckit-test-project-v7-nhs-appointment/)
- [`CLAUDE.md`](CLAUDE.md) — project conventions for AI assistants
