# Architectural Principles Knowledge Base
> Version: 1.0.0 | Owner: SFI Agentic CoE | Status: Active | Last Reviewed: 2026-08-24

This document establishes the foundational architectural principles that govern all design and development decisions across the company. These principles apply to frontend, backend, data, platform, and integration concerns. They are not guidelines — they are the technical constitution every team builds within.

## Table of Contents

1. [Tech Stack](#1-tech-stack)
2. [Identity Provider (IdP)](#2-identity-provider-idp)
3. [Application Architecture](#3-application-architecture)
   - 3.1 [Domain-Driven Design](#31-domain-driven-design-ddd)
   - 3.2 [Microfrontends](#32-microfrontends)
   - 3.3 [Microservices](#33-microservices)
   - 3.4 [Event Sourcing & CQRS](#34-event-sourcing--cqrs-es-cqrs)
   - 3.5 [Quality Assurance](#35-quality-assurance)
   - 3.6 [DevSecOps & Platform Engineering](#36-devsecops--platform-engineering)
4. [Infrastructure Architecture](#4-infrastructure-architecture)
5. [Integration Architecture](#5-integration-architecture)
6. [API Design Standards](#6-api-design-standards)
7. [Data Architecture](#7-data-architecture)
8. [Security Architecture](#8-security-architecture)
9. [Regulatory & Compliance](#9-regulatory--compliance)
10. [Logging, Monitoring & Observability](#10-logging-monitoring--observability)
11. [Resilience Engineering](#11-resilience-engineering)
12. [Operational Excellence & SLAs](#12-operational-excellence--slas)
13. [Developer Experience (DX)](#13-developer-experience-dx)
14. [Cost Engineering](#14-cost-engineering)
15. [AI/ML Architecture](#15-aiml-architecture)
16. [Documentation Standards](#16-documentation-standards)

---

## 1. Tech Stack

### Principles
- **Standardise ruthlessly, innovate deliberately.** A lean, well-chosen stack reduces cognitive overhead, simplifies hiring, and improves maintainability. New technologies must earn their place.
- **Prefer managed services over self-managed.** Unless there is a strong technical or commercial reason, use managed cloud services to reduce operational burden.
- **Language consolidation per tier.** Avoid polyglot sprawl. Define primary languages per tier and require explicit justification for exceptions.

### Baseline Decisions
| Tier | Primary Choice | Notes |
|---|---|---|
| Frontend | TypeScript + React | Next.js for SSR/SSG where needed |
| Backend | TypeScript (Node.js) / Python | Node for APIs, Python for data/ML workloads |
| Mobile | React Native | Code sharing with web where applicable |
| Data / Streaming | Python, SQL, Apache Kafka | Kafka for event streaming; dbt for transformations |
| Search | Elasticsearch / OpenSearch | Full-text search and log analytics |
| Vector / AI | pgvector / Pinecone / Weaviate | Semantic search and RAG use cases |
| Infrastructure | Terraform, Helm, Kubernetes | |
| Databases | PostgreSQL (relational), DynamoDB/MongoDB (document), Redis (cache) | Choice driven by domain data model |
| Testing | Jest (JS/TS), Pytest (Python), Playwright (E2E), k6 (load) | Consistent per language tier |
| Observability | OpenTelemetry + Grafana stack / Datadog | Vendor-agnostic instrumentation layer |

### Technology Radar
- A Technology Radar is maintained and reviewed every 6 months (Adopt / Trial / Assess / Hold).
- Any technology not on the radar requires an RFC (Request for Comment) and CTO sign-off before use in production.
- Technologies on Hold must not be introduced into new projects; existing usages must have a migration plan.

### Guardrails
- All new services must be approved against the standard stack before introducing a new language or framework.
- No runtime dependency on end-of-life or unmaintained libraries.
- Dependency versions must be pinned and managed via a lock file.
- Open source libraries with no active maintainers or < 1,000 GitHub stars require explicit risk assessment before adoption.

---

## 2. Identity Provider (IdP)

### Principles
- **Centralise identity.** All authentication and authorisation flows must go through a single, authoritative IdP. No service manages its own user identities.
- **Standards-first.** Use industry-standard protocols: OAuth 2.0, OpenID Connect (OIDC), and SAML 2.0 where enterprise federation is required.
- **Zero-trust identity model.** Identity is verified at every service boundary — no implicit trust based on network location.

### Baseline Decisions
- **Primary IdP:** [e.g., Auth0 / AWS Cognito / Okta / Azure Entra ID] — single source of truth for all identities.
- **Token format:** JWT (signed, short-lived access tokens + refresh token rotation).
- **On-Behalf-Of (OBO):** Used for Microsoft-ecosystem integrations (SharePoint, ADO, Teams) where delegated user context must be preserved through service chains.
- **Machine-to-machine (M2M):** Client credentials flow for service-to-service authentication, never shared secrets.

### Guardrails
- Tokens must have a maximum TTL of 15 minutes for access tokens; refresh tokens must support rotation.
- No hardcoded credentials anywhere in code, config, or infrastructure — all secrets via a secrets manager.
- MFA enforced for all human identities accessing production systems.
- RBAC (Role-Based Access Control) defined and managed centrally in the IdP; services consume roles/scopes, not user attributes.

---

## 3. Application Architecture

### 3.1 Domain-Driven Design (DDD)

#### Principles
- **Business domains are the unit of organisation.** The software structure mirrors the business structure. Teams own domains, not technical layers.
- **Bounded contexts define service boundaries.** Each domain has a clearly defined bounded context with an explicit ubiquitous language. No context bleeds into another.
- **Every domain exposes two contracts:**
  - **API contract** — synchronous interface for query and command operations (OpenAPI/gRPC spec).
  - **Domain events** — asynchronous notifications of state changes that other domains may consume.
- **Anti-corruption layers (ACL)** are mandatory at every domain boundary to prevent one domain's model from polluting another.

#### Domain Decomposition Process
1. Event Storming to identify domain events, commands, aggregates, and bounded contexts.
2. Define aggregate roots and their invariants.
3. Map bounded contexts to teams and services.
4. Publish domain event schema to the schema registry before implementation.

#### Guardrails
- No direct database access across domain boundaries — ever.
- Shared kernel usage requires explicit cross-team agreement and versioning.
- Domain events must be idempotent and carry enough context to be self-describing.

#### Context Map
A Context Map must be maintained as a living document showing how bounded contexts relate to each other. Relationship types to be explicitly declared:

| Relationship | Meaning |
|---|---|
| Partnership | Two teams collaborate closely; changes coordinated |
| Shared Kernel | Shared subset of the domain model; changes require joint agreement |
| Customer/Supplier | Downstream depends on upstream; upstream prioritises downstream needs |
| Conformist | Downstream conforms to upstream's model with no influence |
| Anti-Corruption Layer (ACL) | Downstream translates upstream's model to protect its own |
| Open Host Service | Upstream publishes a well-defined protocol for multiple consumers |
| Published Language | Shared, well-documented domain language (e.g., CloudEvents schema) |

- Every new integration between bounded contexts must reference the Context Map and declare its relationship type before implementation.

---

### 3.2 Microfrontends

#### Principles
- **Frontend autonomy mirrors backend autonomy.** Each domain team owns its UI slice end-to-end — from API to screen.
- **Composition at runtime, not build time.** Microfrontends are composed dynamically to allow independent deployment without a coordinated release.
- **Shared shell, independent modules.** A thin host application (shell) handles layout, navigation, and shared concerns (auth, theming). Domain modules are independently deployable.

#### Baseline Decisions
- **Module Federation** (Webpack 5 / Vite) as the composition mechanism.
- **Design System:** Single shared component library (e.g., Storybook-driven) consumed by all microfrontends — teams contribute to it but do not fork it.
- **Routing:** Shell owns top-level routing; each microfrontend owns its internal routing.
- **State:** No shared global state across microfrontend boundaries. Cross-domain communication via custom browser events or a lightweight event bus.

#### Guardrails
- Each microfrontend must be independently deployable without requiring changes to the shell or other modules.
- Shared dependencies (React, design system) must be singleton-enforced to avoid version conflicts.
- Performance budget enforced per microfrontend: initial bundle < 200KB gzipped.
- Accessibility (WCAG 2.1 AA) is a non-negotiable requirement, not an afterthought.

---

### 3.3 Microservices

#### Principles
- **Single responsibility per service.** Each microservice owns one bounded context. It is responsible for its own data, its own API, and its own lifecycle.
- **Design for failure.** Every service assumes its dependencies will fail and implements appropriate fallback behaviour.
- **Loose coupling, high cohesion.** Services communicate through well-defined contracts. Internal implementation details are never exposed.
- **Size by team, not by lines of code.** A service should be small enough to be understood and owned by a single team, but not so small it creates unnecessary operational overhead (avoid nanoservice anti-pattern).

#### Baseline Decisions
- **Runtime:** Containerised (Docker), orchestrated via Kubernetes.
- **Service mesh:** Istio or Linkerd for mTLS, observability, and traffic management between services.
- **API style:** REST (OpenAPI 3.x) for external-facing; gRPC for high-throughput internal service-to-service.
- **Health checks:** Every service exposes `/health/live` and `/health/ready` endpoints.

#### Guardrails
- No synchronous circular dependencies between services.
- Services must be stateless — session state lives in a distributed store (Redis), not in-process.
- Database-per-service is the default. Shared databases are prohibited.
- All service interfaces must be versioned from day one.
- **Graceful shutdown:** Every service must handle `SIGTERM` by completing in-flight requests, draining message consumers, and closing connections cleanly before exiting. Kubernetes `preStop` hooks and `terminationGracePeriodSeconds` configured for every deployment.
- **Zero-downtime deployments:** Rolling updates or blue/green deployments only. No in-place restarts that cause dropped connections. Readiness probes must accurately reflect service readiness before traffic is routed.

---

### 3.4 Event Sourcing & CQRS (ES-CQRS)

#### Principles
- **State as a sequence of events.** Rather than storing current state only, the full history of domain events is persisted. Current state is derived by replaying events.
- **Commands and queries are separated.** The write model (commands) and read model (queries) are distinct — optimised independently for their respective concerns.
- **Event store is the system of record.** The event log is append-only, immutable, and the authoritative source of truth.
- **Apply where complexity justifies it.** ES-CQRS adds operational complexity. Apply it to domains with complex business logic, audit requirements, or temporal query needs. Do not apply universally.

#### Baseline Decisions
- **Event store:** Apache Kafka (durable, replayable) or EventStoreDB for domains requiring native event sourcing.
- **Projections:** Materialised views built from event streams, stored in read-optimised stores (e.g., Elasticsearch, PostgreSQL read replicas).
- **Event schema:** CloudEvents specification for envelope; Avro/Protobuf for payload, registered in schema registry.
- **Eventual consistency:** Explicitly acknowledged and documented in domain contracts. Consumers must handle it.

#### Guardrails
- Events are immutable once published — no updates or deletes to the event store.
- Every event must carry: event type, aggregate ID, version, timestamp, correlation ID, and causation ID.
- Schema evolution must follow backward-compatible rules (additive changes only without a major version bump).
- Sagas / process managers handle multi-step business processes spanning multiple aggregates.

---

### 3.5 Quality Assurance

#### Principles
- **Quality is built in, not bolted on.** Testing is a first-class engineering activity, not a phase at the end.
- **Test at the right level.** The testing pyramid guides investment: more unit tests, fewer E2E tests. Heavy E2E test suites are a design smell.
- **Shift left.** Security, performance, and accessibility testing happen during development, not after.

#### Testing Strategy (Testing Pyramid)
```
         ▲
        /E2E\          — Few, critical user journeys only
       /──────\
      /Contract\       — Every service boundary (Pact or similar)
     /──────────\
    / Integration\     — Service + infrastructure integration points
   /──────────────\
  /   Unit Tests   \   — Majority of coverage; fast, isolated, deterministic
 /──────────────────\
```

#### Automation Testing
- **Unit tests:** Mandatory for all business logic. Coverage threshold: 80% minimum, enforced in CI.
- **Integration tests:** Cover service-to-database and service-to-external-dependency interactions.
- **Contract tests:** Every API and domain event contract is covered by consumer-driven contract tests (e.g., Pact). No deploying a breaking contract change without consumer sign-off.
- **E2E tests:** Limited to critical happy-path user journeys. Owned by the team, run in a staging environment.
- **Visual regression:** Automated screenshot comparison for UI components (e.g., Chromatic/Percy).

#### Performance Testing
- Load testing (k6 / Gatling) run against staging before every major release.
- Baseline performance benchmarks defined per service (p95, p99 latency targets).
- Performance budgets enforced for frontend (Lighthouse CI in pipeline).

#### Penetration Testing
- Automated DAST (Dynamic Application Security Testing) in CI using tools such as OWASP ZAP.
- Manual penetration testing by a qualified third party at minimum annually, and before any major public launch.
- Bug bounty programme once product reaches general availability.

---

### 3.6 DevSecOps & Platform Engineering

#### Principles
- **Everything as code.** Pipelines, infrastructure, policies, and configuration are all version-controlled and peer-reviewed.
- **Security is a pipeline citizen.** Security checks run automatically on every commit — not as a separate process.
- **Golden paths, not golden cages.** Platform Engineering provides opinionated, paved paths for common tasks. Teams can deviate with justification but the path of least resistance should be the correct path.
- **Fast feedback loops.** A developer should know within minutes if their change breaks a build, a test, or a security policy.

#### Source Code Management
- **Platform:** GitHub (organisation-level).
- **Branching strategy:** Trunk-based development with short-lived feature branches (< 2 days). No long-lived feature branches.
- **Branch protection:** `main` and `release/*` branches require: passing CI, minimum 1 peer review, no force pushes.
- **Commit conventions:** Conventional Commits enforced via pre-commit hooks (enables automated changelog and semantic versioning).

#### CI/CD Pipeline (per service)
```
Commit → Lint + Format → Unit Tests → Build → SAST → 
Container Scan → Contract Tests → Integration Tests → 
Publish Artifact → Deploy to Staging → DAST → E2E Tests → 
[Manual Gate for Prod] → Deploy to Production
```

#### GitOps
- All deployment state is declared in Git (Argo CD / Flux).
- No manual `kubectl apply` or console-based deployments to any environment.
- Drift detection enabled — any manual change to a cluster is flagged and reverted.
- Promotion between environments (dev → staging → prod) is a pull request, not a script.

#### Code Quality Gates
| Gate | Tool | Threshold |
|---|---|---|
| Coverage | Jest/Pytest + Codecov | ≥ 80% |
| SAST | Semgrep / SonarQube | Zero high/critical findings |
| Dependency vulnerabilities | Snyk / Dependabot | Zero critical CVEs in production |
| Container scanning | Trivy | Zero critical CVEs |
| Secrets scanning | Gitleaks / truffleHog | Zero secrets in commits |
| DAST | OWASP ZAP | Zero high findings |

#### Containerisation
- All services run in Docker containers. No bare-metal or VM-only deployments.
- Base images: official minimal images (e.g., `node:alpine`, `python:slim`). No `latest` tags in production.
- Multi-stage builds mandatory to minimise image size and attack surface.
- Non-root user inside containers — always.
- Images are immutable artefacts. The same image promoted through all environments — no rebuilding per environment.

---

## 4. Infrastructure Architecture

### Principles
- **Infrastructure is code.** Every infrastructure resource is defined, versioned, and deployed via code. Console-created resources are not permitted in production.
- **Immutable infrastructure.** Servers and containers are replaced, not modified in-place. No SSH-and-fix.
- **Cloud-native by default.** Use managed cloud services before building custom solutions. Reserve custom builds for genuine differentiators.
- **Environment parity.** Dev, staging, and production environments are as similar as possible to reduce "works on my machine" failures.

### Infrastructure as Code (IaC)
- **Tool:** Terraform (primary), with Helm for Kubernetes application manifests.
- **Module structure:** Reusable, versioned Terraform modules maintained in a central `infra-modules` repository.
- **State management:** Remote state in S3 (or equivalent) with state locking via DynamoDB.
- **Plan before apply:** `terraform plan` output is reviewed as part of the PR process. No blind `terraform apply`.
- **Policy as code:** OPA (Open Policy Agent) / Sentinel to enforce infrastructure compliance rules (e.g., no public S3 buckets, encryption at rest mandatory).

### Multi-Cloud Strategy
- **Primary cloud:** [AWS / GCP / Azure] — the default for all new workloads.
- **Secondary cloud:** Used for specific capabilities where the primary provider is not optimal, or for resilience/regulatory reasons.
- **Avoid vendor lock-in at the application layer.** Abstract cloud-specific SDKs behind internal interfaces. Applications should not be directly coupled to cloud provider APIs.
- **Kubernetes as the portability layer.** Containerised workloads on Kubernetes can be migrated between clouds without application-level changes.
- **Data portability:** Data formats and storage choices must support export and migration. No proprietary formats that trap data.

### Guardrails
- All infrastructure changes go through the same PR + review process as application code.
- Tagging strategy is mandatory on all cloud resources: `env`, `team`, `service`, `cost-centre`.
- Production environments must be in separate cloud accounts/projects from non-production.
- Network segmentation: production workloads in private subnets; no direct public internet access to compute.

---

## 5. Integration Architecture

### Principles
- **API Gateway as the single entry point.** All external API traffic enters through the API Gateway. No service is directly exposed to the internet.
- **Choose integration style by coupling requirement.** Synchronous for queries and immediate commands; asynchronous for events and eventual-consistency workflows.
- **Contracts first.** Integration contracts (API specs, event schemas) are defined and published before implementation begins.

### External Integration
- **API Gateway:** All external APIs exposed via a managed API Gateway (e.g., AWS API Gateway, Kong, Apigee).
  - Handles: authentication, rate limiting, request/response transformation, routing, TLS termination.
  - No business logic in the gateway — it is infrastructure, not an application layer.
  - All external APIs require an API key or OAuth2 bearer token.

### Internal Integration

#### Synchronous (REST / gRPC)
- Used for: request/response patterns where the caller needs an immediate result.
- All internal REST APIs documented with OpenAPI 3.x specs, committed to the repository.
- gRPC preferred for high-throughput, low-latency internal service communication.
- Circuit breakers mandatory on all synchronous inter-service calls.
- Timeouts and retry policies defined explicitly — no infinite waits.

#### Asynchronous (Domain Events / Message Broker)
- Used for: cross-domain state change notifications, workflows, fan-out patterns.
- **Broker:** Apache Kafka (primary) for durable, replayable event streams.
- Consumers must be idempotent — the same message may be delivered more than once.
- Dead letter queues (DLQ) configured for all consumers to handle poison messages.
- Event schemas registered in a central schema registry (Confluent Schema Registry or AWS Glue).

### Third-Party Integrations
- All third-party integrations wrapped in an ACL (Anti-Corruption Layer) service — never call a third-party API directly from domain logic.
- Credentials for third-party services stored in secrets manager, never in code or environment variables checked into source control.
- Circuit breakers and fallback behaviour defined for every third-party dependency.

---

## 6. API Design Standards

### Principles
- **APIs are products.** They have consumers, versioning, changelogs, and deprecation policies. Design them with the same care as a user-facing product.
- **Consistency reduces cognitive load.** All APIs follow the same conventions — a developer who has used one API knows how to use any other.
- **Design for evolution.** APIs will change. Design them to change without breaking consumers.

### REST API Standards
- Resource-oriented design (nouns, not verbs in URLs).
- HTTP methods used semantically: `GET` (read), `POST` (create), `PUT/PATCH` (update), `DELETE` (remove).
- Plural resource names: `/users`, `/orders`, not `/user`, `/order`.
- Consistent error response envelope:
```json
{
  "error": {
    "code": "RESOURCE_NOT_FOUND",
    "message": "The requested user does not exist.",
    "traceId": "abc-123-def"
  }
}
```
- Pagination on all list endpoints: cursor-based pagination preferred over offset for large datasets.
- HTTP status codes used correctly — never `200 OK` with an error body.

### Versioning
- All APIs versioned from day one: `/api/v1/resource`.
- Breaking changes require a new major version. Non-breaking additions are allowed in the current version.
- Deprecated versions supported for minimum 6 months after successor is GA. Deprecation communicated via `Deprecation` and `Sunset` response headers.

### API Documentation
- OpenAPI 3.x spec is the single source of truth — auto-generated documentation from code annotations is not sufficient; specs must be explicitly maintained.
- Specs committed to the repository alongside the service code.
- Published to an internal developer portal (e.g., Backstage, Stoplight).

### GraphQL (where applicable)
- Used for frontend-facing APIs where flexible querying of complex data graphs is needed.
- Schema-first development: schema defined and agreed before resolver implementation.
- Depth limiting and query complexity analysis to prevent abuse.
- Persisted queries in production to prevent arbitrary query execution.

---

## 7. Data Architecture

### Principles
- **Data ownership follows domain ownership.** Each domain owns its data. No cross-domain direct database access.
- **Data is a product.** Datasets have owners, documentation, SLAs, and quality standards — not just tables.
- **Separate operational and analytical concerns.** Transactional databases are not analytical stores. Data flows from operational systems to the analytical layer through well-defined pipelines.
- **Privacy by design.** Data classification and privacy controls are defined at schema design time, not retrofitted.

### Data Layers
```
Operational Layer        →    Integration Layer     →    Analytical Layer
(Domain databases)            (Event streams /           (Data warehouse /
PostgreSQL, DynamoDB,          CDC pipelines)             Data lake)
Redis                         Apache Kafka               Snowflake / BigQuery /
                              Debezium (CDC)             Redshift + dbt
```

### Data Classification
| Classification | Description | Controls |
|---|---|---|
| Public | Non-sensitive, freely shareable | None required beyond standard access |
| Internal | Business data, not personally sensitive | Access control, audit logging |
| Confidential | Commercially sensitive, PII-adjacent | Encryption at rest + in transit, access review |
| Restricted | PII, financial, health data | Encryption, masking, strict RBAC, audit trail, DLP |

### Data Governance
- **Data catalogue:** All datasets registered in a central data catalogue (e.g., DataHub, Atlan) with owner, schema, classification, and lineage.
- **Data lineage:** End-to-end lineage tracked from source to consumption.
- **Schema management:** Breaking schema changes follow the same review process as API changes.
- **Master Data Management (MDM):** Core entities (Customer, Product, etc.) have a single authoritative source — duplication across domains resolved via domain events, not shared tables.

### Database Guardrails
- No shared databases between services.
- All databases encrypted at rest and in transit.
- Point-in-time recovery enabled on all production databases.
- Regular backup testing — restores are validated, not assumed to work.
- Connection pooling mandatory; direct connection limits to production databases enforced.

### Data Quality
- **Quality dimensions tracked per dataset:** completeness, accuracy, consistency, timeliness, uniqueness.
- Data quality checks run as part of ingestion pipelines — bad records quarantined to a dead letter store, not silently dropped or passed through.
- **SLAs on data freshness** defined for all analytical datasets consumed by dashboards or ML models.
- Data quality dashboards visible to data owners; degradation triggers alerts same as service incidents.

### Real-Time & Streaming Data
- Streaming pipelines (Kafka Streams / Apache Flink / Spark Structured Streaming) used where low-latency data processing is required.
- Batch and streaming logic separated — do not bolt streaming onto a batch pipeline.
- Watermarking and windowing strategies explicitly defined for all streaming aggregations.
- Exactly-once semantics used where data correctness is critical; at-least-once with idempotent consumers for performance-sensitive paths.

---

## 8. Security Architecture

### Principles
- **Security is everyone's responsibility.** Not a team, not a phase — a property of every system and every change.
- **Zero trust.** Never trust, always verify. No implicit trust based on network location, service identity, or prior authentication.
- **Least privilege.** Every identity (human or machine) has only the permissions it needs to perform its function — nothing more.
- **Defence in depth.** Multiple layers of security controls. No single control is relied upon exclusively.
- **Secure by default.** The default configuration of any service, infrastructure component, or feature must be the most secure option.

### Authentication & Authorisation
- All human authentication via the central IdP (see section 2).
- All service-to-service authentication via mTLS (service mesh) + short-lived tokens (no long-lived shared secrets).
- Authorisation decisions made close to the resource, informed by claims in the token.
- RBAC for coarse-grained access; ABAC (Attribute-Based Access Control) for fine-grained resource-level decisions where needed.

### Network Security
- All traffic encrypted in transit (TLS 1.2 minimum, TLS 1.3 preferred).
- Private subnets for all compute; public subnets only for load balancers and API gateway.
- Network policies (Kubernetes) / Security Groups restrict east-west traffic to declared service dependencies only.
- Web Application Firewall (WAF) in front of all public-facing endpoints.
- DDoS protection enabled at the perimeter.

### Secrets Management
- **Tool:** HashiCorp Vault or AWS Secrets Manager / Azure Key Vault.
- No secrets in code, config files, environment variables in source control, or container images.
- Secrets rotated automatically. Applications consume dynamic secrets where supported.
- Secret access is audited and logged.

### Supply Chain Security
- All dependencies scanned for known vulnerabilities on every build (Snyk / Dependabot).
- Software Bill of Materials (SBOM) generated for every container image.
- Container images signed (Sigstore/cosign) and signature verified at deploy time.
- Dependency pinning: exact versions, not ranges.

### Threat Modelling
- Threat modelling (STRIDE or PASTA framework) is mandatory for every new service and every significant architectural change.
- Conducted as a team exercise before design is finalised — not after implementation.
- Output: a documented threat model with identified threats, risk ratings, and mitigations tracked to completion.
- Threat models reviewed annually and when the architecture changes materially.
- Findings feed directly into the security backlog with priority aligned to risk rating.

---

## 9. Regulatory & Compliance

### Principles
- **Compliance is a constraint, not a project.** Regulatory requirements are baked into architecture from day one, not addressed in a compliance sprint before launch.
- **Know your obligations.** The applicable regulatory landscape is reviewed before entering any new market or processing any new category of data.
- **Audit trails are non-negotiable.** Any system touching regulated data must maintain immutable, complete audit logs.

### Applicable Frameworks (baseline — adapt to jurisdiction)
| Framework | Applicability | Key Requirements |
|---|---|---|
| GDPR / UK GDPR | EU/UK user data | Consent, right to erasure, data minimisation, DPA |
| SOC 2 Type II | SaaS / B2B customers | Security, availability, confidentiality controls |
| ISO 27001 | Enterprise sales | Information security management system |
| PCI DSS | If handling card data | Cardholder data environment isolation |
| HIPAA | If handling health data (US) | PHI controls, BAA agreements |

### Data Privacy
- **Data minimisation:** Collect only what is needed. No speculative data collection.
- **Purpose limitation:** Data collected for one purpose is not used for another without consent.
- **Right to erasure:** Architecture must support deletion of a user's data across all systems. This is designed in, not bolted on.
- **Data residency:** Where data is stored and processed must comply with applicable regulations. Multi-region architecture must account for residency requirements.
- **Privacy Impact Assessments (PIA):** Required before launching any new feature that processes personal data.

### Audit & Compliance Controls
- Immutable audit logs for all data access and mutations on regulated data.
- Quarterly access reviews for privileged accounts.
- Annual penetration test by a qualified third party.
- Change management process documented and followed for production changes.
- Incident response plan defined, tested, and kept current.

---

## 10. Logging, Monitoring & Observability

### Principles
- **Observability is a first-class requirement.** A service that cannot be observed in production is not production-ready.
- **The three pillars:** Logs, metrics, and traces work together. No pillar is optional.
- **Structured logging everywhere.** Human-readable logs are for development. Production logs are structured (JSON) and machine-parseable.
- **Correlate everything.** A single user request must be traceable across every service it touches via a correlation/trace ID.

### Logging
- **Format:** JSON structured logs with mandatory fields: `timestamp`, `level`, `service`, `traceId`, `spanId`, `correlationId`, `message`.
- **Levels:** `ERROR` (requires action), `WARN` (notable but not breaking), `INFO` (business events), `DEBUG` (development only — never in production by default).
- **Central log aggregation:** All logs shipped to a central platform (e.g., ELK stack, Datadog, AWS CloudWatch Logs).
- **Retention:** 90 days hot, 1 year cold (adjust per regulatory obligation).
- **Never log:** Passwords, tokens, PII, payment card data — ever.

### Metrics
- **Tool:** Prometheus + Grafana (or Datadog / New Relic as managed alternative).
- **Golden Signals** per service: Latency, Traffic, Errors, Saturation (LTES).
- **SLI/SLO dashboards** visible to all engineering teams.
- **Business metrics** alongside technical metrics — a dashboard should tell you if the system is healthy *and* if the business is healthy.
- Custom metrics exposed via `/metrics` endpoint (Prometheus format) on every service.

### Distributed Tracing
- **Tool:** OpenTelemetry (vendor-agnostic instrumentation) → Jaeger / Tempo / Datadog APM.
- Trace context propagated across all service boundaries (W3C TraceContext standard).
- Sampling strategy: 100% for errors; configurable rate for success traces (default 10%).
- Every external API call, database query, and message publish/consume is a traced span.

### Alerting
- Alerts are based on SLO burn rate, not raw metric thresholds where possible.
- Every alert has a defined owner, severity, and runbook link.
- Alert fatigue is actively managed — noisy, non-actionable alerts are removed.
- On-call rotations defined per service domain.
- **Severity tiers:**
  - P1: Customer impact, immediate response (< 15 min)
  - P2: Degraded service, urgent response (< 1 hour)
  - P3: Non-critical issue, business hours response

### Synthetic Monitoring
- Critical user journeys probed synthetically every 1–5 minutes from multiple regions (e.g., Datadog Synthetics, Checkly, AWS CloudWatch Synthetics).
- Synthetic monitors alert before real users are impacted — external-facing availability must not be learned from customer complaints.
- Synthetic test results included in SLO calculations alongside real traffic.

### Real User Monitoring (RUM)
- Frontend applications instrumented with RUM to capture actual user-perceived performance (Core Web Vitals: LCP, FID/INP, CLS).
- Performance regressions in real user sessions treated with the same urgency as backend latency regressions.
- RUM data segmented by geography, device type, and browser to surface disproportionate impact on user segments.

---

## 11. Resilience Engineering

### Principles
- **Design for failure.** Every dependency will fail at some point. Design systems that degrade gracefully rather than fail catastrophically.
- **Embrace chaos.** Failures in production are inevitable. Use controlled chaos engineering to find weaknesses before they find you.
- **Recovery time matters as much as prevention.** MTTR (Mean Time to Recover) is as important a metric as MTBF (Mean Time Between Failures).

### Patterns (mandatory where applicable)
| Pattern | When to Apply |
|---|---|
| Circuit Breaker | All synchronous inter-service calls |
| Retry with exponential backoff + jitter | Transient failures (network, throttling) |
| Bulkhead | Isolate resource pools to prevent cascade failures |
| Timeout | Every outbound network call — no indefinite waits |
| Fallback | Define degraded behaviour when a dependency is unavailable |
| Idempotency | All write operations, especially in async workflows |
| Dead Letter Queue | All message consumers |
| Rate Limiting | All public-facing APIs and resource-intensive operations |

### High Availability
- No single points of failure in production.
- Multi-AZ deployment for all stateful services (databases, caches, brokers).
- Stateless services horizontally scaled with auto-scaling policies.
- Database failover tested and documented — RTO and RPO targets defined per service.

### Multi-Region Strategy
- Tier 1 services must define their multi-region stance explicitly:
  - **Active-Active:** Traffic served from multiple regions simultaneously; requires conflict resolution strategy for writes.
  - **Active-Passive:** Primary region serves all traffic; secondary region on standby with data replicated and failover automated.
  - **Single-Region with DR:** Primary region only; DR region activated manually in a declared disaster.
- The chosen stance is documented per service in the service catalogue alongside its RTO/RPO.
- DNS failover (Route 53 / Azure Traffic Manager) automated for active-passive and active-active configurations.
- Data replication lag monitored continuously — replication lag exceeding RPO threshold triggers an alert.

### Disaster Recovery
- **RPO (Recovery Point Objective) and RTO (Recovery Time Objective)** defined per service tier:
  - Tier 1 (critical): RPO < 1 hour, RTO < 4 hours
  - Tier 2 (important): RPO < 24 hours, RTO < 24 hours
  - Tier 3 (standard): RPO < 72 hours, RTO < 72 hours
- DR runbooks maintained and tested at least twice a year.
- Backup restoration tested regularly — not assumed to work.
- GameDay exercises conducted quarterly to simulate failure scenarios.

### Chaos Engineering
- Chaos experiments run in staging before production.
- Hypothesis-driven: define expected behaviour before introducing a fault.
- Tooling: Chaos Monkey, Gremlin, or AWS Fault Injection Simulator.

---

## 12. Operational Excellence & SLAs

### Principles
- **Toil is the enemy.** Manual, repetitive operational work is identified, measured, and systematically eliminated.
- **You build it, you run it.** Teams are responsible for the operational health of their services in production.
- **Learn from failure.** Every significant incident produces a blameless post-mortem with concrete action items.

### Service Level Objectives (SLOs)
All production services must define SLOs before going live:

| Metric | Definition | Example Target |
|---|---|---|
| Availability | % of time service responds successfully | 99.9% (8.7h downtime/year) |
| Latency (p95) | 95th percentile response time | < 300ms |
| Latency (p99) | 99th percentile response time | < 1000ms |
| Error rate | % of requests resulting in 5xx | < 0.1% |

- **Error budgets:** Derived from SLOs. When the error budget is exhausted, new feature work pauses and reliability work takes priority.
- SLO dashboards are public to all engineering teams.

### Incident Management
- **Incident severity levels** aligned to customer impact (P1–P3 as defined in section 10).
- **Incident commander** role defined and rotated.
- **Communication cadence** during incidents: status updates every 30 minutes for P1, every hour for P2.
- **Post-mortem** mandatory for P1 and P2 incidents: completed within 5 business days, blameless, with action items tracked to completion.
- **Status page** maintained for customer-facing services.

### Change Management
- All production changes go through the deployment pipeline — no manual hotfixes directly to production.
- Feature flags used for gradual rollout of new features (reduce blast radius).
- Rollback plan defined before every significant deployment.
- Deployment windows defined for high-risk changes (avoid peak traffic periods).

### Capacity Planning
- Every service has a defined capacity model: expected traffic growth, resource headroom, and scaling limits.
- Load tests run quarterly against production-like environments to validate capacity assumptions.
- Auto-scaling policies reviewed every 6 months — scaling triggers adjusted based on observed traffic patterns.
- Capacity reviews conducted before known traffic spikes (product launches, campaigns, seasonal peaks).
- Cloud resource quotas and limits reviewed proactively — hitting a hard limit in production is an avoidable incident.

---

## 13. Developer Experience (DX)

### Principles
- **A great developer experience is a competitive advantage.** Fast, frictionless development loops attract and retain talent, and directly accelerate delivery.
- **Eliminate undifferentiated heavy lifting.** Platform Engineering exists to remove the plumbing so product teams can focus on product.
- **Documentation is a product.** It is written, maintained, and reviewed with the same rigour as code.

### Local Development
- One-command setup: any engineer should be able to clone a repository and have a working local environment with a single command (`make dev` or `docker compose up`).
- Local environment mirrors production topology as closely as practical.
- Service mocks/stubs provided for dependencies that cannot run locally.
- Hot reload enabled for all services in development mode.

### Internal Developer Portal (IDP)
- **Tool:** Backstage (or equivalent).
- **Contents:** Service catalogue, API documentation, runbooks, architecture decision records, onboarding guides, golden path templates.
- Every service registered in the catalogue with owner, dependencies, SLOs, and on-call information.

### Golden Path Templates
- Scaffolding templates for every standard service type (REST API, event consumer, microfrontend, data pipeline).
- Templates pre-configured with: CI/CD pipeline, observability, security scanning, testing framework, linting.
- New service from template to first deployment in < 1 day.

### Architecture Decision Records (ADRs)
- Significant architectural decisions documented as ADRs and committed to the repository.
- ADR format: Context → Decision → Consequences → Status.
- Superseded ADRs retained for historical context.

### Engineering Handbook
- A central Engineering Handbook documents: coding standards, review norms, incident response expectations, on-call responsibilities, and team working agreements.
- Every engineer reads the handbook during onboarding — sign-off required.
- Handbook is version-controlled and updated via pull request — not a wiki that drifts unreviewed.

### Onboarding Standard
- Every new engineer is productive (first PR merged) within their first week.
- Onboarding checklist per role: environment setup, access provisioning, codebase walkthrough, first task (starter issue).
- Buddy assigned for the first 30 days.
- Onboarding experience reviewed every quarter — friction points actioned, not just noted.

---

## 14. Cost Engineering

### Principles
- **Cost is an architectural concern.** Cloud cost is not a finance problem — it is an engineering problem. Architects and engineers are accountable for the cost profile of what they build.
- **Measure before optimising.** Cost optimisation without measurement is guesswork.
- **Design for cost efficiency from the start.** Retrofitting cost efficiency is expensive. Make cost-conscious choices in initial design.

### Tagging & Attribution
- Mandatory resource tagging: `env`, `service`, `team`, `cost-centre`.
- Cost dashboards broken down by team and service — every team can see what their services cost.
- Monthly cost reviews per team.

### Cost Controls
- **Right-sizing:** Compute resources sized to actual usage, not worst-case. Auto-scaling used to match supply to demand.
- **Reserved/committed use:** Baseline workloads covered by reserved instances or committed use discounts. Burstable workloads on on-demand/spot.
- **Spot/preemptible instances:** Used for batch workloads, CI/CD runners, non-critical background jobs.
- **Data transfer costs:** Architecturally minimised — services that communicate frequently are co-located in the same region/AZ.
- **Storage tiering:** Data moved to cheaper storage tiers based on access patterns (hot → warm → cold → archive).

### FinOps Guardrails
- Cost anomaly detection enabled — alerts on unexpected spend spikes.
- Budget alerts configured per team and per environment.
- No production workload deployed without a cost estimate reviewed by the team.
- Unused resources (idle instances, unattached volumes, stale snapshots) reviewed and cleaned up monthly.

---

## 15. AI/ML Architecture

### Principles
- **AI capabilities are first-class citizens.** AI/ML is not a bolt-on — it is architecturally considered from the start for applicable domains.
- **Human in the loop for high-stakes decisions.** Automated AI decisions with significant consequence require human oversight mechanisms.
- **Reproducibility and auditability.** Every model prediction in production must be traceable to the model version, input data, and parameters that produced it.
- **Responsible AI.** Models are evaluated for bias, fairness, and unintended consequences before production deployment.

### ML Platform
- **Feature store:** Centralised feature management (e.g., Feast, Tecton, AWS SageMaker Feature Store) to avoid feature duplication and ensure train/serve consistency.
- **Experiment tracking:** All model experiments tracked (MLflow / Weights & Biases) — hyperparameters, metrics, artefacts.
- **Model registry:** Versioned model artefacts with promotion workflow (experiment → staging → production).
- **Model serving:** Standardised serving layer (e.g., BentoML, Seldon, SageMaker endpoints) decoupled from model code.

### MLOps Pipeline
```
Data Ingestion → Feature Engineering → Model Training → 
Evaluation → Registry → Staging Validation → 
Production Deployment → Monitoring → Retraining Trigger
```

### Model Governance
- Every production model has a **model card**: purpose, training data, performance metrics, known limitations, bias evaluation, and owner.
- **Data drift** and **model drift** monitored continuously in production. Automated alerts trigger retraining pipeline.
- **Shadow mode deployment:** New model versions run in shadow mode (receiving real traffic but not serving predictions) before full promotion.
- **A/B testing framework** for comparing model versions in production with statistical rigour.

### LLM / Generative AI (where applicable)
- LLM calls treated as external dependencies — circuit breakers, retries, and fallbacks apply.
- Prompt templates version-controlled alongside application code.
- Output validation layer between LLM response and application logic.
- Cost per LLM call monitored — token usage tracked by feature/team.
- PII must never be sent to a third-party LLM without explicit consent and contractual data processing agreements.
- RAG (Retrieval-Augmented Generation) preferred over fine-tuning for domain-specific knowledge injection.

---

## 16. Documentation Standards

### Principles
- **Documentation is a first-class engineering deliverable.** A feature is not done until it is documented. Undocumented systems create invisible knowledge silos and make long-term support expensive.
- **Write for the next engineer, not yourself.** Documentation is written assuming the reader has no context about the decision, system, or history — because eventually, they won't.
- **Docs live with the code.** Documentation that lives outside the repository drifts. Where possible, docs are co-located with the code they describe and reviewed in the same pull request.
- **Evergreen over exhaustive.** A short, accurate document that is maintained is more valuable than a comprehensive document that is six months out of date. Prefer conciseness and discipline over volume.

---

### What Must Be Documented (Non-Negotiable)

#### 1. Every Service — README
Every repository must have a `README.md` that covers:
- **What it does** — one paragraph description of the service's purpose and the domain it belongs to
- **How to run it locally** — prerequisites, setup steps, and a working example
- **How to run tests** — single command, with any required setup
- **Key environment variables** — names, descriptions, and whether they are required or optional (never values)
- **Architecture overview** — a brief description or diagram of the key components
- **Dependencies** — upstream services, databases, and message topics this service depends on
- **Links** — to the API spec, runbook, service catalogue entry, and relevant ADRs

#### 2. Every API — OpenAPI / gRPC Spec
- OpenAPI 3.x spec (or `.proto` for gRPC) committed to the repository, kept in sync with the implementation.
- Every endpoint documented with: description, request/response schema, error codes, and at least one example.
- Breaking changes to the spec require a version bump and a migration note.
- Published to the internal developer portal (Backstage / Stoplight) on every merge to `main`.

#### 3. Every Domain Event — Event Catalogue
- All domain events registered in a central event catalogue (or schema registry with descriptions).
- Each event entry includes: event name, producing service, payload schema, description of when it is emitted, and known consumers.
- Schema changes follow the same review process as API changes.

#### 4. Every Architectural Decision — ADR
- Any decision that is hard to reverse, affects multiple teams, or involves a significant trade-off must be captured as an ADR.
- ADRs live in a `/docs/decisions` directory in the relevant repository (or a central `architecture` repository for cross-cutting decisions).
- See Appendix A for the ADR template.

#### 5. Every Production Service — Runbook
A runbook is the on-call engineer's guide to operating the service at 2am. It must cover:
- **Service overview** — what it does, who owns it, and who to escalate to
- **Common alerts** — what each alert means, likely causes, and step-by-step remediation
- **Deployment and rollback** — how to deploy, how to roll back, and what to watch after a deployment
- **Scaling** — how to scale the service manually if auto-scaling is insufficient
- **Known issues and workarounds** — recurring problems and their temporary fixes while a permanent solution is in progress
- **Dependencies and circuit breakers** — what happens when upstream services are unavailable
- Runbooks must be tested by someone other than the author — if they cannot follow it, it is not done.

#### 6. Every Data Pipeline / Dataset — Data Dictionary
- All production datasets documented with: field names, types, descriptions, nullability, and example values.
- Data lineage captured: where does this data come from, what transforms it, who consumes it.
- Data owner and SLA (freshness, availability) documented alongside the schema.
- Stored in the data catalogue (DataHub / Atlan or equivalent).

#### 7. Infrastructure — Architecture Diagrams
- Every production environment has a current architecture diagram showing: services, databases, message brokers, external dependencies, and network boundaries.
- Diagrams stored as code where possible (Mermaid, PlantUML, Structurizr C4) so they can be reviewed in PRs and do not rot in presentation slides.
- C4 model levels used as the standard:
  - **Level 1 (System Context):** How the system fits into the wider world
  - **Level 2 (Container):** High-level technology choices and service decomposition
  - **Level 3 (Component):** Internal structure of a specific container (where warranted)
- Diagrams updated as part of any infrastructure change PR — stale diagrams are treated as bugs.

---

### Documentation Quality Standards

| Type | Owner | Review Cadence | Staleness Trigger |
|---|---|---|---|
| README | Service team | Every PR that changes behaviour | Any functional change |
| API Spec | Service team | Every PR that changes the API | Any API change |
| Event Catalogue | Producing team | Every schema change | Event schema change |
| ADR | Decision author | On status change only | Decision superseded |
| Runbook | On-call team | Quarterly | After every P1/P2 incident |
| Data Dictionary | Data owner | Every schema change | Schema or lineage change |
| Architecture Diagram | Architect / team lead | Every infrastructure change | Infrastructure PR |

### Code-Level Documentation
- **Public interfaces** (exported functions, classes, modules) must have doc comments explaining purpose, parameters, return values, and any non-obvious behaviour.
- **Inline comments** explain *why*, not *what*. Code should be self-explanatory for the what; comments are reserved for business rules, non-obvious trade-offs, and known technical debt.
- **TODO / FIXME comments** must reference a tracked issue (`// TODO: #1234 — remove after migration`). Untracked TODOs are not permitted to merge to `main`.
- Languages enforce doc comment standards via linting (TSDoc for TypeScript, Sphinx/Google style for Python).

### Documentation Debt
- Undocumented services, APIs, and runbooks are tracked as technical debt with the same priority weighting as code debt.
- A documentation health check is included in quarterly engineering reviews — teams report against the non-negotiable list above.
- New services cannot be promoted to production without a completed README and runbook — these are deployment gates, not suggestions.

---

## Appendix A: Architectural Decision Record (ADR) Template

```markdown
# ADR-[number]: [Short Title]

## Status
[Proposed | Accepted | Deprecated | Superseded by ADR-xxx]

## Date
[YYYY-MM-DD]

## Context
[What is the situation or problem that prompted this decision?]

## Decision
[What was decided?]

## Consequences
[What are the positive and negative outcomes of this decision?]

## Alternatives Considered
[What other options were evaluated and why were they rejected?]
```

---

## Appendix B: Principles Summary

| # | Principle | One-liner |
|---|---|---|
| 1 | Standardise the stack | Lean, approved tech; justify every deviation |
| 2 | Centralise identity | One IdP, standards-based, zero-trust |
| 3 | Domains own everything | DDD boundaries, API + event contracts |
| 4 | Independent deployability | Microfrontends and microservices move independently |
| 5 | Event-sourced state where justified | Immutable event log; CQRS for complex domains |
| 6 | Quality is built in | Shift-left testing; no quality gates bypassed |
| 7 | Everything as code | Infra, pipelines, policies — all versioned |
| 8 | API Gateway as the front door | No direct external service exposure |
| 9 | APIs are products | Versioned, documented, evolved without breaking consumers |
| 10 | Data ownership follows domain | No cross-domain DB access; data as a product |
| 11 | Security by default | Zero trust, least privilege, defence in depth |
| 12 | Compliance is designed in | Not retrofitted; privacy and audit from day one |
| 13 | Observe everything | Logs + metrics + traces; no unobservable production service |
| 14 | Design for failure | Circuit breakers, retries, fallbacks, chaos testing |
| 15 | SLOs drive reliability | Error budgets; you build it, you run it |
| 16 | Developer experience matters | Fast loops, golden paths, one-command setup |
| 17 | Cost is an engineering concern | Tag everything, measure, right-size, optimise |
| 18 | AI is first-class | MLOps, model governance, responsible AI from the start |
| 19 | Documentation is a deliverable | README, runbook, API spec, ADR — shipped with the code, not after |

---

*This document is a living artefact. It must be reviewed quarterly and updated as the technical landscape evolves. All significant deviations from these principles must be captured as ADRs.*

---

## Appendix C: Technology Radar Template

The Technology Radar is reviewed every 6 months. Each technology is placed in one of four rings:

| Ring | Meaning |
|---|---|
| **Adopt** | Proven, recommended for production use. Our default choice in this space. |
| **Trial** | Worth pursuing. Use on a project with an intent to share learnings. |
| **Assess** | Worth exploring. Understand how it might affect us; not yet in production. |
| **Hold** | Do not start new projects with this. Existing uses should have a migration plan. |

### Example Radar (illustrative — update each cycle)

**Languages & Frameworks**
- Adopt: TypeScript, React, Python, Node.js
- Trial: Rust (systems/performance-critical components)
- Assess: Bun, Deno
- Hold: CoffeeScript, Angular.js (legacy)

**Platforms & Infrastructure**
- Adopt: Kubernetes, Terraform, GitHub Actions, Docker
- Trial: Argo Workflows (ML pipelines), Crossplane (IaC)
- Assess: WebAssembly (WASM) edge runtimes
- Hold: Ansible for application deployment (use Helm/GitOps instead)

**Data & AI**
- Adopt: Apache Kafka, dbt, PostgreSQL, OpenTelemetry
- Trial: Apache Flink (streaming), pgvector (vector search)
- Assess: Apache Iceberg (open table format), Weaviate
- Hold: Hadoop MapReduce

**Techniques**
- Adopt: Trunk-based development, GitOps, Consumer-driven contract testing, SLO-based alerting
- Trial: Platform Engineering (Internal Developer Platform)
- Assess: eBPF-based observability
- Hold: Feature branching (long-lived), manual deployment scripts
