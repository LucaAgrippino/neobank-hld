# NeoBank — High-Level Design

## Project Overview

System architecture for **Project Digital Leap**: a modernisation of NeoBank's digital banking services. The project builds a modern digital services layer on top of an existing IBM iSeries Mainframe, enabling mobile banking, fund transfers, AI-powered financial advice, and Open Banking APIs — without replacing the legacy system.

This document was produced as the final project for a professional Software Architecture course (Global Dev Experts, instructor: Lee Blum). The project brief, architecture presentation, and all UML diagrams are included in this repository.

## Architecture Highlights

- **Hybrid deployment model** — on-premises transactional processing alongside cloud-hosted analytical and API services, connected via encrypted VPN tunnel
- **CQRS pattern** — reads served from Redis/PostgreSQL in milliseconds, writes routed through the Mainframe as system of record
- **Resilience patterns** — Circuit Breaker, Store and Forward (Kafka), Retry with Idempotency, Saga (orchestration) for handling Mainframe unavailability
- **Security** — STRIDE threat model, OAuth 2.0 with MFA (Keycloak), GDPR-compliant data anonymisation boundary, DMZ pattern
- **Scalability** — 100K users at launch, scaling to 1M within 3 years, with rate limiting to protect the Mainframe from digital traffic

## Technology Stack

| Component         | Technology     | Justification                                      |
|-------------------|----------------|-----------------------------------------------------|
| Message Broker    | Apache Kafka   | Message persistence, replay capability, zero data loss |
| Cache             | Redis          | TTL support, disk persistence, sub-millisecond reads |
| Relational DB     | PostgreSQL     | Strict ACID compliance for financial data            |
| Data Warehouse    | Amazon Redshift| Cloud-managed, EU region, columnar storage           |
| CDC               | Debezium       | Passive WAL reading, zero Mainframe load             |
| Identity Provider | Keycloak       | Free at scale, credentials under bank control        |
| Orchestration     | Kubernetes     | Auto-restart, rolling deployments, horizontal scaling|
| CI/CD             | GitHub Actions | Cloud-agnostic, hybrid deployment support            |

## Key Design Patterns

**Structural:** Client-Server, Backend for Frontend (BFF), API Gateway + Microservices

**Data:** CQRS, Cache-Aside, Materialised View, Change Data Capture (CDC), Partial Event Sourcing

**Resilience:** Circuit Breaker, Store and Forward, Retry with Idempotency, Saga (Orchestration)

**Messaging:** Outbox, Inbox, Publish-Subscribe (Broker Topology)

**Deployment:** Lambda Architecture, Throttling and Rate Limiting

## UML Diagrams

All diagrams were created with Visual Paradigm. The source project file is included in the `design/` folder.

| Diagram | Description |
|---------|-------------|
| **UCD-01/02/03** | Use case diagrams for customers, fintechs, and internal operations |
| **System Deployment** | Two-environment topology (cloud + on-premises) with VPN boundary |
| **COMP-01** | Full system component diagram with all interfaces |
| **COMP-02** | BFF internal structure (Rate Limiter, Circuit Breaker, CQRS, Cache-Aside) |
| **COMP-03** | Integration Layer internals (Store and Forward, Outbox, Inbox, Retry) |
| **CD-01** | Domain model with abstract classes and LSP-compliant design |
| **SM-01** | Transaction lifecycle state machine (7 states, including compensation) |
| **ACT-01** | Fund transfer activity diagram across 5 swim lanes |
| **SD-01 to SD-07** | Sequence diagrams for all critical system flows |
| **ARCH-01** | Lambda Architecture (speed layer + batch layer + serving layer) |

## Repository Structure

```
neobank-hld/
├── docs/
│   ├── neo_bank_project_description.docx  # Project brief and context
│   ├── NeoBank_HLD.docx                   # HLD source document
│   └── NeoBank_HLD.pdf                    # HLD final version (44 pages)
├── diagrams/
│   ├── use-cases/                         # UCD-01, UCD-02, UCD-03
│   ├── deployment/                        # System deployment diagram
│   ├── components/                        # COMP-01, COMP-02, COMP-03
│   ├── domain-model/                      # CD-01 domain model
│   ├── state-machine/                     # SM-01 transaction lifecycle
│   ├── sequence-diagrams/                 # SD-01 through SD-07
│   ├── activity-diagrams/                 # ACT-01 fund transfer
│   └── architecture/                      # ARCH-01 lambda architecture
├── design/
│   └── neobank_design.vpp                 # Visual Paradigm source project
└── presentation/
    └── NeoBank_Presentation_v2_5.pptx     # Architecture presentation
```

## Non-Functional Requirements

| Requirement | Target |
|-------------|--------|
| Availability | 99.999% (~5 min downtime/year) |
| Data Loss | RPO = 0 (zero data loss) |
| Latency | Millisecond-level for all customer-facing APIs |
| Scale | 100K → 1M users in 3 years |
| Compliance | Full GDPR including Right to be Forgotten |

## Author

**Luca Agrippino** — Embedded Software Engineer
[LinkedIn](https://www.linkedin.com/in/luca-agrippino)
