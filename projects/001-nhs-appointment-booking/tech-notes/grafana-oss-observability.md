# Tech Note: Grafana OSS Observability Stack (Prometheus + Loki + Tempo + Grafana)

## Document Control

| Field | Value |
|-------|-------|
| **Document ID** | grafana-oss-observability |
| **Document Type** | Tech Note |
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
| **Distribution** | Project Team, Development Team, Operations |
| **Source Research** | ARC-001-RSCH-v1.0 |

## Revision History

| Version | Date | Author | Changes | Approved By | Approval Date |
|---------|------|--------|---------|-------------|---------------|
| 1.0 | 2026-02-20 | ArcKit AI | Initial creation from `/arckit:research` agent | PENDING | PENDING |

---

## Summary

The Grafana OSS observability stack is the de facto standard for Kubernetes-native observability, combining Grafana (dashboards — Apache 2.0), Prometheus (metrics — Apache 2.0, CNCF Graduated), Loki (log aggregation — AGPL-3.0), Tempo (distributed tracing — Apache 2.0), and AlertManager (alerting — Apache 2.0). Deployed via the kube-prometheus-stack Helm chart on AWS EKS, this stack provides full observability coverage for NFR-M-001 (structured logging, Prometheus metrics, distributed tracing, SLO alerting) at significantly lower cost than commercial alternatives (Datadog: £454,500 vs Grafana OSS: £226,000 over 3 years at NHS appointment booking scale). The stack is widely familiar to NHS engineering teams.

## Key Findings

- **Helm Chart Deployment**: kube-prometheus-stack Helm chart deploys Prometheus, AlertManager, Grafana, and node exporters in a single command. Production-ready in hours rather than weeks.
- **Prometheus RED Metrics**: Prometheus natively instruments HTTP endpoints (Rate, Errors, Duration) for all EKS microservices. NFR-M-001 RED metrics requirement met without custom instrumentation. Prometheus scrapes metrics from EKS pods via ServiceMonitor custom resources.
- **Loki Log Aggregation**: Loki aggregates structured JSON logs from all pods. Log labels from Kubernetes metadata (namespace, pod, container) enable fast log searching. NHS Number and patient data must be EXCLUDED from log fields — implemented via log transformation rules. Loki stores logs on S3 (aws eu-west-2) for cost-effective long-term retention. 90-day operational log retention (Principle 5) implemented via S3 lifecycle.
- **Loki Licence Consideration**: Loki is AGPL-3.0. Self-hosted use for an NHS commercial service requires legal review. AGPL requires making source code available if the software is modified and distributed, but self-hosted deployment without external distribution is generally considered outside AGPL scope. Recommend legal team review before deployment in production NHS service. Grafana Enterprise licence available as alternative if AGPL problematic.
- **Tempo Distributed Tracing**: Tempo stores distributed traces using correlation IDs generated at API Gateway entry point and propagated across all microservice calls (W3C TraceContext standard). Traces enable end-to-end request journey visibility for appointment booking flows — critical for debugging NHS Spine integration latency.
- **AlertManager SLO Alerting**: SLO-based alerting for 99.9% availability (NFR-A-001) configured using Prometheus recording rules. Alerts route to PagerDuty/Slack/email with runbook links (NFR-M-001). Runbooks stored in GitHub and linked from AlertManager annotations.
- **AWS CloudWatch Supplement**: CloudWatch required alongside Grafana OSS for AWS-native metrics (RDS Performance Insights, EKS cluster metrics, SQS queue depth, Lambda metrics). CloudWatch Metrics: ~£12,000/year at 300 custom metrics. Grafana CloudWatch datasource bridges CloudWatch metrics into Grafana dashboards for unified view.
- **8-Year Audit Log Architecture** (NFR-C-003): AuditLog records are NOT stored in Grafana/Loki. They are written to Aurora PostgreSQL, then asynchronously published to SQS, consumed by a Lambda function that writes to S3 with Object Lock WORM (Write Once Read Many) enabled. SHA-256 hash stored per record for tamper-evidence. S3 Glacier Deep Archive after 90 days (~£0.00099/GB-month) for cost-effective 8-year retention.
- **Business Metrics Dashboards**: Grafana dashboards for BR-006 (capacity planning): booking volumes, DNA rates, slot utilisation, reminder delivery rates — all driven by custom application metrics exposed via Prometheus.
- **Cost Comparison**: Grafana OSS £226,000 vs Datadog £454,500 over 3 years — saving £228,500. Datadog log management costs escalate significantly (£110,000/year Year 3) as NHS log volume grows.
- **PII Redaction**: Critical for NHS — Prometheus metric labels and Loki log fields must NEVER contain NHS Number, Date of Birth, or patient contact details. Application layer responsible for correlation ID-based tracing without embedding PII in telemetry.

## Relevance to Projects

- **ARC-001 NHS Appointment Booking Service**: Recommended observability stack. Deployed on existing EKS cluster. Saves £228,500 over 3 years vs Datadog. Meets all NFR-M-001 requirements. AGPL legal review required for Loki before production deployment.

---

**Generated by**: ArcKit `/arckit:research` agent
**Generated on**: 2026-02-20
**ArcKit Version**: 2.2.0
**Project**: NHS Digital Appointment Booking Service (Project 001)
**Model**: Claude Sonnet 4.6
