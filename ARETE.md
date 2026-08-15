# ARETE — Project Constitution

> **Status:** Living normative document  
> **Version:** 0.3.0  
> **Last updated:** 2026-08-15  
> **Canonical file:** `ARETE.md`  
> **Phase 0 audit:** `docs/audits/phase-0/README.md`
>
> **Required reading:** Every developer, reviewer, AI agent, and contributor must read this file before materially changing ARETE.
>
> **Precedence:** law / licensing / privacy / security → safety → data integrity → scientific validity → auditability → athlete goals → product convenience → visual polish.

---

## 1. Mission

ARETE is an evidence-governed, auditable athlete intelligence system for strength, endurance, hybrid training, physique development, recovery, and training-related symptom awareness.

Its purpose is not to collect the most data, maximize engagement, or reproduce existing fitness platforms. Its purpose is to answer, as reliably as available evidence and data permit:

1. What was planned?
2. What actually happened?
3. What state is the athlete currently in?
4. Is there a meaningful deviation, risk, or emerging signal?
5. Is there credible evidence of progress, stability, or regression?
6. Does anything need to change?
7. Why is ARETE making that conclusion, and how certain is it?

> **Observe faithfully. Infer cautiously. Decide transparently.**

---

## 2. First-principles rule

ARETE is not governed by legacy implementation, the preferences of its original user, another open-source project, coaching convention, a popular metric, or an AI suggestion.

For every material feature, rule, metric, data source, or architectural choice ask:

1. What problem are we actually solving?
2. Does solving it materially help the athlete or protect system integrity?
3. What is the simplest scientifically defensible solution?
4. Has the problem already been solved well by a standard, library, or open-source project?
5. Is that solution actually appropriate for ARETE?
6. What are the failure modes, uncertainty, maintenance cost, privacy/security consequences, legal constraints, and opportunity cost?
7. What happens if we do not build it?

Existing ARETE behavior is evidence about real usage, **not authority**. It may be retained, redesigned, or removed.

---

## 3. Personal ARETE and public alpha

### Personal ARETE

The personal ARETE remains a primary product priority and the richest real-world environment. It may have the most integrations and personalization.

It is **not the scientific or architectural specification**. Personal providers, schedules, goals, constraints, external requirements, injury history, preferences, and workflows remain configuration/adapter/profile concerns unless first-principles review shows that they belong in the shared Core.

### Public alpha

The near-term public version is deliberately small: approximately 1–3 trusted external users with different training profiles.

Its purpose is to expose:

- hidden personal assumptions;
- data-model failures;
- matching/reconciliation failures;
- weak defaults;
- missing-data failures;
- provider portability problems;
- incorrect or unclear recommendations;
- unnecessary/missed attention;
- privacy/security/integration problems.

It is **not** evidence that ARETE improves athletic performance, prevents injury, or is clinically valid.

Personal and public versions use the **same evolving ARETE Core**. They may differ in data sources, enabled capabilities, configuration, goals, and UI exposure, but must not maintain separate scientific definitions merely for convenience.

**Core principles should be stable; Core implementation must remain correctable.**

---

# 4. Golden Rules

## GR-00 — First principles outrank legacy, precedent, and preference

A feature is not justified because it exists, another project uses it, a coach commonly recommends it, an AI suggested it, or it looks sophisticated.

## GR-01 — Facts, relationships, assessments, and decisions are different layers

Canonical flow:

```text
Source evidence
→ Canonical facts
→ Associations
→ Domain assessments
→ Decisions
→ Presentation / AI explanation
```

Facts describe what existed or happened. Associations describe relationships. Assessments describe meaning. Decisions describe what to do next. **None may silently rewrite another.**

## GR-02 — Plan and Actual are different facts

`PlannedSession` and `PerformedSession` are separate entities linked by explicit `SessionAssociation` records. Actual activity never overwrites the historical prescription.

## GR-03 — Date and provider IDs are not canonical identity

ARETE-controlled internal IDs identify canonical entities. Date/time and provider identifiers are properties/references.

Rescheduling a planned session does not automatically create a different intended session. Two sessions on the same date must be able to coexist.

## GR-04 — Historical plan revisions are immutable evidence

Plan changes create `PlannedSessionRevision` records rather than rewriting history.

Historical execution must be assessed against the latest legitimately effective/approved revision available **before execution**. A later edit must never retroactively improve adherence or alter what was genuinely prescribed.

## GR-05 — External apps are evidence sources, not ARETE's ontology

Use:

```text
Provider → Adapter → Source Record → Canonical Model → Capability → Domain Engine
```

Domain logic must not depend on whether a user happens to use Xunji, Strava, Runna, Garmin, Apple Health, or another provider.

## GR-06 — Capability detection, not app detection

ARETE asks what it is qualified to answer from available data. Missing integrations reduce capabilities; they do not make the entire product unusable.

## GR-07 — Minimum viable data must still produce value

A strength athlete with exercise/sets/reps/load/date or a runner with basic activity files can already receive appropriately limited analysis. More data expand capability; they do not license stronger claims unless they are actually relevant and reliable.

## GR-08 — Every important datum has provenance

Where relevant distinguish:

```text
measured
reported
imported
estimated
derived
inferred
vendor-derived
```

Important facts retain source, time, method, measurement regime, quality, and lineage.

## GR-09 — Do not select one global provider winner

One real-world session may have multiple source observations. Canonical values may use different sources for different fields, with field-level lineage. Duplicate source evidence is reconciled, not silently deleted.

## GR-10 — Canonical calculations have one source of truth

A derived metric is computed once in its authoritative domain module and reused by UI, reports, rules, and AI. Presentation code must not repair or independently reinterpret canonical truth.

## GR-11 — Missing, ambiguous, partial, and provisional are legitimate states

Never convert unknown to zero, normal, completed, or missed merely to simplify UI.

If a required input is absent, return `not_applicable`, `insufficient_data`, `unknown`, or an equivalent explicit state.

## GR-12 — Freshness is multidimensional

Transport success is not data completeness. Distinguish at least as needed:

```text
last attempt
last successful read
coverage / observed-through
completeness
finality
presentation/decision calculation time
```

A green “updated” state must not imply data are complete when they are merely recently checked.

## GR-13 — Do not collapse different training modalities into fake precision

Strength, endurance, recovery, symptoms, and physique retain their own scientific semantics. Do not create a universal training-load, quality, fatigue, readiness, or hybrid-load score without compelling validation.

## GR-14 — No universal readiness score

Sleep, HRV, resting HR, subjective fatigue, local soreness, psychological readiness, illness, and recent training exposure are distinct observations. Recovery is multidimensional and task-relevant.

## GR-15 — Goal hierarchy matters

Goals must support:

```text
Primary
Secondary
Maintenance
```

Hard constraints are modeled separately. Conflicting decisions protect the primary goal's key outcomes subject to safety and real-world constraints.

## GR-16 — Evidence is not policy, and policy is not a rule

Scientific chain:

```text
ScientificReference
→ EvidenceReview
→ EvidenceClaim
→ ImplementationPolicy
→ ExecutableRule
→ Decision
```

A scientific association does not automatically justify a software threshold.

## GR-17 — Population evidence does not automatically justify individual prescription

Group effects inform prior knowledge and policy. They do not prove that an individual athlete's current state was caused by a specific exposure or that a population threshold should become a personal command.

Consider applicability, effect magnitude, within-person variability, measurement error, alternatives, uncertainty, and costs of false positives/false negatives.

## GR-18 — Evidence certainty, applicability, data quality, and decision confidence are separate

Do not collapse:

```text
scientific evidence certainty
population applicability
data completeness
data quality/measurement reliability
decision confidence
```

into one unexplained score.

## GR-19 — Numeric thresholds disclose what they are

Every executable threshold/rule should be classified, for example:

```text
evidence_supported
consensus_or_best_practice
product_safety_policy
user_specific_policy
experimental
```

A number does not acquire scientific authority because it exists in configuration or code.

## GR-20 — Deterministic authority before generative explanation

Preferred flow:

```text
validated data
→ domain assessment
→ rule evaluation
→ decision
→ reason codes/confidence
→ AI explanation
```

AI may explain, summarize, contextualize, extract candidate facts, and propose candidate actions. It must not silently invent thresholds, citations, diagnoses, missing facts, authoritative metrics, source precedence, or historical state.

## GR-21 — AI is replaceable and Core remains useful without it

AI failure or model change must not stop ingestion, canonical state, deterministic decisions, or basic Athlete/Coach views.

Imported/user text is untrusted data, not instructions that can redefine ARETE policy or authority.

## GR-22 — Consequential recommendations are auditable

Important decisions retain sufficient information to reconstruct:

```text
what was known
what was missing/uncertain
which goals/constraints applied
which rules/policies/evidence versions applied
candidate actions where material
selected action
confidence
precedence/overrides
what the athlete did
what happened later
```

Do not store hidden model chain-of-thought as the audit mechanism. Structured facts/reasons/versions are the audit trail.

## GR-23 — No change is a legitimate coaching decision

`maintain` is a successful outcome when modification is not sufficiently justified. ARETE must not change training merely to appear useful.

Prefer the smallest intervention that solves the identified problem while preserving intended stimulus.

## GR-24 — Decision and plan mutation are different authority levels

```text
DecisionRecord
→ PlanMutationRequest
→ validation / authority / conflict checks
→ user confirmation when required
→ new PlannedSessionRevision
```

No AI, UI component, provider adapter, or ordinary domain rule may bypass this pathway.

## GR-25 — User autonomy is preserved

The athlete controls the plan. User overrides are recorded as evidence, not automatically labeled poor adherence. Original plan revisions remain recoverable.

## GR-26 — Symptoms are a separate safety domain, not merely a recovery score input

Track symptom observations/episodes/trajectory/function without inventing diagnoses. Safety can override progression when materially relevant, but should constrain only the relevant exposure when possible.

A pain cutoff or similar threshold must be identified as evidence-supported, condition-specific, product-policy, or user-specific rather than presented as a universal clinical law.

## GR-27 — Progress claims are question-specific and measurement-aware

There is no universal number of sessions/days that transforms an observation into improvement.

A `ProgressClaim` must identify:

```text
what question is being answered
what observations are comparable
measurement regime/reliability
meaningful-change/evidence gate where appropriate
period
context/confounders
confidence/limitations
```

One good session is not automatically progress.

## GR-28 — Physique conclusions require appropriate evidence

Training progression does not prove hypertrophy. Bodyweight, standardized circumference, standardized photos when voluntarily provided, validated composition methods, and performance/training context are different evidence streams.

Consumer body-fat estimates and cross-device body-composition changes must not be given unjustified precision.

## GR-29 — Attention is scarce

Athlete-facing alerts appear only when information is likely to alter current or next-step behavior. Persistent issues use stable AttentionEpisode identity rather than becoming a new alert after every refresh.

Coach Lens may expose substantially more evidence and uncertainty.

## GR-30 — Decision history is longitudinal evidence, not automatic causality

The sequence:

```text
Decision → Athlete action → Outcome
```

is valuable evidence about the athlete and ARETE. Repeated personal patterns can inform future decisions, but retrospective association does not automatically become a causal or universal rule.

## GR-31 — Privacy/security/compliance are Core correctness

Training, recovery, symptom, body, wearable, and photo data may be sensitive personal information.

Design for purpose limitation, data minimization, user access/export/correction/delete, retention, secure storage/transport, least privilege, source/provider deletion lineage, privacy-conscious logging, and multi-user isolation.

Provider terms and jurisdiction-specific obligations are release requirements, not documentation cleanup.

## GR-32 — Athlete data are not an advertising asset

ARETE should not sell athlete health/training data or use them for targeted advertising. Provider-specific restrictions such as Apple Health rules may be even stricter and must be followed.

## GR-33 — Public alpha tests correctness/generalization, not efficacy

The first external users validate whether Core can support different capabilities without personal assumptions. Early success metrics prioritize data/matching/calculation/rule integrity, explanation quality, false/missed attention, privacy, and security—not engagement.

## GR-34 — Scientific behavior is regression-tested

Material ruleset changes must be run against standardized athlete scenarios. Every decision change is categorized as:

```text
expected unchanged
intentional change
unexpected change
```

A release has no unresolved unexpected scientific-behavior changes.

## GR-35 — Serious failures become tests or invariants

When a bug reveals a class of failure, add a regression fixture/invariant when feasible. Critical invariants include plan/actual separation, anti-hindsight history, unknown-not-zero, association authority, safety precedence, AI mutation boundaries, tenant isolation, and provider deletion lineage.

---

# 5. Canonical model direction

The canonical model should evolve around provider-neutral concepts such as:

```text
Athlete
Goal
Constraint
TrainingPlan
PlannedSession
PlannedSessionRevision
PerformedSession
SessionAssociation
SessionAssessment

SourcePlanRecord
SourceObservation
SourceDependency
CorrectionEvent

ExerciseDefinition
ExerciseConfiguration
SetPrescription
SetPerformance

EndurancePrescription
EnduranceSegmentObservation
ZoneModel
SensorCoverage

RecoveryObservation
RecoveryAssessment
MeasurementRegime

SymptomObservation
SymptomEpisode
SymptomAssessment

BodyMeasurement
MeasurementProtocol
ProgressQuestion
ProgressClaim

ScientificReference
EvidenceReview
EvidenceClaim
ImplementationPolicy
ExecutableRule

DecisionContext
DecisionRecord
PlanMutationRequest
AuditEvent
```

This is a design direction, not a frozen database schema. Cross-cutting schema decisions require ADRs and migration/invariant tests.

---

# 6. Domain responsibilities

## Strength

Represent exercise/configuration, set-level performance, load semantics, RPE/RIR where available, comparison context, goal-specific performance trends, and progression decisions.

Do not equate `sets × reps × kg` with total stimulus. Do not create a universal binary “effective set.” Do not infer hypertrophy from lifting performance alone.

## Endurance

Represent time/distance/pace, HR/power where available, structured intervals, environment/sensor context, versioned zone methods, plan execution, internal/external load descriptors, performance tests, and longitudinal evidence classes.

Constraints are not targets. Execution and physiological response are different questions. Do not hard-code a universal polarized/80-20 distribution.

## Recovery

Represent sleep, autonomic observations, subjective fatigue, local soreness/fatigue, psychological readiness, illness, and measurement regime. Prefer athlete-/method-specific baselines where defensible.

## Symptoms

Represent location, side, severity, quality, provoking task, trajectory, functional impact, recurrence, modification, and original report. Allow conservative training decisions without diagnostic claims.

## Hybrid

Coordinate domain demands using goal priority, session importance, timing, intensity, local overlap, symptoms, and acute-vs-chronic conflict. Do not create a universal Hybrid Load Score.

## Progress / Physique

Progress is claim-specific. Measurement reliability, comparability and standardized protocols determine what can be concluded. Stable performance may be success under a maintenance goal.

---

# 7. Open-source development doctrine

ARETE should actively study strong open-source projects so mature engineering problems are not repeatedly solved in isolation. External projects are references, not authorities.

Current references include Coach Watts, GoldenCheetah, Section 11, wger, Endurain, workout.cool, and Dreeve.

For each material problem:

```text
First-principles problem definition
→ current ARETE audit
→ open-source/standard review
→ scientific review where relevant
→ alternatives/failure/license/maintenance comparison
→ ARETE design decision
→ ADR/design documentation
→ tests
```

### Reuse levels

**Level 1 — Concept/design inspiration**  
Learn an abstraction/workflow and independently design ARETE's solution.

**Level 2 — Independent implementation from specification/evidence**  
Implement a standard/algorithm/scientific method independently with provenance and tests.

**Level 3 — Direct dependency**  
Use a mature library after maintenance, security, license, and compatibility review.

**Level 4 — Source reuse/adaptation**  
Copy/adapt source only with compelling reason and explicit exact-license, attribution, compatibility, provenance, maintenance, and release review.

Until a deliberate compatibility decision says otherwise:

> **Do not copy, line-by-line translate, or adapt GPL/AGPL code into ARETE Core.**

---

# 8. Evidence governance

A scientific reference is not an executable rule.

Each important evidence review should identify:

```text
question
population
intervention/exposure/comparator
outcome
main findings/effect uncertainty
limitations
population applicability
measurement limitations
certainty
what it supports
what it does not support
review date/status
```

Before converting evidence into policy ask:

1. Is the population relevant?
2. Is the endpoint relevant?
3. Is the evidence causal or associative?
4. Is the magnitude practically meaningful?
5. Is it replicated/consistent?
6. Is measurement reliable enough for an individual decision?
7. Has the proposed threshold actually been validated?
8. Does acting improve the decision versus doing nothing?
9. What is the cost of false positive action?
10. What is the cost of false negative inaction?

ARETE may use A/B/C/D/U as an internal certainty shorthand, but it is **not formal GRADE** and must never be represented as such.

Evidence, policies, and rules are versioned. Historical decisions retain the versions under which they were made.

---

# 9. Public-user and integration doctrine

Normal users should never need GitHub knowledge.

GitHub contains code, schemas, evidence, rules, tests, ADRs, and synthetic fixtures. Hosted user data belongs in an appropriate user-scoped runtime datastore.

Integrations are adapters. Support standard/user-controlled import formats where appropriate so ARETE does not depend exclusively on third-party APIs.

For every provider document:

```text
permitted access method
auth/scopes
storage/retention terms
deletion/revocation requirements
rate limits/webhooks
redistribution/display restrictions
commercial/branding restrictions
terms review date/version
```

No unsupported scraping, credential sharing, or assumption that publicly accessible data are freely reusable.

Provider-derived data must support lineage sufficient for required correction/deletion/re-canonicalization.

---

# 10. Legal, privacy, security, and medical boundary

Legal compliance is a release criterion.

### Licensing/provenance

No external code, text, dataset, media, exercise library, prompt library, or other copyrighted material enters ARETE without known provenance and reuse rights.

Before first public release perform dependency/license inventory, provenance review, compatibility review, and required notices. A permissive OSI-approved license remains preferred; Apache-2.0 is a leading candidate, not a final decision.

### Secrets

Never commit API keys, OAuth secrets, access/refresh tokens, certificates, production credentials, or personal datasets.

### Multi-user security

Server-side authorization and tenant isolation apply to every user-owned object and background job. UI filtering is never the security boundary.

Use mature authentication/security primitives; do not invent custom cryptography.

### Privacy

Design for:

```text
clear purpose/notice
data minimization
per-integration scope
access/export
correction
deletion
retention
cross-border/processor review
least privilege
privacy-conscious logging
```

Real personal data and secrets never enter public tests/documentation.

### Health/general-wellness boundary

Initial ARETE is training/general-wellness software, not diagnostic or therapeutic software. Do not claim to diagnose, treat, cure, mitigate, prevent, or rule out disease/injury.

A disclaimer does not override actual intended use/functionality. Clinical expansion requires a new jurisdiction-specific regulatory review.

---

# 11. Athlete Lens, Coach Lens, and AI

## Athlete Lens

Answer the next useful questions with minimum noise:

```text
What should I do today?
Did anything important change?
How did the last session go?
What is next?
Is there credible progress?
```

Capabilities determine navigation. Missing modules do not generate empty dashboard noise.

## Coach Lens

Prefer the structure:

```text
Observed
Interpreted
Decided
Alternatives
Unknown / limitations
```

with drill-down to source/evidence/rules.

## AI

AI gets compact task-relevant canonical context and approved evidence summaries. Material free-text extraction produces candidate structured facts that pass validation/confirmation policy.

AI cannot directly mutate plan/history or invent scientific authority.

---

# 12. Testing and release doctrine

ARETE requires layered testing including:

```text
unit
schema/contracts
provider adapters
canonicalization/reconciliation
plan-actual association
domain engines
rule/precedence conflicts
scientific regression
privacy/deletion
multi-user authorization/security
AI validation boundary
UI/accessibility
schema migration/decision replay
license/provenance/supply-chain checks
```

Critical invariants should have explicit IDs/tests.

A material ruleset release produces a decision diff across standardized synthetic athlete scenarios. Intentional changes reference the evidence/policy/ADR that justified them.

Operational observability uses minimized structured metadata; general logs should not contain raw workout notes, symptom narratives, photos, route geometry, or tokens by default.

A public release requires independent gates:

```text
Engineering
Scientific
Privacy / security / legal / licensing
```

All must pass.

---

# 13. Development sequence after Phase 0

Phase 0 audit is documented in `docs/audits/phase-0/README.md` and its linked design proposals.

When production development resumes, preferred dependency order is:

```text
1. Governance scaffolding
   ADRs / evidence registry / ruleset versioning / provenance

2. Canonical foundation
   IDs / source records / plan revisions / performed sessions / association / source health

3. Runtime data plane
   user-scoped storage / ingestion / deletion lineage / replay

4. Strength Engine

5. Endurance Engine

6. ProgressClaim framework

7. Goals + Decision Engine

8. Symptoms + Recovery

9. Hybrid coordination

10. Athlete / Coach presentation contracts

11. AI explanation/extraction

12. Public-alpha hardening
```

This order is a dependency-oriented recommendation, not a ban on delivering incremental value to the personal ARETE. Changes require ADR justification when they alter cross-cutting architecture.

---

# 14. Definition of Done

A material feature is not done because it renders correctly. Where applicable it must have:

- first-principles problem justification;
- canonical data-model compliance;
- provenance/lineage;
- Plan/Actual/history integrity;
- declared capabilities and data requirements;
- missing/ambiguous/stale/partial behavior;
- measurement/claim limitations;
- evidence → policy → rule traceability where scientific behavior changes;
- goal/constraint/precedence handling;
- DecisionRecord/auditability;
- unit/integration/domain/rule/conflict tests;
- scientific regression coverage when behavior changes;
- privacy/security impact review;
- provider API terms and licensing/provenance review;
- no secrets or real personal data in public repository/tests;
- user-facing claims no stronger than evidence;
- Athlete Lens remains low-noise;
- documentation/ADR updated;
- plan mutation follows its governed pathway.

---

# 15. Things ARETE deliberately avoids

Unless future evidence/ADR overturns the decision:

- Strava-style social-network scope;
- charts/maps without decision value;
- universal readiness/fatigue/quality scores;
- universal hybrid-load scores;
- automatic diagnosis or treatment claims;
- opaque AI coaching authority;
- historical state rewritten by current logic;
- one noisy biomarker treated as truth;
- generic fixed progress thresholds across domains;
- training-log-only hypertrophy claims;
- universal “effective set” truth;
- universal 80/20 endurance compliance;
- unnecessary wearable dependence;
- GitHub as normal end-user workflow/runtime database;
- provider-specific assumptions embedded in Core;
- third-party code copied merely because it is public;
- unsupported API access;
- secrets/personal data in source control;
- feature creep that does not improve decisions or system integrity.

---

# 16. North Star

Immediate strategy:

> **Use first principles, high-quality evidence, and the strongest relevant open-source ideas to improve the personal ARETE; continuously distill genuinely general capabilities into the same ARETE Core; expose that Core through a small, low-friction public alpha with personal data and personal assumptions separated from the shared system.**

ARETE succeeds not because it knows everything, but because it knows:

> **what it knows, what it does not know, how strongly it knows it, and whether that information should change what the athlete does next.**

---

# 17. Maintenance rule

`ARETE.md` is a living constitution.

When changing it:

1. explain why a core principle changes;
2. preserve the previous rationale through ADR/history rather than silent drift;
3. check architecture implications;
4. check scientific implications;
5. check privacy/security/legal/licensing implications;
6. update version/date;
7. update affected scientific-regression scenarios/invariants;
8. keep detailed implementation design in `docs/design/` rather than bloating the constitution.

Every future development session begins with:

> **Read `ARETE.md`; define the problem from first principles; identify the governing rules; inspect current code/data; review relevant evidence and references; only then propose implementation.**

---

## v0.3.0 amendment note

Version 0.3.0 consolidates the completed Phase 0 audits. It does not intentionally reverse the v0.2.0 project direction. It strengthens previously implicit principles, especially:

- facts / associations / assessments / decisions separation;
- stable provider-neutral identity;
- immutable plan revisions and anti-hindsight assessment;
- provider field-level lineage and multidimensional freshness;
- explicit rule classes for policy thresholds;
- claim-specific progress gates;
- DecisionRecord vs PlanMutationRequest separation;
- multidimensional recovery and symptom-episode modeling;
- AI authority boundaries;
- public-alpha privacy/security requirements;
- scientific regression and release governance.
