<!--
regulatory-feasibility-template.md — the Document Template for L0-regulatory-feasibility-checker.
Read by the agent with the GitHub reader at every run (template_repo / template_branch / regulatory_feasibility_template_folder).
Everything after this comment is the document skeleton. Headings and table headers are fixed; each {…} block is a
fill rule the agent must follow, not text to copy. L0-regulatory-feasibility-checker-evaluator and
L0-vision-statement-generator read the finished document by these headings: never rename or reorder them.
-->

# Regulatory Feasibility Assessment: {idea_title}

| Field | Value |
|---|---|
| Source 1 | source agent: `L0-vision-idea-intake`, Date: {a date field in idea-brief.json (generated_date or date, at the root or under content/items), as yyyy-mm-dd. idea-brief.json usually carries NO date, and then this is the Generated date below. Never leave it empty} |
| Status (Draft / In-Review / Approved) | Draft |
| Generated | {current_date as resolved in Input Ingestion — yyyy-mm-dd read from the current date tool (UTC), or "not available"; never copied from an example, a fixture, or this template} |
| Approved by (Human Name) |  |
| Date of Approval |  |
| Human Approval Comments |  |

| Field | Value |
|---|---|
| Target geography | {geography, as stated in the brief} |
| Jurisdiction assessed | {the country the KBs declare, plus any sub-national layer used as the basis} |
| Viability score | {n}/10 |

## Feasibility Summary
**Overall status:** {Green|Amber|Red}
{one-line rationale — driven by the worst individual constraint, not an average}

## Constraints Assessed
### {constraint_name} — {Green|Amber|Red}
**Regulation:** {specific regulation/section}
**Obligated party:** {proposer | <the named customer, user or partner> | unresolved — <the fact that would resolve it>} — {for proposer or a named party: the brief's own sentence that decides it, quoted; for unresolved: "brief does not say"}
**Rationale:** {why this status was assigned — at most 2 sentences}
**Mitigation:** {required if Amber/Red — concrete recommendation in at most 2 sentences, or "requires legal review"}
{repeat one block per constraint — minimum: authorisation/licensing, data protection,
and anything specific to the target user segment or product category}

## Categories Assessed and Not Applicable
{one line per swept category that does not apply, with the reason in at most 12 words; empty only if every category applies}

## Viability Score
**Score:** {n}/10 — {auto_publish_eligible | human_review_required} against the threshold of 7

| Component | Weight | Score | Traced to |
|---|---|---|---|
| Regulatory posture | 0.60 | {n} | {CON ids} |
| Idea clarity | 0.40 | {n} | {idea-brief.json fields} |

**Weighted before caps:** {n}
**Caps applied:** {rule → cap value, triggered by {ids}; or "none"}
**What would raise the score:** {the specific change that would lift the lowest component or clear the binding cap. For every Red or capping constraint, name the resolving fact or action and say which kind it is: a brief gap (supply the fact and re-run) or a real-world step (obtain the approval, restructure, legal review)}

## Open Items
{every open question: anything flagged "requires legal review", every unresolved obligated party or threshold, every sub-national layer raised per Section B, and every item the brief itself deferred; empty only if none of these exist}
