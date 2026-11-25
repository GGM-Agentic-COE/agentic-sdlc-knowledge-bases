<!--
vision-template.md — the Document Template for L0-vision-statement-generator.
Read by the agent with the GitHub reader at every run (template_repo / template_branch / vision_statement_template_folder).
Everything after this comment is the document skeleton. Headings and table headers are fixed; each {…} block is a
fill rule the agent must follow, not text to copy. L0-vision-statement-generator-evaluator checks the finished
document against this layout and publishes it to Confluence: never rename or reorder the headings.
-->

# Vision: {product_name — as resolved in Input Ingestion}

| Field | Value |
|---|---|
| Source 1 | source agent: `L0-vision-idea-intake`, Date: {a date field in idea-brief.json (generated_date or date, at the root or under content/items), as yyyy-mm-dd. idea-brief.json usually carries NO date, and then this is the Generated date from regulatory-feasibility.md's first table. Never leave it empty} |
| Source 2 | source agent: `L0-regulatory-feasibility-checker`, Date: {the Generated row of regulatory-feasibility.md's first table} |
| Source 3 | source agent: `L0-vision-market-analyzer`, Date: {the Generated row of market-analysis.md's header table} |
| Status (Draft / In-Review / Approved) | Draft |
| Approved by (Human Name) |  |
| Date of Approval |  |
| Human Approval Comments |  |

**Product Name -** {product_name exactly as supplied by the user. If you proposed it: "{proposed name} (proposed by agent — no product name was supplied; confirm or replace)"}

## Executive Summary
{3-5 sentences, written LAST: what this is, who it's for, why viable now, the single biggest open risk — every claim must already appear below. One of these sentences states the viability score in running prose, exactly as received and naming its source, e.g. "The regulatory feasibility assessment scores the idea {n}/10." That score sentence is the one exception to "already appears below": it is carried from regulatory-feasibility.md, not from a later section}

## Problem / Target Users / Value Proposition
{carried forward from idea-brief.json — must not contradict it}

## Market Context
{one-paragraph condensation of market-analysis.md's SWOT — the single most decision-relevant insight. If no market analysis is available, keep the heading and write exactly: "Not assessed — no market analysis was available for this run." Never infer a market picture from the idea brief}

## Regulatory Posture
**Overall status:** {carried forward verbatim}
{one line per Amber/Red constraint naming its mitigation — every Amber/Red row in regulatory-feasibility.md must be traceable to a line here. If you state a count ("N Amber constraints"), COUNT THE LINES YOU ACTUALLY WROTE — a count that disagrees with the list makes a reader auditing coverage conclude a constraint was dropped when it was not. Safest is to state no count at all}

## North-Star Metric(s)
{metric + target. A target is EITHER a number upstream actually supports, with its source named, OR the literal "to be baselined in phase 1" — never a number you reasoned your way to. See Processing Rule 7}

## Roadmap Outline (phase-level)
{phase 1 must resolve or de-risk the most severe open risk below. Each phase names the OR-NN risk it resolves, not the CON-NN constraint underneath it — the CON id may follow in parentheses. Timing, where stated, is indicative unless upstream gives a date}

## Open Risks Carried Forward
{every Amber/Red regulatory item, restated as a roadmap dependency; any market weakness/threat worth tracking}
