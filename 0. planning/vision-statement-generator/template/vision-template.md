<!--
vision-template.md — the Document Template for L0-vision-statement-generator.
Read by the agent with the GitHub reader at every run (template_repo / template_branch / vision_statement_template_folder).
Layout modelled on the DairyShield Product Vision (DS-VIS-001). Everything after this comment is the document
skeleton. Headings, callout titles and table headers are fixed; each {…} block is a fill rule the agent must follow,
not text to copy, and none may survive into the finished document. L0-vision-statement-generator-evaluator checks
the finished document against this layout and publishes it to Confluence: never rename or reorder the headings.
Grounding: every statement comes from idea-brief.json, regulatory-feasibility.md or market-analysis.md. Where they
say nothing, write that it is not stated and raise an open question — never fill a section with plausible content.
-->

# {product_name} Product Vision

# {product_name} · Product Management · Status: **Draft**

*{one line: what the product is, in the brief's own terms, followed by ": the problem, the users, the product, its regulatory position, and the phased path to a measured rollout"}*

---

## 1. Purpose and Scope

> **PURPOSE**
>
> This document sets out the product vision for {product_name}. It defines **the problem {product_name} solves and for whom**, **what the product does and what it deliberately does not do**, **how success will be measured**, and **what must be true, commercially and regulatorily, before it scales**. It is the reference against which requirements, architecture and delivery plans are judged. It does not specify requirements or design.

> **KEY PRODUCT ASSUMPTION**
>
> {the single assumption the regulatory posture most depends on — normally the operating model or obligated party regulatory-feasibility.md leaves unresolved — what changes if it does not hold, and the phase that resolves it, with its OR key. If regulatory-feasibility.md leaves nothing unresolved, state the operating model the brief describes and say it is taken from the brief}

### What this document covers

#### In scope

- **Vision, problem and opportunity.**
- **Users, customers and stakeholders**, and the value each receives.
- **Product concept, capabilities and scope boundaries.**
- **Regulatory posture**{ " under both operating models" — only when Section 8 has the Two operating models table}.
- **Success measures, phased roadmap, risks and open questions.**

#### Not attempted here

- **Functional requirements** and acceptance criteria.
- **Architecture and technology** choices.
- **Commercial model and pricing**{", which remain open (`OQ-NN`)" — only when an open question covers them}.
- **Legal opinion**: the regulatory posture needs confirmation by counsel.

> **READING THIS DOCUMENT**
>
> **Section 2** states the vision, summary and product principles · **Section 3** the problem and opportunity · **Section 4** the users, customers and stakeholders · **Section 5** the product concept and capabilities · **Section 6** the scope · **Section 7** the market context · **Section 8** the regulatory posture · **Section 9** the success measures · **Section 10** the roadmap · **Section 11** the risks, assumptions and open questions · **Section 12** the decisions required. Items carry stable keys: capabilities `CAP-`, regulatory obligations `CON-`, metrics `NSM-`, risks `OR-` and open questions `OQ-`.

---

## Document Control and Contents

*Identity, ownership and approval of this document, and how it is organised*

| Attribute | Value |
| --- | --- |
| **Document ID** | {2-4 capital letters abbreviating the product name}-VIS-001 · version 1.0 |
| **Document type** | Product vision |
| **Product** | {product_name exactly as resolved. If you proposed it: "{proposed name} (proposed by agent — no product name was supplied; confirm or replace)"} |
| **Owner** | Product Owner, {product_name} |
| **Approver** | {the approver's name only when the request or an upstream document names one; otherwise "Product Lead"} |
| **Status** | Draft |
| **Source 1** | source agent: `L0-idea-intake`, Date: {a date field in idea-brief.json (generated_date or date, at the root or under content/items), as yyyy-mm-dd. idea-brief.json usually carries NO date, and then this is the Generated date from regulatory-feasibility.md's Document Control table. Never leave it empty} |
| **Source 2** | source agent: `L0-regulatory-feasibility-checker`, Date: {the Generated row of regulatory-feasibility.md's Document Control table} |
| **Source 3** | source agent: `L0-vision-market-analyzer`, Date: {the Generated row of market-analysis.md's header table. Delete this row when no market analysis was read} |
| **Change control** | Revised at the end of each roadmap phase, or when an open risk changes the product's operating model |
| **Classification** | Internal |

### Approvals

| Role | Responsibility | Signature | Date |
| --- | --- | --- | --- |
| **{the Approver, as in Document Control}** | Approver; authorises progression to the next phase |  |  |
| **Product Owner** | Owner of the vision |  |  |
| **Legal and Compliance** | Reviewer; regulatory posture (Section 8) |  |  |
| **Data Protection Officer** | Reviewer; personal data handling{ — keep this row only when Section 8 lists personal data in scope} |  |  |

### How to read the status tags

> **Green** no obstacle identified. **Amber** feasible, with an obligation or uncertainty to resolve. **Red** blocked until resolved. **To be baselined** the measure is defined but has no target yet. **Not assessed** no evidence is available yet.

### Contents

- 1\. Purpose and Scope
- Document Control and Contents
- 2\. Vision and Summary
- 3\. Problem and Opportunity
- 4\. Users, Customers and Stakeholders
- 5\. Product Concept and Capabilities
- 6\. Scope
- 7\. Market Context
- 8\. Regulatory Posture
- 9\. Success Measures
- 10\. Roadmap
- 11\. Risks, Assumptions and Open Questions
- 12\. Decisions Required
- Appendix A. Glossary

---

## 2. Vision and Summary

*Where {product_name} is going, and the position it starts from*

> **VISION STATEMENT**
>
> **{one sentence: the outcome the product exists for, in the brief's own terms.}** {one or two sentences: how the product changes the situation in the problem statement, and who stops bearing the cost}

### Summary

{one paragraph, written LAST: what the product is, what it gives each user, and what data it works from. Every claim must already appear in a later section}

### Product principles

- **{principle, as a short imperative}.** {one sentence, ending with the key it rests on in parentheses — (`CON-NN`), (`OR-NN`) or (idea brief)}
{3-6 principles. Each restates a position a later section already takes; never a principle no later section supports}

### Feasibility position

| Dimension | Status | Basis |
| --- | --- | --- |
| **Regulatory** | **{overall status carried verbatim from regulatory-feasibility.md} · {viability_score exactly as received} / 10** | {what holds the score where it is — the binding cap or the lowest component, and the OR key of the constraint behind it — from regulatory-feasibility.md's Viability Score section} |
| **Technical** | **{Amber, Green or Not assessed}** | {the technical dependencies the brief or regulatory-feasibility.md names, with their OR or OQ keys; "Not assessed — no technical assessment is available" when neither names any} |
| **Market and commercial** | **{Not assessed, or the market analysis's overall verdict}** | {"No market analysis or commercial model is yet available (Section 7)" when none was read; otherwise one line from market-analysis.md} |

> **BOTTOM LINE**
>
> {two or three sentences: whether the product is worth taking to the next phase, and the one or two things that must be settled first — the same items as Section 12's "What must still be answered", the most severe open risk among them}

---

## 3. Problem and Opportunity

*Why {the problem, in a short phrase}, and who pays for it*

> **THE PROBLEM**
>
> {the problem statement carried forward from idea-brief.json without drift: what happens today, where it fails, and who bears the cost}

### Consequences

| Who | What the problem costs them |
| --- | --- |
| **{a user or stakeholder the brief names}** | {the cost the brief describes for them} |
{one row per affected party the brief names}

### The opportunity

{one paragraph from the brief's value proposition: what already exists, what is missing, and how {product_name} fills the gap. No market figure unless market-analysis.md states it}

---

## 4. Users, Customers and Stakeholders

*Who uses {product_name}, who pays for it, and who else has a stake in it*

### Users

| User | Job to be done | What {product_name} gives them | Channel |
| --- | --- | --- | --- |
| **{target user from idea-brief.json}** | {their job, as the brief describes it} | {what the product gives them} | {the channel the brief names, or "To be confirmed (`OQ-NN`)"} |
{one row per target user in idea-brief.json}

### Customer

> **WHO BUYS {PRODUCT_NAME IN CAPITALS}**
>
> {who pays, as the brief states it, who uses the product through them, and how that relates to the obligated party in regulatory-feasibility.md. When the brief does not say who pays, say so and cite the open question}

### Stakeholders

| Stakeholder | Interest |
| --- | --- |
| **{a stakeholder the brief or regulatory-feasibility.md names — including each regulator that document names}** | {their interest, with the OR or CON key where one applies} |

---

## 5. Product Concept and Capabilities

*What {product_name} does, and how its parts work together* · Keys: `CAP`

> **VALUE PROPOSITION**
>
> For {the customer} who {the problem, in one clause}, **{product_name} is {what it is}** that {what it does}. Unlike {today's alternative, as the brief describes it}, it **{the differentiator the brief states}**.

### Capabilities

| Key | Capability | Description | Primary user |
| --- | --- | --- | --- |
| **CAP-01** | {capability} | {what it does} | {user} |
{one row per capability the brief describes (key_capabilities, value_proposition), CAP-01, CAP-02… with no gaps. Never a capability the brief does not describe}

---

## 6. Scope

*What is in the first release, what follows, and what {product_name} will not do*

#### MVP ({the roadmap phase that pilots it, e.g. "Phase 2 pilot"})

*{what the first release must prove, in a few words}*

- {the CAP keys in the first release, and any limit the brief or the roadmap sets}

#### Rollout release ({the roadmap phase that scales it})

*{what the rollout adds, in a few words}*

- {the CAP keys and integrations that follow}

#### Not in scope

*Deliberate boundaries*

- {a boundary the brief states, or a position regulatory-feasibility.md relies on, e.g. "Operating X, unless `OR-NN` decides otherwise". Never a boundary no upstream document supports}

---

## 7. Market Context

*What is known about the market, and what must be established before rollout*

> **STATUS: {NOT ASSESSED, or ASSESSED}**
>
> {No market analysis read: exactly "No market analysis is yet available. The product is therefore justified by the problem it solves, not by a sized opportunity. A market assessment is required before the rollout decision, and it should answer the questions below." With a market analysis: one paragraph condensing its single most decision-relevant insight}

| Key | Question | Why it matters |
| --- | --- | --- |
| **MQ-01** | {a question a market assessment must answer} | {the NSM, OQ or decision it feeds} |
{No market analysis: the questions only — never a figure, a rate or a benchmark. With a market analysis: the questions it left open, or delete the table when it left none}

---

## 8. Regulatory Posture

*Which obligations apply, to whom, and how each is addressed* · Keys: `CON`

> **OVERALL POSITION · {overall status carried verbatim from regulatory-feasibility.md, in capitals}**
>
> {two or three sentences condensing regulatory-feasibility.md's Feasibility Summary: whether the idea is feasible, what the posture depends on, and which obligations apply in every case}. This posture requires confirmation by legal counsel.

### Two operating models

{Keep this subsection ONLY when regulatory-feasibility.md leaves the obligated party or the operating model unresolved; otherwise delete this heading and its table}

| Obligation area | If {product_name} {the first model, e.g. "only provides software"} | If {product_name} {the second model, e.g. "also operates the cold chain"} |
| --- | --- | --- |
| **{obligation area}** | {who carries it under the first model} | {who carries it under the second model} |

### Obligation register

| Key | Obligation | Status | How it is addressed | Phase |
| --- | --- | --- | --- | --- |
| **CON-NN** | {the constraint name, with its regulation} | **{Green, Amber or Red}** | {its mitigation from regulatory-feasibility.md, condensed, with the OR key it maps to} | {the roadmap phase that addresses it} |
{one row per constraint in regulatory-feasibility.md, with the SAME CON ids in the same order — never renumbered, merged or dropped. Every Amber and Red constraint must appear. A count stated anywhere must equal these rows}

> **PERSONAL DATA IN SCOPE**
>
> {the personal data the brief or regulatory-feasibility.md says the product processes, and how each is handled. Delete this callout when there is none}

---

## 9. Success Measures

*How {product_name}'s impact is measured, and how targets will be set* · Keys: `NSM`

> **NORTH-STAR METRIC**
>
> **NSM-01 · {metric}.** This is the outcome the vision exists for. The other measures explain how it is achieved and whether users trust the product.

| Key | Measure | Type | Baseline | Target |
| --- | --- | --- | --- | --- |
| **NSM-01** | {measure} | North star | {where the baseline comes from, and in which phase} | **{a number an upstream document states, with that document named, or "To be baselined"}** |
| **NSM-02** | {measure} | {Outcome or Experience} | {baseline source} | **{same rule}** |

### Leading indicators

| Key | Indicator | Why it leads the outcome |
| --- | --- | --- |
| **LI-01** | {indicator} | {why it leads, with the OR key where it tracks a risk} |

{one sentence: when targets are set and approved, and that no quantitative claim of impact is made until then — citing the advertising or claims CON key when regulatory-feasibility.md has one}

---

## 10. Roadmap

*{the number of phases, in words} indicative phases, each ending in a decision*

| Phase | Objectives | Exit criteria | Resolves |
| --- | --- | --- | --- |
| **1 · {phase name}** | {objectives} | {exit criteria, separated by " · "} | {the OR keys it resolves, then the CON keys} |
{Phase 1 MUST resolve or de-risk the single most severe open risk. Every phase names the OR key it resolves. Timing is indicative unless an upstream document gives a date}

---

## 11. Risks, Assumptions and Open Questions

*What could stop {product_name}, what it takes for granted, and what is still to be decided*

### Open risks

| Key | Risk | Severity | Response | Phase |
| --- | --- | --- | --- | --- |
| **OR-01** | {the risk} | **{High for a Red constraint or the constraint capping the score; Amber otherwise}** | {the response — the mitigation from regulatory-feasibility.md} | {phase} |
{every Amber and Red constraint in regulatory-feasibility.md is covered by at least one row, naming its CON key; related constraints may share a row. The constraint capping the score is the most severe. Add a market weakness or threat only when a market analysis was read}

### Assumptions

| Key | Assumption |
| --- | --- |
| **AS-01** | {an assumption the brief states, or one the document already relies on — never a new fact} |

### Open questions

| Key | Question | Needed by |
| --- | --- | --- |
| **OQ-NN** | {question} | {phase or exit point} |
{the brief's open questions with their own ids, plus the open items in regulatory-feasibility.md that the vision still needs answered}

---

## 12. Decisions Required

*What the approver is asked to decide, and what remains open*

#### Positions this vision takes

- **Position 1:** {a position an earlier section already takes}
{3-6 positions}

#### What must still be answered

- {an unresolved item, with its OR or OQ key}

> **DECISION SOUGHT**
>
> Approve {product_name} to proceed to **Phase 1**, {with progression beyond it conditional on the Phase 1 exit criteria in Section 10}.

| Question | Answer |
| --- | --- |
| **What problem does {product_name} solve?** | {one line} |
| **Who pays, and who uses it?** | {one line} |
| **What is the biggest risk?** | {the most severe open risk — the constraint capping the score, where there is one} |
| **How will we know it works?** | {NSM-01, against its baseline} |
| **What is the controlling principle?** | {one line, from the product principles} |

---

## Appendix A. Glossary

*Terms and abbreviations used in this document*

| Term | Meaning |
| --- | --- |
| **{term}** | {meaning} |
{every abbreviation and domain term the document uses, in alphabetical order}
