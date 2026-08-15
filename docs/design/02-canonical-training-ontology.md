# Design Proposal 02 — Canonical Training Ontology and Plan / Actual Association

> **Status:** proposed architecture; no production implementation yet  
> **Date:** 2026-08-15  
> **Derived from:** Phase 0 Audit 02  
> **Refines:** the session-model section of `docs/design/01-data-session-foundation.md`  
> **Governing principles:** first principles, historical integrity, scientific defensibility, graceful degradation, auditability, legal compliance

---

# 1. Design objective

ARETE needs a stable vocabulary for training reality that does not depend on a particular provider, UI, athlete, or sport.

The canonical sequence is:

```text
SOURCE EVIDENCE
    ↓
CANONICAL FACTS
    ↓
ASSOCIATION
    ↓
ASSESSMENT
    ↓
DECISION
    ↓
PRESENTATION
```

The system must never rely on a presentation object as the authoritative data model.

The defining rule is:

> **Facts describe what existed or happened. Associations describe relationships. Assessments describe meaning. Decisions describe what to do next. None may silently rewrite another.**

---

# 2. Core design principles

## 2.1 Internal identity is provider-neutral

Internal IDs should be stable ARETE identifiers, not provider IDs and not dates.

Provider identifiers belong in source-reference records.

Bad:

```text
strength-plan:2026-08-14
strava:123456
```

as canonical entity IDs.

Preferred conceptual shape:

```text
PlannedSession.id      = ARETE-controlled stable ID
PerformedSession.id    = ARETE-controlled stable ID
SourcePlanRecord       = provider + external ID + source payload/reference
SourceObservation      = provider + external ID + source payload/reference
```

A provider or schedule change must not automatically change the identity of the real intended/actual training entity.

---

## 2.2 Date is a property, not identity

A session can be rescheduled without becoming a different planned session.

Two sessions can occur on the same date without identity collision.

---

## 2.3 Historical plans are immutable evidence

A plan may evolve, but old revisions remain part of the historical record.

The system must be able to answer:

> What prescription did the athlete legitimately have before this training exposure?

A post-hoc edit must not silently become the evaluation baseline for an already-performed session.

---

## 2.4 No plan is a valid state

A user who only logs training still has valid `PerformedSession` data.

The absence of `PlannedSession` means plan adherence is not applicable; it does not make progress analysis impossible.

---

## 2.5 Session association is explicit

A planned session and performed session are never considered linked merely because they share a date.

Date can be evidence for an association, not the association itself.

---

## 2.6 Prescription is not one number

A valid prescription can contain:

- exact targets;
- ranges;
- ceilings/floors;
- effort descriptors;
- equipment/setup constraints;
- optional components;
- conditional autoregulation;
- technique instructions;
- substitutions;
- safety/training guardrails.

ARETE should preserve this structure where practical without forcing every user to fill every field.

---

# 3. Canonical ontology overview

```text
Athlete
  │
  ├── TrainingPlan (optional container)
  │      │
  │      ├── TrainingBlock (optional planning context)
  │      │
  │      └── PlannedSession
  │              │
  │              └── PlannedSessionRevision ── PrescriptionComponent[]
  │
  ├── ScheduleExpectation / TrainingCommitment
  │
  └── PerformedSession
          │
          └── PerformedComponent[]

PlannedSessionRevision
          │
          └──── SessionAssociation ──── PerformedSession
                         │
                         └── method / confidence / relationship / reasons

PlannedSessionRevision + PerformedSession + Association
                         │
                         ▼
                  SessionAssessment
                         │
                         ▼
                      Decision
```

Source evidence and provenance attach to all relevant canonical entities without becoming their identity.

---

# 4. Entity definitions

## 4.1 TrainingPlan

An optional container for a coherent programme or plan source.

Examples:

- a Runna race-training plan;
- a six-week strength block;
- a coach-authored plan;
- an imported programme.

Conceptual fields:

```text
id
athlete_id
name
status
start_date
end_date
source_refs[]
created_at
```

A user does not need a `TrainingPlan` to use ARETE.

---

## 4.2 TrainingBlock

Optional planning context describing a phase with goals or stimulus priorities.

Examples:

```text
base endurance
5K development
hypertrophy specialization
strength maintenance
race taper
```

A block is not reconstructed from UI assumptions.

Conceptual fields:

```text
id
training_plan_id?
athlete_id
name
start_date
end_date
priority_refs[]
goal_refs[]
source_refs[]
```

Detailed goal hierarchy belongs to the later Goals / Planning audit.

---

## 4.3 ScheduleExpectation / TrainingCommitment

Represents recurring or aggregate expectations that are **not session prescriptions**.

Examples:

```text
4 runs per week
quality run usually Wednesday
long run generally Sunday
3 strength exposures per week
```

These may guide planning and source-health expectations, but they must not create a `PlannedSession` automatically.

Conceptual fields:

```text
id
athlete_id
domain
expectation_type
frequency / preferred_window
valid_from
valid_to
priority
source_refs[]
```

---

## 4.4 PlannedSession

Stable identity of one intended training exposure.

It answers:

> Which intended session are we talking about across edits and rescheduling?

Conceptual fields:

```text
id
athlete_id
training_plan_id?
training_block_id?
created_at
lifecycle_status
source_refs[]
```

The mutable details of schedule and prescription belong in revisions.

---

## 4.5 PlannedSessionRevision

Immutable snapshot of what a planned session meant at a particular point in time.

This **replaces/refines** the earlier `PrescriptionRevision` concept from Design Proposal 01 because schedule changes are also historically important.

Conceptual fields:

```text
id
planned_session_id
revision_number
created_at
effective_at
approved_at?
author_type
reason_code?
schedule
prescription_components[]
raw_instruction?
source_refs[]
status
```

Suggested `status` values:

```text
draft
approved
superseded
cancelled
```

A revision should be immutable after creation. A new edit creates another revision.

---

# 5. Schedule model

Do not invent time precision.

A schedule should be able to express:

```text
date_only
fixed_time
start_window
floating_within_day
```

Conceptual shape:

```text
precision
local_date
timezone
scheduled_start?
scheduled_end?
earliest_start?
latest_start?
```

Examples:

### Runna calendar event with date but no reliable time

```text
precision = date_only
local_date = 2026-08-16
timezone = Pacific/Auckland
```

### Gym booking at a fixed time

```text
precision = fixed_time
scheduled_start = ...
```

Rescheduling creates a new `PlannedSessionRevision`; the session ID remains unchanged.

---

# 6. PrescriptionComponent

A planned session may contain one or more components.

Most sessions will contain one domain, but the model should allow legitimate mixed/brick sessions without forcing separate products.

Common conceptual fields:

```text
id
planned_session_revision_id
domain
order
role
intent
optional
targets[]
constraints[]
instructions[]
conditional_rules[]
rationale?
source_refs[]
```

Domains may extend this with their own structured prescription.

---

# 7. Target, constraint, instruction, and rationale

These semantics are deliberately separate.

## Target

What the athlete is trying to achieve.

Examples:

```text
400 m at 4:45/km
8–10 repetitions
RPE 7–8
3 work sets
```

## Constraint

A boundary, not an optimization target.

Examples:

```text
no faster than 5:50/km
RPE must not exceed 8
use this equipment/setup
stay within pain-free ROM
```

## Instruction / cue

Execution guidance that is not necessarily a measurable target.

Examples:

```text
controlled eccentric
keep pelvis stable
conversational effort
```

## Rationale

Why the prescription exists.

Examples:

```text
protect key interval session tomorrow
maintain lower-body strength during running block
shoulder-tolerant pressing choice
```

Raw source instruction should be retained even when structured parsing is complete.

---

# 8. Conditional autoregulation

ARETE must not force advanced training plans into fixed-load/fixed-rep prescriptions.

A legitimate prescription may be:

```text
3 sets of 8–10
Target RPE 7–8
Use warm-up response to select load
If first work set exceeds RPE 8, reduce load for subsequent sets
```

This is not a post-hoc plan change if the conditional rule was part of the valid prescription before execution.

Conceptual conditional-rule fields:

```text
condition
allowed_adjustment
affected_component
source
```

Initial implementation should support only clearly structured rules; unsupported prose remains raw instruction rather than being guessed by AI.

---

# 9. PerformedSession

Provider-neutral representation of a real-world training attempt.

It is not automatically equivalent to a provider activity record.

Conceptual fields:

```text
id
athlete_id
started_at?
ended_at?
timezone?
lifecycle_status
termination_reason?
source_observation_refs[]
created_at
```

Suggested lifecycle values:

```text
in_progress
ended
abandoned
unknown
```

`ended` means the training encounter/recording ended. It does **not** mean the prescription was fully completed.

A session may exist with zero completed work sets.

---

# 10. PerformedComponent

A performed session contains actual training components.

Common conceptual fields:

```text
id
performed_session_id
domain
order
source_observation_refs[]
```

Domain engines extend the component with appropriate observations.

---

# 11. Strength-domain extension points

Detailed exercise ontology will be finalized during the Strength Engine audit, but the canonical training model must reserve these distinctions.

## 11.1 MovementPrescription

Should be able to represent:

```text
exercise reference
configuration/setup
set prescriptions
rest
optional/substitution status
```

## 11.2 SetPrescription

Should support, where available:

```text
set role/type
set count or grouped-set count
repetition target/range
duration target/range
load target/range
load basis
RPE/RIR target/range
rest target/range
tempo
ROM constraint
side/laterality semantics
optional flag
```

## 11.3 MovementExecution / SetExecution

Observed execution is separate from prescription.

Possible load bases include:

```text
total_external_load
per_hand_load
added_load
bodyweight_only
bodyweight_plus_added
machine_display_load
single_side_load
unknown
```

Do not infer kilograms from a machine display or blank provider unit.

## 11.4 Exercise identity boundary

Do not decide in Module 02 that every grip/tempo/ROM difference creates a new canonical exercise.

Reserve separate concepts for:

```text
ExerciseDefinition
ExerciseConfiguration
ExecutionObservation
ComparisonEligibility
```

The Strength Engine audit must determine the exact identity rules.

---

# 12. Endurance-domain extension points

The current Runna parser already provides a useful starting shape.

Endurance prescription should support a structured sequence/tree:

```text
warmup
run
recovery
cooldown
repeat
```

Intensity semantics should be extensible beyond pace:

```text
pace
heart rate
power
RPE / effort
cadence
open / conversational
```

A target can be:

```text
exact
range
ceiling
floor
```

A long run with fast segments remains one structured prescription; `run_type` must not erase the actual step structure.

Actual device laps should not automatically be assumed to equal prescribed intervals.

---

# 13. SessionAssociation

Explicit relationship between a specific planned revision and performed session.

Conceptual fields:

```text
id
planned_session_id
planned_session_revision_id
performed_session_id
association_state
relationship_type
method
confidence
reason_codes[]
created_at
confirmed_by?
supersedes_association_id?
```

## 13.1 Association state

Whether the link is accepted as fact:

```text
candidate
confirmed
ambiguous
rejected
```

## 13.2 Relationship type

What the performed session means relative to the plan:

```text
fulfills
partially_fulfills
replaces
split_part
combined_fulfillment
```

Do not use `unplanned` or `not_performed` as association types when no relationship exists. Those are derived plan/execution states.

## 13.3 Method

How the association was established:

```text
provider_link
manual_confirmation
deterministic_match
heuristic_match
```

`manual_confirmation` can override an automated candidate but the prior automated evidence should remain auditable.

## 13.4 Confidence

Use categorical confidence initially:

```text
low
moderate
high
```

Do not present a numeric matching score as a calibrated probability unless it has actually been validated as one.

---

# 14. Global matching rules

Matching runs across a candidate set rather than independently per activity.

Evidence priority:

1. explicit provider-native linkage;
2. stable external IDs embedded by the plan/provider;
3. athlete manual confirmation;
4. compatible schedule/time window;
5. compatible domain/sport;
6. prescription-vs-actual duration/distance;
7. structured shape;
8. title/text as weak supporting evidence.

A high-confidence link must not be created if another materially plausible competing link exists, unless the relationship explicitly allows split/combined fulfillment.

Same calendar date alone is never enough for authoritative matching.

---

# 15. Derived plan execution state

Plan execution state is derived from plan lifecycle + association + source completeness.

Suggested conceptual values:

```text
not_due
candidate_match
fulfilled
partially_fulfilled
replaced
not_performed
cancelled
unknown
```

`not_performed` should only be asserted when the observation window is sufficiently complete/final. Missing imports must not be interpreted as missed training.

---

# 16. SessionAssessment

Assessment is downstream of canonical facts and association.

Conceptual fields:

```text
id
planned_session_revision_id?
performed_session_id
association_ids[]
domain
assessment_version
ruleset_version
created_at
execution_dimensions
response_dimensions
symptom_context
confidence
evidence_refs[]
reason_codes[]
```

Do not mutate `PerformedSession` to store interpretation.

## 16.1 No universal adherence score by default

Strength may evaluate:

- key movement completion;
- set/rep execution;
- load/RPE/RIR constraints;
- substitutions;
- symptom-driven modification.

Endurance may evaluate:

- distance/duration;
- interval execution;
- target/ceiling compliance;
- effort;
- physiological response where data quality permits.

A single scalar can be added later only for a validated, specific purpose.

---

# 17. Revision timing and anti-hindsight rule

This is a mandatory auditability rule.

For retrospective assessment, ARETE should normally use the latest **approved and legitimately effective** `PlannedSessionRevision` that existed before the relevant training exposure.

A plan edited after execution must remain visible as a later revision but must not silently replace the historical baseline.

Conceptual rule:

```text
assessment_baseline_revision =
latest approved revision whose effective state legitimately preceded execution
```

Special cases such as legitimate live coaching changes during a session should later be represented by explicit timestamped modification/decision events, not backdated edits.

---

# 18. CorrectionEvent

Corrections remain append-oriented, but targets become typed.

Conceptual fields:

```text
id
actor
created_at
target_kind
target_id
category
original_text
structured_patch?
materiality
```

Possible target kinds:

```text
source_observation
source_plan_record
planned_session
planned_session_revision
performed_session
session_association
performed_component
exercise_configuration
symptom_observation
session_assessment_context
```

A correction does not delete the original raw evidence.

---

# 19. Provenance and SourceDependency

Every canonical field or derived object that materially depends on external-provider data must retain lineage.

Conceptual dependency record:

```text
consumer_entity_type
consumer_entity_id
source_record_type
source_record_id
dependency_role
```

This supports:

- audit;
- recalculation;
- correction;
- provider-scoped deletion;
- export;
- legal/terms compliance.

Canonical internal IDs can survive a provider change only if the remaining canonical object is still legally and evidentially valid after required source-data deletion.

---

# 20. Training day is not a training session

A day can contain:

```text
morning strength
late-afternoon intervals
```

These are two sessions.

A UI may summarize them as a `mixed` training day, but `mixed day` is presentation aggregation, not canonical session identity.

Conversely, a legitimate brick/multi-modal training encounter may contain multiple components under one planned/performed session.

The canonical model must not infer session merging solely from shared date.

---

# 21. Public-alpha implications

The same Core supports:

## Strength-only user with no plan

```text
PerformedSession
→ Strength assessment/progress where possible
```

No plan-related warnings are shown.

## Runner with Runna + Strava

```text
SourcePlanRecord → PlannedSessionRevision
SourceObservation → PerformedSession
SessionAssociation
→ Endurance assessment
```

## Hybrid user

Strength and endurance sessions coexist without being collapsed into one load ontology.

## Highly instrumented personal profile

Additional sources enrich the same canonical entities rather than creating a different Core.

---

# 22. Migration from the current personal ARETE

Do not rewrite everything at once.

## Migration A — introduce stable internal IDs

Keep current outputs while mapping provider/date IDs into source references.

## Migration B — introduce canonical `PlannedSession` + `PlannedSessionRevision`

Start with strength and Runna plans.

## Migration C — introduce provider-neutral `PerformedSession`

Initially one-to-one with current Xunji/Strava sessions where unambiguous, while retaining source observations.

## Migration D — introduce `SessionAssociation`

Replace embedded `planned_session` pointers.

## Migration E — move outcome/adherence into `SessionAssessment`

Keep UI output compatible during migration.

## Migration F — introduce typed corrections

Preserve existing correction events through an adapter/migration layer.

## Migration G — remove date/provider identity assumptions from Core

Only after tests demonstrate equivalent or intentionally improved behavior.

---

# 23. Required invariants

These should eventually become schema/database constraints and tests.

1. `PlannedSession.id` does not change when a session is rescheduled.
2. Two planned sessions can share the same date.
3. `PerformedSession.id` is not a provider external ID.
4. A source record cannot be silently destroyed by canonicalization.
5. A revision is immutable once created.
6. An assessment references the exact plan revision used as its baseline.
7. A post-execution plan edit cannot silently change a historical assessment.
8. A performed session can exist without a plan.
9. A planned session can exist without a performed session.
10. A performed session can exist with zero work sets.
11. Association ambiguity is valid and does not force a match.
12. Same date alone cannot confirm an association.
13. Session assessment does not mutate canonical facts.
14. Provider deletion lineage remains discoverable.
15. Presentation aggregation cannot create canonical sessions.

---

# 24. Required synthetic tests before implementation is accepted

At minimum, the 20 cases listed in Phase 0 Audit 02 must be represented as synthetic fixtures.

Additional high-value cases:

21. a Runna workout rescheduled while retaining the same planned-session identity;
22. a strength session revised before execution and edited again after execution;
23. an in-plan conditional load reduction correctly treated as compliant autoregulation;
24. an actual session with the correct total set count but wrong key movement;
25. two source observations representing one performed session;
26. one source observation representing only one component of a multi-component session;
27. a date-only prescription that never receives a fabricated start time;
28. a user manually rejects an incorrect automatic association;
29. a provider external ID changes while internal session identity remains stable after reconciliation;
30. a source is deleted and dependent derived entities are recalculated or removed according to policy.

---

# 25. Reference and licensing posture

This design is informed by, but does not copy code from:

- GoldenCheetah — planned/actual distinction, original planned date, downstream adherence concepts; GPL-family licensing requires caution for direct code reuse.
- wger — separation between routine prescription and workout logs, explicit set/repetition/unit concepts; AGPL licensing requires caution for direct code reuse.
- Coach Watts — provider normalization before higher-level domain logic; Apache-2.0, but any future direct reuse still requires exact-file/version provenance review.

Scientific reporting principles were informed by the Consensus on Exercise Reporting Template (CERT):

- Slade SC et al. Phys Ther. 2016. PMID: 27149962.
- Slade SC et al. Br J Sports Med. 2016. PMID: 27707738.

The current ACSM resistance-training position stand should inform later Strength Engine rule design, not be converted directly into universal individual prescriptions.

---

# 26. Final design decision

Module 02 should establish a **small stable common core** and allow domain-specific depth below it.

The common core is:

```text
PlannedSession
PlannedSessionRevision
PerformedSession
SessionAssociation
SessionAssessment
```

with source evidence and provenance around them.

Strength and endurance should **share these nouns but not be forced to share the same prescription, execution, or assessment variables**.

This achieves the desired balance:

> **one ARETE Core, without pretending every kind of training is the same.**
