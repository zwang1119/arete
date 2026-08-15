# Phase 0 Audit 09 — Goals, Rules, and Decision Engine

> Status: audit complete; no production implementation changes made  
> Date: 2026-08-15

## 1. Purpose

The Decision Engine exists to convert validated athlete state into the smallest justified action that best serves the athlete's goals and constraints. It must separate scientific evidence from product policy and preserve why a decision was made.

## 2. Current strengths worth preserving

The personal ARETE already contains several strong governance ideas:

- explicit decision authority by session/daily/weekly scope;
- safety can override ordinary progression;
- evidence insufficiency defaults to maintaining the framework rather than changing something to appear useful;
- automatic plan writes are disabled;
- blockers prevent plan mutation under incomplete data or significant symptoms;
- reason IDs and confidence are already represented.

These are excellent foundations.

## 3. Critical findings

### 3.1 Goals are currently rich prose, not formal decision objects

The athlete profile contains priorities, maintenance goals and constraints, but they are not normalized into a hierarchy that a conflict resolver can reliably use.

A public core needs explicit:

```text
primary
secondary
maintenance
constraint
```

plus horizon and scope.

### 3.2 Evidence, policy and personal preference are currently mixed

Examples in `coaching-policy.json` include pain cutoffs, set caps, running-load ratios and progression counts. Some are reasonable conservative policies; others are evidence-informed heuristics. The engine needs to know which category each rule belongs to.

**Verdict:** every executable rule needs provenance and rule class.

### 3.3 Numeric thresholds require justification and calibration

Rules such as volume ratios, weekly set caps or fixed RPE cutoffs should never gain scientific authority merely by existing in configuration. A threshold can still be used as:

- user-specific policy;
- temporary conservative product guardrail;
- evidence-supported rule;

but the distinction must be visible.

### 3.4 “No change” is a first-class decision

The current default is directionally correct. ARETE should explicitly model `maintain` as a successful outcome when evidence for modification is inadequate.

### 3.5 The engine needs candidate-action comparison

A recommendation should not jump directly from signal to action. For material decisions, it should consider plausible alternatives and explain why one was preferred.

### 3.6 Plan mutation needs stronger governance than explanation

Explaining “reduce volume” and actually editing future sessions are different authority levels. The latter requires validation, conflict checks, historical revision creation and usually explicit user consent in early product stages.

### 3.7 Decision confidence must not be derived from evidence quality alone

A high-quality scientific rule can have low confidence if athlete data are incomplete. Conversely, a highly certain observation can sit in a domain with weak prescriptive evidence.

## 4. Scientific/decision-science principles

Athlete monitoring consensus (PMID 28463642) supports integrating multiple internal/external measures rather than relying on one load metric. The IOC load/injury consensus (PMID 27535989) emphasizes multifactorial context. Recent multidimensional athlete-monitoring review (PMID 41824225) reinforces the need to interpret short-term readiness signals in longer-term adaptation context.

These support a multidimensional decision engine but do not validate specific universal thresholds.

## 5. Audit verdict

**KEEP THE AUTHORITY/REASON-CODE CULTURE; FORMALIZE GOALS, RULE CLASSES, CONFLICT RESOLUTION AND PLAN-MUTATION GOVERNANCE.**

## 6. Required tests

1. strong progression signal but primary key race session tomorrow;
2. high readiness but relevant worsening symptom;
3. weak recovery signal alone → maintain;
4. multiple converging signals → modify;
5. maintenance goal where stable performance is success;
6. conflicting primary/secondary goals;
7. policy threshold fires but evidence classification is low;
8. incomplete data → no silent plan rewrite;
9. user override preserved;
10. same state under different goal hierarchy produces different justified decision.
