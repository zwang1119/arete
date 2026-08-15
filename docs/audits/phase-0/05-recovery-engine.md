# Phase 0 Audit 05 — Recovery Engine

> Status: audit complete; no production implementation changes made  
> Date: 2026-08-15

## 1. Purpose

Recovery monitoring exists only to improve decisions that matter. It should characterize relevant state and uncertainty, not manufacture a daily score whose apparent precision exceeds the evidence.

## 2. Current state

The personal ARETE has recovery-related ideas in the coaching policy and athlete context (sleep, fatigue, resting HR, illness, HRV concepts), but recovery is not yet a properly modeled first-class domain. This is preferable to prematurely hard-coding weak rules, but the future core needs explicit structures before wearable integrations expand.

## 3. Critical findings

### 3.1 Do not build a universal readiness score

Sleep, resting HR, HRV, soreness, psychological fatigue, local muscular fatigue, illness and symptoms describe different constructs. They can disagree legitimately.

A single score such as `readiness = 72` hides:

- what changed;
- measurement quality;
- local versus systemic state;
- whether the signal is relevant to today's training;
- uncertainty.

**Verdict: prohibit universal readiness as ARETE authority.**

### 3.2 HRV is contextual, not a standalone brake pedal

Resting HRV can contribute to longitudinal monitoring and HRV-guided endurance training is a legitimate research area, but positive and negative training adaptations can both produce non-intuitive HRV changes. Evidence does not justify `HRV down X% → reduce training Y%` as a universal rule.

**Verdict: use athlete-specific longitudinal context; no isolated automatic reduction rule.**

### 3.3 Sleep should be individualized and trend-oriented

Athlete sleep consensus explicitly argues against a one-size-fits-all sleep target. Wearable sleep estimates have methodological limitations. ARETE should preserve duration, timing, regularity, perceived quality and device/method rather than treating a vendor sleep score as truth.

### 3.4 Subjective data are not inferior data

Fatigue, soreness, motivation, perceived recovery and illness symptoms may be directly relevant to training decisions even when no wearable exists. A public user should not need expensive hardware to unlock recovery reasoning.

### 3.5 Local recovery must be separable from systemic recovery

An athlete can be ready for upper-body training while lower-body musculature is locally fatigued. Recovery state should be linked to body region/domain where possible.

### 3.6 Device/method changes can create false trends

HRV, sleep staging, resting HR and vendor recovery scores may change when the device, algorithm, wear position or measurement protocol changes. A `measurement_regime_change` should start a new comparable baseline or lower confidence.

### 3.7 Load is not recovery

Recent training load is an exposure/context variable. It does not directly measure recovery. High load with good tolerance is not necessarily a problem; low load does not guarantee readiness.

## 4. Scientific review

Key evidence:

- Walsh et al. athlete sleep consensus, PMID 33144349: athlete sleep is affected by sport and non-sport factors; a universal 7–9 h recommendation is not ideal for every athlete and individualized assessment is recommended.
- Bellenger et al., PMID 26888648: autonomic HR markers can change with training status, but positive and negative adaptations can overlap; additional measures are needed for interpretation.
- Düking et al., PMID 34489178 and HRV-guided meta-analysis PMID 34639599: HRV-guided endurance training may improve some submaximal/autonomic outcomes, but performance effects are small or inconsistent.
- Buchheit review, PMID 24578692: HR measures should be interpreted against measurement error, meaningful change and training context and combined with other monitoring information.
- Bourdon et al. consensus, PMID 28463642: athlete monitoring is multidimensional and should integrate internal/external load and context rather than rely on one metric.

## 5. Wearable-data principle

Vendor metrics such as Body Battery, Recovery Score or proprietary sleep scores may be stored as `VendorMetricObservation` with provider/method/version. They should not automatically become ARETE's own readiness construct.

## 6. Audit verdict

**BUILD A MULTIDIMENSIONAL RECOVERY DOMAIN; DO NOT BUILD A READINESS SCORE.**

Required dimensions may include:

```text
sleep
autonomic/cardiovascular
subjective fatigue
local soreness/fatigue
psychological readiness
illness
recent training context
```

Each dimension remains optional and carries provenance, comparability and confidence.

## 7. Required tests

1. low HRV but normal sleep/performance/subjective state;
2. normal HRV but illness symptoms;
3. poor sleep after one night versus persistent trend;
4. lower-body soreness with upper-body training planned;
5. no wearable but valid subjective recovery input;
6. device change producing baseline discontinuity;
7. vendor score disagreement with raw observations;
8. missing data not treated as poor recovery;
9. high recent training exposure with normal performance and symptoms;
10. recovery signal that is irrelevant to the next session and should not trigger Athlete Attention.
