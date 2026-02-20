# Vendor Profile: Amazon Web Services (AWS)

## Document Control

| Field | Value |
|-------|-------|
| **Document ID** | aws-profile |
| **Document Type** | Vendor Profile |
| **Project** | NHS Digital Appointment Booking Service (Project 001) |
| **Classification** | OFFICIAL |
| **Status** | DRAFT |
| **Version** | 1.0 |
| **Created Date** | 2026-02-20 |
| **Last Modified** | 2026-02-20 |
| **Review Cycle** | On-Demand |
| **Next Review Date** | 2026-08-20 |
| **Owner** | Enterprise Architect, NHS Digital |
| **Reviewed By** | PENDING |
| **Approved By** | PENDING |
| **Distribution** | Project Team, Architecture Team, Procurement |
| **Source Research** | ARC-001-RSCH-v1.0 |
| **Confidence** | high |

## Revision History

| Version | Date | Author | Changes | Approved By | Approval Date |
|---------|------|--------|---------|-------------|---------------|
| 1.0 | 2026-02-20 | ArcKit AI | Initial creation from `/arckit:research` agent | PENDING | PENDING |

---

## Overview

Amazon Web Services (AWS) is the world's leading cloud platform, operated by Amazon.com, Inc. (NASDAQ: AMZN). AWS provides on-demand cloud computing services including compute, storage, databases, networking, security, AI/ML, and developer tooling. AWS has been the strategic cloud partner for NHS Digital (now NHS England Digital) since at least 2019, with NHS login, the NHS App (31 million registered users), and the Electronic Prescription Service all running on AWS infrastructure. AWS completed its 2024-25 NHS Data Security and Protection Toolkit (DSPT) assessment with "Standards Exceeded" status, valid to June 2026.

## Products & Services

**Compute**: EC2, EKS (Kubernetes), ECS (containers), Lambda (serverless), Auto Scaling Groups

**Database**: Aurora PostgreSQL/MySQL (Serverless v2), RDS PostgreSQL, DynamoDB, ElastiCache (Redis)

**Messaging**: SQS (queuing — Standard and FIFO), SNS (pub/sub), EventBridge (event bus + scheduler)

**Networking**: VPC, CloudFront (CDN), Route 53, ALB/NLB, AWS WAF, AWS Shield

**Security**: IAM, KMS (HSM-backed key management), Secrets Manager, GuardDuty, Security Hub, CloudTrail, Certificate Manager

**Storage**: S3 (with Object Lock for WORM compliance), EBS, EFS, S3 Glacier Deep Archive

**Health-specific**: Amazon HealthLake (FHIR R4 datastore — US regions only as of 2025), AWS HealthImaging

**Developer Tools**: CodePipeline, CodeBuild, CodeDeploy, Cloud9, AWS SDK

## Pricing Model

Pay-as-you-go (on-demand) with significant discounts via Reserved Instances (1-year: 25-35% saving; 3-year: 40-50% saving). Savings Plans available for compute. Spot Instances for interruptible workloads. Free tier available for most services. NHS public sector agreement and Enterprise Discount Program (EDP) available for NHS organisations.

Key rates (eu-west-2, London, 2024):
- EKS cluster: $0.10/hour per cluster
- Aurora PostgreSQL Serverless v2: ~$0.12/ACU-hour
- SQS Standard: $0.40 per million requests (1M free/month)
- S3 Standard: $0.023/GB-month
- CloudFront (UK edge): $0.0085/10,000 HTTPS requests
- CloudWatch custom metrics: $0.30/metric/month (first tier)

## UK Government Presence

- G-Cloud listed: yes (G-Cloud 14, Lot 1 Cloud Hosting)
- DOS listed: yes (various framework agreements)
- UK data centres: yes — eu-west-2 (London, 3 Availability Zones), eu-west-1 (Ireland) as backup
- NHS DSPT status: Standards Exceeded (2024-25 assessment), valid June 2026
- HSCN connectivity: AWS DirectConnect with NHS-documented peering guide (digital.nhs.uk)
- Cyber Essentials Plus: Annual certification confirmed (2023)
- ISO 27001 / SOC 2 Type II / PCI DSS Level 1: All certified

## Strengths

- NHS DSPT "Standards Exceeded" 2024-25 — strongest NHS-specific compliance credential of any public cloud provider
- NHS England Digital runs NHS login (30M+ citizens), NHS App, Electronic Prescription Service, and COVID vaccination service on AWS
- Broadest managed services portfolio in the industry — reduces operational burden for NHS engineering teams
- Aurora Serverless v2 uniquely suited to variable NHS workloads (auto-scales in sub-second, no capacity planning)
- AWS Direct Connect + HSCN peering: official NHS England guidance published for connectivity
- EventBridge Scheduler eliminates need for custom cron infrastructure for appointment reminders
- S3 Object Lock (WORM) meets NHS 8-year immutable audit log retention requirement natively
- 31 million NHS App users processed without major incidents — proven national-scale reference

## Weaknesses

- On-demand pricing can generate unexpected bill spikes if auto-scaling not governed — requires FinOps discipline
- AWS HealthLake (managed FHIR service) not available in eu-west-2 as of 2025 (US East only) — HAPI FHIR self-hosted needed instead
- EKS has higher operational complexity than Azure AKS for initial setup
- Some NHS Trusts have existing Microsoft Azure enterprise agreements — dual-cloud complexity if AWS chosen for primary
- Cost of AWS consulting/professional services premium in NHS market

## Projects Referenced In

- ARC-001: NHS Digital Appointment Booking Service (2026) — RECOMMENDED as primary cloud platform

---

**Generated by**: ArcKit `/arckit:research` agent
**Generated on**: 2026-02-20
**ArcKit Version**: 2.2.0
**Project**: NHS Digital Appointment Booking Service (Project 001)
**Model**: Claude Sonnet 4.6
