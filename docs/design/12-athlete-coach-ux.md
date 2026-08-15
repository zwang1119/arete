# Design Proposal 12 — Athlete Lens, Coach Lens, and Attention UX

> Status: proposed architecture; no production implementation yet  
> Date: 2026-08-15

## 1. Objective

Expose only the information needed for the current user's decisions while preserving full auditability behind progressive disclosure.

## 2. Capability-driven shell

Navigation is generated from capabilities, not providers.

Examples:

### Strength-only

```text
Today
Strength
Progress
Coach
Body (if available)
```

### Endurance-only

```text
Today
Endurance
Progress
Coach
```

### Hybrid

```text
Today
Strength
Endurance
Recovery (if available)
Progress
Coach
```

No empty “connect another app” modules on the primary interface.

## 3. Athlete Lens

Primary questions:

```text
What should I do today?
Did anything important change?
How did the last session go?
What is next?
Is there evidence of meaningful progress?
```

The page should prioritize action and state, not dashboards.

## 4. Attention contract

An Athlete Attention item requires:

```text
behavioral_relevance = true
materiality threshold met
not already resolved/acknowledged unless escalated
sufficient confidence for the requested action
```

Attention classes may include:

```text
safety_review
action_required
data_blocking_decision
meaningful_plan_change
```

`monitor` signals may live quietly in session detail rather than becoming alerts.

## 5. Attention episodes

Create stable identity for an ongoing issue:

```text
attention_episode_id
opened_at
last_changed_at
severity/materiality
status: open | acknowledged | resolved
trigger_refs
```

A refresh does not create a new alert unless the evidence materially changes.

## 6. Data-health language

Athlete wording examples:

```text
“Training is still syncing.”
“Today's workout data are complete.”
“ARETE last checked Strava at 19:20.”
“Plan data are unavailable, so adherence cannot be assessed.”
```

Coach Lens can show transport, coverage, completeness and finality fields.

## 7. Coach Lens

Use a repeatable structure:

### Observed
Canonical facts with provenance.

### Interpreted
Domain assessments and uncertainty.

### Decided
DecisionRecord, rules and goals involved.

### Alternatives
Material candidate actions considered.

### Unknown / limitations
Missing data and evidence boundaries.

Each item can drill down to source/evidence references.

## 8. Progress UI

Render only ProgressClaim objects.

Possible user-facing language:

```text
One encouraging observation
Emerging signal
Probable trend
Established trend
Stable
Not enough comparable data
```

Avoid green arrows for changes smaller than the relevant evidence gate.

## 9. Corrections

When a correction can materially improve interpretation, present a low-friction prompt:

```text
“Were these two machine presses on the same machine?”
```

Do not ask users to clean every ambiguous historical record. Request correction only when it affects a relevant decision or progress claim.

## 10. Provider visibility

Provider names appear in:

- settings/integrations;
- provenance detail;
- sync/data-health diagnostics;
- source-specific legal notices.

They should not define top-level product concepts.

## 11. Accessibility and safety UX

Critical status must not rely solely on color. Use text, icons and accessible labels. Safety messages must avoid panic-inducing wording while making the required action clear.

## 12. Public-alpha principle

The alpha should intentionally have fewer screens and fewer notifications than the personal power-user version. Feature exposure expands by capability and actual decision value.

## 13. Acceptance criteria

A new user with one data source should understand the homepage without learning ARETE's ontology, while an expert reviewer should be able to reach the exact rule/evidence/source chain behind any consequential recommendation.
