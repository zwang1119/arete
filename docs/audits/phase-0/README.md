# ARETE Phase 0 — First-Principles Audit Synthesis

> Status: **Phase 0 audit complete**  
> Date: 2026-08-15  
> Scope: personal ARETE architecture, scientific logic, public-alpha generalization, privacy/security, and release governance  
> Production code changes during Phase 0: **none**

## 1. Why Phase 0 existed

The purpose of Phase 0 was not to redesign ARETE from taste or to turn the personal project into a public clone. It was to identify which concepts survive first-principles, scientific, engineering, open-source-reference, privacy and legal scrutiny before further production development.

The audit method used for every module was:

```text
problem/purpose
→ inspect current personal ARETE
→ challenge assumptions from first principles
→ review relevant scientific evidence
→ review mature open-source/reference implementations
→ identify failure modes and legal/maintenance constraints
→ audit verdict
→ design proposal
→ required tests
```

The personal ARETE was treated as a demanding real-world test case, never as the specification.

---

## 2. Module index

| # | Module | Audit | Design |
|---|---|---|---|
| 01 | Data ingestion, source of truth, freshness, reconciliation | `01-data-ingestion-source-of-truth.md` | `../../design/01-data-session-foundation.md` |
| 02 | Canonical training ontology, Plan / Actual, session identity | `02-canonical-data-model-plan-actual.md` | `../../design/02-canonical-training-ontology.md` |
| 03 | Strength Engine | `03-strength-engine.md` | `../../design/03-strength-engine.md` |
| 04 | Endurance Engine | `04-endurance-engine.md` | `../../design/04-endurance-engine.md` |
| 05 | Recovery Engine | `05-recovery-engine.md` | `../../design/05-recovery-engine.md` |
| 06 | Symptom & Safety Engine | `06-symptom-safety-engine.md` | `../../design/06-symptom-safety-engine.md` |
| 07 | Hybrid / Concurrent Training Engine | `07-hybrid-engine.md` | `../../design/07-hybrid-engine.md` |
| 08 | Progress & Physique Engine | `08-progress-physique-engine.md` | `../../design/08-progress-physique-engine.md` |
| 09 | Goals, Rules, and Decision Engine | `09-goals-decision-engine.md` | `../../design/09-goals-decision-engine.md` |
| 10 | Scientific Evidence Governance | `10-evidence-governance.md` | `../../design/10-evidence-governance.md` |
| 11 | AI & Explanation Layer | `11-ai-explanation-layer.md` | `../../design/11-ai-explanation-layer.md` |
| 12 | Athlete Lens / Coach Lens / Attention UX | `12-athlete-coach-ux.md` | `../../design/12-athlete-coach-ux.md` |
| 13 | Public Alpha / Privacy / Security / Compliance | `13-public-alpha-privacy-security.md` | `../../design/13-public-alpha-privacy-security.md` |
| 14 | Testing / Scientific Regression / Observability / Release | `14-testing-observability-release.md` | `../../design/14-testing-observability-release.md` |

---

## 3. Stable architecture conclusions

The following conclusions survived cross-module review strongly enough to guide implementation unless a future ADR explicitly overturns them.

### 3.1 Reality before presentation

```text
Source evidence
→ Canonical facts
→ Associations
→ Domain assessments
→ Decisions
→ Presentation / AI explanation
```

Facts, associations, assessments and decisions are different layers. None may silently rewrite another.

### 3.2 Provider-neutral Core

Providers are evidence sources and adapters, not ARETE ontology.

```text
Provider
→ Adapter
→ SourceObservation / SourcePlanRecord
→ Canonical ARETE entities
→ Capability
→ Domain Engine
```

A provider change should not require rewriting the scientific Core.

### 3.3 Stable internal identity

Dates and provider IDs are properties/references, not canonical identity.

`PlannedSession` and `PerformedSession` use ARETE-controlled stable IDs. Rescheduling does not create a new intended session merely because the date changed.

### 3.4 Immutable historical plan evidence

Plan evolution is represented by `PlannedSessionRevision`. Historical assessment uses the last legitimately effective/approved revision available before execution.

Post-hoc edits cannot retroactively improve adherence.

### 3.5 Explicit Plan / Actual association

Date equality is evidence for matching, not proof of matching.

Many-to-many real-world relationships are allowed through `SessionAssociation`, with association state and relationship type separated. Ambiguity is a valid state.

### 3.6 Domain-specific engines

Strength, Endurance, Recovery, Symptoms, Hybrid and Physique/Progress share Core entities/governance but retain their own scientific semantics.

Do not force modalities into one training-load/readiness/quality score.

### 3.7 Goal-aware decisions

Training decisions require formal goal hierarchy:

```text
Primary
Secondary
Maintenance
Constraint
```

The same athlete state may justify a different decision under a different goal hierarchy.

### 3.8 Scientific evidence does not directly execute code

Required chain:

```text
ScientificReference
→ EvidenceReview
→ EvidenceClaim
→ ImplementationPolicy
→ ExecutableRule
→ Decision
```

A population association or meta-analysis does not automatically validate an individual threshold.

### 3.9 Policy thresholds disclose what they are

Numeric thresholds may be evidence-supported, consensus/best-practice, product-safety, user-specific or experimental. Existing configuration does not confer scientific authority.

Examples requiring reclassification include universal pain cutoffs, set caps, training-load ratios and fixed progress-exposure counts.

### 3.10 No-change is a legitimate coaching decision

ARETE should prefer the smallest justified action. When evidence for beneficial modification is weak, `maintain` is often the correct answer.

### 3.11 Decision is not mutation

```text
DecisionRecord
→ PlanMutationRequest
→ validation/authority/user confirmation
→ new PlannedSessionRevision
```

AI, UI or a rule may not bypass this path.

### 3.12 Progress is claim-specific

There is no universal “3 observations = improving / 5 = established” rule.

Every progress claim defines its question, comparable evidence, measurement reliability/error, context and evidence gate.

### 3.13 Recovery is multidimensional

No universal readiness score. Sleep, HRV, subjective fatigue, local soreness, psychological readiness, illness and training context remain distinct evidence.

### 3.14 Symptoms have safety precedence without becoming diagnosis

Symptoms are modeled as observations/episodes/trajectories. Safety policies can constrain training while remaining explicit about whether a threshold is scientific, product-policy or user-specific.

ARETE does not independently diagnose disease/injury in the initial product.

### 3.15 Hybrid means conflict resolution, not metric fusion

Concurrent training is coordinated through goal priority, session importance, local overlap, intensity, timing and symptoms. Acute session-quality concerns and chronic adaptation concerns are separate.

No universal Hybrid Load Score.

### 3.16 AI is downstream

AI explains validated state and may extract/propose candidate structured information. It is not the authority for facts, evidence thresholds, diagnosis or plan mutation.

ARETE Core remains usable when AI is unavailable.

### 3.17 Athlete attention is scarce

The Athlete Lens shows information that can change current/next behavior. Persistent issues have stable AttentionEpisode identity so a refresh does not manufacture a new alarm.

Coach Lens exposes Observed / Interpreted / Decided / Alternatives / Unknown with provenance.

### 3.18 Public alpha is a correctness test

The first 1–3 external users test generalization, assumptions, matching, missing-data behavior, UX and trust. They do not validate training efficacy or clinical outcomes.

Public runtime data must be separated from the public Git repository and protected by real multi-user authorization, privacy, deletion and security controls.

### 3.19 Scientific regression is a release requirement

Ruleset changes must be evaluated against standardized athlete scenarios and produce an intentional decision diff.

```text
unchanged
intentional change
unexpected change
```

A release cannot contain unresolved unexpected scientific-behavior changes.

---

## 4. Important domain conclusions

### Strength

- explicit load semantics;
- ExerciseDefinition separated from ExerciseConfiguration and comparison context;
- RPE/RIR useful but uncertain;
- top-set heuristics are candidate signals, not universal authority;
- no universal `effective set` truth;
- training performance does not prove hypertrophy;
- progression is goal/context-aware decision-making, not merely detecting a better set.

### Endurance

- constraints differ from targets;
- structured execution differs from physiological response;
- zone models are method/version dependent;
- training-intensity distribution is descriptive before prescriptive;
- HR drift/decoupling is contextual, not universal pass/fail;
- races/tests/benchmark workouts/submaximal observations carry different evidence strength.

### Recovery

- no wearable required;
- subjective evidence is valid;
- athlete-/method-specific baselines;
- device changes create measurement-regime boundaries.

### Symptoms

- pain intensity alone is inadequate;
- symptom episodes and trajectories matter;
- conservative exposure modification is allowed without diagnostic claims;
- personal 4/10 policy may remain personal policy rather than universal science.

### Physique

- measurement method, precision and standardization govern claims;
- smart-scale body-fat estimates are weak evidence for precise body-composition change;
- standardized circumference/bodyweight/photos can contribute to convergent evidence;
- photographs should not be a default first-alpha data requirement.

---

## 5. Public-alpha non-negotiables

Before the first external participant:

```text
multi-user authentication + server-side authorization
private runtime data store
no real personal data/secrets in public Git
collection/privacy notice
export/delete path
provider terms review
provider-scoped deletion lineage when required
minimal sensitive-data collection
AI processor/data-handling review if AI is enabled
critical security and scientific regression tests
```

The New Zealand Privacy Act 2020, including indirect-collection requirements now in force, must be reflected in the actual data flows. Applicability of the Health Information Privacy Code must be assessed for the actual operator/function rather than assumed. Provider-specific rules such as Strava's current retention/deletion terms are release gates for those adapters.

---

## 6. What remains provisional

Phase 0 defines architecture and governance, not final implementation details.

Still intentionally open:

```text
exact runtime stack/database
final open-source license
exact canonical database schema/field naming
exact e1RM models and supported rep domains
exact strength progression policies
exact zone methods offered by default
exact progress evidence gates per claim
exact recovery baseline algorithms
exact default public symptom policies
exact authentication provider
exact AI provider/model
which provider integrations ship in alpha
```

These require ADRs, evidence/policy records and implementation tests before being frozen.

---

## 7. Recommended implementation dependency order

When production work resumes, do not implement modules in UI order.

```text
1. Governance scaffolding
   ADRs / evidence registry / ruleset versioning / provenance

2. Canonical foundation
   IDs / source records / Plan revisions / PerformedSession / association / source health

3. Runtime data plane
   user-scoped datastore / ingestion jobs / deletion lineage / replay

4. Strength Engine

5. Endurance Engine

6. ProgressClaim framework

7. Goals + Decision Engine

8. Symptoms + Recovery

9. Hybrid coordination

10. Athlete / Coach presentation contracts

11. AI explanation/extraction

12. Public-alpha hardening
    auth / privacy / security / provider compliance / operational observability
```

This order can be changed through ADR when dependencies justify it; it is not a mandate to build every feature before the personal ARETE receives value.

---

## 8. Phase 0 exit criterion

Phase 0 is complete because the project now has a documented answer to:

- what ARETE's core facts are;
- what must remain provider-specific;
- how plans/history are preserved;
- how major training domains differ;
- how progress claims are governed;
- how scientific evidence becomes policy/rules;
- how decisions and mutations are separated;
- what AI may and may not do;
- what Athlete/Coach lenses are for;
- what legal/privacy/security minimums precede external users;
- how scientific behavior will be regression-tested.

The next phase should begin with ADRs and canonical-contract implementation, not another feature brainstorm.
