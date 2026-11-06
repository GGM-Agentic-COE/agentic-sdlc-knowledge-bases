<!--
solution-architecture-template.md — the Document Template for L0-solution-architect.
Read by the agent with the GitHub reader at every run (template_repo / template_branch / template_folder).
Everything after this comment is the document skeleton. Headings, table headers and callout titles are fixed;
each {…} block is a fill rule the agent must follow, not text to copy. Section keys are a contract cited by
change briefs, HLDs, the impact assessor and conformance: never rename or renumber them.
-->

# {Product} Solution Architecture

**{Product} · Solution Architecture** · Status: **Draft · for review**{ · Approval blocked by `PEND-nn`, one per PEND row that blocks approval}

*Application, data, integration, platform and security architecture for the {product} {one-noun description} on {cloud}*

{EA11 missing or not APPROVED: "**The approved platform catalogue is
{missing | not approved}.** Every technology below is *Requires
validation* until the Platform Lead confirms it." Target read as DRAFT:
"**Written against a draft target ({version}).** Nothing here may be
approved until the target is."}

## 1. Purpose and Scope

> **PURPOSE**
>
> {3–4 sentences: what the product is; that the document says which
> components make up the application, where every entity is stored and
> how long it is kept, how components communicate and fail, and what
> platform it runs on; that it realises target-architecture.md
> {version} and names the technology for each demand.}

> **KEY ARCHITECTURAL ASSUMPTION**
>
> {2–4 sentences: the decomposition is fixed by the target (count the
> contexts and services); the cloud and region; event-driven by default
> with named budgeted synchronous calls; the longest retention and its
> immutability.}

### What this document covers

#### In scope
{Four bold-led bullets — Application, Data, Integration, Platform and
security architecture — each with its contents in one line.}

#### Not attempted here
{Bold-led bullets: domain decomposition (owned by the target); component
internals; full interface specifications (each service's OpenAPI
contract); delivery planning.}

> **READING THIS DOCUMENT**
>
> {One "**Section n** gives …" per section, joined by " · ". Then the
> keys this document mints, and that the keys CON-, R- or E-, INV-,
> SEC-, REQ-, QA- refer to the target.}

---

## Document Control and Contents

*Identity, ownership and approval of this document, and how it is organised*

| Attribute | Value |
|---|---|
{Rows: Document · Document type · Realises (target file and version) ·
Status (Draft, for review, and what blocks approval) · Lifecycle (written
at Cycle 0, edited in place, versioned by git) · Section owners (Solution
Architect: 2–7, 9–12 · Platform Lead: 8) · Change control · Technology
source (catalogue name, version, status) · Decision records (target ADR
range; SD-01 to SD-nn) · Classification (Internal).}

### Approvals
| Role | Responsibility | Signature | Date |
|---|---|---|---|
{Solution Architect, Platform Lead, Enterprise Architect (reviewer),
Information Security (reviewer), Data Protection (reviewer). Signature
and Date are left blank.}

### How to read the status tags
> **Ratified** the decision is recorded in an approved ADR. **Approved** the product is in the approved platform catalogue for {cloud} with lifecycle *adopt*, and this document selects it. **Requires validation** the decision is this document's recommendation; it needs confirmation by a named owner before it is relied on, and carries an open item. **Planned** the element is designed but not yet built. **Not yet built** a later delivery band needs the capability; the first change that needs it provisions it.

### Contents
{Anchor links to every numbered section and the appendix.}

---

## 2. Solution Overview

*What the solution is made of, and how its parts fit together*

> **ARCHITECTURE POSITION**
>
> {4–5 sentences: the components and runtime; one schema per stateful
> service; events by default, sync only where the target needs the
> answer in a request; the single ingress; where long-retention evidence
> lives.}

### Logical solution architecture
{FIGURE 1: Mermaid flowchart — channels, edge, domain services, platform.}

### Technology view
| Logical component | Technology | Decision |
|---|---|---|
{One row per capability decided at Cycle 0, citing its SD key and bold
status tag; a final **Not yet built** row for the deferred capabilities,
pointing to PLT-6.}

> **WHAT THIS OVERVIEW SHOWS**
>
> {2–3 sentences: the design's shape in one idea, e.g. a short request
> path and an immutable record path.}

---

## 3. Architectural Drivers and Principle Compliance

*The requirements, qualities and constraints the solution is designed against*

> **WHAT DRIVES THE DESIGN**
>
> {2–3 sentences: which forces are regulatory or commercial, and the
> order in which they shape the design.}

### Quality attributes and how they are met
| Key | Attribute and target | Primary mechanism | Section |
|---|---|---|---|
{Target defines QA-nn → one row per QA-nn by its id and figure, then one
per remaining REQ-* budget keyed by its REQ id; mint no QA id. Target
defines none → QA-01 upward, one per REQ-* budget, ES4 tier or retention
demand. The figure is the source's; a target figure marked Requires
validation stays so, citing its PEND; a budget split shows its
arithmetic; a deferred capability's row says **Not yet built**.}

### Principle compliance
| Principle | Position | How the solution complies |
|---|---|---|
{Every principle in the target's table, by its id. Position
**Satisfied** or **Recorded deviation** (name the DEV, and the PEND if
its scope is open). A principle the target assigns to this document
("Applied in HL-ARC-002") is answered here with the decision.}

### Constraints honoured
| Constraint | How the solution honours it | Section |
|---|---|---|
{Every target CON-*, once, in order — the constraint realisation table
conformance reads.}

---

## 4. Application Architecture

*Components, their responsibilities and dependencies, and the patterns they share* · Keys: `APL`

### APL-1 · Component catalogue
| Component | Kind | Context | Owns (schema) | Status | Realises |
|---|---|---|---|---|---|
{Every APP-2 component, plus any platform component the target names
(e.g. batch-runner). **Planned**, or **Not yet built** with its band.}

### APL-2 · Service designs
{One `####` per service: an italic "Context · role" line, then bold-led
**Responsible for.** **Not responsible for.** (both from APP-2)
**Provides.** (contracts and topics) **Depends on.** (edges, with
failure behaviour) **Notable rules.** (the INV-* it enforces). No module
names: internals belong to the component's design.}

### APL-3 · Experience and platform components
| Component | Design | Rules it must keep |
|---|---|---|

### APL-4 · Cross-cutting application patterns
| Pattern | Design | Why |
|---|---|---|
{Outbox, idempotent consumers and commands, bounded retries and dead
letters, timeouts and circuit breakers, money representation,
correlation, expand and contract — each with a one-clause reason.}

#### Failure policy
| Dependency failure | Behaviour | Reason |
|---|---|---|
{One row per synchronous dependency class, plus one for event consumers.
Behaviour opens with **Fail closed**, **Degrade** or **Retry**. A
degraded mode marks the value unavailable or pending, never a
remembered, cached or last-known value owned by another context —
holding one is an undeclared copy.}

---

## 5. Data Architecture

*Where every entity lives, who owns it, how long it is kept, and how it may be shared* · Keys: `DAT`

> **DATA POSITION**
>
> {2–3 sentences: one owner and one home per entity; references by id;
> the only sanctioned copies.}

### DAT-1 · Entity ownership
{One line: every entity's status and residency if uniform, and any
context that owns no entity.}
| Entity | System of record | Classification | Personal data | Retention | Residency | Physical location |
|---|---|---|---|---|---|---|
{Every entity in the target's APP-1 ownership lists, exactly once, in
context order. Personal data **Yes** per the Residency rule (names or
describes a person, references a buyer or producer, holds an address or
destination; personal and financial, e.g. an invoice or payout, is
Yes), else **No** — decided for every row before Residency, which then
follows mechanically: No → the approved region; Yes → the approved
region only if it meets the target's residency demand, else **Requires
validation** (`PEND-nn`). Classification: the ES2 class, then the class
the target's data-security control names (" · PII", " · Regulatory",
" · Financial"); a class the target states for an entity (e.g.
"classification Financial" for tax rates) binds and is never
downgraded. Retention: a period with a source — a REQ-DAT budget, a
target QA figure or the group retention standard — applied only to the
entities that source names; otherwise "To be set (`PEND-nn`)". A
description is not a period: "operational", "no statutory minimum",
"until account closure", "order lifecycle and dispute window" read "To
be set".}

#### Data rules
| Rule | Statement |
|---|---|
{DAT-1.a one system of record (realises CON-…); DAT-1.b references by id
only, except the target's sanctioned exceptions (realises CON-…); then
one DAT-1.x per invariant or constraint needing a data mechanism. Keep
letters the target already cites (e.g. DAT-1.e for snapshots). Each
statement ends "Realises `…`."}

#### Snapshot register
| Key | Source (owner) | Capturing column | Frozen by | Captured through |
|---|---|---|---|---|
{Only if the target sanctions snapshots. SN-01 upward, one row per value
the target says is captured: every invariant capturing a value under
the snapshot constraint, and every interaction saying "frozen as a
snapshot" or "captured for" an immutable record. The capturing column
belongs to the context holding the immutable record, is captured
through the owner's contract over an INT-1 edge, and sits on the table
whose row the value describes (a per-listing price and title belong on
the order line, not the order header). The target counts more
snapshots or read models than it names → a > **REGISTER GAP** callout
and a PEND row; never invent the missing ones.}

#### Integrity mechanisms
| Concern | Mechanism | Realises |
|---|---|---|
{One row per invariant needing more than one safeguard, mechanisms
numbered. At least one must not depend on database privileges or on the
application behaving — e.g. a copy in the locked evidence archive,
naming the step that writes it (an outbox-fed archive writer). Two
mechanisms an administrator can both undo (an application rule and a
revocable grant) count as one. Then a > **WHY MORE THAN ONE MECHANISM**
callout.}

### DAT-2 · Event envelope and schema governance
{One line: every event carries the envelope; satisfies/realises keys.
The envelope as a JSON block (event_id, event_type
`{context}.{entity}.{event}`, schema_version, occurred_at,
correlation_id, producer, payload). A Change | Rule table, then:}
| Event type | Version | Producer | Consumers | Kind | Status |
|---|---|---|---|---|---|
{Every event the target's contexts publish. A consumer is listed only
when a target R-nn names it, else "None registered". A context never
consumes its own event: an internal projection (e.g. a search index) is
built inside the owning context, not over the topic. A consumer the
design needs but the target does not name is an open item against the
target, never an added row.}

### DAT-3 · Classification, residency and retention
| Class | Handling tag | Retention | Residency | At rest | Erasure request |
|---|---|---|---|---|---|
{One row per ES2 class in use, split by handling tag where the target's
SEC-2 tags differ in treatment. Erasure: Not applicable / Honoured /
Honoured, except … / **Overridden**: statutory — recorded once, here.
Then a Store | Classification applied | Notes table, backups included.
SEC-2 names classes ES2 does not → a
> **THE CLASS MAPPING NEEDS CONFIRMATION** callout and a PEND row.}

### DAT-4 · Operational and analytical planes
| Plane | Realisation | Status |
|---|---|---|
{Operational, object, evidence archive, analytical. An undecided plane
is **Not yet built** with its band, or the target's trigger quoted. Then
one callout on why it waits.}

#### Registered analytical read models
| Key | Read model | Owner | Joins | Freshness | Status |
|---|---|---|---|---|---|
{Only if the target registers read models: one row each, by the target's
key (e.g. R-11). Owner and joins as the target states them; freshness
is the target's figure, surfaced to consumers as an "as of" time; status
follows the analytical plane. Read models are not INT-1 rows.}

---

## 6. Integration Architecture

*How components communicate, which contracts they publish, and what each interaction costs* · Keys: `INT`

### INT-1 · Interaction style rules and decisions
> **THE RULE**
>
> {When a call may be synchronous, in numbered conditions; that every
> synchronous call has a budget, a timeout and a failure behaviour; that
> the degraded mode is a refusal or a deferral. "Satisfies `REQ-INT-…`."}

{FIGURE 2: Mermaid flowchart of the synchronous edges, labelled "R-nn ·
budget"; thick arrows fail closed, thin arrows degrade.}

| Edge | Interaction | Style | Budget p95 | On failure | Why this style | Permitted by |
|---|---|---|---|---|---|---|
{Every request and event in the target's interaction table exactly once,
in order, by its key (R-nn, or E-nn); read models go to DAT-4.
Interaction reads caller → provider for calls, publisher → consumer for
events, whatever the target's column order. An interaction with two
moments (fail-closed at checkout, degrading at basket view) states both
in one row. Budget: the REQ-INT figure, a share of a parent budget with
the arithmetic, or "`PEND-nn`". Permitted by: the DEV or CON exception,
"Default" for events, or the PEND carrying an unpermitted sync edge.}

{A callout for any open principle-scope question, and a
> **REJECTED ALTERNATIVE** callout for the main style alternative.}

### INT-2 · API architecture and contracts
{One line with satisfies/realises keys. #### API standards (bullets:
OpenAPI per service, versioning, consumer-driven verification enumerated
from consumers, authentication declared per operation, p95 declared
before release). #### Gateway and routing (bullets). Then "Operation
paths are indicative; each service's OpenAPI contract is
authoritative." and:}
| Contract · operation | Owner | Consumers | Auth | Budget p95 | Status |
|---|---|---|---|---|---|
{One row per synchronous edge's contract plus the experience-facing
surface: logical name in bold, then an indicative `VERB /v1/resource`.}

### INT-3 · Event architecture
{One line with satisfies keys. FIGURE 3: Mermaid flowchart of producers,
topics and consumer groups. Then:}
| Topic | Producer | Consumer groups | Ordering key | Retention | Status |
|---|---|---|---|---|---|
{Consumer groups follow the DAT-2 consumers exactly. Retention is a
transport replay window, never an entity's retention demand: a
long-retention record lives in its store and the evidence archive. A
window no source states reads "To be set (`PEND-nn`)". Then a Standard |
Rule table: naming (checked by a guardrail), delivery, failure, no
reflexive events.}

---

## 7. End-to-End Flows

*The lasting critical paths of the solution, component by component* · Keys: `INT-4`

> **HOW TO READ THE FLOWS**
>
> {Solid arrows are calls, open arrows are deliveries, budgets are p95,
> failures follow APL-4; a change's flow lives in its brief until it is a
> lasting critical path.}

| Flow | Path | Components | Key rules |
|---|---|---|---|
{FLOW-00 the Cycle 0 exit flow, then FLOW-01 upward, at most four: a
request path with two or more synchronous calls, a path that writes
money or tax evidence, any path with a fail-closed dependency. Name the
flows not drawn in one line.}

{One `####` per flow: a Mermaid sequenceDiagram with autonumber, then a
caption stating the budget arithmetic or the rule the flow proves.}

---

## 8. Platform Architecture

*Where the solution runs, how it is delivered, observed and recovered* · Keys: `PLT`

### PLT-1 · Hosting and runtime topology
{One line with satisfies/realises keys. A > **CLOUD AND REGION** callout:
the cloud, where the choice came from, the primary and backup regions.}
| Runtime element | Configuration | Decision | Satisfies |
|---|---|---|---|
{One row per capability decided at Cycle 0.}
| Choice | Rejected | Reason |
|---|---|---|
{One row per rejected alternative, the reason tied to a budget, a
lifecycle status or an estate rule.}

### PLT-2 · Environments
| Environment | Purpose | Data | Promotion |
|---|---|---|---|
{Then one paragraph: region, promotion by artifact, the production gate,
and the separate penetration-test gate before the first external user.}

### PLT-3 · Delivery pipeline and Cycle 0 exit proof
{FIGURE: Mermaid flowchart LR of the pipeline stages, ES5 controls
included. Every stage names its tool (build, dependency, vulnerability
and secret scanning, test, contract verification, registry, deploy),
and each tool is a PLT-6 row. Bold-led bullets: credentials, secrets,
migrations, IaC.}
#### Cycle 0 exit proof
| Criterion | Evidence required | Status |
|---|---|---|
{The trivial-service criteria, plus one row per **Available** PLT-6 row,
each **Not yet proven**. End: "The Platform Lead signs this proof. This
document never marks it proven."}

### PLT-4 · Observability
{One paragraph: telemetry, correlation, SLO policy, alert routing to the
owning context. A Signal | Alert when table — thresholds are a declared
budget or a REQ figure, never an invented duration. One callout naming
the signal that matters most.}

### PLT-5 · Resilience, retention and recovery
| Component | Availability | Basis | RTO · RPO |
|---|---|---|---|
{Each component in exactly one row. A figure is the target's, or a group
availability tier whose condition actually holds for that component's
records: 99.9% only for records needed in active regulatory defence
(e.g. an FBO-status dispute), 99.5% for legally relevant audit records,
best-effort only for reporting pipelines feeding the group data
warehouse. No condition fits → "To be set (`PEND-nn`)".}
| Tier | Store | Window | Immutability |
|---|---|---|---|
{Then a > **WHY BOTH TIERS ARE NEEDED** callout when a long-retention
demand uses two tiers.}

### PLT-6 · Platform capability register
{One line: every change is checked against this register, and a change
needing a **Not present** capability is raised to Tier 2.}
| Capability | Status | Since | Provided by | Lifecycle | Satisfies | Band and notes |
|---|---|---|---|---|---|---|
{One row per capability the target demands (each REQ-PLT-*, or each § 4.2
class in the older layout), plus estate capabilities. **Available** /
**Partial** / **Not present**. An Available row names in "Band and
notes" the Cycle 0 test it meets: **Exit proof** (only the fixed list in
processing rule 2c), **High lead time** (with the source of the lead
time), **Cross-context policy** or **Band First**; meeting none means
**Not present**, however useful. A Not present row names its candidate,
the band or change that needs it, and any lead-time warning. A candidate
is a specific named product on the chosen cloud (or Any); none
catalogued for that cloud → "No catalogued candidate", lifecycle
`unverified`; another cloud's product is never a candidate. A product
the catalogue lacks is named here as a candidate, not raised as an open
item, unless Cycle 0 itself needs it.}

---

## 9. Security Architecture

*Trust boundaries, implemented controls, and the obligations before launch* · Keys: `SEC`

### Trust boundaries
{FIGURE: Mermaid flowchart of the TB-* crossings.}

### Implemented controls
| Control | Implementation | Status |
|---|---|---|
{Every target SEC-* and, where defined, TB-*, by its target id.
**Planned** for a control Cycle 0 builds, **Not yet built** for one a
later change builds; an absent row is not valid. Never mint a SEC or TB
id.}

### Security obligations before launch
| Obligation | Applied as |
|---|---|
{From the security KB: identity, server-side enforcement, attributable
access, scanning, penetration test, identity review, incident response.}

---

## 10. Requirements Traceability

*Every demand in the target, and the section of this document that realises it*

| Demand | Summary | Realised in | Status |
|---|---|---|---|
{Every target REQ-*, in id order, exactly once. **Met** or **Not yet
built** by the Met rule (processing rule 12), then " · " and the band or
PEND key where one applies, naming what a Not yet built demand waits
on. This is the satisfaction index conformance reads. End with a line
for any REQ id the target leaves unassigned.}

---

## 11. Risks, Assumptions and Open Items

*What could undermine the design, what it takes for granted, and what remains to be decided*

### Risks
| Key | Risk | Rating | Mitigation |
|---|---|---|---|

### Assumptions
| Key | Assumption |
|---|---|

### Open items
| Key | Item | Owner | Resolved when | Blocks |
|---|---|---|---|---|
{PEND-01 upward, numbered without gaps, every row that blocks approval or
the Cycle 0 exit before any that blocks nothing. Item cites the target
open item it carries (OI-nn; older OQ-nn). "Resolved when" is a
condition (a requirement, an event, a gate), never a date. Blocks:
**Approval**, **Cycle 0 exit** or —. A capability deferred to a later
band is NOT an open item; it lives in PLT-6. Every open target question
and every contradiction in the target's own record is an open item
here, and stated in full nowhere else.}

---

## 12. Decision Summary and Implementation Implications

*The solution decisions this document records, and what they mean for delivery*

| Key | Decision | Basis | Status |
|---|---|---|---|
{SD-01 upward: every technology selection and every design decision a
reader could reasonably dispute. Basis: a REQ, CON, ADR, principle or the
catalogue — never contradicting an open item or risk in this document
(a cloud chosen while a residency open item names another cloud states
that gap, never "no reason favours" the other).}

#### Implementation implications
{Bullets: what every later change must do because of these decisions.}

#### What design still must answer
{Bullets grouping the open items by theme, citing PEND keys.}

> **BOTTOM-LINE POSITION**
>
> {2 sentences: the design's real risk, and where it spends its
> complexity.}

| Question | Architecture answer |
|---|---|
{4–5 questions a reviewer would ask, each answered in one or two
sentences.}

---

## Appendix A. Glossary and Abbreviations

*Terms and abbreviations used in this document*

| Term | Meaning |
|---|---|
