# Test Case Standards and Best Practices

## kb-test-case-authoring-standards v1.0.0

Detailed authoring and review standards for ordinary executable test cases. This document complements, and does not replace, the applicable L0 common testing standards or an agent's approved contract.

**Status:** Draft for approval  
**Applies to:** Test Case Writer Agents, test case reviewers/evaluators, and downstream automation or execution components where applicable  
**Document owner:** Designated testing governance owner  
**Effective date:** Set upon approval

---

## 1. Purpose and Scope

This standard defines how to design, write, review, and maintain test cases that verify approved product or process behavior.

A test case is an executable specification of a focused objective, the required starting state, data constraints, actions, and verifiable expected results. A case may be manual or automation-ready depending on its contract and context.

This standard does not define:

- A universal test-management schema or platform-specific field mapping
- Project-specific identifiers, status workflows, severity scales, or priority defaults
- Requirements, product behavior, expected messages, limits, roles, or permissions
- Automation code or execution permissions
- Adversarial case scope; adversarial cases must follow their approved specialist contract

If this document conflicts with approved runtime requirements, the requirements govern product behavior. If it conflicts with the agent's approved output contract, the agent must follow its contract and report the conflict for governance review. Apply the approved L0 common testing standards for general grounding, precedence, traceability, privacy, safety, and responsibility boundaries.

---

## 2. Core Principles

Every test case SHOULD be:

- **Grounded:** derived from an approved requirement, acceptance criterion, scenario, risk, or other permitted source.
- **Traceable:** linked to its immediate upstream artifact and applicable higher-level sources.
- **Focused:** designed to prove one primary behavior or one closely related condition set.
- **Independent:** runnable without relying on another test's execution order unless a documented dependency requires it.
- **Repeatable:** sufficiently specified to produce interpretable results when run again in an equivalent approved environment.
- **Observable:** pass or fail can be determined from specified evidence.
- **Maintainable:** written in domain language, avoiding unnecessary implementation details and duplication.
- **Safe:** uses approved environments and synthetic or approved masked data; destructive or sensitive activity requires authorization.

A test case MUST NOT introduce behavior that is absent from approved sources.

---

## 3. Inputs and Source Validation

Before authoring cases, the responsible agent MUST:

1. Validate that all contractually required source artifacts are available and usable.
2. Identify the applicable requirement, acceptance criterion, scenario, strategy, and supporting context supplied for the task.
3. Preserve source identifiers exactly as supplied.
4. Identify ambiguity, contradiction, missing expected behavior, missing data constraints, and unavailable testability information.
5. Check existing approved cases when deduplication is part of the task and the artifacts are available.
6. Use only the test levels, environments, test data rules, priorities, and classifications supported by approved sources or project configuration.

If a required source is missing, follow the agent contract's stop or review behavior. Do not silently substitute another source or infer missing content.

---

## 4. Test Case Structure

The agent's output schema is authoritative. When the contract supports the fields, a test case SHOULD include:

| Field | Authoring guidance |
|---|---|
| Test case ID | Use only the approved identifier allocator or system of record. Never invent a final ID. |
| Title | State the behavior and relevant condition concisely. |
| Objective | Explain what approved behavior or risk the case verifies. |
| Source references | Include validated immediate upstream references and applicable higher-level IDs. |
| Test level and type | Use controlled values required by the output contract and supported by context. |
| Priority | Derive only from approved impact, criticality, or risk context; otherwise use `review` if permitted. |
| Preconditions | State the required starting state, not the actions used to create it. |
| Test data | Describe required categories and constraints; use exact values only when supplied or approved. |
| Steps | List clear, ordered actions, one primary action per step. |
| Expected results | Give observable, deterministic outcomes tied to approved behavior. |
| Postconditions | Include only if approved context specifies resulting state or cleanup. |
| Environment | Reference the approved environment or constraints when needed; do not assume production equivalence or approval. |
| Automation suitability | Classify only when required and supported by interface, stability, access, and repeatability information. |
| Review notes or gaps | Record unresolved decisions, missing information, or limitations using the contract's fields and statuses. |

This table is a design guide, not a mandatory schema. Do not add fields solely because they appear here if the contract does not allow them.

---

## 5. Titles and Objectives

### 5.1 Titles

Titles SHOULD identify a specific outcome and relevant condition. Use the approved domain terminology.

Recommended pattern:

```text
<Expected behavior> when <condition or action>
```

Avoid vague titles such as `Test checkout`, `Verify functionality`, or `Everything works`.

### 5.2 Objectives

An objective SHOULD answer: “What requirement-backed behavior or risk will this case verify?” It MUST NOT repeat a feature description without identifying what is being proven.

A case MUST NOT combine unrelated objectives merely to reduce the case count.

---

## 6. Case Scope and Granularity

- A case SHOULD verify one primary behavior or one meaningful condition combination.
- Split cases when expected results, permissions, state transitions, ownership, risk, or traceability differ materially.
- Combine variations into a parameterized case only when the workflow and expected-result pattern remain the same and failure diagnosis remains clear.
- Do not create a separate case for every data value when a supported data-driven pattern is clearer.
- Do not merge cases when doing so obscures which condition failed or weakens independent execution.
- Negative, boundary, permission, integration, regression, and adversarial cases MUST be justified by supplied rules, limits, roles, dependencies, change context, or risks.
- A test case is not a defect report, test scenario, test plan, or implementation task. Keep those responsibilities distinct.

---

## 7. Preconditions and State

A precondition describes a state that must already be true before the case begins.

Good examples:

- The user is authenticated.
- The account has the permission specified by the supplied requirement.
- The approved reference record is available.

Invalid precondition examples:

- Click the sign-in button.
- Navigate to the account page.
- Create a record using the steps in this case.

Actions belong in steps. If setup is necessary, describe the required state or reference an approved fixture/setup mechanism; do not hide setup actions inside a precondition. Do not assume cleanup, rollback, persistence, or shared state unless a source defines it.

---

## 8. Test Data

- Describe data by classification and constraints: valid, invalid, boundary, reference, or synthetic, where those classifications are required.
- Use exact values only when supplied by an approved source or formally approved test data.
- Invalid data MUST violate a documented rule; malformed or unusual does not automatically mean invalid.
- Boundary data MUST be derived from an explicit limit, range, threshold, or length. Never infer a limit.
- Preserve supplied data dependencies and referential constraints.
- Do not copy production personal, customer, financial, confidential, credential, token, key, certificate, or secret data into test artifacts.
- Use synthetic or formally approved masked data under the applicable data-handling rules.
- If a test cannot be made executable without an exact value or rule, document the gap and request review rather than fabricating one.

---

## 9. Steps and Expected Results

### 9.1 Steps

- Order steps logically and write each so it is independently understandable.
- Use one clear primary action per step.
- Use imperative language for actions and keep the actor clear when relevant.
- Use domain terms from the approved sources consistently.
- Avoid unnecessary UI selectors, coordinates, database internals, or implementation details unless the approved test level or source requires them.
- Include navigation or setup actions only when needed to make the case executable and allowed by its contract.
- Do not combine unrelated workflows or multiple independent assertions in one step.

### 9.2 Expected results

Expected results MUST be:

- Observable by the specified tester or tool
- Deterministic enough to judge pass or fail
- Specific and directly associated with the relevant action
- Grounded in approved expected behavior

Avoid vague results such as `the system works`, `success is shown`, or `data is correct`. Do not invent exact UI text, status codes, events, notifications, audit records, database changes, or fallback behavior.

When a source establishes a restriction but not the system's response, test only what is supported and mark the missing expected response for review. Do not turn the absence of a specified result into an assumed failure message or behavior.

---

## 10. Test Types and Design Techniques

Use the classifications and techniques defined by the applicable L0 standard and agent contract. A test type describes the coverage intent; it does not itself prove coverage.

| Design need | Apply when supported by sources |
|---|---|
| Equivalence partitioning | Supplied inputs or conditions form behaviorally equivalent classes |
| Boundary-value analysis | An explicit boundary or threshold is documented |
| Decision table | Outcomes depend on multiple documented conditions |
| State-transition testing | Approved states and transitions are supplied |
| Pairwise testing | Many independent parameters create excessive combinations and reduction is allowed |
| Risk-based selection | Approved risk, impact, or criticality context is available |

Pairwise or other reduction MUST preserve mandatory, regulatory, critical, and explicitly high-risk combinations. Do not infer impossible combinations or out-of-scope conditions.

---

## 11. Traceability, Coverage, and Reuse

- Every case MUST link to its approved immediate upstream source and preserve applicable higher-level source IDs.
- A case linked to multiple sources MUST remain understandable and atomic; explain the valid grouping when the schema supports it.
- Each in-scope required behavior MUST be covered or reported as a coverage gap by the responsible component.
- Coverage claims MUST identify their scope. Do not claim complete feature, requirement, regression, or compliance coverage based on a narrower subset.
- Compare existing approved cases when deduplication is in scope. Compare objective, preconditions, action, result, traceability, type, data, permission, state, and risk.
- Reuse or parameterize cases when only data varies and diagnostic clarity is preserved.
- Keep separate cases when the workflow, expected result, role, state transition, source coverage, or risk differs materially.
- If existing cases were unavailable, report that duplicate detection was not performed when the contract allows it.

---

## 12. Priority, Risk, and Automation

### 12.1 Priority and risk

- Derive priority only from approved impact, criticality, or risk context.
- Do not confuse execution priority with risk severity or risk category.
- Use `review` when priority cannot be derived and the output contract permits it.
- Apply any project-specific severity or priority scale only from approved configuration.

### 12.2 Automation suitability

Classify a case as an automation candidate only when the applicable contract asks for it.

- `yes`: repeatable, deterministic, stable, accessible to the approved automation interface, and valuable for repeated validation.
- `no`: depends on unsupported physical interaction, subjective judgment, or one-time exploratory investigation.
- `review`: feasibility depends on unavailable interface, environment, tool, access, or stability information.

Automation suitability does not authorize script generation. Automation agents must use approved frameworks and environments, preserve case intent, avoid hard-coded secrets, and report actual execution evidence separately from generation results.

---

## 13. Worked Examples

The examples in this section are hypothetical teaching examples only. Their behavior, values, wording, and identifiers MUST NOT be copied into a real artifact unless independently present in approved runtime sources.

### 13.1 Grounded case pattern

Hypothetical source statement for illustration only: “When a customer submits an order with all required details, the system creates the order and displays the documented confirmation.”

| Element | Illustrative content |
|---|---|
| Title | Order is created when all required details are supplied |
| Objective | Verify the documented successful order-submission behavior |
| Source | `<actual requirement or scenario ID from approved input>` |
| Preconditions | Customer is in the state required by the approved scenario |
| Test data | Valid order data satisfying the supplied constraints; no invented values |
| Step | Submit the order using the supplied required details |
| Expected result | The order is created and the documented confirmation is observable |

This example is intentionally not a complete case. A real case must use the actual approved input, schema, IDs, data constraints, and result wording.

### 13.2 Ungrounded boundary to avoid

If a source says a field is required but provides no minimum or maximum length, do not create “minimum length” or “maximum length” cases with guessed values. Record a boundary-definition gap if boundary coverage is required.

### 13.3 Expected-result gap to flag

If a source says an action is prohibited but does not define the response, do not assert a specific message, status, or fallback. Preserve the source reference and request clarification for the expected response when it is necessary to determine pass or fail.

---

## 14. Review Checklist

Before a case is approved for the next workflow stage, confirm all applicable checks:

- [ ] Case is within the Test Case Writer's assigned responsibility.
- [ ] Required sources are present, approved, and usable.
- [ ] Title and objective describe a specific, source-grounded behavior.
- [ ] Source IDs are exact and traceability is present.
- [ ] Case is atomic enough for clear diagnosis and independent execution where possible.
- [ ] Preconditions describe state, not actions.
- [ ] Steps are clear, ordered, and limited to one primary action each.
- [ ] Test data uses supplied constraints and approved classifications.
- [ ] Expected results are observable and directly tied to approved behavior.
- [ ] Test level, type, priority, risk, and automation classification are supported.
- [ ] Boundaries, roles, states, dependencies, and failure handling were not invented.
- [ ] Sensitive data and environment constraints are handled according to approved policy.
- [ ] Duplicates were checked or marked unavailable when applicable.
- [ ] Coverage gaps and unresolved ambiguities are reported honestly.
- [ ] Output conforms to the agent's schema; no unauthorized upload or execution is claimed.

---

## 15. Glossary

| Term | Definition |
|---|---|
| Test case | Executable specification of required state, data, action, and expected result |
| Test scenario | Higher-level statement of behavior, condition, or risk to be tested |
| Preconditions | State that must exist before executing a test case |
| Expected result | Observable outcome used to determine whether the tested behavior conforms to approved sources |
| Test data constraint | Approved condition that test data must satisfy or violate |
| Traceability | Recorded relationship between a case and its approved upstream or downstream sources |
| Automation candidate | Classification indicating suitability for further automation assessment, not authorization to generate code |

---
