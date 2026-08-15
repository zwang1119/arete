# Design Proposal 07 — Hybrid / Concurrent Training Engine

> Status: proposed architecture; no production implementation yet  
> Date: 2026-08-15

## 1. Objective

Coordinate strength and endurance training according to goal priority, session importance, local overlap and current constraints while preserving each domain's own load/performance semantics.

## 2. Core entities

```text
GoalPriority
SessionImportance
TrainingExposureVector
CrossDomainConflict
SchedulingAssessment
SchedulingDecision
```

## 3. Goal hierarchy

Every meaningful hybrid decision should understand:

```text
primary
secondary
maintenance
constraint
```

A session can also be marked:

```text
key
important
ordinary
optional
```

The engine should protect the primary goal's key sessions first, subject to safety constraints.

## 4. TrainingExposureVector

Do not produce one number. Represent dimensions such as:

```text
cardiovascular_demand
endurance_intensity
running_mechanical_exposure
lower_body_resistance_exposure
upper_body_resistance_exposure
explosive_power_exposure
local_muscle_exposure[]
session_duration
```

Values may be categorical/ordinal where exact quantification is not defensible.

## 5. Conflict assessment

For each pair of nearby sessions evaluate:

```text
time_gap
body_region_overlap
movement_pattern_overlap
intensity_overlap
session_importance
goal_priority
acute_quality_risk
possible_long_term_interference
symptom_constraints
```

Output should include reason codes rather than a numeric interference score.

## 6. Scheduling principles

1. Safety/medical-relevance constraints override optimization.
2. Protect the primary goal's key session.
3. When two sessions compete for the same local capacity, place the higher-priority session first or separate them when feasible.
4. Upper-body strength and easy endurance often require little special handling unless other evidence says otherwise.
5. Same-day training is allowed.
6. Several-hour separation may be recommended when session-quality or explosive-strength protection is important, but no universal minimum gap is hard-coded.
7. Maintenance work may be reduced rather than forcing equal priority across domains.
8. Avoid unnecessary plan changes when no material conflict exists.

## 7. Acute vs chronic reasoning

Store separately:

```text
acute_session_quality_concern
chronic_adaptation_concern
```

Example:

```text
hard run 2 h before lower-body strength
→ acute lifting quality concern: moderate
→ long-term hypertrophy concern: uncertain/low
```

Do not convert the acute concern into a claim of long-term interference.

## 8. Order

When same-day conflict cannot be avoided:

```text
higher-priority adaptation/session first
```

unless safety, logistics or the actual program goal indicates otherwise.

This principle is preferred to a universal `strength always first` rule.

## 9. Decision outputs

```text
maintain_schedule
reorder_same_day
increase_separation
reduce_maintenance_dose
move_optional_session
review_manually
```

Every recommendation records which goal/session is being protected.

## 10. Public-alpha behavior

A strength-only or endurance-only user never loads Hybrid Engine logic.

Hybrid capability activates only when multiple training domains are present and goals/constraints make coordination relevant.

## 11. Acceptance criteria

A reviewer must be able to answer:

> What conflict was detected, what goal was protected, why was this scheduling change preferred, and what scientific uncertainty remains?

No output may say only “hybrid fatigue high”.
