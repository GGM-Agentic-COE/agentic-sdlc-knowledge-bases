#L1 Vision Idea Intake-Evaluation Criteria

## Purpose

Evaluate the quality, grounding, completeness, and structural correctness of the `L1-vision-idea-intake` agent output.

The evaluator must use the attached evaluation knowledge base:

`kb-L1-vision-idea-intake-evaluation-slim`

as the **source of truth** for scoring dimensions, thresholds, and quality gates.

The evaluator must evaluate the generated `idea-brief.json` only against the information available in the document and the applicable evaluation rubric.

---

## Input

* `document_content`: Full `idea-brief.json` content produced by the upstream `L1-vision-idea-intake` agent.
* `generator_output`: Agent output metadata containing `workflow_execution_id` and artifact location.
* `workflow_execution_id`: Inherit from `generator_output`.
* Evaluation KB: `kb-L1-vision-idea-intake-evaluation-slim`.

---

## Processing

1. Load the quality gates and scoring dimensions from the evaluation KB.
2. Validate the complete `idea-brief.json` structure.
3. Evaluate every applicable quality gate and record pass/fail.
4. Score every evaluation dimension on a `0.0–1.0` scale according to the evaluation KB thresholds.
5. For every failed criterion:

   * record a finding;
   * identify the applicable category;
   * describe the specific issue;
   * reference the affected content;
   * identify the applicable rubric criterion.
6. Apply a correction only when the required correction is directly supported by the existing document or source evidence.
7. Do not invent missing business information.
8. If information cannot be safely corrected, retain the gap and report it as a finding.
9. Determine the final verdict:

   * `pass` — all mandatory scores meet or exceed their thresholds and all mandatory quality gates pass.
   * `fail` — one or more mandatory thresholds or quality gates fail.
10. If corrections are applied, re-upload the corrected `idea-brief.json` to the same artifact location using the storage-write tool available in the current runtime context.
11. Return only the final evaluation result.

---

# Evaluation Dimensions

## 1. Input Grounding and Accuracy

Evaluate whether the idea brief accurately represents the source input.

Check:

* Business idea summary is supported by the input.
* Problem statement is supported by the input.
* Pain points are supported by the input.
* Target users are supported by the input.
* Value proposition is supported by the input.
* Business benefits are supported by the input.
* Assumptions are distinguishable from facts.
* No unsupported business claims are introduced.
* No fabricated features, users, benefits, metrics, or outcomes are introduced.

**Scoring principle:** Penalize unsupported or materially inaccurate content.

---

## 2. Problem Statement Quality

Evaluate whether the problem statement:

* Clearly represents the business problem.
* Preserves the meaning of the source.
* Is specific enough to be actionable.
* Is grounded in source evidence.
* Contains a valid `traced_to` reference.
* Has an appropriate confidence score.
* Does not introduce unsupported interpretation.

---

## 3. Pain Point Identification

Evaluate whether `current_pain_points`:

* Capture the actual pain points present in the source.
* Are distinct rather than repetitive.
* Are supported by source evidence.
* Do not introduce unsupported operational problems.
* Preserve the meaning of the original input.

Missing information should not be converted into invented pain points.

---

## 4. Target User Identification

Evaluate whether `target_users`:

* Identify users supported by the input.
* Distinguish users from stakeholders.
* Do not infer unsupported user segments.
* Contain appropriate evidence/traceability.
* Do not use `stakeholder_list` as evidence for user behavior unless the input supports it.

---

## 5. Value Proposition Quality

Evaluate whether the value proposition:

* Accurately represents the proposed solution or intended value.
* Is grounded in the source.
* Does not introduce unsupported product capabilities.
* Contains appropriate confidence.
* Contains valid `traced_to` evidence.
* Clearly connects the proposed solution to the identified problem.

---

## 6. Business Benefits

Evaluate whether `business_benefits`:

* Are supported by the input.
* Are reasonable consequences of the stated problem/value proposition.
* Are not presented as confirmed outcomes when they are only potential benefits.
* Do not introduce unsupported financial, operational, or business claims.
* Have appropriate source citations.

---

## 7. Success Metrics

Evaluate `candidate_success_metrics` with particular attention to classification.

### Stated

A metric may be classified as `stated` only when the input explicitly provides the metric, target, baseline, or measurable outcome.

### Suggested

A metric may be classified as `suggested` when it is reasonably derived from an explicitly stated problem, objective, or value proposition.

The evaluator must verify:

* Correct `stated` / `suggested` classification.
* No inferred metric is incorrectly classified as stated.
* Numerical targets are not fabricated.
* Metric descriptions are meaningful.
* Metrics are traceable to source evidence.
* Confidence is appropriate.

---

## 8. Constraints, Risks, and Dependencies

Evaluate whether:

* `known_constraints` contain only supported constraints.
* `risks` represent risks reasonably grounded in the source.
* `dependencies` represent dependencies supported by the available information.
* Unsupported assumptions are not presented as facts.
* Unknown information is represented as an `open_question` where appropriate.

---

## 9. Open Questions

Evaluate whether `open_questions`:

* Identify genuinely unresolved information.
* Capture important gaps required to progress the idea.
* Do not duplicate information already established.
* Do not fabricate answers to unresolved questions.
* Reflect missing business, user, metric, ownership, constraint, or dependency information where relevant.

---

## 10. Confidence and Reasoning

Evaluate whether confidence scores:

* Reflect the strength of available evidence.
* Are higher for explicit statements.
* Are lower for inferred or ambiguous information.
* Are not artificially inflated.
* Are consistent with the associated reasoning.

The evaluator must ensure that confidence does not compensate for missing evidence.

---

## 11. Traceability and Citations

Evaluate whether:

* Required narrative fields contain `traced_to`.
* `traced_to` values correspond to actual source content.
* Citation sources correspond to the actual evidence used.
* Document references are populated only when an actual external document was used.
* No fabricated source, document, or document ID is introduced.
* Citations do not claim evidence that does not exist.

---

## 12. Schema and Structure Compliance

Evaluate whether `idea-brief.json` follows the approved standalone structure.

The output must contain the defined idea-brief sections, including:

* `items`
* `citations`
* `document_reference`
* `execution_summary`

The following **must not appear** in `idea-brief.json`:

* `agent_id`
* `agent_version`
* `execution_id`
* `workflow_execution_id`
* top-level AgentOutput `status`
* `content`
* `content.type`
* `content.schema_version`

The evaluator must also verify:

* Valid JSON.
* Correct nesting.
* Correct field names.
* Correct data types.
* No unexpected AgentOutput wrapper.
* No market-analysis-specific fields or schema.

---

## 13. Execution Summary Accuracy

Evaluate whether `execution_summary` reflects actual execution.

Check:

* Produced counts are accurate.
* Knowledge sources contain only actually used sources.
* Tools contain only actually invoked tools.
* Tool status reflects the actual outcome.
* Validation status is accurate.
* No `not_invoked` tools are reported as executed.
* No knowledge base is reported as consulted unless it was actually used.
* No guardrail is reported as evaluated unless it was actually executed.

Execution metadata must represent runtime events, not planned or configured behavior.

---

## 14. Guardrail Compliance

Evaluate the execution behavior for:

`gr-L1-impact-assessment-quality-gate`

The evaluator must verify that:

* The guardrail is invoked only on the final successful iteration.
* It is not invoked on interim iterations.
* It is not invoked on failed or retry iterations.
* The reported result matches the actual guardrail execution result.

---

## 15. Artifact Integrity

If the evaluator applies corrections:

* Correct only identified issues.
* Preserve all correct content.
* Do not introduce unsupported information.
* Produce the corrected content verbatim.
* Re-upload the corrected artifact to the same artifact location.
* Do not create a second artifact location.
* Confirm that the uploaded artifact is the corrected version.

If no correction is required, the original artifact must remain unchanged.

---

# Quality Gates

* [ ] Input content is sufficient for evaluation.
* [ ] All applicable rubric dimensions are evaluated.
* [ ] Every finding contains a category and description.
* [ ] Every finding identifies the applicable rubric criterion.
* [ ] Every finding includes `fix_applied`.
* [ ] Every score is between `0.0` and `1.0`.
* [ ] No unsupported content is introduced during correction.
* [ ] Problem statement is grounded.
* [ ] Target users are grounded.
* [ ] Value proposition is grounded.
* [ ] Success metrics have correct `stated` / `suggested` classification.
* [ ] Citations and `traced_to` evidence are valid.
* [ ] Standalone `idea-brief.json` structure is valid.
* [ ] AgentOutput metadata is not embedded in `idea-brief.json`.
* [ ] Execution summary reflects actual runtime events.
* [ ] Tools reported as invoked were actually invoked.
* [ ] Knowledge sources reported as used were actually used.
* [ ] Guardrail reporting reflects actual execution.
* [ ] If fixes were applied, corrected artifact was re-uploaded to the same location.
* [ ] Verdict is consistent with the final scores.

---

# Scores

| Evaluation Dimension  | Threshold | Primary Check                                                     |
| --------------------- | --------: | ----------------------------------------------------------------- |
| Accuracy              |    ≥ 0.90 | Content is grounded and factually supported                       |
| Fix Quality           |    ≥ 0.85 | Corrections resolve findings without introducing issues           |
| Completeness          |    ≥ 0.90 | All applicable rubric dimensions are evaluated                    |
| Grounding             |    ≥ 0.90 | No unsupported or hallucinated content                            |
| Schema Compliance     |    ≥ 0.95 | Standalone `idea-brief.json` follows the required structure       |
| Traceability          |    ≥ 0.90 | Claims and metrics can be traced to source evidence               |
| Metric Classification |    ≥ 0.90 | Stated vs suggested metrics are correctly classified              |
| Execution Reporting   |    ≥ 0.90 | Tools, KBs, guardrails, and summary reflect actual runtime events |

The evaluation KB remains the authoritative source if it defines different thresholds or additional dimensions.

---

# Verdict

### PASS

Return `pass` only when:

* All mandatory quality gates pass.
* All mandatory evaluation dimensions meet their thresholds.
* No critical grounding or schema issue remains.
* Any applied fixes are valid and grounded.

### FAIL

Return `fail` when:

* Any mandatory quality gate fails.
* Any mandatory score remains below threshold.
* The document is fundamentally unusable.
* Required information cannot be corrected without inventing content.
* A critical schema, grounding, or traceability issue remains.

A fundamentally unusable document must not be repaired by inventing missing business information.

---

# Finding Structure

Each finding must contain:

```json
{
  "category": "<rubric_category>",
  "description": "<specific issue>",
  "rubric_criterion": "<applicable criterion from evaluation KB>",
  "severity": "<critical|major|minor>",
  "fix_applied": true,
  "fix_description": "<what was corrected>"
}
```

If no fix can safely be applied:

```json
{
  "category": "<rubric_category>",
  "description": "<specific issue>",
  "rubric_criterion": "<applicable criterion from evaluation KB>",
  "severity": "<critical|major|minor>",
  "fix_applied": false,
  "fix_description": null
}
```

---

# Execution Summary

The final `execution_summary` must contain:

* Final verdict.
* Dimension scores.
* Quality-gate results.
* Findings count.
* Fixes applied count.
* Artifact re-upload status.
* Evaluation KB name and dimensions/thresholds used.
* Tools actually invoked and their outcomes.
* Guardrails actually invoked and their outcomes.

Do not report tools, knowledge bases, or guardrails that were not actually invoked.

---

# Reflection Checklist

Before delivery, verify:

* [ ] Every finding maps to a rubric criterion from the evaluation KB.
* [ ] Every score is within `0.0–1.0`.
* [ ] Every applicable dimension was evaluated.
* [ ] No unsupported content was introduced.
* [ ] No correct content was unnecessarily changed.
* [ ] Stated and suggested metrics were correctly classified.
* [ ] All `traced_to` evidence is valid.
* [ ] Citations accurately represent source usage.
* [ ] AgentOutput metadata is absent from `idea-brief.json`.
* [ ] Execution summary reflects actual runtime activity.
* [ ] Verdict matches the final scores and quality gates.
* [ ] If fixes were applied, the re-uploaded artifact is the corrected version.
* [ ] No interim reasoning or reflection output is returned.
