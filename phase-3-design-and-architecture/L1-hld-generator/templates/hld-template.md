# High-Level Design Template — `hld.md` (as-built / target-vs-MVP style)

> This is a different flavor from a per-service "living document" HLD: it's built for a single
> module/feature slice that has both a **target enterprise architecture** and a **current build**
> that only partially realizes it (a demo, an MVP, a phase-gated rollout). Every section carries
> the target and the reality side by side, on purpose, so nothing gets silently claimed as "done"
> when it's actually mocked, deferred, or simplified.
>
> All diagrams below are plain ASCII text in ordinary code blocks — no Mermaid, no renderer
> plugin needed. Open the raw `.md` file in anything (a text editor, GitHub, VS Code, Notepad)
> and the diagrams show up exactly as drawn, immediately.

---

## PART A — HOW TO USE THIS TEMPLATE

### A.1 The governing rule
**Never merge "target" and "reality" into one claim.** If the target is Kafka and the build uses
a mocked REST endpoint, say both, in the same sentence if needed. The reader (a reviewer, a
future engineer, an auditor) needs to know exactly what's real right now without cross-referencing
five other files.

### A.2 Section-by-section intent
| # | Section | Answers |
|---|---|---|
| 0 | Revision note | What changed between versions of *this document*, and why |
| 1 | System Component Diagram | What are the pieces, and which ones are real vs. stubbed |
| 2 | Data Flow Diagrams | For each real user-facing flow, exactly what calls what, in what order, including every branch |
| 3 | State Machine | If the core domain object has a lifecycle, what are its legal transitions and guards |
| 4 | Deployment View | Where does this actually run — target platform vs. today's reality |
| 5 | Security View | AuthN/AuthZ mechanics, target vs. reality, plus the authorization matrix as actually enforced |
| 6 | Observability | What signals exist, target vs. reality |
| 7 | NFR Compliance Summary | For each NFR in the PRD, is it met, met-differently, or not applicable yet |
| 8 | Gap Register | Single consolidated list of every target-vs-reality gap raised above, with an owner |

### A.3 Correlation ID
If this HLD is produced by an agentic/traceable pipeline, carry a correlation ID in the header so
this document, its LLD, its ADRs, and its audit trail can all be tied back together.

### A.4 When to add/omit a section
- No lifecycle-bearing core entity (e.g. a pure read-only reporting service) → omit §3, note why.
- Nothing is mocked or deferred (rare, but possible for a truly finished module) → §4/§8 shrink to
  "target = reality," stated explicitly rather than left blank.

### A.5 Diagram conventions used throughout this file
```
[ Box ]              a component / actor / datastore
──▶                  synchronous call, direction of arrow
- ->                 async / cached / conditional relationship
├─ [condition]       a branch in a flow (mutually exclusive with sibling ├─/└─ at same indent)
└─ [condition]       the last branch at that indent level
```

---

## PART B — THE TEMPLATE (copy everything below this line into a new file)

```markdown
# High-Level Design (HLD)
**Document Version:** <e.g. 1.0.0>
**Baseline Reference:** `<kb-L1-enterprise-architecture-doc>` v<X>
**Feature Coverage:** <E#.F#–F# committed this cycle> · <E#.F# designed, not built — see §8>
**Stack:** <language/framework> + <datastore, target / actual> + <other libs> · <frontend stack>
**Correlation ID:** `<uuid>`

---

## 0. Revision note

<What version 1.0.0 covered, at what level of depth. What changed in this version and why —
name the new components/flows/sections added, and the reason (feedback, new feature, expanded
field set, etc.).>

---

## 1. System Component Diagram

```
                    ┌───────────────┐        ┌───────────────┐
                    │  <Actor 1>    │        │  <Actor 2>    │
                    │  <client tech>│        │  <client tech>│
                    └──────┬────────┘        └───────┬───────┘
                           │  HTTPS                   │  HTTPS
                           └────────────┬─────────────┘
                                         ▼
                          ┌─────────────────────────────┐
                          │  Ingress / Reverse Proxy      │
                          │  target: <e.g. cloud ALB>      │
                          │  MVP:    <e.g. none, direct     │
                          │          localhost>              │
                          └───────────────┬───────────────┘
                                          │
                    ┌─────────────────────┴──────────────────────┐
                    ▼                                             ▼
        ┌───────────────────────┐                    ┌───────────────────────┐
        │  <frontend app>        │   REST, Bearer      │  <backend app>         │
        │  target: <deploy shape>│◀- - - - token - - ->│  target: <deploy shape>│
        │  MVP: <actual shape>   │                      │  MVP: <actual shape>  │
        └───────────────────────┘                    └───────────┬───────────┘
                                                                   ▼
                                                     ┌───────────────────────────┐
                                                     │  AuthFilter                 │
                                                     │  MVP:    <mocked mechanism>  │
                                                     │  target: <real mechanism>    │
                                                     └─────────────┬─────────────┘
                                              ┌────────────────────┴───────────────────┐
                                              ▼                                        ▼
                                ┌───────────────────────┐              ┌───────────────────────┐
                                │  <Controller 1>         │              │  <Controller 2>         │
                                └────────────┬───────────┘              └────────────┬───────────┘
                                             ▼                                        ▼
                                ┌───────────────────────┐              ┌───────────────────────┐
                                │  <Service 1>            │              │  <Service 2>            │
                                └────────────┬───────────┘              └────────────┬───────────┘
                                             │                                        │
                          ┌──────────────────┴──────────┐                            │
                          ▼                              ▼                           ▼
              ┌───────────────────────┐   ┌────────────────────────────────┐  ┌───────────────┐
              │  <Datastore>            │   │  <ExternalIntegrationConnector>  │  │  (also writes) │
              │  target: <managed DB>   │   │  MOCKED — no real external call, │  │  to <Datastore>│
              │  MVP: <embedded/memory> │   │  see §8                           │  └───────────────┘
              └───────────┬───────────┘   └────────────────────────────────┘
                          ▲
                          │ target: cached TTL
              ┌───────────────────────┐
              │  <Cache>                │
              │  target: <e.g. Redis>   │
              │  MVP: <e.g. not used>   │
              └───────────────────────┘
```

**What changed vs. a from-scratch design:** <net-new module, or integrating with an existing
system — name the legacy component(s) it slots next to, and which integration points are the
genuinely open/brownfield risk vs. which are internal and fully under this team's control.>

---

## 2. Data Flow Diagrams

### 2.1 <Flow name> [<feature/story IDs>]

Participants: <Actor / upstream system> (target: <real mechanism>, MVP: <actual mechanism>) |
<Controller> | <Service> | Database | NotificationService

```
1. <Actor>     ──<request>──▶                          <Controller>
2. <Controller> ──<call>──▶                             <Service>
   ├─ [<branch 1 condition>]
   │    3. <Service> ──write, status=X──▶               Database
   │    4. <Service> ──notify(<event>)──▶                NotificationService
   └─ [<branch 2 condition>]
        5. <Service> ──write, status=Y──▶                Database
6. <Service> ──<response>──▶                             <Actor>
```

<Repeat §2.N for every real, user-triggered flow — not every possible internal call, only the
ones a reviewer would actually need to trace end-to-end.>

---

## 3. <Core Entity> State Machine [<feature IDs> · BL — business logic]

```
                              ┌─────────────┐
              <trigger>       │             │
        ─────────────────────▶│  STATE_A    │
                              │             │
                              └──────┬──────┘
                                     │ <trigger>, <guard>
                                     ▼
                              ┌─────────────┐
                              │  STATE_B    │──── <guard failed> ────▶ (rejected, no transition)
                              └──────┬──────┘
                                     │ <terminal trigger>
                                     ▼
                              ┌─────────────┐
                              │  STATE_C     │   <-- terminal state
                              │ (terminal)   │       <anything found missing/fixed at review
                              └─────────────┘       worth calling out here>
```

### State summary

| State | Reachable from | <Action A> allowed | <Action B> allowed |
|---|---|---|---|
| STATE_A | <trigger> | <yes/no + condition> | <yes/no + condition> |

---

## 4. Deployment View

### 4.1 Target (per `<kb-L1 reference>` <EA-ref>)

```
┌───────────────────────────────────────────────────────────────────────────┐
│  <Cluster>                                                                  │
│                                                                              │
│  ┌─────────────────────────── Namespace: <name> ─────────────────────────┐ │
│  │                                                                        │ │
│  │   ┌─────────────────────┐        ┌─────────────────────┐             │ │
│  │   │ <Frontend> Deployment│        │ <Backend> Deployment │             │ │
│  │   │  Pod 1 ... Pod n      │        │  Pod 1 ... Pod n      │             │ │
│  │   │  HPA max: <N>          │        │  HPA max: <N>          │             │ │
│  │   └──────────┬──────────┘        └──────────┬──────────┘             │ │
│  │              │                                │                        │ │
│  │              └──────────────┬─────────────────┘                        │ │
│  │                              ▼                                          │ │
│  │                    ┌───────────────────┐                                │ │
│  │                    │  Ingress            │                                │ │
│  │                    │  TLS <version>      │                                │ │
│  │                    │  Rate limits: r/w    │                                │ │
│  │                    └───────────────────┘                                │ │
│  └────────────────────────────────────────────────────────────────────────┘ │
│                                                                              │
│  ┌───────────────────── Namespace: data — shared ────────────────────────┐ │
│  │   ┌───────────────────┐   ┌──────────────┐   ┌──────────────┐         │ │
│  │   │ Managed DB          │   │ Cache Primary │──▶│ Cache Replica │         │ │
│  │   │ (Multi-AZ)          │   └──────────────┘   └──────────────┘         │ │
│  │   └───────────────────┘                                                │ │
│  └────────────────────────────────────────────────────────────────────────┘ │
└───────────────────────────────────────────────────────────────────────────┘
```

| Service | Image base | Port | Resources (request/limit) | Probes |
|---|---|---|---|---|
| <frontend> | <base image> | <port> | <cpu/mem> | <probe> |
| <backend> | <base image> | <port> | <cpu/mem> | <probe, or "target — not yet added"> |

### 4.2 MVP reality (this repo, right now)

```
┌───────────────────────┐      ┌────────────────────────┐
│  <dev run command>    │      │  <dev run command>     │
│  <frontend>            │─────▶│  <backend>              │
│  http://localhost:<p> │ REST │  http://localhost:<p>  │
│  (single process, no  │      │  (single process, no   │
│   HPA, hot-reload)     │      │   HPA, embedded DB)     │
└───────────────────────┘      └────────────┬────────────┘
                                              │
                                    ┌─────────▼─────────┐
                                    │  <in-memory DB>    │
                                    │  (state lost on    │
                                    │   restart)          │
                                    └────────────────────┘
```
<One line: what infra is entirely absent from this MVP and why that's fine — link to §8 for the
authoritative gap list.>

---

## 5. Security View

Participants: Browser | AuthFilter | Protected Endpoint
target: <real auth scheme> · MVP: <mocked mechanism> — see §8

```
1. Browser      ──any protected request + Authorization header──▶   AuthFilter
   ├─ [OPTIONS preflight]
   │    2. AuthFilter ──pass through (CORS)──▶                      Browser
   └─ [non-OPTIONS]
        3. AuthFilter ──resolve caller──▶ (self)
           ├─ [no matching caller]
           │    4. AuthFilter ──401──▶                               Browser
           └─ [caller found]
                5. AuthFilter ──attach current-user context──▶      Protected Endpoint
                6. Protected Endpoint ──RBAC check: <role A>: <scope>; <role B>: <scope>──▶ (self)
                   ├─ [authorized]   7. Protected Endpoint ──200──▶  Browser
                   └─ [not authorized] 7. Protected Endpoint ──403──▶ Browser
```

### Encryption boundaries

| Boundary | Target | MVP reality |
|---|---|---|
| Browser → API | <e.g. TLS 1.3> | <e.g. plain HTTP, localhost only> |
| API → Database | <e.g. TLS 1.2+> | <e.g. N/A, embedded DB> |
| Auth token | <e.g. signed JWT> | <e.g. opaque mocked token> |

### Authorization matrix (as actually enforced in code)

| Endpoint | <Role A> | <Role B> |
|---|---|---|
| `<METHOD> <path>` | <scope, or "Forbidden (403)"> | <scope> |

---

## 6. Observability

| Signal | Target | MVP reality |
|---|---|---|
| Structured logs | <e.g. JSON, correlation ID per line> | <e.g. default console logging> |
| Metrics | <e.g. metrics library → dashboarding> | <e.g. not instrumented> |
| Health probes | <e.g. /health endpoint> | <e.g. not added> |
| Tracing | <e.g. distributed tracing> | <e.g. not instrumented> |
| Process/SDLC-level audit trail | — | <if the design process itself is logged separately from app runtime, say so — don't conflate the two> |

---

## 7. NFR Compliance Summary

| NFR (from PRD/EA ref) | Target | MVP status |
|---|---|---|
| <NFR 1> | <target mechanism> | **Met** / **Met differently** / **Not applicable** / **Not tested** — <one line why> |

---

## 8. Gap Register — Target Architecture vs. This Build

| # | Gap | Target (EA reference) | Owner of the resolution |
|---|---|---|---|
| 1 | <what's mocked/deferred/simplified> | <EA ref> | <team/role> |

<Every gap above should be cross-referenced from the point earlier in this document where it
first matters — this table is the single source of truth for "what's real vs. what's demo," not
a duplicate of scattered footnotes.>
```

---

## PART C — WORKED EXAMPLE (HarvestLink Marketplace, Order Placement Module)

# High-Level Design (HLD)
**Document Version:** 1.1.0 (revised — see §0)
**Baseline Reference:** `kb-L1-harvestlink-enterprise-architecture.md` v1.0.0
**Feature Coverage:** F-02a, F-02b, F-02e (MVP-committed, Cycle 3) · F-02c, F-02d (designed, not built — see §8)
**Stack:** Java 17 + Spring Boot 3.x + PostgreSQL (target) / H2 (MVP demo) · React 18 + TypeScript (frontend)
**Correlation ID:** `4B2E7A10-3F5C-4D8E-9A11-6C0F2E7D9B44`

## 0. Revision note

Version 1.0.0 covered order creation only (F-02a) as a single component list. Version 1.1.0
restructures the document into a full system component diagram, a flow diagram per real flow
(including the Cycle 9 brownfield "repeat order" flow), an explicit order state machine, a
deployment view separating the target ECS/RDS topology from the MVP's two-local-process reality,
a security view, an observability section, and a Gap Register consolidating every mocked/deferred
piece — following the same pattern the Solution Architect asked for on the Catalogue Service HLD.

## 1. System Component Diagram

```
                    ┌───────────────┐
                    │  Buyer Browser│
                    │  React 18 SPA │
                    └──────┬────────┘
                           │  HTTPS
                           ▼
              ┌─────────────────────────────┐
              │  Ingress / Reverse Proxy      │
              │  target: AWS ALB                │
              │  MVP:    none, direct localhost  │
              └───────────────┬───────────────┘
                              │
        ┌─────────────────────┴──────────────────────┐
        ▼                                             ▼
┌───────────────────────┐                    ┌───────────────────────────┐
│  buyer-spa              │   REST, Bearer      │  orders-api                 │
│  React 18 · Vite         │◀- - - token - - ->│  Spring Boot                 │
│  target: Nginx, HPA 2-6  │                     │  target: HPA 2-8 pods       │
│  MVP: Vite dev server    │                     │  MVP: single process :8081  │
└───────────────────────┘                    └───────────┬───────────────┘
                                                            ▼
                                             ┌───────────────────────────┐
                                             │  AuthFilter                  │
                                             │  MVP:    mocked bearer token │
                                             │  target: Identity OIDC JWT   │
                                             └─────────────┬─────────────┘
                                     ┌────────────────────┴────────────────────┐
                                     ▼                                         ▼
                       ┌───────────────────────┐              ┌───────────────────────────┐
                       │  OrderController         │              │  OrderHistoryController      │
                       │  (create order)           │              │  (history + repeat order)     │
                       └────────────┬───────────┘              └────────────┬───────────────┘
                                     ▼                                        ▼
                       ┌───────────────────────┐              ┌───────────────────────────┐
                       │  OrderService            │◀─────────────│  RepeatOrderAction (Cy. 9)  │
                       │  placement + idempotency  │              └───────────────────────────┘
                       └──────┬───────┬────┬─────┘
                              │       │    │
              ┌───────────────┘       │    └──────────────────┐
              ▼                       ▼                       ▼
┌───────────────────────┐  ┌───────────────────┐  ┌───────────────────────────┐
│  CatalogueAvailability  │  │  PaymentGateway     │  │  NotificationService         │
│  Client (real, sync)    │  │  Connector          │  └───────────────────────────┘
└───────────────────────┘  │  MOCKED — no real   │
              │              │  Stripe call, §8    │
              ▼              └───────────────────┘
┌───────────────────────┐
│  PostgreSQL              │
│  target per EA4          │
│  MVP: H2 in-memory       │
└───────────┬───────────┘
            ▲
            │ target: cached 30s TTL (OrderHistoryController)
┌───────────────────────┐
│  Redis                   │
│  target: history cache   │
│  MVP: not used            │
└───────────────────────┘
```

**What changed vs. a from-scratch design:** Orders is a net-new bounded context — no legacy
service does order placement today (per `impact-assessment.md` §1 for Cycle 3). The genuinely
open integration points are downstream (a real payment gateway, mocked here) and cross-context
(Catalogue's availability check, which is real and synchronous, not mocked) — both called out
explicitly rather than glossed over.

## 2. Data Flow Diagrams

### 2.1 Place Order (new order) [F-02a · REQ-03]

Participants: Buyer | OrderController | OrderService | CatalogueAvailabilityClient |
PaymentGatewayConnector (mock) | Database | NotificationService

```
1. Buyer            ──POST /v1/orders {items, idempotencyKey}──▶     OrderController
2. OrderController   ──placeOrder(cmd)──▶                             OrderService
3. OrderService      ──checkIdempotency(key)──▶ (self)
   ├─ [duplicate key]
   │    4. OrderService ──200 (original order, no re-insert)──▶      OrderController
   └─ [new key]
        5. OrderService ──GET availability(listingId)──▶             CatalogueAvailabilityClient
           ├─ [insufficient stock]
           │    6. OrderService ──409 Conflict──▶                    OrderController
           └─ [stock OK]
                7.  OrderService ──authorize(amount) [MOCK]──▶       PaymentGatewayConnector
                8.  PaymentGatewayConnector ──mock authorization id──▶ OrderService
                9.  OrderService ──INSERT orders(status=PAYMENT_AUTHORIZED)──▶ Database
                10. OrderService ──UPDATE status=CONFIRMED──▶        Database
                11. OrderService ──notify(ORDER_CONFIRMED, buyer & producer)──▶ NotificationService
                12. OrderService ──201 Created──▶                    OrderController
```

### 2.2 Repeat Order [F-02e · brownfield, Cycle 9]

Participants: Buyer | OrderHistoryController | RepeatOrderAction | OrderService | Database

```
1. Buyer                  ──POST /v1/orders {repeatFromOrderId, overrides}──▶  OrderHistoryController
2. OrderHistoryController ──execute(originalOrderId, overrides)──▶             RepeatOrderAction
3. RepeatOrderAction       ──findById(originalOrderId)──▶                       Database
4. Database                ──original Order──▶                                  RepeatOrderAction
5. RepeatOrderAction       ──apply overrides (qty/address), new idempotencyKey──▶ (self)
6. RepeatOrderAction       ──placeOrder(cmd)──▶                                 OrderService
                              (from here, same path as §2.1 step 3 onward)
7. OrderService             ──201 Created──▶                                    OrderHistoryController
8. OrderHistoryController  ──201 Created──▶                                     Buyer
```

## 3. Order State Machine [F-02a, F-02e · BL]

```
                                       ┌────────────────────────┐
        place/repeat order,            │                        │
        payment mock-authorized        │  PAYMENT_AUTHORIZED     │
       ────────────────────────────────▶│                        │
                                       └──────────┬─────────────┘
                                                   │
                              auth succeeds ───────┼─────── auth fails
                                                   ▼                  ▼
                                       ┌──────────────┐    ┌──────────────┐
                                       │  CONFIRMED    │    │  CANCELLED    │
                                       └──────┬───────┘    └──────────────┘
                                              │
                       producer marks   ┌─────┴─────┐  refund issued
                       shipped          │           │  (F-02d, not built)
                       (F-02c,          ▼           ▼
                       not built)
                            ┌──────────────┐   ┌──────────────┐
                            │  FULFILLED    │   │  REFUNDED     │
                            │ (not built)   │   │ (not built)   │
                            └──────────────┘   └──────────────┘
```
CONFIRMED is terminal for MVP purposes — fulfillment and refund transitions are designed
(F-02c/F-02d) but have no code path yet.

### State summary

| State | Reachable from | Repeat allowed (source order) | Edit allowed |
|---|---|---|---|
| PAYMENT_AUTHORIZED | Place/repeat order | No (not yet confirmed) | No |
| CONFIRMED | Authorization success | Yes | No (orders are immutable once confirmed) |
| CANCELLED | Authorization failure | Yes (repeat re-attempts payment) | No |
| FULFILLED / REFUNDED | Not reachable in MVP | Yes | No |

## 4. Deployment View

### 4.1 Target (per `kb-L1-harvestlink-enterprise-architecture.md` EA6)

```
┌───────────────────────────────────────────────────────────────────────────┐
│  ECS Cluster                                                                │
│                                                                              │
│  ┌─────────────────────────── Namespace: orders ─────────────────────────┐ │
│  │                                                                        │ │
│  │   ┌─────────────────────┐        ┌─────────────────────┐             │ │
│  │   │ buyer-spa Service     │        │ orders-api Service    │             │ │
│  │   │  Task 1 ... Task n      │        │  Task 1 ... Task n      │             │ │
│  │   │  HPA max: 6              │        │  HPA max: 8              │             │ │
│  │   └──────────┬──────────┘        └──────────┬──────────┘             │ │
│  │              └──────────────┬─────────────────┘                        │ │
│  │                              ▼                                          │ │
│  │                    ┌───────────────────┐                                │ │
│  │                    │  ALB Ingress        │                                │ │
│  │                    │  TLS 1.3             │                                │ │
│  │                    │  Read: 300/min/user   │                                │ │
│  │                    │  Write: 60/min/user   │                                │ │
│  │                    └───────────────────┘                                │ │
│  └────────────────────────────────────────────────────────────────────────┘ │
│                                                                              │
│  ┌───────────────────── Namespace: data — shared ────────────────────────┐ │
│  │   ┌───────────────────┐   ┌──────────────┐   ┌──────────────┐         │ │
│  │   │ PostgreSQL (RDS)    │   │ Redis Primary  │──▶│ Redis Replica │         │ │
│  │   │ Multi-AZ, orders    │   └──────────────┘   └──────────────┘         │ │
│  │   │ schema               │                                              │ │
│  │   └───────────────────┘                                                │ │
│  └────────────────────────────────────────────────────────────────────────┘ │
└───────────────────────────────────────────────────────────────────────────┘
```

| Service | Image base | Port | Resources (request/limit) | Probes |
|---|---|---|---|---|
| buyer-spa | `nginx:alpine` (multi-stage build) | 80 | 100m/500m CPU · 128Mi/256Mi | `/` (200) |
| orders-api | `eclipse-temurin:17-jre-alpine` | 8081 | 250m/1000m CPU · 256Mi/512Mi | `/actuator/health` (target — not yet added, see §8) |

### 4.2 MVP reality (this repo, right now)

```
┌───────────────────────┐      ┌────────────────────────┐
│  npm run dev (Vite)   │      │  mvn spring-boot:run    │
│  buyer-spa             │─────▶│  orders-api             │
│  http://localhost:5174│ REST │  http://localhost:8081 │
│  (single process, no  │      │  (single process, no   │
│   HPA, hot-reload)     │      │   HPA, embedded H2)     │
└───────────────────────┘      └────────────┬────────────┘
                                              │
                                    ┌─────────▼─────────┐
                                    │  H2 in-memory DB   │
                                    │  (state lost on    │
                                    │   process restart)  │
                                    └────────────────────┘
```
No ECS, no ALB, no Redis, no PostgreSQL, no CI runs this MVP — two local processes on a developer
machine, by design (demo scope for Cycle 3). §8 is the authoritative gap list.

## 5. Security View

Participants: Browser | AuthFilter | Protected Endpoint
target: Identity-issued RS256 JWT (EA5) · MVP: Bearer {buyerId}-token against a seeded
in-memory user table — NOT a real auth scheme, see §8

```
1. Browser  ──any /api/* request + Authorization: Bearer {token}──▶  AuthFilter
   ├─ [OPTIONS preflight]
   │    2. AuthFilter ──pass through (CORS)──▶                       Browser
   └─ [non-OPTIONS]
        3. AuthFilter ──parse token, look up buyer──▶ (self)
           ├─ [no matching buyer]
           │    4. AuthFilter ──401──▶                                Browser
           └─ [buyer found]
                5. AuthFilter ──attach currentUser──▶                Protected Endpoint
                6. Protected Endpoint ──RBAC: BUYER=own orders only; PRODUCER=orders
                   against own listings only──▶ (self)
                   ├─ [authorized]      7. Protected Endpoint ──200──▶ Browser
                   └─ [not authorized]  7. Protected Endpoint ──403──▶ Browser
```

### Encryption boundaries

| Boundary | Target (EA5) | MVP reality |
|---|---|---|
| Browser → API | TLS 1.3 | Plain HTTP, localhost only |
| API → Database | TLS 1.2+ | N/A — H2 embedded |
| Auth token | RS256, Identity-issued | Opaque mocked token, no signature |

### Authorization matrix (as actually enforced in code)

| Endpoint | Buyer | Producer |
|---|---|---|
| `POST /v1/orders` | Own `buyerId` only (403 otherwise) | Forbidden (403) |
| `POST /v1/orders` (repeat) | Own prior order only (403 otherwise — found missing, IDOR, fixed) | Forbidden (403) |
| `GET /v1/orders/history` | Own orders only, forced server-side | Orders against own listings only |

## 6. Observability

| Signal | Target (EA9) | MVP reality |
|---|---|---|
| Structured logs | JSON, correlationId per line | Default Spring Boot console logging |
| Metrics | Micrometer → Prometheus | `order.repeated` custom counter only; nothing else instrumented |
| Health probes | `/actuator/health` | Not added |
| Tracing | OpenTelemetry | Not instrumented |

## 7. NFR Compliance Summary

| NFR (from PRD/EA7) | Target | MVP status |
|---|---|---|
| Order placement ≤ 300ms P95 | Async availability check, indexed idempotency lookup | **Met trivially** — sub-second at demo scale, not stress-tested |
| Idempotent submission | DB unique constraint on `idempotency_key` | **Met** — enforced at the DB level, verified by the double-click regression test |
| 99.9% availability | Multi-AZ RDS, ECS auto-scaling | **Not applicable** — single-process local demo has no HA story |
| PCI-DSS scope (card payment) | Tokenized payment via PCI-compliant gateway | **Not applicable yet** — `PaymentGatewayConnector` is fully mocked, no real card data ever leaves the browser in this MVP |

## 8. Gap Register — Target Architecture vs. This Build

| # | Gap | Target (EA reference) | Owner of the resolution |
|---|---|---|---|
| 1 | Payment authorization is fully mocked | EA8 — real gateway integration is an open architectural question | Billing squad to confirm Stripe vs. Adyen before Cycle 8 |
| 2 | Auth is a mocked bearer token, not Identity OIDC | EA5 | Identity team — swap `AuthFilter` for a real OIDC validator |
| 3 | H2 in-memory, not PostgreSQL | EA1, EA4 | Orders squad — schema already documented in LLD §2 |
| 4 | No ECS deployment, no HPA, no ALB | EA6 | Standard platform onboarding once module leaves demo status |
| 5 | No Redis caching layer | EA1 | Add once order-history read volume justifies it |
| 6 | No application-level observability (metrics, tracing, health probes) | EA9 | Add Spring Boot Actuator + Micrometer before staging |
