# Design Proposal 10 — Scientific Evidence Governance

> Status: proposed architecture; no production implementation yet  
> Date: 2026-08-15

## 1. Objective

Create an auditable chain from scientific literature to software behavior while preventing population-level evidence from being silently transformed into individual prescription rules.

## 2. Core entities

```text
ScientificReference
EvidenceReview
EvidenceClaim
ImplementationPolicy
ExecutableRule
EvidenceDependency
```

## 3. ScientificReference

Store bibliographic identity only:

```text
id
citation
DOI/PMID
publication_type
year
url
retraction_or_warning_status
```

Do not attach software meaning directly to a citation.

## 4. EvidenceReview

A structured review answers:

```text
question
population
intervention/exposure
comparator
outcomes
study/design body
main findings
effect magnitude and uncertainty
risk/limitations
population applicability
measurement limitations
certainty
reviewed_at
reviewer
status
```

It must include `does_not_support` where useful.

## 5. EvidenceClaim

A narrow claim derived from one or more reviews:

```text
claim
scope
certainty
applicable_population
boundary_conditions
supporting_reviews[]
conflicting_reviews[]
```

Example:

```text
“Higher resistance-training set volume is generally associated with greater hypertrophy, with diminishing returns.”
```

This claim does **not** imply a universal optimal weekly set count.

## 6. ImplementationPolicy

Converts evidence plus product philosophy into action policy.

```text
policy_id
purpose
claim_refs
tradeoffs
false_positive_cost
false_negative_cost
chosen_action_threshold_or_logic
why_this_policy
policy_class
```

This is the place to state when ARETE deliberately chooses a conservative threshold even though the literature does not validate that exact number.

## 7. ExecutableRule

```text
rule_id
version
policy_ref
required_data
optional_data
applicability
logic
outputs
precedence
review_status
```

No executable scientific rule without a policy/evidence chain unless explicitly classified as user-specific or operational.

## 8. Certainty dimensions

Keep separate:

```text
scientific_evidence_certainty
population_applicability
data_quality
data_completeness
decision_confidence
```

Never multiply/average them into one unexplained score.

## 9. Population → individual rule

ARETE should use group evidence as contextual prior knowledge.

Individual longitudinal observations may strengthen or weaken confidence that a pattern applies to the athlete, but the system should use language such as:

```text
consistent with
supports
raises concern
repeatedly associated in this athlete
```

rather than claiming individual causality without an appropriate design.

## 10. Contradictory evidence

If credible evidence conflicts:

```text
certainty = U or downgraded
conflicting_review_refs stored
prescriptive behavior becomes more conservative
```

A reviewer must be able to see why a rule was not implemented despite a positive study.

## 11. Evidence review cadence

Each record has:

```text
last_reviewed
next_review_due
status: current | review_due | disputed | superseded
```

High-impact safety/prescriptive rules receive shorter review cycles than descriptive low-impact metrics.

New major evidence can trigger:

```text
EvidenceReview update
→ dependent policies flagged
→ ruleset candidate change
→ scientific regression tests
→ documented release
```

## 12. Evidence levels

ARETE may use A/B/C/D/U as internal shorthand, but every UI/documentation occurrence must state that it is an ARETE-specific certainty classification and not formal GRADE.

## 13. Research provenance

For each rule we should eventually be able to answer:

- Who reviewed the evidence?
- When?
- Which version of the literature was considered?
- What exact claim was accepted?
- What claims were rejected?
- Why did that claim become this policy?
- Which decisions used that ruleset?

## 14. Acceptance criteria

A rule is not scientifically production-ready if the chain `reference → review → claim → policy → rule` cannot be reconstructed or if the rule's action is materially stronger than the evidence review supports.
