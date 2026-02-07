# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an **ArcKit test project** for an NHS Digital Appointment Service. It demonstrates enterprise architecture governance using ArcKit's slash commands to generate architecture artifacts.

**Project Context**: NHS appointment booking system with NHS Spine integration and GDPR safeguards.

## Using ArcKit Commands

ArcKit provides 40 slash commands for generating architecture documents. Commands follow a workflow sequence:

### Recommended Workflow Order

1. `/arckit.principles` - Create enterprise architecture principles (already done - see `.arckit/memory/architecture-principles.md`)
2. `/arckit.stakeholders` - Analyze stakeholder drivers, goals, and outcomes
3. `/arckit.risk` - Create risk register (HM Treasury Orange Book)
4. `/arckit.sobc` - Create Strategic Outline Business Case (Green Book 5-case model)
5. `/arckit.requirements` - Create comprehensive requirements (BR/FR/NFR/INT/DR)
6. `/arckit.data-model` - Create data model with ERD and GDPR compliance
7. `/arckit.dpia` - Generate Data Protection Impact Assessment
8. `/arckit.research` - Research technology options with build vs buy analysis
9. `/arckit.wardley` - Create strategic Wardley Maps
10. `/arckit.diagram` - Generate architecture diagrams (Mermaid)
11. `/arckit.sow` - Generate Statement of Work / RFP
12. `/arckit.evaluate` - Create vendor evaluation framework
13. `/arckit.backlog` - Convert requirements to user stories and sprints
14. `/arckit.servicenow` - Design ServiceNow service management
15. `/arckit.traceability` - Generate requirements traceability matrix

### UK Government Compliance Commands

- `/arckit.tcop` - Technology Code of Practice (13 points)
- `/arckit.service-assessment` - GDS Service Standard (14 points)
- `/arckit.secure` - Secure by Design (NCSC CAF, Cyber Essentials, UK GDPR)
- `/arckit.ai-playbook` - AI Playbook compliance (for AI systems)
- `/arckit.atrs` - Algorithmic Transparency Recording Standard

## Project Structure

```
.arckit/
├── memory/                    # Global architecture artifacts
│   └── architecture-principles.md  # Enterprise principles (already created)
├── templates/                 # Document templates used by commands
└── scripts/bash/              # Helper scripts for project management

# ArcKit commands provided by the ArcKit plugin (marketplace)

projects/                      # Individual architecture projects (created by commands)
└── 001-project-name/          # Numbered project directories
    ├── stakeholder-drivers.md
    ├── risk-register.md
    ├── requirements.md
    ├── data-model.md
    └── vendors/

docs/                          # GitHub Pages documentation site
├── index.html                 # Generated documentation portal
└── manifest.json              # Project artifact index
```

## Key Patterns

**Token Limit Handling**: Large documents (requirements, SOBC, research) use the Write tool to avoid the 32K output token limit. If you hit this limit, ask Claude to write directly to file and show only a summary.

**Template-Driven**: All commands read templates from `.arckit/templates/` - never generate freeform documents.

**Traceability Chain**: Stakeholders → Goals → Requirements (BR/FR/NFR/INT/DR) → Data Model → Components → User Stories

**Requirement ID Prefixes**:
- BR-xxx: Business Requirements
- FR-xxx: Functional Requirements
- NFR-xxx: Non-Functional (NFR-P Performance, NFR-SEC Security, NFR-A Availability)
- INT-xxx: Integration Requirements
- DR-xxx: Data Requirements

## NHS-Specific Context

This project follows NHS Digital standards:
- NHS Data Dictionary for clinical coding
- HL7 FHIR for clinical data exchange
- NHS login for citizen authentication
- PDS (Personal Demographics Service) integration
- DSPT (Data Security and Protection Toolkit) compliance
- DCB0129 Clinical Risk Management for health IT

## Helper Scripts

```bash
# Create a new numbered project directory
.arckit/scripts/bash/create-project.sh --name "project-name" --json

# Generate document ID (e.g., ARC-001-REQ-v1.0)
.arckit/scripts/bash/generate-document-id.sh PROJECT_ID DOC_TYPE VERSION

# List all projects
.arckit/scripts/bash/list-projects.sh
```

## Publishing Documentation

Run `/arckit.pages` to generate a GitHub Pages site at `docs/index.html` with all project documentation and Mermaid diagram rendering.
