# Phase 0 Audit 04 — Endurance Engine

> Status: audit complete; no production implementation changes made  
> Date: 2026-08-15

## 1. Purpose

The Endurance Engine should represent planned and performed endurance work, evaluate session execution, summarize longitudinal training exposure, and detect credible performance trends without turning noisy physiological observations into deterministic prescriptions.

## 2. Current strengths worth preserving

The current personal ARETE has several strong ideas:

- Runna prescription and Strava execution are conceptually separated.
- Pace ceilings are treated as constraints rather than targets.
- Structured sessions can be aligned against laps/segments.
- HR coverage and interpretation limits are represented.
- Session-RPE is treated as contextual internal load, not performance quality.
- HR drift/decoupling is not currently allowed to automatically diagnose fatigue or rewrite training.
- Adaptation claims require repeated comparison-eligible exposures rather than a single run.

These principles are scientifically conservative and should remain.

## 3. Critical findings

### 3.1 Run-type inference from titles is weak evidence

The current adapter uses workout type/title text to infer race/quality/long/easy. This is acceptable as metadata enrichment but should not define physiological training purpose when a structured prescription or athlete annotation exists.

**Verdict: demote to low-confidence classification.**

### 3.2 Structured execution and physiological response must remain separate

A session may be executed exactly as prescribed while producing an unexpectedly high HR/RPE response. Conversely, a physiologically easy run may deviate from a prescribed interval structure.

Do not create one universal “run quality” score.

### 3.3 HR zones are method-dependent

Zone labels based on percentages of HRmax, HR reserve, threshold HR, ventilatory/lactate thresholds, or provider-specific zones are not interchangeable. The engine must preserve the derivation method and version. A zone migration can otherwise create false longitudinal change.

### 3.4 Training-intensity distribution should be descriptive before prescriptive

Evidence does not support a universal requirement that every athlete must maintain a fixed polarized distribution. Recent reviews show substantial low-intensity volume is common in trained endurance athletes, but pyramidal and polarized approaches can both be effective and response may depend on training level, sport and phase.

**Verdict: never hard-code an 80/20 or equivalent distribution as a universal compliance target.**

### 3.5 HR drift / aerobic decoupling has contextual value but no universal ARETE threshold

Cardiovascular drift is affected by pace change, heat, hydration, duration, terrain and measurement quality. It can describe a session; it should not be given a universal pass/fail cutoff or be called “aerobic efficiency” without qualification.

**Verdict: retain descriptive metric only when computation is technically valid; no automatic plan changes from a single value.**

### 3.6 Session-RPE is useful but cannot unify all load

The session-RPE method is valid and practical across sports, but it is still an internal-load estimate. It should not be summed with resistance-training set load into a single universal hybrid score.

### 3.7 Performance tests need explicit comparability

Race, time trial, standardized interval, and ordinary training-run performance are different evidence classes. Personal bests should not be inferred from arbitrary training segments unless rules explicitly define the test.

### 3.8 Environmental/context data are important confounders, not mandatory prerequisites

Temperature, wind, elevation, surface, traffic interruptions and sensor quality can materially affect pace/HR relationships. Missing context should lower confidence rather than make the entire run unusable.

## 4. Scientific review

Key evidence:

- Oliveira et al. (Sports Medicine, 2024), PMID 38717713: polarized training showed a small advantage for VO2peak in some subgroups, but time-trial and several other performance outcomes were similar across distributions.
- Individual-participant network meta-analysis (2025), PMID 39888556: no overall superiority of polarized versus pyramidal training for VO2max or time-trial performance; response may vary by training level.
- Casado et al./training-periodization literature, PMID 35418513: elite runners commonly use large low-intensity volumes with pyramidal/polarized patterns that change by phase.
- Session-RPE review, PMID 29163016: session-RPE has good validity and practical usefulness for monitoring internal load across sports.
- HRV-guided endurance reviews, PMID 34489178 and 34639599: HRV-guided training can alter training prescription and may improve some submaximal markers, but performance benefits are small/inconsistent; HRV is therefore contextual, not a standalone authority.
- Athlete load monitoring consensus, PMID 28463642: internal and external load should be interpreted using multiple measures and context.

## 5. Open-source references

GoldenCheetah is valuable for planned-activity semantics, lap/interval analysis, training history, expected-vs-actual planning and mature endurance metrics. ARETE should borrow the semantic patterns, not its GPL code.

Coach Watts is useful for provider normalization, training-state aggregation and separating retrospective analysis from prescriptive coaching.

Endurain is useful for FIT/TCX/GPX ingestion and provider-neutral activity storage.

## 6. Audit verdict

**KEEP THE CONSERVATIVE SCIENTIFIC PRINCIPLES; REBUILD ENDURANCE FACTS AROUND A PROVIDER-NEUTRAL ACTIVITY MODEL.**

Keep:

- plan/actual separation;
- structured step parsing;
- constraints vs targets;
- interpretation limits;
- HR coverage;
- repeated-exposure evidence gates.

Redesign:

- run-type inference authority;
- zone provenance/versioning;
- session comparability;
- environmental/context confidence;
- longitudinal performance evidence classes;
- training-intensity distribution as descriptive rather than universal prescription.

## 7. Required tests

1. easy run with a pace ceiling correctly slower than ceiling;
2. fast run that violates easy constraint despite good performance;
3. interval plan split across imperfect watch laps;
4. same run represented by FIT and Strava;
5. race/time trial classified independently of generic training run;
6. missing HR data;
7. HR sensor dropout;
8. device/zone-method change;
9. hot day with high HR but normal RPE;
10. traffic interruption that should not become fatigue evidence;
11. longitudinal submaximal trend with adequate comparability;
12. no universal 80/20 compliance alert.
