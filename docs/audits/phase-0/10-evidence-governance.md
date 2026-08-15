# Phase 0 Audit 10 — Scientific Evidence Governance

> Status: audit complete; no production implementation changes made  
> Date: 2026-08-15

## 1. Purpose

Evidence governance determines what scientific claims ARETE is justified in making and which of those claims may be converted into executable policy. It protects the system from citation theatre: having references is not enough if the references do not justify the implemented decision.

## 2. Current strengths worth preserving

The current running evidence file already includes several high-quality habits:

- required data;
- supported and unsupported claims;
- confounders;
- references/PMIDs;
- confidence language;
- explicit bans on single-session fitness claims and composite readiness/quality scores.

This is an excellent prototype for a formal registry.

## 3. Critical findings

### 3.1 References are currently attached directly to rules without a formal evidence-review layer

A paper can support a physiological relationship without validating the exact software action. ARETE needs an intermediate evidence review that states population, outcome, certainty, applicability and what the evidence does *not* justify.

### 3.2 Evidence certainty and decision threshold are different

Strong evidence that high training volume is associated with greater hypertrophy does not identify the optimal set count for one individual. Strong evidence that sleep deprivation can impair performance does not validate `sleep < 6h → reduce load 20%`.

### 3.3 Group evidence cannot be promoted directly to individual causality

Population-average treatment effects inform priors and policies. They do not prove that an individual athlete's observed change was caused by a specific exposure. Longitudinal personal data can update confidence but usually do not establish causal effects without stronger design.

### 3.4 Evidence hierarchy alone is insufficient

Study design quality matters, but so do:

```text
population match
training status
sex representation
sport/task specificity
outcome relevance
measurement validity
intervention duration
effect magnitude
uncertainty
replication
```

### 3.5 Contradictory evidence must remain visible

ARETE should not cherry-pick one review that supports a preferred rule. When high-quality reviews disagree, the registry should represent the disagreement and usually lower prescriptive certainty.

### 3.6 Evidence requires lifecycle/versioning

Research changes. Every evidence record needs a review date/status and ruleset dependencies so a scientific update can trigger regression review.

### 3.7 Legal/scientific references are different registries

Scientific evidence, provider terms, privacy law and software standards should link to decisions but should not be conflated into one “evidence score.”

## 4. Audit verdict

**PROMOTE THE EXISTING RUNNING-EVIDENCE PATTERN INTO A FIRST-CLASS, VERSIONED EVIDENCE REGISTRY.**

Required separation:

```text
Reference
→ EvidenceReview
→ Interpretation
→ Policy
→ ExecutableRule
→ Decision
```

## 5. Provisional evidence certainty

The existing A/B/C/D/U concept can remain provisional:

```text
A: strong convergent evidence / high-quality consensus
B: reasonably consistent experimental/prospective evidence
C: limited, heterogeneous, indirect or observational evidence
D: expert practice / mechanistic rationale
U: uncertain or conflicting
```

But this is an ARETE governance shorthand, not formal GRADE and must never be presented as equivalent to GRADE.

## 6. Required tests/audits

1. paper supports association but rule attempts causal prescription → blocked/review;
2. strong evidence but poor population applicability → lower applicability;
3. conflicting meta-analyses → disagreement visible;
4. old evidence review becomes review-due;
5. rule references evidence version that changed → scientific regression review;
6. user-specific policy has no paper but is clearly labeled policy;
7. marketing claim cannot exceed evidence/policy support;
8. reference removal/retraction flags dependent rules.
