# Phase 0 Audit 02 — Canonical Data Model, Plan / Actual Ontology, and Session Identity

> **Status:** Audit complete; no production implementation changes made  
> **Date:** 2026-08-15  
> **Scope audited:** current personal ARETE in `zwang1119/training-weekly-data`  
> **Governing document:** `ARETE.md` v0.2.0  
> **Related design:** `docs/design/01-data-session-foundation.md`  
> **Decision posture:** first principles > scientific defensibility > auditability > legacy behavior > convenience

---

## 1. Module purpose

Module 02 defines the nouns of ARETE.

Before strength, endurance, recovery, progress, or coaching rules can be trusted, the system must know what the following things actually are:

- a training plan;
- a planned session;
- a prescription;
- a revision of a plan;
- a performed training attempt;
- a source record;
- a plan/actual association;
- a session assessment;
- a correction;
- a training block or recurring commitment.

If these entities are conflated, downstream calculations can be internally consistent while answering the wrong question.

The required separation is:

```text
Source evidence
    ↓
Canonical facts
    ↓
Plan / actual association
    ↓
Assessment
    ↓
Decision
    ↓
Presentation
```

---

## 2. Current implementation reviewed

Personal ARETE files reviewed for this module include:

- `schemas/arete/normalized-evidence.schema.json`
- `schemas/arete/correction.schema.json`
- `schemas/arete/presentation.schema.json`
- `apps/arete-web/src/presentation/types.ts`
- `scripts/arete/adapters.mjs`
- `scripts/arete/pipeline.mjs`
- `scripts/arete/runna.mjs`
- `scripts/arete/runna-presentation.mjs`
- `config/athlete-profile.json`
- `plans/2026-08-10.strength.json`
- current Xunji source records and ARETE tests

External reference implementations reviewed for concepts only:

- GoldenCheetah 3.8 planned-activity model and plan-adherence workflow;
- wger routine / set-configuration / workout-log separation;
- Coach Watts canonical ingestion and planning architecture.

Scientific reporting reference reviewed:

- Consensus on Exercise Reporting Template (CERT), PMID 27149962 and PMID 27707738.

No third-party source code is proposed for incorporation.

---

# 3. What the current model already does well

## 3.1 Plan and actual are not completely collapsed

Strength plans live separately from Xunji observations, and Runna prescriptions are conceptually separate from Strava observations.

**Verdict:** preserve and formalize.

## 3.2 Raw provider evidence is retained

ARETE does not rely only on flattened presentation objects. Source references remain available.

**Verdict:** preserve.

## 3.3 Ambiguity is sometimes allowed

Exercise comparison can return `eligibility_uncertain`, Runna parsing can return `partial` or `raw_only`, and overlapping approved strength plans fail closed.

**Verdict:** strong pattern; generalize it to the whole ontology.

## 3.4 Runna already distinguishes target from constraint

For example, an easy-run statement such as “no faster than 5:50/km” is represented as a ceiling rather than incorrectly treated as a target pace.

**Verdict:** make this a cross-domain prescription principle.

## 3.5 Corrections are append-oriented

User context does not silently destroy the original provider record.

**Verdict:** preserve, but correction targeting must be redesigned.

---

# 4. Critical findings

## F02-01 — There is not yet a true canonical domain schema

**Severity:** Critical  
**Current state:** `normalized-evidence.schema.json` is primarily a generic envelope. The operational “canonical session” is effectively defined by pipeline code plus presentation types.

This causes presentation needs to influence domain semantics.

Examples of provider names, interpretation fields, session outcome, reasoning, and UI-facing concepts appearing in the same structures show that canonical facts and presentation have not yet been cleanly separated.

**First-principles judgement:** incorrect boundary.

**Required direction:** introduce provider-neutral canonical schemas independent of UI contracts.

---

## F02-02 — Planned-session identity is date-derived

**Severity:** Critical  
**Current state:** strength plan normalization creates IDs such as:

```text
strength-plan:2026-08-14
```

A date is a scheduling property, not the identity of the intended training exposure.

This fails for:

- two planned sessions on the same date;
- a session rescheduled from Wednesday to Thursday;
- long-lived source references across plan revisions;
- plan comparisons before and after rescheduling.

**Required direction:** internal stable IDs independent of provider and date.

---

## F02-03 — Performed-session identity is provider-derived

**Severity:** Critical  
**Current state:** IDs such as `xunji:<id>` and `strava:<id>` are used as session identities.

A provider record is evidence of a real-world training event; it is not necessarily the event itself.

One real session can have multiple provider records, and one provider recording can cover only part of an intended session.

**Required direction:** separate `SourceObservation.external_id` from internal `PerformedSession.id`.

---

## F02-04 — Plan revision history is not a first-class domain concept

**Severity:** Critical

Git history preserves file changes, but Git history is not equivalent to a canonical plan-revision model.

The domain needs to distinguish:

- the stable identity of the planned session;
- when a revision was created;
- when it became effective;
- who or what created it;
- why it changed;
- the schedule and prescription that were valid at a particular time.

Without this, a future implementation could unintentionally evaluate a completed workout against a plan that was edited after the workout.

**Required principle:** historical evaluation must use the legitimate prescription state that applied to the athlete at execution time. Post-hoc edits must not silently improve apparent adherence.

---

## F02-05 — Plan/actual association is embedded rather than modeled

**Severity:** Critical

Current strength matching is essentially same-date plan lookup. Running matching is also built around date-based candidate selection.

The relationship itself has no durable first-class identity.

This cannot robustly represent:

- one plan → two recordings;
- two planned components → one recording;
- rescheduled execution;
- replacement training;
- manual confirmation;
- candidate vs confirmed matches;
- rejected matches;
- ambiguous matches;
- source-fragment reconciliation.

**Required direction:** introduce `SessionAssociation` as an explicit entity.

---

## F02-06 — Fact, association, assessment, and decision are conflated

**Severity:** Critical

Current session objects contain combinations of:

- factual session data;
- `completion`;
- `outcome`;
- comparison status;
- reasoning;
- decision action;
- source freshness.

These belong to different conceptual layers.

**Required direction:**

```text
PerformedSession = what happened
SessionAssociation = what it was connected to
SessionAssessment = how execution compares with intent
Decision = what should change next
```

---

## F02-07 — “Completed” currently overstates what is known

**Severity:** Critical

A saved/ended workout does not necessarily mean the prescribed workout was completed.

Examples:

- athlete stopped halfway because of pain;
- watch recording ended normally but intervals were abandoned;
- strength session was saved after only warm-up sets;
- workout was deliberately shortened under an autoregulation rule.

**Required terminology:** distinguish an ended/recorded training attempt from prescription fulfillment.

---

# 5. High-priority findings

## F02-08 — Strength session adherence is currently over-reduced to work-set count

**Severity:** High

Current logic substantially uses:

```text
completed_work_sets / planned_work_sets
```

for session outcome.

This can misclassify:

- wrong exercises with the same set count;
- an added accessory exercise compensating numerically for a skipped key exercise;
- planned 3×10 becoming 3×3;
- symptom-driven substitutions;
- intentionally reduced volume;
- partial completion of a key movement.

**Required direction:** adherence is multidimensional and domain-specific. Do not replace it with one universal scalar.

---

## F02-09 — Recurring commitments are not planned sessions

**Severity:** High

The athlete profile correctly states that running commitments are mesocycle expectations, not session-level prescriptions. However, parts of the base pipeline still use fixed weekday commitments as if they imply an upcoming planned run.

Examples of concepts that must remain separate:

```text
4 runs per week               = expectation / target frequency
quality run usually Wednesday = scheduling preference / commitment
Runna Rolling 400s Wednesday  = planned session
```

**Required direction:** introduce an explicit `TrainingCommitment` / `ScheduleExpectation` concept rather than manufacturing a session from a recurring rule.

---

## F02-10 — Prescription semantics are buried in free text

**Severity:** High

The current strength plan contains valuable information such as:

- target RPE ranges;
- repetition ranges;
- optional load progression;
- pain guardrails;
- conditional reductions;
- equipment requirements;
- rest instructions;
- technique cues;
- exercise alternatives;
- rationale.

Much of this exists only in `note` text.

CERT identifies dosage, tailoring, and compliance among the core categories needed to describe exercise interventions adequately. ARETE therefore needs structured support for actionable prescription semantics while retaining the original human-readable instruction.

**Required direction:** separate structured operational fields from raw instruction and rationale.

---

## F02-11 — Target, constraint, preference, cue, and rationale are different things

**Severity:** High

These should not be mixed into one note string.

Example:

```text
400 m at 4:45/km               = target
no faster than 5:50/km         = constraint
RPE 7–8                        = target range / guardrail depending context
use semi-neutral grip          = execution constraint / setup
keep scapula controlled        = technique cue
because shoulder is sensitive  = rationale / context
```

**Required direction:** model them separately.

---

## F02-12 — Strength load semantics are under-specified

**Severity:** High

A number such as `40 kg` is not self-explanatory.

It may mean:

- total external barbell load;
- one dumbbell;
- two dumbbells combined;
- added load on a bodyweight movement;
- machine stack display;
- single-side effective load;
- unknown unit/display value.

The current personal plan already needs prose to explain that some Xunji entries use a different load convention, and current machine records can have blank units.

**Required direction:** preserve `load_basis`, unit certainty, equipment identity, and source convention. Never silently convert an unknown display value into kilograms.

---

## F02-13 — Exercise identity and execution context are over-coupled

**Severity:** High

Current comparison identity includes exercise name/variant plus fields such as grip, ROM, stance, tempo, machine identity, and seat setting.

Those fields can matter for comparability, but not all should automatically redefine the canonical exercise itself.

**Required direction:** distinguish:

```text
ExerciseDefinition
ExerciseConfiguration / Setup
ExecutionObservation
ComparisonEligibility
```

Detailed strength exercise ontology should be finalized in the Strength Engine audit rather than prematurely frozen here.

---

## F02-14 — Set prescription and set execution need distinct structures

**Severity:** High

A planned set can be:

- a fixed value;
- a range;
- RPE/RIR constrained;
- load-autoregulated;
- duration based;
- per-side;
- warm-up;
- work;
- back-off;
- drop-set;
- isometric;
- optional.

A performed set is an observation of what happened.

The two should not share one ambiguous shape.

**Required direction:** define separate `SetPrescription` and `SetExecution` concepts under the strength domain.

---

## F02-15 — Time precision is too weak

**Severity:** High

A date-only plan, a fixed 07:00 workout, and a flexible “Saturday” session are not equivalent schedules.

Do not fabricate precision that the source does not contain.

**Required direction:** use a schedule/window object that preserves:

- timezone;
- source precision;
- date-only vs fixed time vs flexible window;
- historical schedule revisions.

Performed timestamps should be normalized consistently while retaining source/local timing provenance.

---

## F02-16 — Correction targeting is source-centric

**Severity:** High

Current corrections target `source_id` and optional movement ID.

Future corrections need to distinguish whether the athlete is correcting:

- a provider source record interpretation;
- a canonical session property;
- an association;
- an exercise configuration;
- a symptom observation;
- a session assessment context.

**Required direction:** typed correction targets with immutable raw evidence preserved.

---

## F02-17 — `revision = correction count` is not revision history

**Severity:** High

A correction count can be useful metadata but is not a version identifier.

Entity revision must reflect actual domain-version changes and remain independent from the number of user correction events.

---

## F02-18 — Current training block is synthesized from personal assumptions

**Severity:** High for generalization; Medium for current personal use

The current pipeline creates a `physique_specialization` block and fixed stimulus allocations from personal profile dates and hard-coded logic.

A block should eventually be a first-class planning entity with goals/priorities and provenance, not a presentation-time reconstruction.

**Required direction:** move this into a future Planning / Goal Hierarchy model. Do not hard-code this personal block into ARETE Core.

---

# 6. Scientific and coaching assessment

## 6.1 The ontology must preserve prescription variables without pretending every variable has equal causal importance

The current ACSM resistance-training position stand synthesizes a large evidence base showing that different prescription variables can matter differently depending on the adaptation of interest. ARETE therefore needs enough structure to represent the actual prescription, but it should not infer that every logged detail has equal physiological importance.

**Design consequence:** flexible optional fields, not a maximal mandatory form.

## 6.2 Tailoring must be representable

An advanced strength prescription often legitimately says:

```text
choose the load that keeps the set within 8–10 reps at RPE 7–8
```

rather than prescribing one mandatory load.

Likewise an endurance plan may prescribe a ceiling, range, effort descriptor, or structured intervals rather than one pace.

**Design consequence:** a prescription must support ranges and constraints, not only exact numbers.

## 6.3 Adherence must be relative to intent

A planned easy run and a planned interval session cannot be evaluated using the same dimensions. A hypertrophy accessory movement and a heavy strength exposure also have different relevant prescription variables.

**Design consequence:** `SessionAssessment` is domain-specific and should not collapse into one universal score.

## 6.4 Do not create retrospective adherence by editing history

If the athlete or coach changes a prescription after execution, the old prescription remains historically meaningful.

**Design consequence:** evaluation must reference a frozen historical revision, with later corrections or explanations layered on top.

---

# 7. Open-source lessons worth borrowing

## GoldenCheetah — borrow semantics, not code

Useful lessons:

- planned activity is distinct from actual activity;
- original planned date is worth retaining;
- plan adherence is a downstream comparison;
- import can associate actual activity with a planned activity.

GoldenCheetah is GPL-licensed; this audit uses these as design concepts only.

## wger — borrow prescription/log separation and flexible units, not code

Useful lessons:

- routine prescription is separate from workout logs;
- set types are explicit;
- repetition units are not always repetitions;
- load/weight units can vary;
- progression logic is attached to prescription configuration rather than inferred from UI display.

wger is AGPL-licensed; no code reuse is proposed.

## Coach Watts — borrow canonical-domain separation selectively

Useful lesson:

- provider data should be normalized before higher-level coaching logic consumes it.

ARETE should not automatically inherit Coach Watts’ specific workout scoring, readiness rules, or AI decisions.

---

# 8. Legal / privacy / provenance implications

The canonical model must not sever lineage from provider evidence.

Every canonical fact and important derived result should be able to identify which source observations it depends on so that:

- errors can be audited;
- user corrections can be applied without destroying raw evidence;
- provider-scoped deletion can propagate when required;
- a user can export meaningful personal history;
- public tests can remain synthetic.

Internal canonical IDs must not expose a requirement to retain provider records longer than permitted by provider terms.

No GPL/AGPL source code from reviewed projects should be copied into ARETE Core under the current permissive-license direction without a separate compatibility decision.

---

# 9. Audit verdict

## Overall judgement

**REFACTOR THE ONTOLOGY BEFORE EXPANDING FEATURES. DO NOT REWRITE THE PRODUCT.**

The personal ARETE already contains several correct instincts, but the current presentation-oriented session model is not robust enough to become the public ARETE Core.

### Keep

- raw evidence preservation;
- plan/actual conceptual distinction;
- explicit uncertainty;
- append-oriented corrections;
- conservative Runna parsing;
- comparison eligibility checks;
- fail-closed conflicts;
- strong test culture.

### Replace or formalize

- date/provider-derived entity IDs;
- embedded plan/actual pointers;
- correction-count “revision”;
- same-date matching as the primary association mechanism;
- work-set-count session adherence;
- provider-specific canonical session types;
- presentation objects serving as the domain model;
- free-text-only prescription semantics;
- recurring commitments treated as planned sessions.

---

# 10. Required design output from this audit

The corresponding design proposal must define at least:

```text
TrainingPlan
TrainingBlock / ScheduleExpectation boundary
PlannedSession
PlannedSessionRevision
PrescriptionComponent
PerformedSession
PerformedComponent
SessionAssociation
SessionAssessment
CorrectionEvent
SourceDependency / provenance lineage
```

It must also specify:

- stable internal identity;
- schedule precision;
- revision timing;
- anti-hindsight evaluation rules;
- many-to-many association semantics;
- partial / abandoned sessions;
- target vs constraint semantics;
- strength and endurance domain extension points;
- no-plan users;
- public-alpha edge cases.

---

# 11. Minimum synthetic cases required before implementation

1. one strength plan, one exact actual;
2. one strength plan rescheduled to another day;
3. two strength sessions on the same date;
4. one Runna workout and one actual run;
5. one Runna workout split across two recordings;
6. two planned workouts and one combined recording;
7. one unplanned workout;
8. one cancelled plan;
9. one replacement workout;
10. one zero-work-set abandoned session with symptoms;
11. one additional accessory movement not present in the plan;
12. one key movement omitted despite matching total set count;
13. one autoregulated strength prescription using rep/RPE ranges;
14. one endurance pace ceiling that must not be treated as a target;
15. one machine exercise with unknown unit/equipment identity;
16. one unilateral exercise with explicit load basis;
17. one post-hoc plan edit that must not change historical adherence;
18. one manual plan/actual association correction;
19. one provider record deleted while canonical lineage remains legally consistent;
20. one athlete with actual training data but no training plan.

---

## Final audit statement

The central ontology rule for ARETE should be:

> **Facts describe what existed or happened. Associations describe relationships. Assessments describe meaning. Decisions describe what to do next. None of those layers may silently rewrite another.**
