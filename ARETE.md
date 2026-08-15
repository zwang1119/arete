# ARETE — Project Constitution

> **Status:** Living normative document  
> **Version:** 0.2.0  
> **Last updated:** 2026-08-15  
> **Canonical file:** `ARETE.md`
>
> **Required reading:** Every developer, reviewer, AI agent, and contributor must read this file before changing ARETE.
>
> **Precedence:** law / licensing / privacy / security → safety → data integrity → scientific validity → auditability → athlete goals → product convenience → visual polish.

---

## 1. Mission

ARETE is an evidence-governed, auditable athlete intelligence system for strength, endurance, hybrid training, physique development, recovery, and training-related symptom awareness.

Its purpose is not to collect the most data or reproduce existing fitness platforms. Its purpose is to answer, as reliably as the available evidence and data allow:

1. What was planned?
2. What actually happened?
3. What state is the athlete currently in?
4. Is there a meaningful deviation, risk, or emerging signal?
5. Is there credible evidence of progress or regression?
6. Does anything need to change?
7. Why is ARETE making that conclusion, and how certain is it?

**Core doctrine:**

> **Observe faithfully. Infer cautiously. Decide transparently.**

---

## 2. First-principles rule

ARETE is not governed by its legacy implementation, by the preferences of its original user, by another open-source project, by coaching convention, or by an AI suggestion.

For every material feature, metric, rule, data source, or architectural choice, ask:

1. What problem are we actually solving?
2. Does solving it materially help the athlete or protect system integrity?
3. What is the simplest scientifically defensible solution?
4. Has the problem already been solved well by a standard, library, or open-source project?
5. Is that external solution actually suitable for ARETE?
6. What are its failure modes, maintenance cost, legal constraints, and privacy/security implications?
7. What happens if we do not build it?

Existing ARETE behavior is evidence about real usage, **not authority**. Existing behavior may be retained, redesigned, or removed when a better solution is found.

---

## 3. Personal ARETE and the public alpha

### 3.1 Personal ARETE

The personal ARETE remains a **primary product priority and the richest proving environment**. It is the original motivation for the project and will continue to receive the most complete integrations and personalization.

However, it is **not the scientific or architectural specification**. Personal providers, schedules, goals, constraints, workflows, and preferences must remain profile/configuration/adapter concerns unless first-principles review shows they belong in the shared core.

### 3.2 Small public version

The near-term public version should be deliberately **small and low-friction**, initially intended for approximately 1–3 trusted external users with different training profiles.

The goal is not scale, engagement, clinical validation, or proof of performance efficacy. The goal is to discover:

- hidden assumptions from the personal implementation;
- data-model failures;
- poor defaults;
- missing-data failures;
- provider portability problems;
- confusing recommendations;
- unnecessary alerts;
- technically or scientifically weak logic.

The public version should remove personal data and personal-specific constraints, while using the **same evolving ARETE Core**.

Personal and public versions may differ in data sources, capabilities, user settings, goals, and UI modules. They should not maintain separate scientific definitions of progress or separate decision logic merely for convenience.

**Core principles should be stable; core implementation must remain correctable.**

---

## 4. Golden Rules

### GR-00 — First principles outrank legacy, precedent, and preference

A feature is not justified because it already exists, another project uses it, a coach commonly recommends it, an AI suggested it, or it looks good. Material choices require a defensible problem, evidence, feasibility, and failure analysis.

### GR-01 — Plan and Actual are different facts

`PlannedSession` and `PerformedSession` are separate entities linked by an explicit association. Actual activity must never overwrite the historical prescription.

### GR-02 — External apps are sources, not the data model

Use:

```text
Provider → Adapter → Canonical ARETE Model → Capability → Domain Engine
```

Do not architect the product as a collection of app-specific features.

### GR-03 — Capability detection, not app detection

ARETE should determine what it can reliably answer from available data. Missing integrations should remove unsupported capabilities, not make the whole product unusable.

### GR-04 — Minimum viable data must still produce value

A strength athlete with exercise, sets, reps, load, and date should already obtain meaningful strength analysis. Additional RPE/RIR, body weight, recovery data, plans, and symptoms should improve the answer rather than become arbitrary prerequisites.

### GR-05 — Every important datum has provenance

Where relevant, distinguish `measured`, `reported`, `imported`, `estimated`, `derived`, and `inferred`. Important data should retain source, timestamp, method, quality, and confidence.

### GR-06 — Canonical calculations have one source of truth

A metric should be computed once in an authoritative domain module and reused by UI, reports, rules, and AI. Do not reimplement the same formula in multiple surfaces.

### GR-07 — Do not collapse different training modalities into fake precision

Strength and endurance should retain separate domain semantics. Do not create a universal training-load or hybrid-load score without compelling validation.

### GR-08 — No universal readiness score by default

Sleep, HRV, pain, soreness, local muscular fatigue, illness, and psychological fatigue are different signals. Prefer multidimensional state assessment over one visually attractive but weak composite score.

### GR-09 — Goal hierarchy matters

Goals should be expressible as `Primary`, `Secondary`, `Maintenance`, and `Constraint`. Conflicting training decisions must respect goal priority and hard constraints.

### GR-10 — Evidence is not policy

Distinguish:

```text
Evidence → Interpretation → Policy → Executable Rule
```

A scientific association does not automatically justify a software threshold.

### GR-11 — Evidence quality, data quality, and decision confidence are different

Do not collapse evidence certainty, data completeness, measurement quality, and decision confidence into one score.

### GR-12 — Population evidence does not automatically justify individual prescription

Group means, associations, and risk relationships can inform an individual decision but must not be converted directly into personal commands without considering applicability, within-person variability, measurement reliability, uncertainty, effect size, alternatives, and the consequences of acting.

### GR-13 — Deterministic authority before generative explanation

Preferred flow:

```text
validated data → derived state → rules → decision → reason codes/confidence → AI explanation
```

AI may explain, summarize, contextualize, and identify candidate structured information. It must not silently invent thresholds, missing facts, citations, provenance, diagnoses, or authoritative metrics.

### GR-14 — Consequential recommendations are auditable

Important recommendations should be reconstructable from inputs, timestamps, rule/evidence versions, missing data, confidence, precedence, and overrides.

### GR-15 — Rule conflicts require formal precedence

A preliminary hierarchy is:

```text
Legal/security
→ Safety/significant symptoms
→ Hard athlete constraints
→ Primary-goal protection
→ Plan integrity
→ Recovery/fatigue modification
→ Progression opportunity
→ Optimization/convenience
```

Conflict behavior must be tested rather than left to an LLM.

### GR-16 — Symptoms are not just another recovery input

Pain, sharp pain, illness, neurological symptoms, and other important health signals require their own domain and precedence. ARETE may support conservative training modification but must not pretend to diagnose disease or injury.

### GR-17 — Progress claims require an evidence gate

One good session is not automatically improvement. Distinguish observation, candidate signal, emerging signal, established improvement, stable, possible regression, and insufficient comparable data.

### GR-18 — Physique conclusions require appropriate evidence

Training progression alone does not prove hypertrophy. Prefer combinations of body-weight trend, standardized circumference, standardized photos when voluntarily provided, training exposure, and performance trend. Consumer body-fat estimates require explicit uncertainty.

### GR-19 — Attention is scarce

Athlete-facing alerts should appear only when information is likely to change current or next-step behavior. Detailed evidence belongs in Coach/Audit views.

### GR-20 — Missing data is a valid state

If optional inputs are absent, use a reduced-information pathway with calibrated confidence. If required inputs are absent, return `not_applicable` or `insufficient_data`, never a guessed result.

### GR-21 — User autonomy is preserved

ARETE advises; the athlete controls the plan. Significant automatic modifications require explicit justification, reversibility, and audit history. Original plans remain recoverable.

### GR-22 — Decisions and their context are longitudinal evidence

Important training decisions are not disposable chat output. Where they materially affect training, preserve:

```text
what was known
what was uncertain
what was planned
what decision was made
why it was made
which rule/evidence version was used
what the athlete actually did
what happened afterwards
```

This history can later help evaluate the athlete and ARETE itself. Retrospective patterns must not automatically become causal rules.

---

## 5. Canonical system model

The provider-neutral model should evolve around concepts such as:

```text
Athlete
Goal / Constraint
TrainingPlan / PlannedSession / Prescription
PerformedSession / SessionAssociation / SessionAssessment
Exercise / ExerciseAlias / SetPerformance
RecoveryObservation / SleepObservation / SymptomObservation
BodyMeasurement / PhysiqueObservation
DerivedMetric
EvidenceRecord / Rule / Decision / Recommendation
SourceRecord / ImportEvent / AuditEvent
```

This is a starting model, not a frozen schema. Cross-cutting schema changes require an architecture decision record (ADR).

---

## 6. Domain responsibilities

### Strength

Exercise identity, sets/reps/load, RPE/RIR where available, equipment/variation, laterality, comparable exposures, strength trends, progression eligibility, and muscle/movement exposure.

Do not equate simple volume load with total stimulus. Do not infer hypertrophy from training logs alone.

### Endurance

Time, distance, pace/speed, HR/power where available, laps/intervals, elevation, explicit zone derivation, plan-versus-actual execution, volume/intensity trends, race/test performance, and scientifically defensible longitudinal metrics.

Do not expose a metric simply because another endurance platform exposes it.

### Hybrid

Coordinate domains rather than erase them: goal priority, scheduling, local overlap, high-intensity clustering, interference considerations, key-session protection, recovery time, and competing progression demands.

### Recovery

Sleep, HRV, resting HR, subjective fatigue, soreness, illness, and recent exposure may inform decisions. Prefer athlete-specific baselines where defensible and avoid overreacting to isolated noisy signals.

### Symptoms

Track location, severity, quality, provoking movement, timing, trend, functional effect, and notes. Allow conservative training logic; avoid diagnosis.

### Physique

Use body-weight trend, standardized measurements, optional standardized photos, body-composition estimates with uncertainty, training exposure, and performance trends.

---

## 7. Evidence governance

Rules that materially change training should link to structured evidence records where feasible.

Each evidence record should identify:

- claim;
- target population;
- relevant outcome;
- evidence summary;
- certainty and rationale;
- implementation implication;
- limitations;
- references/identifiers;
- last review date;
- review status.

Before converting research into an executable rule, ask:

1. Is the population relevant?
2. Is the endpoint relevant?
3. Is the evidence causal or associative?
4. Is the magnitude practically meaningful?
5. Is it replicated and sufficiently consistent?
6. Is the measurement reliable enough for an individual decision?
7. Has the proposed threshold actually been validated?
8. Does the rule improve a decision compared with doing nothing?
9. What is the cost of a false positive?
10. What is the cost of a false negative?

Scientific rules should be versioned. Historical decisions should retain the ruleset/evidence version under which they were made.

---

## 8. Standing on the shoulders of open source

ARETE should actively study high-quality open-source projects so that it does not repeatedly solve mature engineering problems in isolation. External projects are references, not authorities.

Current areas of interest include:

- **Coach Watts:** canonical domain logic, repositories, integrations, background processing, AI/domain separation;
- **GoldenCheetah:** planned-versus-actual semantics and endurance analysis;
- **Section 11:** deterministic/auditable decision protocols;
- **wger:** strength data modeling and progression abstractions;
- **Endurain:** provider adapters and ingestion/normalization;
- **workout.cool:** modular frontend/domain organization;
- **Dreeve:** activity automation and analytics concepts.

For each problem use:

```text
First-principles problem definition
→ current ARETE audit
→ open-source/reference review
→ scientific review where relevant
→ alternative/failure/license/maintenance comparison
→ ARETE design decision
→ documentation
→ tests
```

### Four reuse levels

**Level 1 — Concept/design inspiration**  
Learn the abstraction or workflow, then implement an ARETE-specific solution.

**Level 2 — Independent implementation from specification/evidence**  
Implement a standard, algorithm, or scientific method independently from an appropriate source with provenance and tests.

**Level 3 — Direct dependency**  
Use a mature library for a generic technical problem after maintenance, security, and license review.

**Level 4 — Source reuse/adaptation**  
Copy/adapt code only with a compelling reason and explicit review of exact license, attribution, compatibility, provenance, maintenance, and release consequences. This is the highest-friction option, not the default.

The quality of a source project does not exempt its scientific assumptions from review. Scientific merit does not exempt an implementation from engineering review.

---

## 9. Public-user architecture

The end user should not need GitHub knowledge.

GitHub should contain source code, schemas, rules, evidence, tests, documentation, and ADRs. A hosted web application should use an appropriate datastore for user data.

Integrations should be adapters, for example:

```text
xunji / strava / garmin / apple-health / intervals / fit / tcx / gpx / csv / manual
```

Domain engines must not directly call provider APIs.

Manual and standard-file import remain important fallbacks because third-party APIs can change or disappear.

ARETE must expect duplicate representations of one real-world workout and reconcile them without deleting provenance.

---

## 10. Legal, licensing, privacy, and compliance

Legal compliance is a release criterion, not an afterthought.

### Third-party material

No external code, text, dataset, media, exercise library, prompt library, or other copyrighted material enters ARETE unless origin and reuse rights are known.

Record source, URL, exact version/commit, license/terms, type of use, required attribution, verification date, and reviewer.

Until formal compatibility review is complete:

> **Do not copy, line-by-line translate, or adapt GPL/AGPL code into ARETE Core.**

Such projects may still be studied for concepts and architecture.

### License direction

A permissive OSI-approved license is preferred for original ARETE code; Apache-2.0 is a leading candidate, not a final decision. Before first public release perform a dependency/license inventory, third-party provenance review, compatibility review, and automated license scan, then create the final `LICENSE` and any required notices.

### APIs

Use official/permitted provider access methods. Review authentication, scopes, storage/retention, deletion, rate limits, branding, redistribution, commercial restrictions, and terms. Do not build production integrations on unsupported scraping or credential sharing.

### Secrets

Never commit API keys, OAuth secrets, tokens, certificates, production credentials, or personal datasets.

### Sensitive data

Assume training, recovery, symptom, wearable, and body data may be sensitive personal information. Use data minimization, clear purpose, export/delete controls, appropriate retention, secure transport/storage, least privilege, and privacy-conscious logging.

### Medical boundary

Initial public ARETE is training/general-wellness software, not diagnostic or therapeutic software. Do not claim to diagnose, treat, cure, mitigate, or prevent disease or injury. Intended use and actual functionality matter more than disclaimers.

---

## 11. Testing doctrine

ARETE requires:

- **unit tests** for calculations, conversions, matching, mappings;
- **integration tests** for provider → canonical data and duplicate reconciliation;
- **rule tests** for deterministic decisions;
- **conflict tests** for precedence;
- **scientific regression tests** comparing ruleset versions on standard athlete cases;
- **UI contract tests** for stale data, missing capabilities, attention noise, and uncertainty communication.

A rule update should explicitly show expected unchanged decisions, intentional changes, and unexpected changes.

---

## 12. Early external testing

The initial external cohort should be deliberately heterogeneous, for example:

1. **strength-only** athlete with a lifting log;
2. **endurance-only** athlete with one endurance source;
3. **simple hybrid** athlete with strength + endurance but fewer data sources than the personal implementation.

The personal ARETE acts as a high-data stress case, not as the universal specification.

Early alpha success metrics are primarily:

- data integrity;
- matching integrity;
- calculation integrity;
- rule integrity;
- explanation clarity;
- false attention;
- missed important attention;
- portability across users/providers.

A useful error taxonomy should include:

```text
DATA / MATCHING / DOMAIN / SCIENCE / RULE / PRECEDENCE / AI / UX / INTEGRATION / SECURITY
```

One to three users can expose architecture and usability failures. They cannot establish clinical validity or prove performance benefit.

---

## 13. Development sequence

### Phase 0 — First-principles audit

- audit the personal ARETE without assuming current behavior is correct;
- inventory rules, metrics, source precedence, decisions, and dependencies;
- identify personal-only assumptions;
- compare material subsystems with strong references;
- identify unsupported scientific logic, technical debt, and rule conflicts;
- preserve useful historical decisions and rationale.

### Phase 1 — Canonical core

Define canonical entities, provenance, capabilities, source reconciliation, freshness semantics, synthetic fixtures, and tests.

### Phase 2 — Evidence and rules

Define evidence schema, rule schema, precedence, decision/audit objects, ruleset versioning, and scientific regression tests.

### Phase 3 — Strength and endurance packs

Build the two domains independently with provider-neutral contracts.

### Phase 4 — Hybrid coordination

Add goal hierarchy, scheduling, local overlap, key-session protection, and conflict logic without collapsing modalities into one score.

### Phase 5 — Recovery, symptoms, and physique

Add only where data and evidence justify useful decisions.

### Phase 6 — AI layer

Add explanation, summarization, coach dialogue, and candidate note extraction after authoritative core outputs are stable.

### Phase 7 — Small public web alpha

Add authentication, multi-user isolation, privacy/security controls, provider setup, data export/delete, and the minimum UX needed for a few trusted users.

Scale only if the small version is genuinely useful, scientifically defensible, maintainable, and legally safe.

---

## 14. Decision records and accumulated evidence

Important decisions should receive an ADR or durable decision record when they affect architecture, scientific behavior, data semantics, legal posture, or user-facing recommendations.

An ADR should include:

- context/problem;
- alternatives considered;
- decision;
- reasons;
- evidence/references;
- consequences and trade-offs;
- migration implications;
- date and version.

Training decisions generated by ARETE should likewise retain enough context to later evaluate whether the decision was sensible and what happened afterwards.

This accumulated history is valuable evidence, but it must be analyzed cautiously. Personal historical success is not automatically a universal rule.

---

## 15. Definition of Done

A material feature is not done because it renders correctly. Where applicable it must have:

- canonical data-model compliance;
- provenance;
- plan/actual integrity;
- declared capability requirements;
- missing/stale-data behavior;
- rule/evidence linkage;
- calibrated confidence;
- conflict/precedence handling;
- audit trail;
- unit/integration/rule/conflict tests;
- scientific regression coverage when behavior changes;
- privacy/security review;
- licensing/API-term review;
- no secrets or real personal data in the public repository;
- claims no stronger than evidence;
- low-noise Athlete experience;
- updated documentation.

---

## 16. Things ARETE should deliberately avoid

Unless future evidence overturns the decision:

- building a Strava-style social network;
- building maps or charts without decision value;
- universal readiness scoring;
- universal hybrid-load scoring;
- automatic clinical diagnosis;
- opaque AI coaching;
- replacing history with regenerated state;
- treating one noisy biomarker as authoritative;
- claiming progress from one observation;
- requiring a large wearable ecosystem;
- making GitHub part of the normal user workflow;
- hard-coding the original user's schedule, goals, injuries, apps, or preferences into Core;
- copying third-party code merely because it is public;
- unsupported API access;
- storing secrets in source control;
- feature creep that does not improve athlete decisions.

---

## 17. North Star

The immediate strategy is:

> **Use first principles, high-quality evidence, and the strongest relevant open-source ideas to improve the personal ARETE; then expose the same core through a small, low-friction public alpha with personal data and personal-specific integrations/configuration separated from the shared system.**

The public alpha is intentionally not a mass-market product. If 1–3 external users with different training profiles can use the same core without hidden personal assumptions, that is meaningful architectural evidence. Expansion comes later, only if justified.

ARETE should become valuable not because it knows everything, but because it knows:

> **what it knows, what it does not know, how strongly it knows it, and whether that information should change what the athlete does next.**

---

## 18. Maintenance rule

`ARETE.md` is a living constitution.

When changing it:

1. explain why a core principle changes;
2. check architecture implications;
3. check scientific implications;
4. check privacy/security/legal/licensing implications;
5. update version/date;
6. prefer explicit amendment over silent drift;
7. preserve durable ADRs for major decisions.

Every future development session should begin with:

> **Read `ARETE.md`; define the problem from first principles; identify the governing rules; inspect current code/data; review relevant evidence and references; only then propose implementation.**