# Phase 0 Audit 06 — Symptom & Safety Engine

> Status: audit complete; no production implementation changes made  
> Date: 2026-08-15

## 1. Purpose

The Symptoms domain should preserve athlete-reported symptoms, detect training-relevant escalation, and conservatively constrain training decisions without pretending to diagnose injury or disease.

## 2. Current strengths worth preserving

The personal ARETE already treats symptoms separately from normal performance signals and gives them higher decision priority. It records pain score, body region, sharp quality, swelling, instability, limp, progression and forced modification. It also preserves original user text and allows later corrections/resolution.

This is a strong direction.

## 3. Critical findings

### 3.1 `pain >= 4/10` is a policy threshold, not a universal scientific law

Pain scales are subjective and context-dependent. Condition-specific rehabilitation literature sometimes uses pain-monitoring models, but a threshold validated in one tendinopathy or rehabilitation context cannot be generalized to every body region, injury, athlete and training task.

**Verdict:** the current 4/10 trigger may remain a conservative personal policy, but it must not be encoded as universal ARETE Core science.

### 3.2 Symptom features need temporal episodes, not isolated flags

A single note and a persistent/recurrent symptom are different states. The engine should model episodes and trajectories:

```text
onset
provoking task
severity trend
functional impact
modification required
resolution/recurrence
```

### 3.3 Pain intensity alone is inadequate

Training relevance also depends on:

- quality/character;
- worsening across sets or days;
- swelling;
- instability/giving way;
- altered gait/function;
- neurological or systemic features when reported;
- whether the symptom forces technique/ROM/load modification;
- whether the next planned session stresses the same region.

The engine should not convert these into a diagnosis.

### 3.4 “Resolved” should be an observation, not deletion

A symptom episode that resolves remains historical evidence. Recurrence after resolution is clinically/training-relevant context.

### 3.5 Symptom-based modification is not treatment

ARETE may recommend conservative training actions such as stopping a provoking movement, reducing exposure, substituting a lower-provocation option, or seeking professional evaluation when warranted. It must not claim to treat patellofemoral pain, tendinopathy, impingement or another diagnosis unless the product is deliberately moved into a regulated clinical role.

### 3.6 Condition-specific evidence must remain condition-specific

An Achilles tendinopathy trial showed continued sport can be compatible with rehabilitation under a specific pain-monitoring model. Patellofemoral pain consensus supports exercise therapy, particularly hip- and knee-focused exercise. Neither finding validates a universal pain rule for all ARETE users.

## 4. Scientific review

Key evidence:

- Silbernagel et al. RCT, PMID 17307888: continued Achilles tendon-loading activity under a pain-monitoring model was studied within a specific tendinopathy rehabilitation protocol. This is evidence for context-specific monitoring, not a universal pain threshold.
- International patellofemoral pain consensus, PMID 29925502: exercise therapy is supported for PFP, particularly combined hip/knee approaches; treatment conclusions are condition-specific.
- Athlete pain-assessment Delphi, PMID 39680670: comprehensive athlete pain assessment requires multiple dimensions rather than pain intensity alone.
- Hamstring return-to-sport consensus, PMID 36650032: rehabilitation progression should be individualized by athlete, demand and injury; pain thresholds vary by activity and evidence gaps remain.
- IOC load/injury consensus, PMID 27535989 and monitoring consensus PMID 28463642: load, wellness and injury risk are multifactorial and require contextual interpretation.

## 5. Regulatory/product boundary

The initial ARETE product should remain training/general-wellness software. Symptom recording and conservative training modification are compatible with that intent only if marketing and functionality avoid diagnosis/treatment claims. Release-specific regulatory review remains required.

## 6. Audit verdict

**KEEP SYMPTOMS AS A HIGH-PRIORITY DOMAIN; REMOVE UNIVERSAL DIAGNOSTIC/PRESCRIPTIVE MEANING FROM SIMPLE THRESHOLDS.**

Keep:

- original text;
- severity and quality;
- functional impact;
- repeated symptom detection;
- safety precedence;
- correction/resolution history.

Redesign:

- symptom episodes and trajectories;
- policy-vs-evidence labeling;
- activity/body-region relevance;
- escalation logic;
- clinical boundary.

## 7. Required tests

1. mild stable symptom that does not affect technique;
2. worsening symptom across sets;
3. low pain score but instability/swelling/forced modification;
4. prior resolved episode with recurrence;
5. symptom in unrelated body region to next session;
6. same symptom affecting both running and lifting;
7. abandoned zero-work session;
8. missing diagnosis never auto-filled;
9. conservative escalation without claiming injury diagnosis;
10. personal 4/10 policy configured versus another user with no such threshold.
