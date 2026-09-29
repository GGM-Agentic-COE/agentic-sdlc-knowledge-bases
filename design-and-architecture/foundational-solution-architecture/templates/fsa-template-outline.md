# Foundational Solution Architecture — Document Template

## 0. How to use this template

## 1. Document control
### 1.1 Version history

## 2. Purpose, ownership and boundaries
### 2.1 Purpose statement
### 2.2 What this document owns
### 2.3 What this document must never contain
### 2.4 Position in the lifecycle

## 3. Inputs and provenance
### 3.1 Inputs consumed [BOTH]
### 3.2 Additional inputs for brownfield retrofit [BF]
### 3.3 Requirement coverage map

## 4. PRIN — Binding principles applied
### 4.1 Declared deviations

## APP-1 — Bounded contexts
### APP-1.0 Context map (summary)
### APP-1.1 Context register
### APP-1.2 Context cards
### APP-1.3 Cross-cutting concerns that are deliberately not contexts

## APP-2 — Component map and responsibility allocation
### APP-2.1 Component register
### APP-2.2 Responsibility allocation rules
### APP-2.3 Context interaction map
### APP-2.4 Derived epic seed

## APP-3 — Aggregate boundaries and invariants
### APP-3.1 Aggregate register
### APP-3.2 Invariant register
### APP-3.3 Consistency boundaries

## CON — Cross-viewpoint constraints
### CON.1 Rule precedence

## REQ-DAT / REQ-INT / REQ-PLT — Demands on the three viewpoints
### REQ-DAT — Demands on the Data viewpoint
### REQ-INT — Demands on the Integration viewpoint
### REQ-PLT — Demands on the Platform viewpoint
### REQ.1 Demand coverage check

## SEC — Security overlay
### SEC.1 Trust boundaries
### SEC.2 Data classification scheme
### SEC.3 Controls required per viewpoint
### SEC.4 Regulatory obligations traced

## OPEN — Deferred structural decisions with trigger conditions
### OPEN.1 Firing record

## 12. Derived planning outputs
### 12.1 Component dependency graph (seed)
### 12.2 Epic seed with sequencing constraints
### 12.3 Capability classes the platform must provide before the first feature cycle
### 12.4 What each downstream agent reads from this document

## 13. Options considered and rejected

## 14. Assumptions, risks and open questions
### 14.1 Assumptions
### 14.2 Risks to the decomposition
### 14.3 Open questions

## 15. ADR register and change control
### 15.1 Governing ADRs
### 15.2 What moves this document, and how far
### 15.3 Structural delta procedure
### 15.4 Convergence verdict reference [BF]

## Appendix A — Brownfield retrofit: baseline reconciliation [BF]
### A.1 Recovery method
### A.2 Reconciliation by section ID
### A.3 Legacy decisions adopted into the target
### A.4 Legacy decisions explicitly rejected by the target
### A.5 Retrofit gate additions

## Appendix C — Brownfield structural delta: target move package [BF]
### C.1 Trigger record
### C.2 Delta record
### C.3 Sections touched
### C.4 Completeness check for a target move — BLOCKING
### C.5 Expected conformance effect
### C.6 Gate record for this move

## Appendix B — Authoring agent self-check and quality gate
### B.1 Content boundary — BLOCKING
### B.2 Completeness — BLOCKING
### B.3 Consistency — BLOCKING
### B.4 Quality — review findings
### B.5 Anti-patterns the reviewer looks for


