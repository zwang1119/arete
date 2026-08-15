# Design Proposal 06 — Symptom & Safety Engine

> Status: proposed architecture; no production implementation yet  
> Date: 2026-08-15

## 1. Objective

Create a symptom system that is conservative enough to protect training decisions, transparent enough to audit, and narrow enough to remain a training/general-wellness feature rather than an implicit diagnostic engine.

## 2. Core entities

```text
SymptomObservation
SymptomEpisode
FunctionalImpact
SymptomAssessment
SafetyPolicy
EscalationDecision
```

### SymptomObservation

```text
id
observed_at
body_region
side
severity_scale
severity_value
quality[]
provoking_activity
provoking_movement
swelling
instability
gait_or_function_change
forced_modification
original_text
source
```

Unknown fields remain unknown.

### SymptomEpisode

Groups observations that plausibly describe the same ongoing problem without assigning a diagnosis.

```text
id
region/side
onset_at
latest_observation_at
trajectory: improving | stable | worsening | recurrent | unknown
status: active | resolved | recurrent
observation_refs[]
```

## 3. Evidence vs policy

Every safety rule should declare whether it is:

```text
clinical/general safety principle
condition-specific evidence
product policy
user-specific policy
```

Example:

```text
pain >= 4/10 → review
```

may be a deliberately conservative user/product policy, but must not be labelled a universally validated clinical cutoff.

## 4. Escalation dimensions

Assess multiple dimensions:

```text
severity
trajectory
quality
objective/functional change when reported
forced training modification
recurrence
same-region exposure in upcoming training
systemic/high-risk symptom category
```

Do not calculate a fake numeric injury-risk score.

## 5. Training actions

Allowed ARETE actions may include:

```text
continue_with_monitoring
reduce_or_modify_exposure
avoid_specific_provoking_component
stop_current_exposure
review_before_next_exposure
recommend_professional_evaluation
```

The rationale, triggering observations, policy version and uncertainty are logged.

These are training-management actions, not diagnoses or medical treatment plans.

## 6. Safety precedence

A symptom rule may override progression/recovery optimism when it is materially relevant.

Example:

```text
strong performance signal
+ worsening knee symptom during squatting
→ progression recommendation suppressed
```

The override should identify exactly which training exposure is constrained; it should not unnecessarily block unrelated training.

## 7. Resolution and recurrence

Resolution does not delete history.

A user may report:

```text
resolved on date X
```

A later observation in the same region can create a recurrent episode relationship.

## 8. Clinical boundary

ARETE must not automatically output labels such as:

```text
patellar tendinopathy
meniscus injury
rotator cuff tear
overtraining syndrome
```

unless a future regulated clinical product is explicitly developed and validated.

When the user already has a clinician-provided diagnosis, ARETE may record it as `user_or_clinician_reported_label` with provenance; it should not independently verify it.

## 9. Athlete Attention

Symptoms appear in Athlete Attention only when they are likely to alter current/next behavior.

Coach Lens can show the full episode trajectory.

No repeated nuisance alerts for stable low-materiality symptoms unless a policy says they need action.

## 10. Public-alpha configuration

Default public policy should be conservative and simple. User-specific thresholds may be added later, but the system must display that these are personal policies rather than scientific constants.

## 11. Acceptance criteria

A reviewer must be able to inspect:

```text
what the athlete reported
what ARETE inferred
which policy fired
why it mattered to this session
what ARETE did not infer
```

If that chain cannot be reconstructed, the symptom decision is not production-ready.
