# Google Cloud Technology Research: NHS Digital Appointment Booking Service

> **Template Status**: Experimental | **Version**: 1.1 | **Command**: `/arckit.gcp-research`

## Document Control

| Field | Value |
|-------|-------|
| **Document ID** | ARC-001-GCRS-v1.1 |
| **Document Type** | Google Cloud Technology Research |
| **Project** | NHS Digital Appointment Booking Service (Project 001) |
| **Classification** | OFFICIAL |
| **Status** | DRAFT |
| **Version** | 1.1 |
| **Created Date** | 2026-02-10 |
| **Last Modified** | 2026-02-10 |
| **Review Cycle** | Monthly |
| **Next Review Date** | 2026-03-10 |
| **Owner** | Enterprise Architect, NHS Digital |
| **Reviewed By** | PENDING |
| **Approved By** | PENDING |
| **Distribution** | Project Team, Architecture Team, Clinical Safety Team |

## Revision History

| Version | Date | Author | Changes | Approved By | Approval Date |
|---------|------|--------|---------|-------------|---------------|
| 1.0 | 2026-02-10 | ArcKit AI | Initial creation from `/arckit.gcp-research` agent | PENDING | PENDING |
| 1.1 | 2026-02-10 | ArcKit AI | Minor content refresh with updated details on pricing and UK Gov compliance. | PENDING | PENDING |

---

## Executive Summary

### Research Scope

This document presents Google Cloud-specific technology research findings for the project requirements. It provides Google Cloud service recommendations, architecture patterns, and implementation guidance based on official Google documentation.

**Requirements Analyzed**: 12 functional, 15 non-functional, 6 integration, 4 data requirements

**Google Cloud Services Evaluated**: 15+ Google Cloud services across 5 categories

**Research Sources**: Google Cloud Documentation, Google Cloud Architecture Center, Google Cloud Architecture Framework, Google Developer Knowledge MCP, Google Web Search for compliance/pricing.

### Key Recommendations

| Requirement Category | Recommended Google Cloud Service | Tier | Monthly Estimate |
|---------------------|----------------------------------|------|------------------|
| Compute | Cloud Run & Cloud Functions | On-Demand | £14,000 - £20,000 |
| Data | Cloud SQL (PostgreSQL), Cloud Firestore, Cloud Storage, Memorystore, BigQuery | On-Demand | £7,000 - £10,000 |
| Integration | API Gateway, Cloud Pub/Sub, Eventarc, Cloud Tasks | Pay-as-you-go | £3,000 - £5,000 |
| Security | Cloud Armor, Secret Manager, Security Command Center, Cloud KMS, IAM, VPC Service Controls | Standard/Premium | £2,500 - £4,000 |
| AI/ML | Vertex AI | Pay-as-you-go | £500 - £1,500 |

### Architecture Pattern

**Recommended Pattern**: Serverless Web Application with Event-Driven Microservices

**Reference Architecture**: [https://cloud.google.com/architecture/serverless-web-app-architecture](https://cloud.google.com/architecture/serverless-web-app-architecture)

### UK Government Suitability

| Criteria | Status | Notes |
|----------|--------|-------|
| **UK Region Availability** | ✅ europe-west2 (London) | Primary UK region for all recommended services. europe-west1 (Belgium) for DR. |
| **G-Cloud Listing** | ✅ G-Cloud 14 | Google Cloud services are available via suppliers on the UK G-Cloud Digital Marketplace. |
| **Data Classification** | ✅ OFFICIAL / OFFICIAL-SENSITIVE | VPC Service Controls recommended for sensitive data. Google Cloud is not suitable for SECRET classification in UK Government projects. |
| **NCSC Cloud Security Principles** | ✅ 14/14 principles met | Google Cloud provides detailed attestations and documentation on alignment with NCSC Cloud Security Principles. |

---

## Google Cloud Services Analysis

### Category 1: Compute

**Requirements Addressed**: FR-001, FR-002, FR-003, FR-004, FR-005, FR-006, FR-007, FR-008, FR-009, FR-011, NFR-P-001, NFR-P-002, NFR-P-003, NFR-A-003, NFR-S-001, NFR-U-001

**Why This Category**: The project requires highly scalable, resilient, and cost-efficient compute for citizen-facing web interfaces, API backends, staff interfaces, and event-driven tasks like reminders. Serverless options are preferred for their operational simplicity, automatic scaling, and pay-as-you-go cost model, directly addressing throughput, performance, and horizontal scaling NFRs.

---

#### Recommended: Cloud Run & Cloud Functions

**Service Overview**:
- **Full Name**: Cloud Run & Cloud Functions
- **Category**: Compute
- **Documentation**: [https://cloud.google.com/run/docs](https://cloud.google.com/run/docs), [https://cloud.google.com/functions/docs](https://cloud.google.com/functions/docs)

**Key Features**:
- **Cloud Run**: Fully managed serverless platform for containerized applications. Ideal for hosting the API backend for all functional requirements (FR-001 to FR-009, FR-011) and could also serve the staff interface (FR-011). Offers more flexibility for complex services. Scales automatically from zero to meet demand, ensuring NFR-S-001 and NFR-P-003 compliance.
- **Cloud Functions**: Event-driven serverless compute for lightweight, single-purpose functions. Best suited for automated reminders (FR-005, UC-003) and simpler, event-triggered API endpoints or backend processing tasks that integrate with Pub/Sub.
- **Automatic Scaling**: Both services scale automatically based on traffic, handling demand spikes efficiently and meeting NFR-S-001.
- **Pay-per-use**: Costs are based on actual resource consumption, optimizing for cost efficiency, aligning with NFR-S-001 (scales to zero).
- **Custom Domains & SSL**: Cloud Run supports custom domains and automatic SSL, essential for citizen-facing services.

**Pricing Model**:
- **Cloud Run**: Billed per vCPU-second, GiB-second, and per request. First 2 million requests and a certain amount of compute/memory are free monthly. Beyond free tier, roughly $3.00-$4.00 per million requests, $0.000024 per vCPU-second, and $0.0000025 per GB-second.
- **Cloud Functions**: Similar pay-as-you-go model, billed per invocation, compute time, and memory. Generous free tier for invocations and compute.

**Estimated Cost for This Project**:
| Resource | Configuration | Monthly Cost | Notes |
|----------|---------------|--------------|-------|
| Cloud Run | 10M API requests, 4 vCPU-GB/hour avg. | £10,000 - £15,000 | API Backend, potentially Staff UI |
| Cloud Functions | 50M invocations, 1 GB-hour/day avg. | £4,000 - £5,000 | Reminders, Event Processing |
| **Total** | | **£14,000 - £20,000** | (Estimates include network egress) |

**Google Cloud Architecture Framework Assessment**:

| Pillar | Rating | Notes |
|--------|--------|-------|
| **Sustainability** | ⭐⭐⭐⭐⭐ | Serverless nature reduces idle resources, contributing to lower carbon footprint. |
| **Operational Excellence** | ⭐⭐⭐⭐⭐ | Fully managed services minimize operational overhead; integrated with Cloud Monitoring and Logging. |
| **Security, Privacy and Compliance** | ⭐⭐⭐⭐☆ | IAM for granular access control; built-in security features; private networking options. |
| **Reliability** | ⭐⭐⭐⭐⭐ | High availability and automatic failover across zones/regions; automatic scaling for resilience. |
| **Cost Optimization** | ⭐⭐⭐⭐⭐ | Pay-as-you-go model, scales to zero when idle. Generous free tiers reduce initial costs. |
| **Performance Optimization** | ⭐⭐⭐⭐⭐ | Rapid scaling to meet demand spikes, low-latency execution, efficient resource allocation. |

**UK Region Availability**:
- ✅ europe-west2 (London) - Primary deployment region for all services.
- ✅ europe-west1 (Belgium) - Available for disaster recovery (DR) strategy.

---

#### Alternative: Google Kubernetes Engine (GKE)

**Service Overview**:
- **Full Name**: Google Kubernetes Engine (GKE)
- **Category**: Compute
- **Documentation**: [https://cloud.google.com/kubernetes-engine/docs](https://cloud.google.com/kubernetes-engine/docs)

**Key Features**: Managed Kubernetes service. Provides more control over the underlying infrastructure and container orchestration, suitable for complex microservice architectures or lift-and-shift scenarios. Offers auto-scaling of nodes and pods.

**Comparison**: While GKE provides greater flexibility, Cloud Run and Cloud Functions are preferred for their higher abstraction, reduced operational overhead, and faster development cycles, aligning better with the NHS Digital Service Standard's emphasis on rapid delivery and lean operations. GKE could be considered for future phases if specific custom orchestration or very complex stateful workloads are required.

---

### Category 2: Data

**Requirements Addressed**: DR-xxx (Appointment, Citizen, Organisation, AuditLog), NFR-A-002, NFR-C-001, NFR-SEC-003, NFR-S-002, NFR-C-003

**Why This Category**: The project demands robust, highly available, and compliant data storage solutions for transactional appointment data, dynamic user profiles, referential organisation data, and immutable audit logs. Strict adherence to UK data residency, retention, and encryption requirements is paramount.

---

#### Recommended: Cloud SQL (PostgreSQL), Cloud Firestore, Cloud Storage, Memorystore (Redis), BigQuery

**Service Overview**:
- **Full Name**: Cloud SQL (PostgreSQL), Cloud Firestore, Cloud Storage, Memorystore (Redis), BigQuery
- **Category**: Database & Storage
- **Documentation**: [https://cloud.google.com/sql/docs](https://cloud.google.com/sql/docs), [https://cloud.google.com/firestore/docs](https://cloud.google.com/firestore/docs), [https://cloud.google.com/storage/docs](https://cloud.google.com/storage/docs), [https://cloud.google.com/memorystore/docs](https://cloud.google.com/memorystore/docs), [https://cloud.google.com/bigquery/docs](https://cloud.google.com/bigquery/docs)

**Key Features**:
- **Cloud SQL (PostgreSQL)**: Fully managed relational database service. Recommended for the core transactional `Appointment` and `Organisation` data, ensuring strong consistency, ACID properties, and relational integrity. Offers high availability (multi-zone), automated backups, and encryption at rest and in transit. Supports `NFR-A-002` RPO/RTO.
- **Cloud Firestore**: Scalable, serverless NoSQL document database. Ideal for storing dynamic `Citizen` profiles and user preferences (e.g., notification preferences) that might have varying structures and require real-time updates and syncing across client applications (for mobile apps).
- **Cloud Storage**: Highly durable and available object storage. Essential for long-term retention of immutable `AuditLog` records (NFR-C-003) using Object Retention Lock for WORM (Write Once, Read Many) compliance. Also suitable for storing any static assets for the web frontend (NFR-U-001).
- **Memorystore (Redis)**: Fully managed in-memory data store. Recommended for caching frequently accessed data (e.g., available appointment slots, user session data) to meet `NFR-P-001` and `NFR-P-002` performance requirements, and to reduce load on primary databases.
- **BigQuery**: Serverless, highly scalable, and cost-effective enterprise data warehouse. Essential for `BR-006: Data-Driven Capacity Planning` and analytics, enabling complex queries on aggregated (anonymised) appointment data, DNA rates, and operational metrics.

**Pricing Model**:
- **Cloud SQL (PostgreSQL)**: Billed for CPU, memory, storage (SSD), and network egress. Per-second billing. Example: 4 vCPU, 16GB RAM instance with 500GB SSD could be approximately £400-£500/month in europe-west2.
- **Cloud Firestore**: Billed per document reads/writes/deletes, and storage. Free tier available. Example: 100M reads, 50M writes, 100GB storage could be around £1,000-£1,500/month.
- **Cloud Storage**: Billed per storage volume (tiered), network egress, and operations. Example: 1TB Standard storage, 10TB Archive for audit logs could be around £50-£100/month.
- **Memorystore (Redis)**: Billed per GB-hour for memory capacity. Example: 5GB Standard Tier Redis instance could be £100-£200/month.
- **BigQuery**: Billed for data storage, query processing (on-demand or flat-rate), and streaming inserts. First 1TB query processing is free per month. Example: 5TB active storage, 10TB query processing could be £200-£500/month.

**Estimated Cost for This Project**:
| Resource | Configuration | Monthly Cost | Notes |
|----------|---------------|--------------|-------|
| Cloud SQL (PostgreSQL) | db-g2-standard-4 (4 vCPU, 16GB RAM), 500GB SSD | £4,000 - £6,000 | Core Transactional Data |
| Cloud Firestore | 100M reads/writes, 100GB storage | £1,000 - £1,500 | User Profiles, Preferences |
| Cloud Storage | 1TB Standard, 10TB Archive | £100 - £200 | Audit Logs, Static Assets |
| Memorystore (Redis) | 5GB Standard Tier | £100 - £200 | Caching |
| BigQuery | 5TB active storage, 10TB query processing | £200 - £500 | Analytics & Capacity Planning |
| **Total** | | **£7,000 - £10,000** | (Estimates include network egress) |

**Google Cloud Architecture Framework Assessment**:

| Pillar | Rating | Notes |
|--------|--------|-------|
| **Security, Privacy and Compliance** | ⭐⭐⭐⭐⭐ | Encryption at rest (AES-256) and in transit (TLS) by default. IAM for fine-grained access. Object Retention Lock for WORM compliance (NFR-C-003). Data residency in UK regions (NFR-C-001). |
| **Reliability** | ⭐⭐⭐⭐⭐ | Multi-zone deployments for high availability. Automated backups and point-in-time recovery for Cloud SQL. Robust disaster recovery options across regions (NFR-A-002). |
| **Cost Optimization** | ⭐⭐⭐⭐☆ | Tiered storage for Cloud Storage. Pay-per-use for Firestore, BigQuery. Committed Use Discounts for Cloud SQL. |
| **Performance Optimization** | ⭐⭐⭐⭐⭐ | High IOPS for Cloud SQL SSDs. Low-latency caching with Memorystore. Fast query performance with BigQuery. |
| **Operational Excellence** | ⭐⭐⭐⭐⭐ | Fully managed services reduce operational burden, with integrated monitoring and logging. |
| **Sustainability** | ⭐⭐⭐⭐☆ | Efficient resource utilization and carbon-neutral operations in Google Cloud regions. |

**UK Region Availability**:
- ✅ All services available in europe-west2 (London) for primary deployment.
- ✅ europe-west1 (Belgium) is available for geo-redundant disaster recovery, adhering to UK data residency.

---

### Category 3: Integration

**Requirements Addressed**: INT-001 to INT-006, NFR-A-003 (Resilience Patterns)

**Why This Category**: A robust and secure integration layer is critical to connect the appointment booking service with diverse external NHS systems (GP Connect, Hospital PAS, NHS Spine PDS, NHS Notify, NHS Login) and internal components. Emphasis is on loose coupling and asynchronous communication for resilience and scalability, as per Architecture Principles 9 & 10.

---

#### Recommended: API Gateway, Cloud Pub/Sub, Eventarc, Cloud Tasks

**Service Overview**:
- **Full Name**: API Gateway, Cloud Pub/Sub, Eventarc, Cloud Tasks
- **Category**: Application Integration, Networking
- **Documentation**: [https://cloud.google.com/api-gateway/docs](https://cloud.google.com/api-gateway/docs), [https://cloud.google.com/pubsub/docs](https://cloud.google.com/pubsub/docs), [https://cloud.google.com/eventarc/docs](https://cloud.google.com/eventarc/docs), [https://cloud.google.com/tasks/docs](https://cloud.google.com/tasks/docs)

**Key Features**:
- **API Gateway**: Fully managed service for creating, securing, and managing APIs. Essential for `INT-001`, `INT-002`, `INT-003` to expose secure, rate-limited, and authenticated APIs to external NHS systems and internal microservices. Supports OpenAPI specifications for definition.
- **Cloud Pub/Sub**: A global, real-time messaging service for asynchronous communication. Ideal for `INT-004: NHS Notify` (sending notifications), `INT-006: Analytics Platform` (event streaming), and internal event-driven architectures (Architecture Principle 10). Supports push and pull subscriptions.
- **Eventarc**: Event infrastructure that connects services in an event-driven architecture. Used to route events from Google Cloud sources (e.g., Pub/Sub topics, Cloud Storage) to Cloud Run services or Cloud Functions, enabling event-driven processing of notifications or data updates from external systems.
- **Cloud Tasks**: A fully managed service for dispatching asynchronous tasks. Crucial for managing reliable appointment reminder delivery (FR-005, UC-003) with configurable retry logic, schedules, and deduplication, ensuring resilience (NFR-A-003).

**Pricing Model**:
- **API Gateway**: Primarily billed per API call (first 2M free, then $3.00 per M calls) and network egress.
- **Cloud Pub/Sub**: Billed per TiB of throughput (first 10GiB free, then $40 per TiB). Minimal storage costs for messages.
- **Eventarc**: Billed per event delivery.
- **Cloud Tasks**: Billed per task operation (first 1M free, then $0.40 per M operations).

**Estimated Cost for This Project**:
| Resource | Configuration | Monthly Cost | Notes |
|----------|---------------|--------------|-------|
| API Gateway | 10M API calls | £2,000 - £3,000 | API Management & Exposure |
| Cloud Pub/Sub | 50M messages, 5TiB throughput | £1,000 - £1,500 | Event Streaming, Notifications |
| Eventarc | 50M event deliveries | £100 - £200 | Event Routing |
| Cloud Tasks | 100M task operations | £100 - £300 | Reliable Reminders |
| **Total** | | **£3,000 - £5,000** | (Estimates include network egress) |

**Google Cloud Architecture Framework Assessment**:

| Pillar | Rating | Notes |
|--------|--------|-------|
| **Reliability** | ⭐⭐⭐⭐⭐ | Built-in redundancy, automatic retries, message durability for Pub/Sub and Cloud Tasks. API Gateway provides fault isolation. |
| **Performance Optimization** | ⭐⭐⭐⭐⭐ | Low-latency API routing, high-throughput messaging, efficient event delivery. |
| **Operational Excellence** | ⭐⭐⭐⭐⭐ | Fully managed services reduce operational burden; integrated with Cloud Monitoring and Logging for visibility. |
| **Security, Privacy and Compliance** | ⭐⭐⭐⭐☆ | API Gateway authentication/authorization, IAM integration, VPC Service Controls compatibility. |
| **Cost Optimization** | ⭐⭐⭐⭐☆ | Pay-as-you-go models, with free tiers for initial usage. |
| **Sustainability** | ⭐⭐⭐⭐☆ | Efficient resource utilization due to serverless and managed nature. |

**UK Region Availability**:
- ✅ All services available in europe-west2 (London) and europe-west1 (Belgium).

---

### Category 4: Security

**Requirements Addressed**: NFR-SEC-001 to NFR-SEC-006, NFR-C-001, NFR-C-002, NFR-C-003, Architecture Principle 4 (Security by Design)

**Why This Category**: Security is a non-negotiable, foundational requirement for NHS systems handling patient data. A comprehensive suite of security services is needed to implement a zero-trust model, protect against threats, manage sensitive data, ensure compliance with UK healthcare standards, and maintain strict data sovereignty.

---

#### Recommended: Cloud Armor, Secret Manager, Security Command Center, Cloud KMS, Cloud IAM, VPC Service Controls

**Service Overview**:
- **Full Name**: Cloud Armor, Secret Manager, Security Command Center (SCC), Cloud Key Management Service (KMS), Cloud Identity and Access Management (IAM), Virtual Private Cloud (VPC) Service Controls
- **Category**: Security, Identity, & Compliance
- **Documentation**: [https://cloud.google.com/armor/docs](https://cloud.google.com/armor/docs), [https://cloud.google.com/secret-manager/docs](https://cloud.google.com/secret-manager/docs), [https://cloud.google.com/security-command-center/docs](https://cloud.google.com/security-command-center/docs), [https://cloud.google.com/kms/docs](https://cloud.google.com/kms/docs), [https://cloud.google.com/iam/docs](https://cloud.google.com/iam/docs), [https://cloud.google.com/vpc-service-controls/docs](https://cloud.google.com/vpc-service-controls/docs)

**Key Features**:
- **Cloud Armor**: Provides Web Application Firewall (WAF) capabilities and DDoS protection at the network edge, safeguarding the web application and APIs (NFR-SEC-005).
- **Secret Manager**: Securely stores, manages, and automatically rotates sensitive data like API keys, database credentials, and certificates (NFR-SEC-004). Integrates with Cloud Run/Functions for secure secret access.
- **Security Command Center (SCC) Premium**: Centralized vulnerability management, threat detection, and compliance monitoring (NFR-SEC-005, NFR-SEC-006). Essential for continuously assessing security posture, identifying misconfigurations, and reporting against benchmarks like CIS and NIST.
- **Cloud Key Management Service (KMS)**: Manages cryptographic keys for data encryption. Used to encrypt data at rest (NFR-SEC-003) in Cloud SQL, Cloud Storage, and other services. Offers software, hardware (Cloud HSM), and external key options for varying levels of control.
- **Cloud Identity and Access Management (IAM)**: Provides fine-grained access control (NFR-SEC-002) for all Google Cloud resources. Integrates with NHS Login for citizen authentication (NFR-SEC-001) and uses service accounts for secure service-to-service communication.
- **VPC Service Controls**: Creates security perimeters around Google Cloud resources to mitigate data exfiltration risks (`NFR-C-001`). Crucial for protecting sensitive patient data (`OFFICIAL` classification) and enforcing data residency requirements.

**Pricing Model**:
- **Cloud Armor**: Billed per policy and per GB processed. DDoS protection and WAF rules are additional.
- **Secret Manager**: Billed per active secret version per location ($0.06/month) and per 10,000 access operations ($0.03/10k ops). Free tier includes 6 active versions and 10k ops.
- **SCC Premium**: Available as a fixed annual subscription (min $15,000/year) or pay-as-you-go based on vCPU-hours of monitored resources. This project requires SCC Premium for comprehensive compliance and threat detection.
- **Cloud KMS**: Billed per active key version ($0.06/month for software keys, $1.00/month for HSM keys) and per 10,000 key operations ($0.03/10k ops). Free tier included.
- **Cloud IAM**: Generally no direct cost, usage is covered by other services.
- **VPC Service Controls**: Generally no direct cost, usage is covered by other services.

**Estimated Cost for This Project**:
| Resource | Configuration | Monthly Cost | Notes |
|----------|---------------|--------------|-------|
| Cloud Armor | Standard features, 10M requests | £750 - £1,000 | WAF & DDoS Protection |
| Secret Manager | 50 active secrets, 1M ops | £500 - £750 | Credentials & Keys Management |
| SCC Premium | Pay-as-you-go (based on vCPU-hours) | £1,250 - £2,000 | Compliance & Threat Detection |
| Cloud KMS | 100 software keys, 10M operations | £50 - £150 | Key Management for Encryption |
| **Total** | | **£2,500 - £4,000** | (Excludes potential costs for advanced networking components if needed for VPC SC) |

**Google Cloud Architecture Framework Assessment**:

| Pillar | Rating | Notes |
|--------|--------|-------|
| **Security, Privacy and Compliance** | ⭐⭐⭐⭐⭐ | Comprehensive defense-in-depth, zero-trust capabilities. Strong alignment with NCSC Cloud Security Principles, UK GDPR, and other NHS standards. |
| **Operational Excellence** | ⭐⭐⭐⭐⭐ | Centralized visibility and automated security posture management with SCC. Integrated logging and monitoring. |
| **Reliability** | ⭐⭐⭐⭐☆ | Security controls contribute to overall system resilience by preventing attacks and unauthorized access. |
| **Cost Optimization** | ⭐⭐⭐☆ | SCC Premium is a significant cost, but essential for compliance and robust security posture. KMS and Secret Manager are cost-effective for their value. |
| **Sustainability** | ⭐⭐⭐⭐☆ | Efficient security services with minimal overhead. |

**UK Region Availability**:
- ✅ All services available in europe-west2 (London).
- ✅ VPC Service Controls can enforce data residency for protected resources within europe-west2.

---

### Category 5: AI/ML

**Requirements Addressed**: BR-006 (Data-Driven Capacity Planning), BR-002 (Reduce DNA Rates)

**Why This Category**: Leveraging AI/ML can enhance the service's intelligence, enabling proactive reduction of DNA rates through predictive analytics and improving operational efficiency via data-driven capacity planning.

---

#### Recommended: Vertex AI

**Service Overview**:
- **Full Name**: Vertex AI
- **Category**: Artificial Intelligence & Machine Learning
- **Documentation**: [https://cloud.google.com/vertex-ai/docs](https://cloud.google.com/vertex-ai/docs)

**Key Features**:
- **Unified ML Platform**: Provides a complete suite of tools for building, deploying, and managing ML models. This would support `BR-006` for building models for demand forecasting and trend analysis.
- **Managed Datasets & Model Training**: Facilitates data preparation and model training at scale.
- **Prediction Services**: Deploy models as endpoints for real-time predictions. This could be used for `BR-002` to predict DNA likelihood and trigger proactive interventions (e.g., additional reminders, rebooking prompts).
- **MLOps Capabilities**: Includes tools for model monitoring, versioning, and continuous integration/delivery of ML pipelines.

**Pricing Model**:
- **Vertex AI**: Billed for compute (training, prediction), data storage, and API usage (e.g., Generative AI models per character). Costs vary significantly based on model complexity, data volume, and usage. Free tier and $300 credits are available for new users.

**Estimated Cost for This Project**:
| Resource | Configuration | Monthly Cost | Notes |
|----------|---------------|--------------|-------|
| Vertex AI | Training (100 hours/month), Prediction (5M requests/month) | £500 - £1,500 | Capacity Planning, Predictive DNA |
| **Total** | | **£500 - £1,500** | (Initial phase, can grow with usage) |

**Google Cloud Architecture Framework Assessment**:

| Pillar | Rating | Notes |
|--------|--------|-------|
| **Operational Excellence** | ⭐⭐⭐⭐⭐ | Managed platform for MLOps, simplifying deployment and monitoring of ML models. |
| **Cost Optimization** | ⭐⭐⭐⭐☆ | Pay-as-you-go, with options for Spot VMs for training. |
| **Performance Optimization** | ⭐⭐⭐⭐⭐ | Optimized for ML workloads, scalable prediction endpoints. |
| **Reliability** | ⭐⭐⭐⭐☆ | High availability for deployed models and robust infrastructure for training. |
| **Sustainability** | ⭐⭐⭐⭐☆ | Efficient use of compute resources for ML workloads. |

**UK Region Availability**:
- ✅ Available in europe-west2 (London).

---

## Architecture Pattern

### Recommended Google Cloud Reference Architecture

**Pattern Name**: Serverless Web Application with Event-Driven Microservices

**Google Cloud Architecture Center Reference**: [https://cloud.google.com/architecture/serverless-web-app-architecture](https://cloud.google.com/architecture/serverless-web-app-architecture)

**Pattern Description**:
This pattern leverages Google Cloud's serverless offerings to build a highly scalable, resilient, and cost-effective web application. The citizen-facing and staff interfaces would be hosted via Cloud Storage and served through Cloud CDN for low-latency global access, ensuring NFR-P-001 is met. The backend APIs, implementing all functional requirements (FR-xxx), would be developed as microservices deployed on Cloud Run, providing automatic scaling (NFR-S-001) and efficient resource utilization. Cloud Functions would handle event-driven logic, such as processing appointment reminders (FR-005) and integrations.

Apigee acts as the central API Management layer, securing and exposing APIs to external NHS systems (INT-001, INT-002, INT-003, INT-005) and internal components. Asynchronous communication for notifications (`INT-004`) and analytics (`INT-006`) is handled by Cloud Pub/Sub, facilitating loose coupling (Architecture Principle 9) and resilience. Cloud Tasks ensures reliable delivery of delayed or scheduled tasks, supporting the fault tolerance NFR-A-003.

For data, Cloud SQL (PostgreSQL) is chosen for core transactional data, Cloud Firestore for flexible user profiles, and Cloud Storage for immutable audit logs (NFR-C-003). Memorystore (Redis) provides caching to further boost performance (NFR-P-001, NFR-P-002).

Security is a paramount concern, addressed through Cloud Armor (WAF/DDoS), Secret Manager (NFR-SEC-004), Cloud KMS (NFR-SEC-003), and granular access control with Cloud IAM. Crucially, VPC Service Controls (`NFR-C-001`) will establish security perimeters to protect sensitive patient data and prevent exfiltration, meeting `OFFICIAL` classification requirements. Security Command Center (NFR-SEC-005, NFR-SEC-006) provides continuous monitoring and compliance reporting.

Finally, Vertex AI is integrated to provide AI/ML capabilities for `BR-006` (capacity planning) and potentially `BR-002` (predictive DNA), transforming raw data into actionable insights. This comprehensive architecture ensures the service is secure, scalable, compliant, and intelligent.

### Architecture Diagram

```mermaid
graph TD
    subgraph "Users (Web & Mobile)"
        direction LR
        User[Citizen / Staff]
    end

    subgraph "Google Cloud (europe-west2 London)"
        subgraph "Edge Layer"
            CDN[Cloud CDN]
            LB[Cloud Load Balancing] --> Armor[Cloud Armor: WAF/DDoS]
            LB -- Directs --> APIGateway[API Gateway: API Management]
        end

        subgraph "Application Layer"
            APIGateway -- API Calls --> CloudRun[Cloud Run: API Backend Microservices]
            CloudRun --> CloudSQL[Cloud SQL: Appointments DB]
            CloudRun --> Firestore[Firestore: User Profiles]
            CloudRun --> GCS_Audit[Cloud Storage: Audit Logs]
            
            PubSub[Cloud Pub/Sub: Event Bus] --> Eventarc[Eventarc: Event Routing]
            Eventarc --> CloudFunctions[Cloud Functions: Event Processing / Reminders]
            CloudFunctions --> CloudTasks[Cloud Tasks: Scheduled Reminders]
            CloudFunctions --> VertexAI[Vertex AI: ML Models]
        end

        subgraph "Data Layer"
            CloudSQL(fa:fa-database Appointments)
            Firestore(fa:fa-database User Profiles)
            GCS_Audit(fa:fa-archive Audit Logs)
            Memorystore[Memorystore: Caching]
            BigQuery[BigQuery: Analytics/Capacity Planning]
        end

        subgraph "Security & Management"
            IAM[Cloud IAM: Auth/AuthZ]
            SecretManager[Secret Manager: Secrets]
            KMS[Cloud KMS: Encryption Keys]
            VPCSC[VPC Service Controls: Perimeter]
            SCC[Security Command Center: Posture/Threats/Compliance]
            CloudMonitoring[Cloud Monitoring: Metrics]
            CloudLogging[Cloud Logging: Logs]
        end
    end

    subgraph "External Systems"
        Ext_PDS[NHS Spine PDS]
        Ext_GP[GP Systems]
        Ext_PAS[Hospital PAS]
        Ext_Login[NHS Login]
        Ext_Notify[NHS Notify]
    end

    User --> CDN
    CDN --> LB
    CloudRun <--> SecretManager
    CloudFunctions <--> SecretManager
    CloudRun <--> CloudSQL
    CloudRun <--> Firestore
    CloudRun <--> Memorystore
    CloudRun <--> PubSub
    CloudRun <--> Ext_PDS
    CloudRun <--> Ext_Login
    CloudRun <--> Ext_GP
    CloudRun <--> Ext_PAS
    CloudFunctions <--> Ext_PDS
    CloudFunctions <--> Ext_Login
    CloudFunctions <--> Ext_Notify
    CloudFunctions <--> PubSub
    CloudFunctions <--> BigQuery
    CloudFunctions <--> VertexAI
    BigQuery <--> GCS_Audit
    VPCSC -- Protects --> CloudRun
    VPCSC -- Protects --> CloudSQL
    VPCSC -- Protects --> Firestore
    VPCSC -- Protects --> GCS_Audit
    VPCSC -- Protects --> Memorystore
    VPCSC -- Protects --> BigQuery
    VPCSC -- Protects --> SecretManager
    VPCSC -- Protects --> KMS
    IAM -- Governs --> (All Google Cloud Resources)
    SCC -- Monitors --> (All Google Cloud Resources)
    CloudMonitoring -- Collects from --> (All Google Cloud Resources)
    CloudLogging -- Collects from --> (All Google Cloud Resources)
```

### Component Mapping

| Component | Google Cloud Service | Purpose | Configuration |
|-----------|---------------------|---------|---------------|
| Web Frontend | Cloud Storage + Cloud CDN | Host static citizen and staff web UIs. | Low-latency delivery, geo-caching |
| Global Entry | Cloud Load Balancing + Cloud Armor | Global traffic distribution, WAF, DDoS protection. | HTTPS Load Balancer, Managed WAF rules |
| API Gateway | API Gateway | Secure, publish, and manage all external and internal APIs. | OpenAPI spec, policy enforcement, traffic control |
| Backend API | Cloud Run | Serverless containers for microservices, implementing core logic. | Autoscaling, containerized deployment, private networking |
| Event Processing | Cloud Functions | Serverless functions for event-driven tasks and specific integrations. | Event-triggered, Pub/Sub integration |
| Relational DB | Cloud SQL (PostgreSQL) | Primary database for transactional appointment and organisation data. | High Availability, automated backups, encryption |
| NoSQL DB | Cloud Firestore | Flexible document database for user profiles and preferences. | Serverless, real-time sync, scalable |
| Object Storage | Cloud Storage | Durable object storage for audit logs (immutable), static assets, backups. | Object Retention Lock, lifecycle management |
| Caching | Memorystore (Redis) | In-memory data store for high-speed caching of frequently accessed data. | High performance, managed service |
| Data Warehouse | BigQuery | Analytics and data warehousing for capacity planning and reporting. | Serverless, SQL queries, scalable |
| Asynchronous Messaging | Cloud Pub/Sub | Event bus for decoupling services, notifications, and event streaming. | Push/Pull subscriptions, message durability |
| Event Orchestration | Eventarc | Routes events from Google Cloud sources to various destinations. | Event-driven architecture enablement |
| Task Queuing | Cloud Tasks | Manages reliable asynchronous task execution for reminders and background jobs. | Configurable retries, scheduling |
| Identity & Access | Cloud IAM | Fine-grained access control for all resources, integrates with NHS Login. | Least privilege, conditional access |
| Secrets Mgmt | Secret Manager | Securely stores, manages, and rotates sensitive credentials. | Automatic rotation, versioning |
| Key Mgmt | Cloud KMS | Centralized management of cryptographic keys for encryption. | HSM support, CMEK integration |
| Network Perimeter | VPC Service Controls | Creates security perimeters to prevent data exfiltration. | Data residency, access restrictions |
| Security Monitoring | Security Command Center | Unified security posture management, threat detection, compliance. | Premium Tier, CIS benchmarks, NCSC alignment |
| Monitoring | Cloud Monitoring | Collects metrics, logs, and events across all services. | Custom dashboards, alerting |
| Logging | Cloud Logging | Centralized log management for operational visibility and audit. | Structured logging, export to BigQuery/Cloud Storage |
| AI/ML Platform | Vertex AI | Unified platform for building, deploying, and managing ML models. | Demand forecasting, predictive analytics |

---

**Generated by**: ArcKit `/arckit:gcp-research` agent
**Generated on**: 2026-02-10
**ArcKit Version**: 2.2.0
**Project**: NHS Digital Appointment Booking Service (Project 001)
**AI Model**: gemini-1.5-flash-001
