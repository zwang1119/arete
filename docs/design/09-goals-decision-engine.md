# Design Proposal 09 — Goals, Rules, and Decision Engine

> Status: proposed architecture; no production implementation yet  
> Date: 2026-08-15

## 1. Objective

Generate the smallest justified training decision from canonical state, scientific evidence, athlete goals, constraints and current uncertainty.

## 2. Core entities

```text
Goal
Constraint
DecisionContext
Rule
RuleEvaluation
CandidateAction
DecisionRecord
PlanMutationRequest
```

## 3. Goal model

```text
id
category
priority: primary | secondary | maintenance
scope
horizon
success_definition
measurement_refs
status
```

Constraints are separate:

```text
injury/symptom constraint
schedule constraint
equipment constraint
preference
medical/professional instruction
external requirement
```

Do not treat a constraint as merely a low-priority goal.

## 4. Rule classes

Every executable rule declares:

```text
rule_class:
  evidence_supported
  consensus_or_best_practice
  product_safety_policy
  user_specific_policy
  experimental
```

Plus:

```text
required_data
optional_data
scope
applicability
precedence
evidence_refs
version
review_date
```

This is how ARETE distinguishes “we chose this conservatively” from “research validates this exact cutoff.”

## 5. Decision pipeline

```text
canonical facts
    ↓
domain assessments
    ↓
relevant goals/constraints
    ↓
applicable rules
    ↓
rule evaluations
    ↓
candidate actions
    ↓
conflict resolution
    ↓
smallest justified action
    ↓
DecisionRecord
```

## 6. Precedence

Default precedence:

```text
law / security / privacy
> material safety/symptom constraints
> external hard constraints
> primary goal protection
> planned-session intent
> recovery/fatigue modification
> progression opportunity
> optimization/convenience
```

This is a policy architecture, not a claim that every case can be solved by one fixed list. Exceptions must be explicit and tested.

## 7. Candidate actions

Common actions:

```text
maintain
monitor
modify_load
modify_reps
modify_sets
modify_intensity
substitute_component
reschedule
remove_optional_work
review_before_exposure
recommend_professional_review
```

The engine should prefer the least disruptive action that addresses the identified problem while preserving the intended stimulus.

## 8. No-change principle

`maintain` is not failure to coach.

When evidence is weak or signals conflict without a clear reason to intervene:

```text
decision = maintain
reason = insufficient evidence for beneficial modification
```

This should be common in a conservative high-quality system.

## 9. Conflict resolution

For material conflicts, record:

```text
candidate actions considered
benefit being protected
cost/trade-off
rules supporting/opposing
selected action
why alternatives were rejected
```

Not every trivial decision needs verbose counterfactual logging; Coach Lens can surface detail on demand.

## 10. Decision confidence

Derive from separate components:

```text
data completeness
data quality
applicability of evidence
rule certainty
conflict/ambiguity
```

Do not average them into a pseudo-precise percentage unless a validated calibration model exists.

Use interpretable labels and reasons.

## 11. Plan mutation

A decision and a mutation are separate objects.

```text
DecisionRecord
    ↓
PlanMutationRequest
    ↓
validation
    ↓
new PlannedSessionRevision
```

Requirements for a mutation:

- authoritative current plan/revision identified;
- no unresolved association/source conflict;
- mutation is within allowed authority;
- safety and goal constraints checked;
- exact delta recorded;
- old revision preserved;
- user confirmation required during early rollout except explicitly permitted low-risk changes.

## 12. User override

An athlete may override a recommendation. Store:

```text
decision_ref
override_action
reason_optional
timestamp
```

Do not treat override as bad adherence. It becomes longitudinal evidence for future review.

## 13. Decision journal

Every consequential record should include:

```text
id
as_of
ruleset_version
goal_snapshot_refs
input_evidence_refs
rule_evaluations
candidate_actions
selected_action
confidence
missing_data
overrides
outcome_followup_refs
```

This creates the `Decision → Action → Outcome` evidence trail without pretending it is causal proof.

## 14. Acceptance criteria

The engine must be deterministic for the same state/ruleset, explain why no change was made, preserve user autonomy, and never let an LLM or UI component mutate training state outside this pathway.
