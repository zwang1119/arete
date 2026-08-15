# Design Proposal 04 — Endurance Engine

> Status: proposed architecture; no production implementation yet  
> Date: 2026-08-15

## 1. Objective

Represent endurance training in a way that is faithful to the prescription, technically robust across devices/providers, and scientifically conservative about physiological interpretation.

## 2. Canonical endurance entities

```text
EnduranceActivity
EndurancePrescription
EnduranceStepPrescription
EnduranceSegmentObservation
EnduranceSessionAssessment
EndurancePerformanceTest
ZoneModel
EnvironmentContext
SensorCoverage
```

The generic `PlannedSession` / `PerformedSession` ontology remains authoritative; these are domain-specific payloads.

## 3. Prescription semantics

A step can express:

```text
kind: warmup | steady | interval | recovery | cooldown | strides | free
quantity: distance | duration | repetition_count
intensity_target: pace | speed | HR | power | effort | race_pace_reference
constraint: ceiling | floor | range
instruction
optional
```

Preserve source wording when parsing is incomplete.

A ceiling is not a target. A conversational instruction is qualitative guidance, not an exact physiological threshold.

## 4. Actual activity facts

Preserve objective observations separately:

```text
start/end
distance
moving/elapsed time
GPS/elevation
pace/speed
HR samples/summary
power when available
laps/segments
RPE
sensor/source metadata
interruptions/context
```

Every derived statistic should identify which samples/segments contributed and whether coverage is adequate.

## 5. ZoneModel is versioned

```text
id
method
inputs
thresholds
created_at
valid_from
source
confidence
```

Examples of methods:

```text
HRmax percentage
HR reserve
threshold HR
pace/power thresholds
provider-defined
```

Do not compare “Zone 2 minutes” across methods without explicitly accounting for a model change.

## 6. Session assessment layers

### A. Prescription execution

Question: Did the athlete perform the intended structure?

Possible dimensions:

```text
distance/duration completion
interval completion
target-range execution
constraint compliance
recovery execution
warm-up/cool-down completion
```

### B. Physiological response

Question: How did the athlete respond?

Possible observations:

```text
HR response
RPE
pace-HR relationship
cardiovascular drift
recovery response
```

### C. Context

```text
weather
terrain
elevation
traffic/stops
illness/fatigue
sensor quality
```

Never collapse A+B+C into an authoritative single quality score.

## 7. Matching structured steps

Use deterministic alignment where feasible:

1. explicit workout-step IDs;
2. recorded laps matching planned distances/durations;
3. sequence-constrained matching;
4. tolerant alignment with confidence;
5. `unavailable/ambiguous` when the recording cannot support it.

Do not fabricate interval precision from coarse splits.

## 8. Training-load representation

Keep external and internal load distinct.

External examples:

```text
distance
duration
elevation
work/power-based metrics where valid
high-intensity exposure
```

Internal examples:

```text
session-RPE load
HR-derived metrics
```

Provider-specific scores (Relative Effort, Training Effect, etc.) may be retained as vendor observations, not canonical truth.

## 9. Training-intensity distribution

ARETE may summarize distribution using declared zone models and methods such as time-in-zone or session-goal classification.

Output should be descriptive:

```text
low / moderate / high exposure
method used
period
coverage
```

Do not prescribe a universal polarized/pyramidal ratio. Goal, athlete level, event, phase and coach/program design determine whether a distribution is appropriate.

## 10. Longitudinal performance evidence classes

Use explicit evidence classes:

```text
A: official race / standardized test
B: planned repeatable benchmark workout
C: comparable submaximal exposure
D: opportunistic training observation
```

Higher classes can support stronger progress claims.

Submaximal trends should use repeated exposures and account for context/measurement regime. Examples include similar pace with lower HR/RPE or faster pace at similar HR/RPE, but only when comparability is defensible.

## 11. HR drift / decoupling

If computed:

- define the formula;
- define included time window;
- require adequate HR and pace/power coverage;
- expose heat/hydration/terrain limitations;
- report as descriptive within-session response;
- do not impose a universal pass/fail threshold.

## 12. Public-alpha minimum

A runner with only FIT/TCX/GPX or a permitted provider activity source should still receive:

```text
training history
volume
session structure where available
performance/test trends
basic plan-actual analysis if plan exists
```

HR, power, sleep and recovery are enhancements, not prerequisites.

## 13. Acceptance criteria

The engine must be able to say:

- “execution was good, response was unusually high”;
- “response looked normal but the prescription was not followed”;
- “the data are too coarse to judge intervals”;
- “this trend is emerging but not established”;

without forcing these states into one score.
