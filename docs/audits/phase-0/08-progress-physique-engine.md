# Phase 0 Audit 08 — Progress & Physique Engine

> Status: audit complete; no production implementation changes made  
> Date: 2026-08-15

## 1. Purpose

Progress should answer a specific longitudinal question with an evidence threshold appropriate to the measurement and claim. The Physique domain should track body change without overinterpreting noisy body-composition devices, photographs, or training performance.

## 2. Current strengths worth preserving

The personal ARETE already has an explicit progress gate with states such as early signal / improving / established trend, requires comparison-eligible exposures, and resists claiming fitness improvement from one session. This is substantially better than common dashboard behavior.

The personal measurement protocol also recognizes standardization of bodyweight, circumference and photographs. The principle is correct.

## 3. Critical findings

### 3.1 Current fixed progress thresholds are product heuristics, not scientific constants

The current core uses fixed counts/time spans such as 2 eligible exposures for any signal, 3 exposures/14 days for strength “improving,” and 5 exposures/28 days for “established trend.” These can be useful conservative defaults, but there is no scientific basis for applying the same counts across exercise performance, running, symptoms, bodyweight and physique.

**Verdict:** replace universal thresholds with claim-specific evidence gates.

### 3.2 “Progress” is not one construct

Distinct claims include:

```text
strength performance improved
endurance performance improved
training tolerance improved
symptom burden improved
body mass increased/decreased
waist changed
muscle size likely increased
body fat likely changed
adherence improved
```

They require different data and different measurement-error treatment.

### 3.3 Measurement error must be part of the gate

A change smaller than normal measurement variation should not be called improvement merely because it moved in the desired direction.

Where known, use technical error / least significant change. Where not known, require standardized repeated observations and conservative language.

### 3.4 Body-composition methods are not interchangeable

Longitudinal comparison should preferentially use the same instrument and standardized protocol. DXA itself has measurement error and biological variability. Consumer smart-scale body-fat estimates can have substantial absolute error.

**Verdict:** body-composition observations require method/device provenance and regime-change handling.

### 3.5 Training performance does not prove hypertrophy

Progressive overload can support the plausibility of an effective training process, but muscle growth requires body/anthropometric/imaging evidence. Strength changes can reflect neural/technical adaptation and task familiarity.

### 3.6 Photographs are valuable qualitative longitudinal evidence but must be standardized

Lighting, distance, lens, pose, pump, glycogen, hydration and body mass can alter appearance. Photos should support a trend when conditions are controlled; they should not become a precise body-fat or muscle-mass measurement without validated methodology.

### 3.7 Weight trend is not equivalent to tissue change

Short-term body mass is influenced by fluid, glycogen, gut content and sodium. Tissue-change claims should use sustained trend plus other evidence.

## 4. Scientific review

Key evidence:

- Nana/athlete DXA methodology review, PMID 25029265: standardization of preparation, positioning and analysis is important for longitudinal body-composition precision.
- Detecting meaningful DXA change in athletes, PMID 27027548: real-world precision error materially affects whether observed change can be considered meaningful.
- Hind et al. review, PMID 29754949: athlete DXA longitudinal interpretation should incorporate measurement precision and standardized best practice.
- Same- vs consecutive-day precision study, PMID 30454952: biological variation affects least-significant-change estimates.
- Method comparison study, PMID 30325573: body-composition methods should not be translated interchangeably; longitudinal monitoring should use the same instrument where possible.
- Smart-scale validation, PMID 33929337: consumer smart scales can measure weight adequately but body-composition estimates are not sufficiently accurate to replace reference methods.
- Surface anthropometry vs DXA longitudinal study, PMID 29757050: standardized surface anthropometry can identify direction of change reasonably well in athletes, while magnitude should be interpreted cautiously.

## 5. Audit verdict

**KEEP THE PROGRESS GATE, BUT MAKE IT CLAIM-SPECIFIC AND MEASUREMENT-AWARE.**

Replace:

- universal 3/5 exposure thresholds;
- one generic `direction` across all domains;
- physique inference from gym performance;
- cross-device body-composition continuity.

Keep:

- staged language;
- comparable-exposure requirements;
- standardized measurement protocols;
- explicit uncertainty.

## 6. Required tests

1. one better bench session → observation only;
2. repeated comparable performance improvement → strength trend;
3. device/configuration change → new/uncertain baseline;
4. 0.3 kg scale fluctuation → no muscle/fat claim;
5. sustained weight gain + stable waist + strength/measurements trend → cautiously stronger physique inference;
6. smart-scale body-fat change alone → weak/no claim;
7. standardized circumference change larger than known error;
8. non-standardized photos → evidence quality reduced;
9. symptom trajectory improves while performance stays flat;
10. maintenance goal with stable performance → success without “improvement” requirement.
