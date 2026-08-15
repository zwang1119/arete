# Phase 0 Audit 07 — Hybrid / Concurrent Training Engine

> Status: audit complete; no production implementation changes made  
> Date: 2026-08-15

## 1. Purpose

The Hybrid Engine should coordinate strength and endurance demands without flattening them into one load score. Its central problem is allocation of limited recovery/adaptation capacity according to athlete goals and constraints.

## 2. Current strengths worth preserving

The personal ARETE already contains a conservative concurrent-training rule: same-day/concurrent context may be reviewed, particularly when explosive strength is a priority, but one run must not automatically remove running or rewrite the strength plan. This is directionally correct.

The profile also contains explicit priorities and maintenance goals, which is the right information to resolve trade-offs.

## 3. Critical findings

### 3.1 The interference effect must not be exaggerated

Concurrent endurance and resistance training is generally compatible with improvements in aerobic fitness, maximal strength and hypertrophy. Updated meta-analyses suggest the clearest interference concern is often explosive strength/power and some lower-body strength contexts, not a universal loss of muscle or strength.

**Verdict:** ARETE should manage conflicts, not fear concurrent training by default.

### 3.2 Same-day training is not automatically wrong

Evidence suggests same-session concurrent work can attenuate explosive-strength development more than separated sessions in some contexts, while maximal strength and hypertrophy are often less affected. Separation of several hours can be useful when priorities demand it, but there is no universal minimum interval that should be hard-coded for every athlete and goal.

### 3.3 Goal priority is missing from formal conflict resolution

A scheduling decision cannot be made from fatigue/load alone. The same Wednesday double session may be appropriate when 5K performance is primary and lower-body strength is maintenance, but inappropriate when explosive lower-body strength is the primary goal.

### 3.4 Local overlap matters more than a global load sum

Upper-body lifting plus easy running is not equivalent to heavy lower-body lifting plus intervals. The engine needs body-region/movement and intensity overlap.

### 3.5 Acute performance protection and long-term adaptation are different questions

Running before lifting may reduce same-day lifting quality. That does not automatically mean long-term adaptation is impaired. The engine must distinguish:

```text
acute session-quality conflict
long-term adaptation conflict
```

### 3.6 Training order should follow priority when conflict is real

Meta-analytic evidence suggests strength-before-endurance can favor lower-body strength in some contexts, but sequence effects on aerobic capacity are small. This supports a priority-based principle rather than a fixed order rule.

### 3.7 No universal Hybrid Load Score

Strength set exposure and endurance mechanical/cardiorespiratory load do not share a validated common unit. A combined scalar would create false precision.

## 4. Scientific review

Key evidence:

- Schumann et al. updated meta-analysis, PMID 34757594: concurrent training did not materially compromise maximal strength or whole-muscle hypertrophy overall; explosive-strength gains were more attenuated, particularly when modes occurred in the same session.
- Huiberts et al. (2024), PMID 37847373: concurrent training showed small lower-body strength interference in males in pooled data, with limited evidence for other outcomes and important training-status/sex uncertainty.
- Wilson et al. classic meta-analysis, PMID 22002517: modality, frequency and duration can influence interference, but later evidence modifies several of the stronger older generalizations.
- Sequence meta-analysis, PMID 28783467: strength-before-endurance favored lower-body strength while aerobic-capacity improvement was not materially affected by order.
- 2026 umbrella review, PMID 41762427: across meta-analyses, concurrent training generally produces comparable strength/power/hypertrophy adaptations to resistance training while adding aerobic benefits; exact moderators remain context-dependent.

## 5. Audit verdict

**BUILD A CONFLICT-RESOLUTION ENGINE, NOT A HYBRID SCORE.**

Required inputs:

```text
goal hierarchy
session importance
body-region/local overlap
modality
intensity
timing
recent exposure/recovery
symptoms
constraints
```

Required outputs:

```text
no meaningful conflict
acute quality conflict
possible adaptation conflict
safety/constraint conflict
insufficient data
```

## 6. Required tests

1. upper-body strength + easy run same day;
2. heavy lower-body strength before key intervals;
3. key intervals before maintenance lower-body strength;
4. primary strength goal versus primary running goal;
5. same session versus separated sessions;
6. low-intensity cycling versus running;
7. high symptom burden overriding all scheduling optimization;
8. maintenance goal allowing reduced dose;
9. incomplete recovery data without invented readiness;
10. no universal combined load score produced.
