# ARETE — Project Constitution, Golden Rules & Development Doctrine

> **Status:** Living normative document  
> **Version:** 0.1.0  
> **Last updated:** 2026-08-15  
> **Canonical file:** `ARETE.md`
>
> **Instruction to every future developer, agent, reviewer, and contributor:**  
> Read this file before changing ARETE. When a feature request, existing implementation, AI suggestion, convenience shortcut, or legacy behavior conflicts with this document, the precedence is:
>
> **Law / licensing / privacy / security → safety → data integrity → scientific validity → auditability → user goals → product convenience → visual polish.**
>
> This document is intentionally designed to evolve. Changes to core principles should be explicit, reviewed, dated, and justified rather than silently drifting through implementation.

---

## 1. What ARETE is

ARETE is intended to become an **open-source, evidence-governed, auditable athlete intelligence system** for strength, endurance, hybrid training, physique development, recovery, and training-related symptom awareness.

Its purpose is not to collect the most data, generate the most charts, or imitate Strava, TrainingPeaks, Garmin, Hevy, Runna, or any other existing product.

Its purpose is to answer, as reliably as the available evidence and data permit:

1. **What was planned?**
2. **What actually happened?**
3. **What state is the athlete currently in?**
4. **Is there a meaningful deviation, risk, or emerging signal?**
5. **Is there credible evidence of progress or regression?**
6. **Does anything need to change?**
7. **Why is ARETE making that conclusion, and how certain is it?**

The central doctrine is:

> **Observe faithfully. Infer cautiously. Decide transparently.**

---

# 2. Product identity

ARETE should not be designed around one highly instrumented athlete.

A user may be:

- a strength athlete with only a lifting log;
- a runner with only Strava or FIT files;
- a physique athlete with training, body weight, circumferences, and photos;
- a hybrid athlete with strength + endurance data;
- an advanced athlete with training plan, wearable, recovery, symptoms, body composition, and multiple data sources.

All are valid ARETE users.

**More data should expand ARETE's capabilities, not change the product from unusable to usable.**

ARETE must degrade gracefully.

If the system has insufficient data to answer a question, it should say so clearly rather than fabricate precision.

---

# 3. Golden Rules

## GR-01 — Plan and Actual are different facts

A planned session and a performed session are never the same entity.

```text
PlannedSession
      │
      │ matched_to
      ▼
PerformedSession
      │
      ▼
SessionAssessment
```

The actual activity must never overwrite the historical prescription.

The system should preserve what was intended even when the athlete deviates from it.

---

## GR-02 — External apps are sources, not the ARETE data model

ARETE must not be architected as:

```text
Runna feature
Strava feature
Xunji feature
Garmin feature
```

It should be:

```text
Provider
   ↓
Adapter
   ↓
Canonical ARETE model
   ↓
Capability
   ↓
Domain engine
```

The provider can change without rewriting the domain logic.

---

## GR-03 — Capability detection, not app detection

The system should ask:

> What information can ARETE reliably infer from the data available?

not:

> Which apps does the user have?

Example capability state:

```json
{
  "strengthPerformance": true,
  "endurancePerformance": false,
  "plannedTraining": true,
  "recoverySignals": false,
  "bodyCompositionTracking": true,
  "symptomTracking": true
}
```

Features that are not supported by available capabilities should normally disappear rather than display a wall of unavailable states.

---

## GR-04 — Minimum viable data must still produce value

A strength athlete with sets, reps, load, date, and exercise identity should be able to use ARETE meaningfully.

RPE/RIR, body weight, planned sessions, sleep, symptoms, and other variables should improve the analysis rather than be mandatory unless a specific rule genuinely requires them.

The same principle applies to endurance and other domains.

---

## GR-05 — Every important datum has provenance

A value without origin is incomplete.

Where appropriate, records should distinguish:

- measured;
- user reported;
- imported;
- estimated;
- derived;
- inferred.

Important data should be able to record:

```text
value
unit
source
provider
device
observed_at
imported_at
method
quality
confidence
```

An estimated 1RM is not an observed 1RM.

A wearable-derived metric is not necessarily a direct physiological measurement.

A user-reported symptom is not a medical diagnosis.

---

## GR-06 — Canonical calculations have one source of truth

Derived metrics must not be independently reimplemented in multiple pages, reports, background jobs, or AI prompts.

```text
Canonical data
      ↓
Domain calculation
      ↓
Single derived result
      ↓
UI / reports / coach / AI
```

The same inputs and rule version must produce the same authoritative result.

---

## GR-07 — Strength and endurance are separate domains

Do not force different training modalities into one artificial score.

At minimum, ARETE should preserve distinct dimensions such as:

- cardiovascular exposure;
- endurance intensity exposure;
- running mechanical exposure;
- lower-body resistance exposure;
- upper-body resistance exposure;
- local muscle exposure;
- high-intensity exposure.

A heavy squat session and a 90-minute easy run should not be naively converted into a single universal load number.

---

## GR-08 — No universal readiness score by default

Sleep, HRV, soreness, pain, psychological fatigue, local muscular fatigue, illness, and systemic fatigue are not interchangeable.

A hybrid athlete can be:

- ready for upper-body strength;
- poorly prepared for high-intensity running;
- locally fatigued in the quadriceps;
- otherwise systemically well.

ARETE should prefer multidimensional state assessment over a visually attractive but scientifically weak single readiness number.

---

## GR-09 — Goal hierarchy is mandatory for meaningful coaching

Goals must be expressible as:

- **Primary**
- **Secondary**
- **Maintenance**
- **Constraint**

Example:

```text
Primary: improve 5 km performance
Secondary: upper-body hypertrophy
Maintenance: lower-body strength
Constraint: knee symptoms
```

When goals conflict, the hierarchy should guide trade-offs.

---

## GR-10 — Evidence is not the same as policy

The system must distinguish:

```text
Evidence
"What do we know?"
        ↓
Interpretation
"What does it imply?"
        ↓
Policy
"When should ARETE act?"
        ↓
Rule
"How is the decision encoded?"
```

An association in the literature must never automatically become a prescriptive threshold.

---

## GR-11 — Evidence level and decision confidence are separate

A rule may be based on strong evidence but applied to incomplete athlete data.

Conversely, a highly reliable athlete observation may inform a domain with uncertain evidence.

ARETE should distinguish at least:

```text
Evidence quality
Data availability
Data quality
Decision confidence
```

These must not be collapsed into one number.

---

## GR-12 — Important decisions should be deterministic before they are generative

Where rules can reasonably be encoded, use deterministic logic first.

Preferred:

```text
validated data
    ↓
derived state
    ↓
rule engine
    ↓
decision
    ↓
reason codes + confidence
    ↓
AI explanation
```

Avoid:

```text
all user data
    ↓
LLM
    ↓
authoritative decision
```

LLMs may explain, summarize, contextualize, identify candidate structured information, and surface uncertainty.

LLMs must not silently invent thresholds, provenance, missing data, scientific claims, or historical facts.

---

## GR-13 — Every consequential recommendation is auditable

A meaningful recommendation should be traceable to:

```text
decision_id
decision
trigger(s)
input data
data timestamps
rule version
evidence reference(s)
missing inputs
confidence
precedence / override
alternative considered where relevant
```

"AI thinks this is better" is not an acceptable audit trail.

---

## GR-14 — Rule conflicts require formal precedence

Do not let an LLM resolve conflicting rules ad hoc.

A preliminary precedence model is:

```text
Legal / security
    ↓
Safety / significant symptoms
    ↓
Hard athlete constraints
    ↓
Primary goal protection
    ↓
Training-plan integrity
    ↓
Recovery / fatigue modification
    ↓
Progression opportunity
    ↓
Optimization / convenience
```

Specific precedence rules must be tested.

---

## GR-15 — Symptoms are not just another readiness input

Pain, sharp pain, illness, neurological symptoms, or other potentially important health signals require their own domain and precedence.

ARETE may support training-related symptom awareness and conservative training modification, but must not pretend to diagnose disease or injury.

---

## GR-16 — Progress claims require an evidence gate

One better workout is not automatically "progress."

The system should distinguish:

- observation;
- candidate signal;
- emerging signal;
- established improvement;
- stable;
- possible regression;
- insufficient comparable data.

Progress logic should consider, as appropriate:

- repeated exposures;
- comparable exercise/session conditions;
- RPE/RIR;
- body mass;
- distance/pace/HR;
- measurement reliability;
- time frame;
- technical or equipment changes;
- relevant confounding.

---

## GR-17 — Physique conclusions require appropriate evidence

Training progression alone does not prove hypertrophy.

A physique domain should preferentially combine:

- body-weight trend;
- standardized circumferences;
- standardized photographs where voluntarily provided;
- training exposure;
- performance trend.

Consumer body-fat estimates should be treated according to their measurement limitations and should not be given unjustified precision.

---

## GR-18 — Attention is scarce

Athlete-facing attention should be reserved for information likely to change current or next-step behavior.

The system may know much more than it shows.

The Coach/Audit view may be detailed.

The Athlete view should be selective.

---

## GR-19 — Missing data is a valid state

Missing HRV does not mean the system is broken.

If HRV is optional for a rule, the rule may operate without it with appropriately changed confidence.

If a required input is absent, the rule should return:

```text
not_applicable
```

or:

```text
insufficient_data
```

—not a guessed result.

---

## GR-20 — User autonomy is preserved

ARETE advises; the athlete controls the plan.

Significant automatic plan modification should require explicit design justification and should be reversible and auditable.

The original plan must remain recoverable.

---

# 4. Canonical domain model

The canonical model should be provider-neutral.

Initial candidate entities:

```text
Athlete
Goal
Constraint

TrainingPlan
PlannedSession
Prescription

PerformedSession
SessionAssociation
SessionAssessment

Exercise
ExerciseAlias
ExercisePerformance
SetPerformance

DailyObservation
RecoveryObservation
SleepObservation
SymptomObservation

BodyMeasurement
PhysiqueObservation

DerivedMetric
EvidenceRecord
Rule
Decision
Recommendation

SourceRecord
ImportEvent
AuditEvent
```

This is a design starting point, not a frozen schema.

Changes to canonical entities should be treated as architecture decisions because they affect all adapters and downstream engines.

---

# 5. Domain engines

## 5.1 Strength Engine

Should eventually support:

- canonical exercise identity;
- aliases across providers/languages;
- sets, reps, load;
- RPE/RIR when available;
- unilateral/bilateral distinction;
- equipment and variation;
- estimated strength metrics;
- performance trend;
- progression eligibility;
- muscle/movement exposure;
- program adherence;
- comparable-exposure logic.

Do not equate volume load (`sets × reps × kg`) with total training stimulus.

Do not assume all exercises contribute equally to a muscle.

Do not claim hypertrophy from training logs alone.

---

## 5.2 Endurance Engine

Should eventually support:

- time, distance, pace/speed;
- HR and power where available;
- laps/intervals;
- elevation;
- zones with explicit derivation;
- planned versus actual;
- interval execution;
- easy/long-run response;
- longitudinal performance;
- race/time-trial outcomes;
- volume and intensity distribution;
- expected versus observed progression where scientifically defensible.

Do not expose a metric merely because a mature endurance platform exposes it.

Every metric must earn its place through usefulness and defensibility.

---

## 5.3 Hybrid Engine

The Hybrid Engine should coordinate domains rather than erase them.

Its job is to reason about:

- goal priority;
- scheduling;
- local muscle overlap;
- high-intensity clustering;
- interference considerations;
- key-session protection;
- recovery time;
- competing progression demands.

Do not create a universal "Hybrid Load Score" without compelling validation evidence.

---

## 5.4 Recovery Engine

Potential inputs:

- sleep;
- HRV;
- resting HR;
- subjective fatigue;
- soreness;
- illness;
- recent training exposure.

The engine should:

- compare to athlete-specific baselines where appropriate;
- avoid overreacting to isolated noisy signals;
- preserve uncertainty;
- avoid false precision.

Recovery should support decisions, not dominate them without justification.

---

## 5.5 Symptom Engine

Must remain distinct from recovery.

Should support:

- location;
- severity;
- quality;
- provoking movement;
- timing;
- trend;
- functional impact;
- user notes.

It may trigger conservative training rules and escalation prompts.

It must not assign a medical diagnosis unless the product is deliberately redesigned, clinically validated, and appropriately regulated for that function.

---

## 5.6 Physique Engine

Possible inputs:

- body-weight trend;
- circumferences;
- standardized photos;
- body-composition estimates;
- training exposure;
- strength/performance changes.

The engine should model measurement uncertainty and avoid unjustified claims about muscle gain or fat loss.

---

# 6. Evidence governance

## 6.1 Evidence Registry

Rules that can materially change training should, where feasible, link to structured evidence records.

Candidate structure:

```yaml
evidence_id: EVID-XXXX
domain: strength | endurance | hybrid | recovery | symptoms | physique

claim: >
  Human-readable scientific claim.

population:
  description: ...

outcomes:
  - ...

evidence_summary: ...

certainty:
  level: A | B | C | D | U
  rationale: ...

implementation_implication: ...

limitations:
  - ...

references:
  - citation: ...
    doi_or_identifier: ...

last_reviewed: YYYY-MM-DD
review_status: current | review_due | disputed
```

The exact grading framework should be finalized after methodological review.

---

## 6.2 Proposed evidence-level concept

This is provisional and must not be mistaken for formal GRADE:

- **A** — convergent high-quality systematic review/meta-analysis and/or strong consensus;
- **B** — reasonably consistent randomized/prospective evidence;
- **C** — limited, indirect, heterogeneous, or observational evidence;
- **D** — expert practice or mechanistic rationale;
- **U** — uncertain, conflicting, or currently unsuitable for prescriptive use.

Evidence level alone never determines action.

---

## 6.3 Scientific implementation rule

Before converting literature into executable coaching behavior, ask:

1. Is the population relevant?
2. Is the endpoint relevant?
3. Is the finding causal or associative?
4. Is the magnitude practically meaningful?
5. Is there replication?
6. Is the measure reliable enough for individual decisions?
7. Is a threshold actually validated?
8. Does the rule improve a decision compared with doing nothing?
9. What harms arise from false positives?
10. What harms arise from false negatives?

If those questions cannot be answered, implementation should remain conservative.

---

# 7. AI doctrine

AI is a subordinate reasoning and communication layer, not ARETE's source of truth.

## AI may

- summarize longitudinal history;
- explain deterministic decisions;
- contextualize trends;
- convert structured data into readable reports;
- identify candidate information in user notes;
- suggest hypotheses;
- surface uncertainty;
- compare alternatives;
- help developers review evidence and code.

## AI must not silently

- change stored historical facts;
- change plan prescriptions;
- invent missing measurements;
- invent citations;
- invent scientific thresholds;
- infer a diagnosis;
- bypass rule precedence;
- bypass validation;
- create authoritative metrics using an undocumented formula;
- expose private data beyond the required context.

Any AI-produced structured state that affects decisions should be validated before becoming authoritative.

---

# 8. Public-user architecture

ARETE public should not require GitHub knowledge.

GitHub should host:

```text
source code
schemas
rules
evidence registry
tests
documentation
architecture decisions
```

A production application should use an appropriate application datastore for user information.

It should not require users to:

- fork a repository;
- configure GitHub Actions;
- add repository secrets;
- edit YAML;
- understand Git.

---

# 9. Integration architecture

Candidate structure:

```text
integrations/
  xunji/
  strava/
  garmin/
  apple-health/
  intervals/
  fit/
  tcx/
  gpx/
  csv/
  manual/
```

An integration should map provider data into ARETE contracts.

Domain engines must not directly call provider APIs.

Manual and standard-format import paths are strategically important because third-party APIs can change, disappear, restrict access, or be unavailable to some users.

---

# 10. Data reconciliation

ARETE must expect duplicate representations of the same real-world event.

Example:

```text
Apple Workout
Garmin
Strava
```

may describe one performed run.

The system needs explicit identity/reconciliation logic so one workout is not counted three times.

Matching should preserve provenance rather than deleting source records.

---

# 11. Exercise identity

Strength systems require a canonical exercise registry.

Examples such as:

```text
Bench Press
Barbell Bench Press
卧推
Flat Bench
```

may map to a canonical exercise where appropriate.

However, materially different variations must not be collapsed.

The registry should support:

- canonical ID;
- aliases;
- language;
- equipment;
- angle/variation;
- laterality;
- provider-specific mapping;
- user override.

---

# 12. Data freshness and update semantics

Every user-visible conclusion that depends on external data should know the freshness of its inputs.

The UI should distinguish:

```text
last source observation
last import/sync
last calculation
last decision evaluation
```

"Updated at" must have one precise meaning in each context.

Stale data should not silently present as current.

---

# 13. Public UX doctrine

## 13.1 The interface follows athlete capabilities

Possible experiences:

### Strength athlete

```text
Today
Strength
Progress
Plan
Coach
Body
```

### Runner

```text
Today
Running
Performance
Training
Coach
```

### Physique athlete

```text
Today
Training
Physique
Measurements
Progress
Coach
```

### Hybrid athlete

```text
Today
Strength
Endurance
Recovery
Progress
Coach
```

These are different lenses on the same core, not separate products.

---

## 13.2 Progressive enhancement

Connecting another valid data source should unlock new capability without breaking existing functionality.

Example:

```text
Strength log
    ↓
Strength capability

+ Strava
    ↓
Endurance capability

+ wearable
    ↓
Additional recovery signals
```

No user should be made to feel that their existing data is inadequate merely because another integration exists.

---

# 14. Legal, licensing, privacy, and compliance doctrine

> **This section is an engineering governance policy, not jurisdiction-specific legal advice. Public release should receive appropriate legal/privacy review for the jurisdictions and features actually shipped.**

Legal compliance is a release criterion, not an afterthought.

## 14.1 No untracked third-party code

No third-party code, text, media, dataset, exercise library, prompt library, or other copyrighted material may enter ARETE unless its origin and reuse rights are known.

At minimum record:

```text
project/source
URL
exact version or commit
license / terms
files or concepts used
type of use
required attribution
date verified
reviewer
```

---

## 14.2 Distinguish inspiration from incorporation

Use four categories:

### A. Concept inspiration

We learn an architecture or product idea and implement our own solution.

### B. Independent algorithm implementation

We implement an algorithm from an appropriate public scientific specification or standard, with citation and independent code.

### C. Direct dependency

We use the original package according to its license and isolate it appropriately.

### D. Adapted/copied code

We modify source code from another project.

Category D requires explicit license-compatibility review before merge.

---

## 14.3 Copyleft guardrail

Until a formal project licensing decision and compatibility review is complete:

> **Do not copy, translate, adapt line-by-line, or incorporate GPL/AGPL code into ARETE core.**

GPL/AGPL projects may still be studied for architecture, behavior, terminology, and problem framing.

Clean independent implementation should be used when we want similar behavior unless counsel/review determines direct reuse is appropriate.

---

## 14.4 Initial open-source license direction

The preferred direction is a **permissive OSI-approved license** for ARETE's original code so that the project can be widely inspected, reused, integrated, and contributed to.

**Apache-2.0 is the current leading candidate** because it is permissive and includes an explicit patent-license framework.

This is **not yet a final license decision**.

Before first public release:

1. generate a dependency inventory;
2. identify all direct/indirect license obligations;
3. review third-party copied/adapted material;
4. choose the final ARETE license;
5. create `LICENSE`;
6. create `NOTICE` / third-party notices if required;
7. run automated license scanning;
8. review compatibility again when dependencies materially change.

---

## 14.5 Current inspiration registry — ideas only unless separately approved

License status must be re-verified at the exact version/commit before any direct reuse.

| Project | ARETE interest | Reuse posture |
|---|---|---|
| Coach Watts | canonical domain, adapters, repositories, AI orchestration | architecture inspiration; direct reuse only after exact license review |
| GoldenCheetah | plan/actual semantics, endurance analytics | concept inspiration; no code copying without copyleft review |
| Section 11 | auditable rules, validation, reason codes | concept inspiration; direct reuse only with attribution/license review |
| wger | strength models and progression concepts | concept inspiration; no code copying without AGPL compatibility review |
| Endurain | adapters, ingestion, normalization | concept inspiration; no code copying without AGPL compatibility review |
| workout.cool | frontend/domain organization | concept inspiration until license/version reviewed |
| Dreeve | automation and activity analytics ideas | concept inspiration until license/version reviewed |

This table is not a legal clearance list.

---

## 14.6 API and platform terms

An adapter must not ship merely because an API can technically be called.

For every third-party provider, document:

```text
official API / permitted access method
authentication mechanism
allowed scopes
data permitted to store
retention requirements
deletion requirements
rate limits
webhook/polling requirements
branding/attribution requirements
redistribution restrictions
commercial-use restrictions if any
date terms reviewed
```

Do not use unsupported scraping or circumvention as a production integration strategy.

Do not ask users to provide account passwords when OAuth or another provider-approved mechanism is required.

---

## 14.7 Secrets

Never commit:

- API keys;
- OAuth client secrets;
- access tokens;
- refresh tokens;
- private certificates;
- production connection strings;
- personal datasets.

Secrets belong in appropriate secret-management systems or environment configuration.

Public example files must contain placeholders only.

---

## 14.8 Personal and health-related data

ARETE should assume training, recovery, symptom, body, and wearable information can be sensitive personal information.

Engineering requirements should include:

- data minimization;
- purpose limitation;
- clear collection notice;
- explicit user control;
- access/export;
- correction where appropriate;
- deletion;
- retention policy;
- secure transmission;
- encryption for sensitive stored data where appropriate;
- least-privilege internal access;
- logging that avoids unnecessary sensitive payloads;
- breach-response planning;
- vendor/subprocessor inventory for hosted versions.

The applicable law depends on operator, users, jurisdictions, and the exact data/functions involved.

---

## 14.9 Medical / general-wellness boundary

The initial public product should be positioned as training and general-wellness software, not software that diagnoses, treats, cures, mitigates, or prevents disease or injury.

Training-related symptom handling may:

- record symptoms;
- modify training conservatively;
- explain uncertainty;
- suggest appropriate professional evaluation when warranted.

It should not:

- declare a clinical diagnosis;
- claim an injury has been ruled out;
- prescribe clinical treatment;
- make unsupported injury-prevention claims.

**A disclaimer alone does not determine regulatory status. Intended use, claims, and actual functionality matter.**

If ARETE later adds disease-specific, diagnostic, therapeutic, or clinical decision-support functionality, stop and perform a jurisdiction-specific regulatory assessment before release.

---

## 14.10 Claims discipline

Marketing claims must be held to the same evidence standard as coaching rules.

Do not claim:

- "prevents injuries";
- "optimizes recovery";
- "detects overtraining";
- "guarantees hypertrophy";
- "medically validated";
- "clinically proven";

unless the exact shipped functionality and evidence justify the exact wording and relevant regulatory/advertising requirements have been reviewed.

---

## 14.11 No real personal data in tests or documentation

Public repositories must use:

- synthetic fixtures;
- explicitly licensed public datasets;
- anonymized data only after an appropriate assessment that re-identification risk is acceptable.

Do not assume removing a name makes health/training data anonymous.

---

# 15. Security doctrine

Security is part of correctness.

At minimum, the public web architecture should eventually implement:

- secure authentication;
- strict user/tenant isolation;
- authorization checks at the data-access layer;
- encrypted transport;
- secure secret storage;
- dependency and vulnerability scanning;
- input validation;
- CSRF/XSS/SQL-injection protections appropriate to the stack;
- rate limiting where appropriate;
- audit logging for sensitive mutations;
- backup and restore testing;
- account/data deletion;
- least-privilege OAuth scopes;
- webhook verification;
- token rotation/revocation support.

High-impact security decisions should be documented through Architecture Decision Records (ADRs).

---

# 16. Testing doctrine

ARETE requires more than ordinary UI tests.

## 16.1 Unit tests

Examples:

- unit conversions;
- e1RM formulas;
- zone calculations;
- rolling trends;
- exercise mapping;
- session matching.

## 16.2 Integration tests

Examples:

- provider payload → canonical entity;
- duplicate source records → one performed session;
- planned session + activity → correct association;
- data deletion → downstream cleanup.

## 16.3 Rule tests

Example:

```text
Given:
  planned high-intensity run
  significant symptom signal

When:
  decision rules execute

Then:
  symptom/safety precedence overrides progression opportunity
```

## 16.4 Conflict tests

Explicitly construct conflicting athlete states.

Examples:

```text
high performance readiness
+
sharp pain
```

or:

```text
strength progression eligible
+
primary race session tomorrow
```

Expected precedence must be deterministic.

## 16.5 Scientific regression tests

When rules or evidence change:

```text
old ruleset
vs
new ruleset
```

must be run against standard synthetic athlete cases.

Review:

```text
expected unchanged decisions
intentional changed decisions
unexpected changed decisions
```

No silent scientific behavior drift.

## 16.6 UI contract tests

Athlete-facing UI should test that:

- unavailable capabilities do not produce noise;
- stale data is identified;
- important attention is surfaced;
- insignificant anomalies do not automatically become alerts;
- confidence/uncertainty is not misleadingly hidden.

---

# 17. Architecture direction

A likely long-term structure:

```text
packages/
  core/
    athlete/
    goals/
    sessions/
    observations/
    provenance/
    decisions/

  evidence/
    registry/
    grading/
    references/

  strength/
    exercises/
    sets/
    progression/
    volume/
    performance/

  endurance/
    activities/
    zones/
    intervals/
    trends/

  hybrid/
    scheduling/
    interference/
    priority/

  recovery/
    sleep/
    hrv/
    subjective/
    fatigue/

  symptoms/
    observations/
    constraints/
    escalation/

  physique/
    weight/
    circumference/
    photos/

integrations/
  xunji/
  strava/
  garmin/
  apple-health/
  intervals/
  fit/
  tcx/
  gpx/
  csv/
  manual/

apps/
  web/
```

This is directional, not binding until stack and repository structure are formally selected.

---

# 18. ARETE Core vs ARETE App

Prefer separation between:

## `arete-core`

Provider-independent logic:

- schemas;
- provenance;
- evidence registry;
- domain calculations;
- rule engine;
- validation;
- decisions;
- tests.

## `arete-app`

Product implementation:

- web UI;
- authentication;
- database;
- integrations;
- background jobs;
- notifications;
- user settings;
- AI interface.

This separation reduces product lock-in and makes the scientifically important core easier to test and reuse.

---

# 19. Development sequence

Do not begin by building the most visually impressive dashboard.

## Phase 0 — Constitution and audit

- maintain this document;
- audit the existing private/personal ARETE;
- identify assumptions that are specific to one athlete;
- inventory existing rules;
- inventory existing external data dependencies;
- identify current technical debt and rule conflicts.

## Phase 1 — Canonical core

- define canonical entities;
- define provenance;
- define capability model;
- define source reconciliation;
- define timestamps/freshness semantics;
- create synthetic fixtures;
- create tests.

## Phase 2 — Evidence and rules framework

- define evidence schema;
- define rule schema;
- define precedence;
- define decision/audit object;
- implement scientific regression tests.

## Phase 3 — Strength Pack

Start here because structured lifting data provides a comparatively tractable public use case.

- exercise identity;
- sets/reps/load;
- RPE/RIR;
- progression;
- comparable exposures;
- strength progress evidence gate.

## Phase 4 — Endurance Pack

- FIT/TCX/GPX ingestion;
- provider adapters;
- plan/actual matching;
- endurance trends;
- interval execution;
- conservative training-load analytics.

## Phase 5 — Hybrid Pack

- combine, do not collapse, strength and endurance;
- goal hierarchy;
- scheduling;
- local overlap;
- key-session protection;
- conflict logic.

## Phase 6 — Recovery + Symptoms

- multidimensional recovery;
- athlete-specific baselines;
- symptom precedence;
- conservative decision support.

## Phase 7 — Physique

- weight trend;
- standardized measurement protocol;
- optional photo tracking;
- evidence-gated physique progress.

## Phase 8 — AI layer

Only after core outputs are stable.

- explanation;
- summaries;
- coach dialogue;
- candidate note extraction;
- no bypass of deterministic authority.

## Phase 9 — Public web hardening

- authentication;
- multi-user isolation;
- privacy;
- security;
- API terms;
- data export/delete;
- operational monitoring;
- release/legal review.

---

# 20. Feature proposal template

Every material feature should answer:

```text
Problem:
Who has this problem?

Capability:
What minimum data is required?

Scientific basis:
What evidence supports this behavior?

Data provenance:
Where does the data come from?

Failure mode:
What happens when data is missing, stale, duplicated, or wrong?

Decision impact:
Can this change training behavior?

Auditability:
How can the conclusion be inspected?

Privacy/security:
What sensitive data or new permissions are introduced?

Third-party rights:
Does this use external code, data, APIs, media, or content?

Testing:
What unit, integration, conflict, and regression tests are required?

UI:
Does the athlete actually need to see this?

Exit criterion:
What would cause us not to ship it?
```

A feature that cannot answer these questions is not ready to implement.

---

# 21. Definition of Done

A material ARETE feature is not done merely because it renders correctly.

Where applicable, it should satisfy:

- [ ] canonical data model respected;
- [ ] provenance preserved;
- [ ] no plan/actual corruption;
- [ ] capability requirements declared;
- [ ] missing-data behavior defined;
- [ ] stale-data behavior defined;
- [ ] rule/evidence relationship documented;
- [ ] decision confidence modeled appropriately;
- [ ] precedence conflict considered;
- [ ] audit trail available;
- [ ] unit tests;
- [ ] integration tests;
- [ ] rule/conflict tests;
- [ ] scientific regression fixtures updated if behavior changed;
- [ ] privacy/security impact reviewed;
- [ ] third-party licensing/API terms reviewed;
- [ ] no secrets or real personal data committed;
- [ ] user-facing claim is no stronger than the evidence;
- [ ] Athlete Lens remains low-noise;
- [ ] documentation updated.

---

# 22. Architecture Decision Records

Important irreversible or cross-cutting decisions should receive an ADR.

Examples:

```text
ADR-001 Plan and actual separation
ADR-002 Canonical provider-neutral model
ADR-003 Capability-based UX
ADR-004 Evidence registry
ADR-005 Rule precedence
ADR-006 AI authority boundary
ADR-007 Public data storage model
ADR-008 Project license
```

An ADR should state:

- context;
- decision;
- alternatives;
- reasons;
- consequences;
- evidence;
- migration implications.

---

# 23. Things ARETE should deliberately avoid

Unless future evidence and requirements overturn this decision:

- building a Strava social clone;
- building a GPS map platform for its own sake;
- maximizing the number of charts;
- universal readiness scoring;
- universal hybrid-load scoring;
- automatic clinical diagnosis;
- arbitrary "AI coach" decisions without rule traceability;
- replacing user history with regenerated state;
- treating one noisy biomarker as authoritative;
- claiming progress from one observation;
- requiring a large wearable ecosystem;
- making GitHub part of the normal end-user workflow;
- hard-coding one athlete's schedule, goals, injury history, apps, or preferences into ARETE Core;
- copying third-party code because it is publicly visible;
- depending on unsupported API access;
- storing secrets in source control;
- feature creep that does not improve athlete decisions.

---

# 24. Release doctrine

Before a public release, perform three independent reviews.

## Engineering review

- correctness;
- reliability;
- security;
- observability;
- migration safety.

## Scientific review

- evidence validity;
- rule validity;
- uncertainty;
- unintended overclaiming;
- behavior regression.

## Legal/compliance review

- project/dependency licenses;
- attribution;
- API/provider terms;
- privacy notices and policies;
- data handling;
- product claims;
- medical/general-wellness boundary;
- jurisdiction-specific obligations relevant to the actual release.

No single review substitutes for another.

---

# 25. North Star

ARETE should become useful not because it knows everything about the athlete, but because it knows:

> **what it knows, what it does not know, how strongly it knows it, and whether that information should change what the athlete does next.**

The public project succeeds if:

- a lifter with one good training log gets a clean strength system;
- a runner with one endurance source gets a clean endurance system;
- a hybrid athlete can combine both without fake unified metrics;
- a highly instrumented athlete gains richer reasoning without overwhelming noise;
- every important conclusion can be challenged, traced, tested, and improved;
- contributors can improve algorithms and evidence without turning the codebase into an opaque collection of opinions;
- no source, dependency, API, scientific claim, or AI output is allowed to quietly become authoritative without justification.

---

# 26. Maintenance rule for this document

`ARETE.md` is a living project constitution.

When updating it:

1. preserve the core principles unless there is a documented reason to change them;
2. state why a golden rule changes;
3. check downstream architecture implications;
4. check scientific implications;
5. check licensing/privacy/security implications;
6. update the version and date;
7. prefer explicit amendment over silent deletion;
8. when a mature policy moves into a dedicated document, leave a concise reference here.

Future development sessions should begin with:

> **Read `ARETE.md`, identify the governing rules for the requested change, then inspect the current code before proposing implementation.**

---

## Appendix A — Current design inspirations

ARETE is informed by lessons from mature and emerging open-source projects including:

- Coach Watts — canonical domain logic, integration architecture, asynchronous processing, AI orchestration;
- GoldenCheetah — endurance semantics, planned-versus-actual training, mature performance analysis;
- Section 11 — deterministic/auditable coaching protocol concepts;
- wger — strength workout data modeling and progression concepts;
- Endurain — provider adapters, activity ingestion, provider-independent normalization;
- workout.cool — modular frontend/domain organization;
- Dreeve — activity automation and analytics concepts.

**ARETE is not intended to be a fork or derivative of any one of these projects.**

Architecture ideas should be independently evaluated. Scientific rules must be independently evidence-reviewed. Code reuse requires separate license review.

---

## Appendix B — Legal/reference baseline reviewed during initial drafting

Initial engineering governance was informed by public official guidance current at drafting time, including:

- Open Source Initiative (OSI) approved-license resources;
- New Zealand Privacy Act 2020 principles and current Privacy Commissioner guidance;
- New Zealand Health Information Privacy Code where applicable to entities/functions within its scope;
- U.S. FDA 2026 General Wellness guidance as an example of why intended use and disease-related claims can alter regulatory treatment.

These references are not a substitute for a release-specific legal review and must be rechecked because law, guidance, provider terms, and product functionality change over time.
