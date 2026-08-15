# Design Proposal 03 — Strength Engine

> Status: proposed architecture; no production implementation yet  
> Date: 2026-08-15  
> Derived from: Phase 0 Audit 03

## 1. Objective

Build a strength engine that can support strength, hypertrophy, maintenance and hybrid users without pretending that one score captures all adaptations.

## 2. Canonical strength entities

```text
ExerciseDefinition
ExerciseConfiguration
SetPrescription
SetPerformance
MovementPerformance
StrengthSessionAssessment
ComparisonContext
ProgressionDecision
```

### ExerciseDefinition

Stable movement identity independent of provider naming.

Recommended concepts:

```text
id
canonical_name
movement_family
primary_pattern
laterality_capability
aliases
```

Do not encode every setup detail into the definition.

### ExerciseConfiguration

Represents the version actually prescribed/performed:

```text
exercise_definition_id
implement
load_basis
equipment_id
bench_angle
grip
rom
stance
assistance
tempo
side
configuration_unknowns
```

### Load basis

Use explicit semantics:

```text
total_external_load
per_hand_load
added_load
bodyweight_only
bodyweight_plus_added
machine_display_load
single_side_load
assisted_load
unknown
```

Never silently convert an unknown machine display into kilograms of external resistance.

## 3. Prescription model

A set prescription should support ranges and autoregulation:

```text
set_role        warmup | work | backoff | drop | technique | test | optional
reps_target     exact | range | none
effort_target   RPE/RIR range | none
load_target     exact | range | reference | none
rest_target
execution_constraints
conditional_adjustments
```

Example:

```text
3 work sets
8–10 reps
RPE 7–8
load = athlete-selected from warm-up
if first work set > RPE8 → reduce load
```

Following the condition correctly is adherence, not deviation.

## 4. Performance facts

Each performed set should preserve:

```text
completed
set_role
repetitions
duration
load_value
load_unit
load_basis
RPE
RIR
side
notes
source_refs
quality_flags
```

RPE/RIR may be missing. Missing subjective effort lowers the range of claims available; it does not invalidate the set.

## 5. Comparison is question-specific

Do not ask only:

> Are these the same exercise?

Ask:

> Are these sufficiently comparable for this analytic question?

Examples:

- maximal-strength comparison may require highly similar configuration;
- hypertrophy-volume accounting may tolerate a broader family relationship;
- movement-tolerance monitoring may require the same provoking pattern but not identical equipment.

Use:

```text
ComparisonContext
  question
  required_fields
  tolerated_differences
  unresolved_unknowns
  result: comparable | partially_comparable | not_comparable | uncertain
```

## 6. Performance assessment is multidimensional

A movement exposure can generate candidate signals:

```text
load_change
rep_change
RPE/RIR_change
estimated_strength_change
set_completion
technique_or_ROM_change
symptom_change
```

Do not immediately collapse them into one score.

An `estimated_strength_change` may use an e1RM model only when:

- the formula is declared;
- the rep range is appropriate for that formula;
- configuration is comparable;
- effort context is adequate;
- the output is explicitly labeled estimated.

ARETE should support multiple formulas behind one interface rather than pretending one e1RM equation is ground truth.

## 7. Progression logic

Progression is a decision, not merely detection of a better performance.

A progression decision should consider:

```text
primary goal
prescription intent
recent comparable exposures
performance evidence
RPE/RIR
symptoms
exercise configuration
available equipment increments
upcoming competing sessions
uncertainty
```

Possible actions:

```text
maintain
increase_load
increase_reps_within_range
increase_sets
reduce_load
reduce_sets
change_variation
review
```

The engine should usually change the smallest variable needed to continue the intended progression.

No universal rule such as `two good sessions = add weight` belongs in ARETE Core. A user/program policy may use such a rule if explicitly configured and evidence-tagged.

## 8. Volume model

Store objective descriptors first:

```text
work_sets
repetitions
external_load_volume_when_semantically_valid
direct_exposure
indirect_exposure
muscle_or_movement_mapping_confidence
```

Do not make `sets × reps × kg` a universal stimulus metric.

For hypertrophy-oriented summaries, ARETE may later use direct/fractional set accounting, but the mapping must expose uncertainty and must not imply that 0.5 indirect set is a biological constant.

Do not create a universal `effective_sets` counter.

## 9. Strength progress claims

Use staged claims:

```text
single-exposure observation
candidate performance signal
repeatable performance signal
established performance trend
```

Examples of claims that can be supported:

- “Rep performance improved under a comparable load/configuration.”
- “Estimated strength is trending upward across comparable exposures.”

Claims that cannot be made from training performance alone:

- “This muscle grew.”
- “The program is optimal.”
- “Recovery has improved.”

## 10. Goal-specific interpretation

### Strength priority

Favor specificity, heavier-load performance, technique stability and estimated/maximal strength evidence.

### Hypertrophy priority

Favor sustainable volume, proximity-to-failure context, progression over time and body/physique evidence downstream. Do not require failure.

### Maintenance

Success may mean stable performance at lower dose, not continuous progression.

### Rehabilitation/tolerance

Success may mean increased tolerated ROM/load/repetitions with stable or improving symptoms. This must not be conflated with medical treatment claims.

## 11. Public-alpha minimum

A strength-only user should receive value with only:

```text
exercise
sets
reps
load/date
```

Optional capabilities unlock with:

```text
RPE/RIR
planned training
bodyweight
symptoms
measurements
```

## 12. Acceptance criteria

The engine is acceptable only if it can explain why two exposures were or were not compared, preserve load semantics, distinguish performance from hypertrophy, and return `insufficient_data` rather than fabricate a progression decision.
