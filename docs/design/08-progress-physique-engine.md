# Design Proposal 08 — Progress & Physique Engine

> Status: proposed architecture; no production implementation yet  
> Date: 2026-08-15

## 1. Objective

Turn longitudinal observations into claims only when the claim-specific evidence, comparability and measurement reliability justify it.

## 2. Core entities

```text
ObservationSeries
MeasurementProtocol
MeasurementRegime
ProgressQuestion
ProgressClaim
EvidenceGateResult
```

### ProgressQuestion

Examples:

```text
has maximal-strength performance improved?
has submaximal running response improved?
has body mass trend changed?
has waist circumference changed?
is there evidence consistent with hypertrophy?
has symptom tolerance improved?
```

The question determines the required evidence.

## 3. Evidence-gate pipeline

```text
candidate observation
    ↓
measurement validity / source quality
    ↓
comparability
    ↓
repeatability / duration
    ↓
measurement error / meaningful-change threshold
    ↓
context/confounders
    ↓
claim-specific evidence rule
    ↓
ProgressClaim
```

## 4. Claim states

Use language such as:

```text
insufficient_data
single_observation
emerging_signal
probable_trend
established_trend
stable
possible_regression
```

Do not assume every claim needs the same number of observations or days.

Each state stores:

```text
rule_id
evidence_refs
period
measurement_regime
confidence
limitations
```

## 5. Measurement protocols

### Body weight

Store raw weight and conditions when known. Use a configurable smoothing method for display/decision support. Seven-day rolling mean can be a useful user policy, not a universal biological rule.

### Circumference

Store:

```text
anatomical_site
side
measurement_method
tape/device
protocol
conditions
repeat_measurements_if_available
```

Prefer same-site/same-protocol comparisons.

### Photos

Store or reference only with explicit consent and strong privacy controls.

Protocol metadata may include:

```text
view
camera/device
distance
height
lighting
pose
pump/training status
```

ARETE should initially treat photos as user-visible qualitative evidence. Automated body-composition inference should not ship without independent validation, privacy review and evidence governance.

### Body composition

Store:

```text
method
instrument
software/version
protocol
estimate type
precision/LSC if known
```

A new instrument creates a measurement-regime change.

## 6. Hypertrophy inference

A stronger hypertrophy claim may combine convergent evidence such as:

```text
standardized circumference/validated composition change
+ body-mass trend
+ training exposure/progression support
+ waist/context where relevant
```

Training progression is supporting evidence, not proof.

Output language should match evidence strength, for example:

```text
“Measurements are consistent with increased upper-arm size.”
```

rather than:

```text
“You gained exactly 0.8 kg of muscle.”
```

unless a method genuinely supports that precision.

## 7. Goal-aware success

Progress is goal-relative.

Examples:

- maintenance goal: stable strength at lower training volume may be success;
- rehabilitation/tolerance goal: more load/ROM with stable symptoms may be progress;
- physique goal: body change without unacceptable waist increase may be relevant;
- endurance goal: improved standardized test or repeated submaximal response.

## 8. Independent supporting signals

A progress rule may require or reward independent supporting evidence, but “independent” must mean a genuinely different measurement pathway, not two metrics derived from the same underlying source.

Example:

```text
Strava pace + Strava-derived pace/HR metric
```

are not fully independent.

## 9. No hindsight rewriting

Progress claims are versioned. A later correction may produce a new claim revision, but the original decision/claim and evidence available at that time remain auditable.

## 10. Public-alpha behavior

Users with minimal data receive modest claims. More measurement capability increases the number and strength of questions ARETE can answer; it must never incentivize users to collect unnecessary sensitive data merely to fill dashboards.

## 11. Acceptance criteria

No `Improved` label may appear unless the underlying claim identifies:

- what improved;
- compared with what;
- over what period;
- under what measurement regime;
- what evidence passed the gate;
- remaining uncertainty.
