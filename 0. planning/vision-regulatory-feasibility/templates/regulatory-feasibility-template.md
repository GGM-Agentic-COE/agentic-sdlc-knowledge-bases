<!--
regulatory-feasibility-template.md — the Document Template for L0-regulatory-feasibility-checker.
Read by the agent with the GitHub reader at every run (template_repo / template_branch / regulatory_feasibility_template_folder).
Layout follows the same house style as the product vision template (DairyShield DS-VIS-001). Everything after this
comment is the document skeleton. Headings, callout titles and table headers are fixed; each {…} block is a fill rule
the agent must follow, not text to copy, and none may survive into the finished document.
L0-regulatory-feasibility-checker-evaluator and L0-vision-statement-generator read the finished document by these
headings and table rows: never rename or reorder them.
-->

# {idea_title} Regulatory Feasibility Assessment

# {idea_title} · Regulatory Assessment · Status: **Draft**

*{one line: the idea in the brief's own terms, followed by ": the obligations that apply, who carries them, how each is addressed, and the viability score that gates the vision"}*

---

## 1. Purpose and Scope

> **PURPOSE**
>
> This document assesses whether {idea_title} can be launched in {the jurisdiction assessed} under the law that applies there. It identifies **every regulatory obligation that applies, and to whom**, classifies each **Green, Amber or Red**, sets out **how each Amber and Red obligation is addressed**, and derives the **viability score** that gates the product vision. It is not a legal opinion.

> **KEY ASSUMPTION**
>
> {the assumption the assessment most depends on — normally who performs the regulated activity (the obligated party) — with the brief's own sentence that decides it, quoted; or "The brief does not say who {the activity}", with the OI key that resolves it}

### What this document covers

#### In scope

- **Jurisdiction**, resolved against the brief's target geography.
- **Every coverage category** in the regulatory frameworks index, as a constraint or as not applicable.
- **A mitigation** for every Amber and Red constraint, or a legal-review flag.
- **The viability score** and the caps that bind it.

#### Not attempted here

- **Legal opinion**: every constraint flagged "requires legal review" needs counsel.
- **Product vision, market and roadmap**: these belong to the vision document.
- **Jurisdictions the knowledge bases do not cover**{": " and their names — only when the brief names any; otherwise end the line at "cover"}.

> **READING THIS DOCUMENT**
>
> **Section 2** states the overall position and the score · **Section 3** the jurisdiction assessed · **Section 4** every constraint, with its regulation, obligated party, rationale and mitigation · **Section 5** the categories that do not apply · **Section 6** how the viability score was derived · **Section 7** the open items. Items carry stable keys: constraints `CON-` and open items `OI-`.

---

## Document Control and Contents

*Identity, sources and approval of this document, and how it is organised*

| Attribute | Value |
| --- | --- |
| **Document ID** | {2-4 capital letters abbreviating the idea title}-REG-001 · version 1.0 |
| **Document type** | Regulatory feasibility assessment |
| **Idea** | {idea_title} |
| **Source 1** | source agent: `L0-idea-intake`, Date: {a date field in idea-brief.json (generated_date or date, at the root or under content/items), as yyyy-mm-dd. idea-brief.json usually carries NO date, and then this is the Generated date below. Never leave it empty} |
| **Status** | Draft |
| **Generated** | {current_date as resolved in Input Ingestion — yyyy-mm-dd read from the current date tool (UTC), or "not available"; never copied from an example, a fixture, or this template} |
| **Target geography** | {geography, as stated in the brief} |
| **Jurisdiction assessed** | {the country the KBs declare, plus any sub-national layer used as the basis} |
| **Overall status** | **{Green, Amber or Red}** |
| **Viability score** | {n}/10 |
| **Classification** | Internal |

### Approvals

| Role | Responsibility | Signature | Date |
| --- | --- | --- | --- |
| **Legal and Compliance** | Reviewer; confirms the constraints and every "requires legal review" item |  |  |
| **Product Lead** | Approver; accepts the regulatory posture and the viability score |  |  |

### How to read the status tags

> **Green** no obstacle identified. **Amber** feasible, with an obligation or uncertainty to resolve. **Red** blocked until resolved, or a decision only counsel can make. **Not applicable** assessed, and does not apply to this idea.

### Contents

- 1\. Purpose and Scope
- Document Control and Contents
- 2\. Feasibility Summary
- 3\. Jurisdiction
- 4\. Constraints Assessed
- 5\. Categories Assessed and Not Applicable
- 6\. Viability Score
- 7\. Open Items
- Appendix A. Glossary

---

## 2. Feasibility Summary

*The overall position, and what drives it*

> **OVERALL POSITION · {GREEN, AMBER or RED}**
>
> {one-line rationale — driven by the worst individual constraint, not an average — naming its CON key}

| Dimension | Status | Basis |
| --- | --- | --- |
| **Overall status** | **{Green, Amber or Red}** | {the worst constraint and its CON key; or, when every Amber and Red has a precedented mitigation, why the verdict is one level better} |
| **Viability score** | **{n}/10** | {auto_publish_eligible or human_review_required against the threshold of 7, and the binding cap or lowest component} |

> **BOTTOM LINE**
>
> {two sentences: whether the idea is feasible in this jurisdiction, and the one thing that must be settled first, with its CON or OI key}

---

## 3. Jurisdiction

*Which law this assessment applies, and why*

| Attribute | Value |
| --- | --- |
| **Target geography (brief)** | {target_geography, as the brief states it} |
| **Jurisdiction declared by the knowledge bases** | {each KB's #jurisdiction declaration — country and the sub-national layers in scope} |
| **Basis of this assessment** | {same country / a sub-national region of it / vague geography read as the KBs' jurisdiction — and the sub-national layers raised as constraints or open items} |

---

## 4. Constraints Assessed

*Every obligation that applies, who carries it, and how it is addressed* · Keys: `CON`

### Obligation register

| Key | Obligation | Regulation | Status | Obligated party | Mitigation |
| --- | --- | --- | --- | --- | --- |
| **CON-01** | {constraint_name} | {specific regulation/section} | **{Green, Amber or Red}** | {proposer, the named party, or unresolved} | {the mitigation in one line, "requires legal review", or "—" for Green} |
{one row per constraint, CON-01, CON-02… with no gaps — minimum: authorisation/licensing, data protection, and anything specific to the target user segment or product category}

### CON-01 · {constraint_name} — {Green, Amber or Red}

**Regulation:** {specific regulation/section}
**Obligated party:** {proposer | <the named customer, user or partner> | unresolved — <the fact that would resolve it>} — {for proposer or a named party: the brief's own sentence that decides it, quoted; for unresolved: "brief does not say"}
**Rationale:** {why this status was assigned — at most 2 sentences}
**Mitigation:** {required if Amber/Red — concrete recommendation in at most 2 sentences, or "requires legal review"}

{repeat one block per constraint, in the same order and with the same keys as the register}

---

## 5. Categories Assessed and Not Applicable

*Coverage categories that were swept and do not apply to this idea*

| Category | Why it does not apply |
| --- | --- |
| **{category}** | {the reason, in at most 12 words} |
{one row per swept category that does not apply. When every category applies, replace the table with the line "Every coverage category applies to this idea."}

---

## 6. Viability Score

*How the score that gates the vision was derived*

**Score:** {n}/10 — {auto_publish_eligible | human_review_required} against the threshold of 7

| Component | Weight | Score | Traced to |
| --- | --- | --- | --- |
| Regulatory posture | 0.60 | {n} | {CON ids} |
| Idea clarity | 0.40 | {n} | {idea-brief.json fields} |

**Weighted before caps:** {n}
**Caps applied:** {rule → cap value, triggered by {ids}; or "none"}
**What would raise the score:** {the specific change that would lift the lowest component or clear the binding cap. For every Red or capping constraint, name the resolving fact or action and say which kind it is: a brief gap (supply the fact and re-run) or a real-world step (obtain the approval, restructure, legal review)}

---

## 7. Open Items

*What is still to be decided, and by whom* · Keys: `OI`

| Key | Item | Type | Resolved by |
| --- | --- | --- | --- |
| **OI-01** | {the open item} | {requires legal review, unresolved obligated party, unresolved threshold, sub-national layer, or deferred by the brief} | {who or what resolves it} |
{every open question: anything flagged "requires legal review", every unresolved obligated party or threshold, every sub-national layer raised per Section B, and every item the brief itself deferred. When there are none, replace the table with the line "No open items."}

---

## Appendix A. Glossary

*Terms and abbreviations used in this document*

| Term | Meaning |
| --- | --- |
| **{term}** | {meaning} |
{every regulator, statute abbreviation and domain term the document uses, in alphabetical order}
