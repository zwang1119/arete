# Design Proposal 01 — Data, Session Identity, Freshness, and Provider Boundaries

> **Status:** proposed architecture; session ontology refined by Design Proposal 02  
> **Date:** 2026-08-15  
> **Governing principle:** first principles, scientific defensibility, auditability, legal compliance, and graceful degradation  
> **Normative refinement:** for canonical training entities, revision semantics, and plan/actual association, `docs/design/02-canonical-training-ontology.md` takes precedence over Section 4 of this document.

## Amendment after Module 02 audit

The Module 02 audit refined two concepts originally introduced here:

1. `PrescriptionRevision` is replaced by the broader **`PlannedSessionRevision`**, because schedule changes and prescription changes both matter historically.
2. Association acceptance state and relationship meaning must be separate. `candidate / confirmed / ambiguous / rejected` describe whether a link is accepted; `fulfills / partially_fulfills / replaces / split_part / combined_fulfillment` describe what the link means. `unplanned` and `not_performed` are derived execution states, not association types.

If this document conflicts with Design Proposal 02 on those points, Design Proposal 02 governs.

---

## 1. Design objective

ARETE should treat providers as evidence sources, not as the ontology of the system. The core should remain stable if a user changes from Xunji to Hevy, Strava to Garmin, or adds/removes Apple Health.

Target flow:

```text
Provider
  → Provider Adapter
  → SourceObservation / SourcePlanRecord
  → Canonicalization
  → Session Association / Reconciliation
  → Canonical PlannedSession / PerformedSession
  → Domain Engine
  → Decision
  → Presentation
```

The system must preserve enough lineage to explain every important fact, to reverse corrections, and to delete provider-derived data when platform terms or user revocation require it.

---

## 2. Provider-neutral authority

Do not encode provider names in ARETE Core authority rules.

Use semantic roles such as:

```text
strength.prescription
strength.observation
endurance.prescription
endurance.observation
recovery.observation
body.observation
symptom.report
```

A user/profile/integration layer maps providers to roles.

Example personal configuration:

```text
strength.prescription  → approved strength plan
strength.observation   → Xunji
endurance.prescription → Runna calendar
endurance.observation  → Strava
```

Another user can map the same semantic roles to different providers without changing Core.

Authority should be field- and role-specific, not a global provider ranking.

---

## 3. Preserve source records; do not choose one global winner

One real-world workout may be represented by Apple Workout, Strava, Garmin, Xunji, or another service.

ARETE should preserve source observations and link them to one canonical performed session.

```text
SourceObservation: Apple
          ┐
SourceObservation: Strava ─→ PerformedSession
          │
SourceObservation: Garmin
          ┘
```

Different source records can be useful for different fields. The canonical value for a field should retain lineage:

```text
value
unit
source_observation_id
selection_method
quality
observed_at
```

Never delete a duplicate source record merely because it is not the selected canonical observation.

---

## 4. Session model — summary only

The detailed canonical ontology now lives in Design Proposal 02.

The required common entities are:

```text
PlannedSession
PlannedSessionRevision
PerformedSession
SessionAssociation
SessionAssessment
```

with source evidence and provenance around them.

Key rules:

- planned-session identity is stable across rescheduling;
- a revision is immutable historical evidence;
- a performed session is provider-neutral and may exist with zero work sets;
- plan/actual association is a first-class entity;
- facts, associations, assessments, and decisions remain separate;
- post-hoc edits do not silently replace the historical evaluation baseline.

See `docs/design/02-canonical-training-ontology.md` for normative details.

---

## 5. Deterministic-first matching

Matching should operate across the candidate set, not independently per activity.

Use evidence such as:

1. stable provider identifiers where available;
2. explicit user/provider links;
3. date/time overlap;
4. sport/domain;
5. planned vs actual duration/distance;
6. structured interval shape;
7. title/metadata as weak supporting evidence.

Do not rely on title text as an authoritative field.

A candidate match should not become authoritative if competing matches are materially plausible.

Ambiguity should be a valid state and can be resolved by the athlete.

Design Proposal 02 further separates association acceptance state from relationship type.

---

## 6. Freshness is multidimensional

Do not use one `fresh/stale` flag to represent all source health.

Separate at least:

```text
transport_status        success | failed | syncing | unavailable
last_attempt_at
last_success_at
coverage_start
coverage_end
observed_through
completeness            complete | partial | unknown
finality                provisional | likely_final | final
expected_event_state    expected | not_expected | unknown
relevance_to_decision   relevant | not_relevant
```

A successful API request can still return incomplete same-day training data.

The UI should distinguish:

- source last checked;
- source last successfully read;
- evidence observed through;
- presentation last calculated.

"Updated" must never ambiguously refer to all four.

---

## 7. Event-driven ingestion where the provider supports it

Prefer provider-supported push/webhook delivery for ongoing activity updates when available, with bounded recovery polling/backfill.

Do not poll frequently merely to simulate real-time behavior.

For providers without supported push mechanisms, use a clearly documented polling cadence and expose its limitations.

A refresh pipeline should update canonical state directly; it should not require a source-data Git commit followed by a full CI/deploy cycle in the public runtime architecture.

Git remains useful for code, scientific rules, ADRs, synthetic fixtures, and optionally personal archival snapshots.

---

## 8. Runna handling

Runna's user-provided iCalendar subscription is suitable as a plan-source adapter if use is permitted by applicable terms and the user intentionally supplies the feed.

Keep the conservative parsing behavior:

```text
full | partial | raw_only
```

Move Runna canonicalization before presentation. Presentation must not patch canonical truth.

When parsing cannot represent a workout faithfully, retain the raw instruction and mark unsupported semantics rather than guessing.

---

## 9. Apple Health handling

Apple Health / HealthKit should not be treated as a normal server-side web API.

If public ARETE later supports HealthKit, prefer a minimal iOS companion that:

1. requests only necessary HealthKit types;
2. reads data locally with user authorization;
3. normalizes or securely uploads only what the user has enabled;
4. retains provenance and source metadata;
5. supports revocation and deletion.

The web application must tolerate absence of Apple Health entirely.

---

## 10. Garmin handling

Do not make Garmin Connect APIs a requirement for the initial small public alpha.

Garmin's official developer programs have business/approval constraints and some commercial licensing considerations. A lower-friction early path is user-owned FIT/TCX/GPX import where appropriate, while preserving a future Garmin adapter boundary.

---

## 11. Strava handling and deletion lineage

Strava integration must be designed around current official API terms, OAuth consent, webhooks, rate limits, user revocation, deletion requirements, and retention restrictions.

Important architectural consequence:

Every canonical or derived object that depends on Strava API data should be able to identify that dependency so that provider-scoped deletion can remove both source data and derived personal data when required.

Do not design ARETE's permanent longitudinal archive on the assumption that unrestricted retention of Strava API data is allowed.

For longitudinal portability, support user-controlled standard-file imports and exports in addition to third-party APIs.

---

## 12. Safety-critical zero-performance sessions

Do not discard a session because completed work-set count is zero.

Represent independently:

```text
session_attempt
performance_data
symptom_data
modification/abandonment_reason
```

Example:

```text
planned lower-body session
→ athlete starts warm-up
→ sharp knee pain 5/10
→ session abandoned
```

This is a high-value training/safety event even with zero work sets.

---

## 13. Adapter boundary

Adapters should perform:

- parsing;
- unit normalization;
- provider identifier capture;
- lossless mapping where possible;
- explicit unknowns;
- provenance.

Adapters should not perform high-level coaching inference.

Examples that belong downstream rather than in the provider adapter:

- whether symptoms require training modification;
- whether performance improved;
- whether an easy run was physiologically appropriate;
- whether a session should be progressed.

---

## 14. Privacy and legal design requirements

For public alpha, assume training, wearable, symptom, body, and photo data are sensitive personal information.

Design for:

- explicit purpose and consent;
- data minimization;
- per-integration scopes;
- user access/export;
- correction;
- deletion;
- provider-scoped cascade deletion;
- retention policy;
- secure transport and storage;
- least privilege;
- audit logging without unnecessary sensitive payloads;
- no secrets in Git;
- synthetic data in public tests.

Provider terms must be reviewed at the version effective when an integration is released.

---

## 15. Recommended migration strategy

Do not rewrite the personal ARETE in one step.

### Step A — Introduce semantic source roles

Keep existing providers but stop adding new provider names to Core.

### Step B — Introduce source observation + canonical session IDs

Preserve current output while building the new model behind it.

### Step C — Introduce canonical planned-session revisions

Use stable `PlannedSession` identity and immutable `PlannedSessionRevision` history as defined in Design Proposal 02.

### Step D — Move Runna from presentation overlay into canonical plan ingestion

Keep parser behavior; change architectural position.

### Step E — Replace binary freshness with multidimensional source health

Make timestamps and completeness explicit.

### Step F — Introduce global session association

Support ambiguity, explicit relationship semantics, and manual correction.

### Step G — Separate assessment from canonical facts

Move adherence/outcome interpretation out of the session fact object.

### Step H — Separate runtime data plane from Git deployment pipeline

Keep Git as archive/governance where useful.

### Step I — Add provider-scoped deletion lineage before public external integrations

This is a release requirement, not optional cleanup.

---

## 16. Acceptance tests for this foundation

The design is not complete until synthetic tests cover at least:

1. one planned strength session + one Xunji record;
2. one Runna session + one Strava record;
3. one workout represented by two providers;
4. two runs on the same day with one plan;
5. two plans and one actual session;
6. rescheduled plan;
7. partial workout;
8. abandoned zero-work-set session with symptoms;
9. stale source but complete historical data;
10. successful sync but incomplete same-day data;
11. provider revocation with cascade deletion;
12. manual correction without destruction of raw evidence;
13. provider unavailable while another provider still supports the capability;
14. a post-execution plan edit that cannot retroactively change the historical baseline;
15. two planned sessions on the same calendar date with distinct stable IDs.

Detailed ontology tests are expanded in Design Proposal 02.

---

## 17. Current recommendation

**Refactor, do not rewrite.**

Preserve the current strengths: raw evidence, append-oriented corrections, conservative Runna parsing, schema validation, fail-closed presentation, provenance, and testing culture.

Replace the fragile boundaries: provider-specific Core authority, presentation overlays that repair canonical state, one-dimensional freshness, independent per-session matching, date/provider-derived canonical identity, embedded plan/actual relationships, presentation-owned domain semantics, and Git-as-runtime-data-plane assumptions.
