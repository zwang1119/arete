# Phase 0 Audit 03 — Strength Engine

> Status: audit complete; no production implementation changes made  
> Date: 2026-08-15  
> Governing posture: first principles > scientific defensibility > individual longitudinal evidence > legacy heuristics

## 1. Purpose

The Strength Engine should answer, without pretending to know more than the data support:

- what exercise and configuration were performed;
- what was prescribed versus performed;
- whether two exposures are sufficiently comparable;
- what changed in performance;
- whether the change is likely meaningful;
- whether progression is justified for the athlete's current goal;
- what cannot be concluded from the training log alone.

It must support strength-focused, hypertrophy-focused, maintenance, rehabilitation/tolerance, and hybrid contexts without assuming that the same metric means the same thing in each context.

## 2. Current strengths worth preserving

The current personal ARETE already has several unusually good foundations:

1. It does not compare obviously different exercise variants blindly.
2. It records RPE at working-set level where available.
3. It preserves equipment/setup uncertainty and can mark a comparison as `eligibility_uncertain`.
4. It separates warm-up sets from working sets.
5. It supports autoregulated prescriptions rather than treating planned load as a mandatory number.
6. User corrections can invalidate or restore comparison eligibility without erasing the original record.

These are directionally correct and should survive the redesign.

## 3. Critical findings

### 3.1 Current `performanceDirection` is too coarse to be scientific authority

The existing heuristic labels performance positive/negative/stable mainly from a top work set using load, repetitions, and a 0.5-RPE difference. This is useful as a prototype signal but not a defensible general progression model.

Problems include:

- one top set ignores the rest of the session;
- a heavier set at fewer repetitions may or may not represent improvement;
- RPE error is non-zero and athlete-specific;
- rep performance is exercise- and load-dependent;
- technique/ROM/equipment changes can alter apparent performance;
- hypertrophy stimulus and maximal-strength performance are different outcomes;
- fatigue can suppress acute performance without indicating loss of adaptation.

**Verdict: replace as an authoritative rule; retain only as a low-level candidate signal until a stronger assessment model exists.**

### 3.2 Exercise identity and comparison identity are currently conflated

A bench press remains a bench press if tempo changes, but that does not mean the exposure is directly comparable for performance inference. Conversely, a minor setup change does not always justify discarding all longitudinal information.

The model needs to separate:

- `ExerciseDefinition` — what movement this is;
- `ExerciseConfiguration` — implement, angle, laterality, grip, ROM, assistance, machine, etc.;
- `ExecutionObservation` — what actually happened;
- `ComparisonEligibility` — whether a specific analytic comparison is defensible for a specific question.

**Verdict: refactor.**

### 3.3 Load semantics are under-specified

`40 kg` is not a sufficient fact. It can mean barbell total load, load per dumbbell, added chin-up load, a selectorized machine display, cable-stack display, unilateral effective load, or unknown provider semantics.

A public core must never silently normalize these into the same concept.

**Verdict: critical schema gap.**

### 3.4 Volume must not become a fake stimulus score

The current weekly/session soft caps are useful personal guardrails but are not universal scientific thresholds. Set count is a practical training-dose descriptor, not a direct measurement of hypertrophic stimulus, fatigue, or injury risk.

Recent resistance-training evidence supports a dose-response between weekly set volume and adaptation with diminishing returns, but the relationship depends on outcome, training status, direct versus indirect work, exercise selection, effort and other variables. ARETE should therefore record and summarize volume without presenting a universal optimal set number.

**Verdict: preserve volume tracking, remove universal scientific authority from arbitrary caps.**

### 3.5 `Effective set` should not be a primitive fact

There is no universally validated binary threshold at which a set becomes “effective”. Proximity to failure is relevant to hypertrophy, but evidence does not justify converting every set into a precise effective/non-effective flag from a single RPE/RIR cutoff.

**Verdict: do not create an authoritative `effective_set=true/false` field.**

### 3.6 RPE/RIR is useful but should remain an observation with uncertainty

RIR/RPE can be reliable and practically useful for prescribing and monitoring resistance training, particularly in experienced users, but it is not an objective laboratory measurement. ARETE should use it as a valuable internal-load/performance-context signal, not as exact ground truth.

### 3.7 Hypertrophy cannot be inferred from strength-log progression alone

More load/reps can support the inference that performance capacity improved. It does not prove muscle hypertrophy. Hypertrophy claims belong downstream in the Physique/Progress engine and require appropriate body-composition or anthropometric evidence.

## 4. Scientific review

Key evidence informing this audit:

- ACSM Position Stand (2026), PMID 41843416: resistance training improves strength, hypertrophy and performance; prescription variables affect outcomes differently; higher volumes generally support hypertrophy while heavier loading particularly supports strength.
- Pelland et al. (Sports Medicine, 2026), PMID 41343037: weekly volume shows positive dose-response relationships for strength and hypertrophy with diminishing returns; direct/indirect set classification materially affects interpretation.
- Refalo et al. (2024), PMID 38970765: closer proximity to failure was associated with greater hypertrophy in exploratory meta-regression, while the relationship with strength was negligible; the model fit and estimated nature of RIR warrant caution.
- Refalo et al. (2023), PMID 36334240: momentary failure was not clearly superior to non-failure training for hypertrophy.
- Lovegrove et al. (2022), PMID 36135029: RIR can be a reliable tool for resistance-training load prescription in tested exercises.
- Huang et al. (2025), PMID 40791980 and Zhang et al. (2021), PMID 33776802: autoregulated approaches are reasonable strategies for strength development, but no single implementation should be assumed universally optimal.

These findings justify flexible autoregulation and multidimensional tracking; they do not justify rigid universal thresholds.

## 5. Open-source references

wger is useful conceptually for separating routines, set configurations, exercise types, units and workout logs. ARETE should borrow those modeling ideas only. Its AGPL implementation should not be copied into ARETE Core without explicit license review.

## 6. Audit verdict

**REFACTOR THE ANALYTIC MODEL; KEEP THE DATA-DISCIPLINE PRINCIPLES.**

Keep:

- set-level logging;
- warm-up/work-set distinction;
- RPE/RIR where available;
- comparison eligibility;
- uncertainty and user corrections;
- autoregulated prescription support.

Replace or redesign:

- top-set-only progress authority;
- binary exercise identity/comparability logic;
- ambiguous load semantics;
- universal set caps presented as science;
- any future “effective set” binary truth;
- hypertrophy inference from lifting performance alone.

## 7. Required downstream tests

Synthetic cases must include:

1. same exercise, same setup, more reps at similar RPE;
2. same reps/load, materially lower RPE;
3. heavier load but fewer reps;
4. dumbbell per-hand versus total-load ambiguity;
5. machine change;
6. ROM or assistance change;
7. unilateral versus bilateral execution;
8. exercise substitution that preserves session intent;
9. added optional work without false adherence inflation;
10. low-RPE work that should not be discarded;
11. zero-set abandoned session;
12. strength improvement without physique evidence.
