# Design Proposal 05 — Recovery Engine

> Status: proposed architecture; no production implementation yet  
> Date: 2026-08-15

## 1. Objective

Represent recovery as a set of partially independent observations relevant to training decisions, with athlete-specific baselines and explicit uncertainty.

## 2. Core model

```text
RecoveryObservation
RecoveryDimensionState
MeasurementRegime
RecoveryAssessment
```

### RecoveryObservation

```text
metric
value
unit
observed_at
measurement_window
source
method
device
raw_or_vendor_derived
quality_flags
```

### Recovery dimensions

```text
sleep
autonomic
subjective_fatigue
local_fatigue_soreness
psychological_readiness
illness
```

Do not require all dimensions.

## 3. Baseline logic

Baselines should be athlete-specific and method-specific.

Store:

```text
baseline_window
minimum_observations
central_tendency
variability
measurement_regime_id
```

Do not use a universal percentage drop as a standalone action threshold.

When device/method changes materially:

```text
measurement_regime_change
→ comparability = uncertain/new baseline
```

## 4. Sleep model

Prefer observable/ interpretable components:

```text
sleep_duration
sleep_window
sleep_midpoint
regularity
awakenings_if_reliable
subjective_quality
sleep_need_or_preference
```

Vendor stages/scores remain vendor observations.

A single short night is a signal, not a deterministic training modification. Persistent sleep restriction plus fatigue/performance/symptom evidence may raise decision relevance.

## 5. HRV and resting HR

For HRV:

- preserve metric type (e.g. RMSSD);
- measurement position/timing/method;
- device;
- averaging procedure;
- artifact/coverage quality;
- athlete-specific baseline.

Prefer rolling/serial interpretation over isolated values.

No rule may say `HRV below threshold = unready` without a separately approved evidence/policy record.

## 6. Subjective recovery

A minimal, low-burden check-in may capture:

```text
overall fatigue
local soreness
motivation/readiness
illness symptoms
sleep quality
```

The user should not be forced to answer every day. Sparse subjective data remain valid observations.

## 7. Local versus systemic relevance

Recovery assessments may attach scope:

```text
systemic
upper_body
lower_body
specific_region
endurance_specific
```

Decision relevance is then evaluated against the upcoming session.

Example:

```text
lower-body fatigue high
+ bench session today
→ record context, likely no Athlete Attention

lower-body fatigue high
+ interval run today
→ decision-relevant
```

## 8. RecoveryAssessment output

Do not return one number.

Return:

```text
dimensions[]
  state: within_baseline | deviated | persistent_deviation | unknown
  direction
  evidence_refs
  confidence
  relevant_to_next_session

summary:
  decision_relevant_signals
  unresolved_uncertainty
```

## 9. Vendor metrics

```text
VendorMetricObservation
provider
metric_name
value
provider_version_if_known
```

They may support explanation but do not override raw/subjective evidence automatically.

## 10. Decision integration

Recovery is one input to the Decision Engine.

A modification should preferably require one of:

- a strong, directly relevant signal;
- multiple converging weaker signals;
- persistent deviation with deteriorating performance/tolerance;
- interaction with an important upcoming session.

The exact policy is versioned and evidence-governed.

## 11. Public-alpha minimum

Recovery capability can operate with no wearable. Manual fatigue/soreness/sleep/illness observations are sufficient for basic context.

Wearables progressively enhance capability; they never become a requirement for ARETE usability.

## 12. Safety boundary

Recovery observations must not diagnose overtraining syndrome, infection, arrhythmia or other disease. Concerning health symptoms are routed to the Symptoms/Safety domain rather than converted into a readiness score.
