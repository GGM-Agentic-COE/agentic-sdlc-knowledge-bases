# Evaluation — L1-vision-statement-generator

## Checks
- [ ] All required fields present (see output_schema.json's top-level required list)
- [ ] **BLOCKER — Reconciliation:** every constraint_id in regulatory_posture.constraint_summaries appears in at least one open_risks entry's related_ids — coverage, not 1:1 count; grouping related Amber items into one combined risk is fine, a constraint_id appearing in NO entry is not
- [ ] problem_statement/target_users/value_proposition do not contradict idea-brief.json (the upstream source of record)
- [ ] roadmap phase 1 addresses the single most severe open risk
- [ ] Section 2's Summary (items.executive_summary) introduces no claim absent from the sections below it. The viability score is stated in the Feasibility position table's Regulatory row as "<overall status> · <n> / 10", carried from regulatory-feasibility.md
- [ ] Section 8's Obligation register has one row per constraint in regulatory-feasibility.md, with the same CON ids and statuses — none renumbered, merged or dropped
- [ ] viability_score in items and in vision.md matches regulatory-feasibility.md and the input parameter exactly — carried, never recomputed, re-derived, rounded, or averaged
- [ ] Where the score was capped upstream by a Red or legal-review constraint, that constraint is covered in open_risks and named as the biggest open risk in Section 2's Bottom line and in Section 12's "What is the biggest risk?" answer — the number and the narrative describe the same situation
- [ ] Product Name: taken verbatim from the first segment of the run folder (<product name>/<workflow id>, named in the checker evaluator's artifacts[0].storage.folder), or proposed and labelled as proposed when the folder has none or its first segment is a workflow id ("wf-…"). Written in the Document Control table's Product row. The H1 ("<name> Product Vision"), the second heading line and the Product row carry the same name. A name the agent proposed is labelled as proposed in the Product row — a proposed name presented as the user's is a fail
- [ ] Document Control table: one Source row per upstream document actually read (agent name and that document's own date; Source 1 may carry regulatory-feasibility.md's date when the brief has none), Status "Draft", no Generated row. Approvals table present, with every Signature and Date cell left empty
- [ ] The full vision document was saved to S3 as <run folder>/vision.md — the folder named in the checker evaluator's artifacts[0].storage.folder, never the source folder_name the brief was first read from — and the vision.md artifact's storage (bucket, key, folder) is copied from the S3 writer's reply. When the write failed or no folder was given, the artifact's content holds the whole document instead. Nothing was written to GitHub or Confluence by the generator
- [ ] The document follows the vision-template.md read from GitHub: its headings and order, with no {…} fill rule or template HTML comment left in it  

## Scores (minimum per dimension)
| Evaluator | ≥ | Checks |
|-----------|---|--------|
| Faithfulness | 0.90 | Every carried-forward item matches its upstream source |
| Hallucination | ≤ 0.10 | No claim introduced that isn't grounded in an upstream input — and no market picture inferred when no market analysis was available |
| Consistency | 0.95 | Reconciliation check (above) passes fully — this is the highest consistency bar of any Phase 0 agent, since a dropped risk here reaches a human decision-maker |
| Relevance | 0.85 | Roadmap and metrics are usable as-is for Phase 1 planning |
| Reasoning quality | 0.80 | Every north_star_metric and roadmap phase explains its derivation |
| Citation completeness | N/A | This agent synthesizes upstream agent outputs, not KB/external sources — reconciliation check substitutes for citation |

**Sources of record:** `idea-brief.json` for problem/users/value (JSON, read by
key path), `regulatory-feasibility.md` for the constraint list *and* the
viability score, `market-analysis.md` for market context where it exists.

## Reflection Checklist
- [ ] Zero regulatory Amber/Red items missing from open_risks
- [ ] Section 2's Summary written last, after all other sections finalized
- [ ] No section filled with plausible content no upstream document supports — capabilities, scope boundaries, principles, assumptions and market questions all trace to idea-brief.json, regulatory-feasibility.md or market-analysis.md
- [ ] viability_score reported honestly even if low — not omitted or softened

## Reflection Process
1. Generate → 2. Check all items above → 3. Fix silently → 4. Deliver final only
