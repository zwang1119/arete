# Phase 0 Audit 01 — Data Ingestion, Source of Truth, Freshness, and Reconciliation

> **Status:** Audit complete; no implementation changes made  
> **Date:** 2026-08-15  
> **Scope audited:** current personal ARETE in `zwang1119/training-weekly-data`  
> **Governing document:** `ARETE.md` v0.2.0  
> **Decision posture:** first principles > legacy behavior > convenience

---

## 1. Module purpose

This module exists to answer four questions reliably before any coaching logic runs:

1. What source records exist?
2. What real-world fact does each record represent?
3. Which record is authoritative for a given fact or role?
4. How current, complete, and trustworthy is that evidence?

If this layer is wrong, every downstream conclusion can be wrong while still looking internally consistent.

The target flow should ultimately be:

```text
Provider
  → Adapter
  → Raw Source Record
  → Canonical Plan / Observation
  → Reconciliation / Association
  → Source Health + Provenance
  → Domain Engine
  → Decision
  → Presentation
```

---

## 2. Current implementation reviewed

Key personal-ARETE files reviewed:

- `scripts/arete/core.mjs`
- `scripts/arete/adapters.mjs`
- `scripts/arete/pipeline.mjs`
- `scripts/arete/runna.mjs`
- `scripts/arete/runna-presentation.mjs`
- `scripts/arete/build-presentation.mjs`
- `scripts/fetch-xunji.mjs`
- `scripts/fetch-xunji-range.mjs`
- `scripts/fetch-runna-calendar.mjs`
- `.github/workflows/fetch-training-daily.yml`
- `.github/workflows/fetch-strava.yml`
- `.github/workflows/arete-web.yml`
- `.github/workflows/arete-deploy.yml`
- `apps/arete-web/worker/index.mjs`

Reference implementations reviewed for this module:

- Coach Watts — centralized canonical domain/repository pattern and provider ingestion architecture.
- Endurain — provider integrations plus provider-independent database-backed activity storage and standard file imports.

No third-party source code is proposed for copying in this audit.

---

## 3. What the current implementation does well

### 3.1 Raw evidence is generally preserved

Xunji and Strava are stored as source documents before downstream interpretation. Runna stores normalized prescription data without storing the private calendar URL or raw ICS. This is a strong provenance direction.

**Verdict:** keep.

### 3.2 Adapters already exist

`adapters.mjs` separates provider parsing from presentation construction to a meaningful degree. This is much better than letting UI components interpret provider payloads directly.

**Verdict:** keep the pattern, strengthen the boundary.

### 3.3 Corrections are append-oriented rather than destructive

User corrections are persisted as separate correction events and later applied to interpretation/comparison. The original evidence is not silently rewritten.

**Verdict:** strong design; retain.

### 3.4 Schema validation and fail-closed presentation are already present

AJV validation, canonical presentation validation, private-cache headers, access checks, and rejection of raw private material are all good foundations.

**Verdict:** retain and later generalize for multi-user alpha.

### 3.5 Runna parser degrades conservatively

Runna parsing explicitly distinguishes `full`, `partial`, and `raw_only`. Unsupported semantics are retained rather than silently converted into false structured facts.

**Verdict:** very good principle; retain.

### 3.6 Duplicate running evidence retains provenance

The deduplication function preserves duplicate source references rather than simply deleting the losing record.

**Verdict:** keep the provenance behavior; replace the current heuristic when multi-provider ingestion is introduced.

### 3.7 Source manifests exist

Xunji and Runna record last attempt/success metadata. This is the correct direction for observable ingestion.

**Verdict:** retain, but expand the health model substantially.

---

# 4. Critical findings

## F-01 — Provider names are embedded in Core authority

**Severity:** CRITICAL architecture issue  
**Current behavior:** `core.mjs` hard-codes:

```text
strength_prescription → github_approved_plan
running_prescription  → runna
completed_strength    → xunji
completed_running     → strava
```

### Why this is wrong from first principles

A provider is not a domain truth. It is one possible source capable of filling a role.

The rule currently means the core implicitly assumes one specific athlete ecosystem. It prevents a future user from using Hevy for strength, Garmin for running, CSV for historical data, or a manually entered plan without modifying core logic.

### Required direction

Core should reason about provider-independent roles, for example:

```text
strength.prescription
strength.observation
endurance.prescription
endurance.observation
recovery.observation
body.observation
```

A user/integration policy should resolve which provider(s) may fill each role and with what precedence.

Example:

```yaml
roles:
  strength.observation:
    primary: xunji
    fallbacks: []
  endurance.observation:
    primary: strava
    fallbacks: [fit_upload]
  endurance.prescription:
    primary: runna_calendar
```

### Decision

**Refactor required before public Core extraction.**

Do not add further provider-specific authority mappings to `core.mjs`.

---

## F-02 — Runna is currently a presentation overlay rather than a first-class canonical source

**Severity:** CRITICAL architecture issue

The base pipeline builds running sessions with `planned_session: null` and contains legacy running commitments. `applyRunnaPresentationOverlay()` then re-reads Runna state and rewrites:

- run planned sessions;
- source health;
- freshness;
- Today;
- Week;
- Next;
- running progress visibility;
- Coach running state;
- hybrid context.

### Why this is dangerous

There are effectively two running pipelines:

```text
legacy/base running model
        +
post-hoc Runna overlay
```

This creates duplicate policy and makes it possible for base logic and overlay logic to disagree.

A presentation layer should format canonical state, not repair it.

### Required direction

Runna should enter before canonical state is built:

```text
Runna Adapter
    ↓
Canonical PlannedSession[]
    ↓
Plan ↔ Actual association
    ↓
Canonical athlete state
    ↓
Presentation
```

### Decision

**Refactor required.**

The existing Runna parser can largely be retained. The overlay architecture should not become part of ARETE Core.

---

## F-03 — Source health collapses several different concepts into one status

**Severity:** CRITICAL correctness issue

`assessSourceHealth()` currently derives one status such as `fresh`, `stale`, or `failed` primarily from last successful sync age.

But these are different questions:

1. Did the transport/API request succeed?
2. How long ago was the source fetched?
3. What time/date range is actually covered?
4. Is expected data present?
5. Is the source complete or still mutable?
6. Is the evidence recent enough for the current decision?

A successful API request can be fresh while still missing the just-completed workout.

### Example failure mode

If Xunji was fetched recently but today's completed session has not yet appeared, the current source can still be labelled `fresh` because sync age is under the stale threshold.

### Required target model

At minimum, track separate fields:

```text
transport_status
last_attempt_at
last_success_at
observed_through / coverage_end
coverage_start
completeness
finality
expected_event_state
latency
relevance_to_current_decision
```

Possible states should distinguish:

```text
transport_ok_but_expected_data_missing
coverage_partial
source_stale_for_decision
no_data_expected
sync_failed
syncing
complete
```

### Decision

**Replace the single-dimensional source-health model.**

Do not simply add more `if` branches to the existing status enum.

---

## F-04 — Runtime data updates are coupled to Git commits and full CI/deployment

**Severity:** CRITICAL for public architecture; HIGH for personal maintainability

Current personal flow can be:

```text
fetch external data
→ write JSON into repository
→ git commit/push
→ trigger full ARETE contract/web CI
→ after CI succeeds, trigger deploy
→ regenerate and publish presentation
```

### Why this was reasonable initially

For one private athlete it creates a transparent, versioned history and makes debugging easy.

### Why it should not become the runtime architecture

Code deployment and athlete-data ingestion have different lifecycles.

Runtime evidence should not require rebuilding/testing the application bundle. A public alpha would also make per-user Git commits impractical and inappropriate.

### Required direction

Separate:

**Code/config plane**
```text
GitHub
```

from:

**Runtime data plane**
```text
provider ingestion → database/object store → canonical evaluation → presentation cache
```

For the personal ARETE, GitHub may remain a useful append/archive/export target if desired, but it should not be the only runtime state mechanism long-term.

### Decision

**Do not reproduce the Git-backed runtime data plane in public ARETE.**

Personal ARETE can migrate gradually rather than through a rewrite.

---

# 5. High-priority findings

## F-05 — Current refresh semantics do not implement a 30-minute freshness guarantee

**Severity:** HIGH

Current scheduled behavior includes:

- Xunji daily fetch: hourly evening window around 18:15–23:15 Auckland time, Monday–Saturday;
- Strava fetch: hourly evening window around 18:20–23:20 Auckland time;
- private ARETE scheduled deployment: 12:00 and 21:00 Auckland time;
- production deploy performs a live Xunji refresh and Runna refresh, but not a live Strava refresh;
- repository data changes trigger full web verification before the deploy workflow publishes them.

Therefore a UI statement or product expectation equivalent to "updates every 30 minutes" is not supported by the current architecture.

### Specific consequence

A morning/afternoon run can remain absent until the Strava evening sync window even though the ARETE site itself is online.

### Required direction

Define an explicit service contract instead of a vague refresh promise:

```text
Source ingestion cadence
Canonical recomputation trigger
Presentation publication latency
On-demand refresh behavior
Maximum acceptable stale period by capability
```

For the personal version, freshness should eventually be event-driven/on-demand where provider terms allow, with scheduled polling as recovery/fallback rather than presentation deployment as the synchronization mechanism.

### Decision

**Product contract and implementation are currently inconsistent. Must be corrected.**

---

## F-06 — Plan ↔ Actual matching is not globally one-to-one

**Severity:** HIGH

`matchPrescription(session, prescriptions)` evaluates each completed running session independently.

If there is one Runna prescription and two actual runs on the same day, both actual runs can independently match the same prescription with high confidence.

### Why this matters

A plan/actual association is an entity relationship, not a label attached independently to every session.

### Required direction

Association should operate over the day's/week's candidate sets and enforce cardinality explicitly:

```text
PlannedSession 0..1 ←→ 0..1 PerformedSession
```

unless a prescription explicitly represents multiple activities.

Matching inputs can include:

- stable provider workout IDs;
- date/time;
- modality;
- distance/duration;
- structured-step similarity;
- user confirmation.

Every automatic association should store confidence and reason codes and be manually reversible.

### Decision

**Replace per-session independent matching with explicit association logic.**

---

## F-07 — Runna source health can report completeness from unrelated prescriptions

**Severity:** HIGH

The overlay treats `prescriptions.length > 0` as observed data and `complete`, even when the cached prescriptions may not cover the decision date of interest.

### Required direction

Use manifest coverage:

```text
earliest_date
latest_date
relevant_date_present
```

A source can be healthy but irrelevant to today's decision.

### Decision

**Coverage-aware health required.**

---

## F-08 — Zero-completed-set sessions can disappear, including potentially meaningful symptom evidence

**Severity:** HIGH safety/data-integrity concern

During Xunji normalization:

```text
if completedSetCount === 0 → skip session
```

Symptom extraction happens in the same normalization path.

A session that was opened and then abandoned because of pain, or a note-only event documenting an important symptom, can therefore disappear from canonical evidence if no set was completed.

### Required direction

Separate:

```text
session existence
performance evidence
symptom/context evidence
```

A zero-work-set session may still be important evidence.

### Decision

**Change required before symptom logic is trusted for safety-sensitive decisions.**

---

## F-09 — Provider parsing and interpretation are partially mixed

**Severity:** HIGH

`adapters.mjs` performs source normalization but also:

- heuristic symptom interpretation;
- exercise-identity inference;
- equipment inference;
- guardrail-vs-real-symptom interpretation.

### Risk

A heuristic inference can become indistinguishable from an observed source fact.

### Required direction

Adapters should preserve raw source semantics and may produce clearly labelled candidate interpretations:

```text
raw_note: observed
candidate_symptom: inferred
inference_method: ...
confidence: ...
```

Promotion to authoritative canonical observation should happen in a validation/interpretation layer.

### Decision

**Boundary tightening required.**

The heuristics themselves will be audited later in the Exercise Identity and Symptom modules.

---

## F-10 — Hand-written ICS parsing should be challenged before public reuse

**Severity:** HIGH maintainability/edge-case risk

The current parser intentionally handles a constrained subset of ICS and is conservative when workout text cannot be fully parsed. That is good.

However, calendar semantics can include:

- timezones;
- recurrence;
- folded lines;
- escaped content;
- event updates;
- sequence/revisions;
- cancelled events;
- DTSTART parameter semantics.

### First-principles question

Is maintaining our own general calendar parser a differentiating ARETE problem?

Probably not.

### Required direction

For the future Core/public adapter, evaluate a mature library or standards implementation (**Reuse Level 3**) after exact license/security/maintenance review, while retaining ARETE-specific Runna workout-text interpretation ourselves.

### Decision

**Do not expand the home-grown ICS parser into a general calendar subsystem without comparison against mature libraries.**

---

# 6. Medium-priority findings

## F-11 — `generated_at` and "last updated" semantics are ambiguous

`today.hero.last_updated` is presentation generation time, not necessarily:

- last source observation;
- last successful import;
- last canonical calculation;
- last decision evaluation.

The Worker also exposes generated time as a response header.

### Required direction

Use distinct timestamps:

```text
source_observed_at
source_fetched_at
canonical_evaluated_at
presentation_generated_at
published_at
```

UI labels must say which one they display.

### Decision

**Rename/clarify before broader use.**

---

## F-12 — Running deduplication is useful but too source-specific for future Core

Current duplicate matching uses near-identical:

- start time;
- distance;
- duration.

Winner selection favors title/run classification/segment richness.

This is acceptable for a narrow personal context but not enough for multi-provider reconciliation.

Future reconciliation should consider provider identity, native IDs, device/source lineage, timestamps, duration, distance, and raw-data quality.

### Decision

**Keep current behavior temporarily; replace when second actual-running provider is introduced.**

---

# 7. Open-source comparison

## Coach Watts — lessons worth borrowing

Strong ideas relevant to this module:

- provider ingestion is separated from canonical workouts/metrics;
- normalized entities are stored centrally;
- a repository layer centralizes duplicate handling and ownership checks;
- background jobs handle ingestion separately from UI rendering;
- integration sync status is explicit;
- provider-specific push/pull policy can be bounded independently of domain logic.

**ARETE conclusion:** borrow the separation of provider ingestion, canonical storage, repository/access rules, and sync state. Do not inherit Coach Watts' coaching thresholds without scientific audit.

## Endurain — lessons worth borrowing

Strong ideas relevant to this module:

- provider-independent database-backed activity model;
- Strava/Garmin integrations are ingestion routes rather than the product ontology;
- standard file imports (`FIT`, `TCX`, `GPX`) provide portability and fallback.

**ARETE conclusion:** public Core should support adapter contracts and portable import paths so provider APIs are optional capabilities rather than existential dependencies.

---

# 8. Scientific/coaching review

This module should contain almost no prescriptive sports-science logic.

Its scientific responsibility is **measurement integrity**:

- do not confuse observed with inferred;
- do not compare non-comparable exposures;
- do not treat missing evidence as negative evidence;
- do not treat sync success as evidence completeness;
- retain enough provenance to understand measurement limitations.

Any rule such as HR thresholds, hypertrophy inference, injury logic, or training-load interpretation belongs downstream and will be audited separately.

---

# 9. Target architecture decision

The current personal ARETE should evolve toward:

```text
                    ┌──────────────────┐
                    │ Provider Adapter │
                    └────────┬─────────┘
                             ▼
                    Raw Source Record
                             │
                  provenance preserved
                             ▼
              Canonical Plan / Observation
                             │
             ┌───────────────┴───────────────┐
             ▼                               ▼
       Reconciliation                 Source Health
   (identity / dedup /              (transport, coverage,
    plan↔actual link)                completeness, latency)
             └───────────────┬───────────────┘
                             ▼
                     Canonical State
                             ▼
                       Domain Engine
                             ▼
                    Decision / Audit
                             ▼
                       Presentation
```

No presentation overlay should redefine canonical truth.

---

# 10. Immediate guardrails — before implementation begins

Until this module is redesigned:

1. **Do not add more provider names to Core authority maps.**
2. **Do not add new Runna-specific policy to the presentation overlay.**
3. **Do not advertise a 30-minute freshness guarantee.**
4. **Do not interpret `fresh` as `complete`.**
5. **Do not rely on zero-work-set filtering for symptom safety.**
6. **Do not add a second running provider without first designing canonical reconciliation.**
7. **Do not use Git-backed runtime state as the architecture for the public alpha.**
8. Preserve raw evidence and corrections during any migration.

---

# 11. Module verdict

| Dimension | Verdict |
|---|---|
| Provenance | **Strong foundation** |
| Raw-data preservation | **Good** |
| Adapter concept | **Good but boundary is porous** |
| Canonical provider neutrality | **Poor / must change** |
| Plan↔Actual association | **Promising but structurally incomplete** |
| Freshness semantics | **Insufficient** |
| Runtime data architecture | **Suitable for personal prototype, not public Core** |
| Failure handling | **Generally conservative** |
| Portability to other users/providers | **Low in current form** |
| Auditability | **Above average** |

### Overall conclusion

**Do not rewrite the module from scratch.** There is substantial good work worth preserving: raw evidence, corrections, schemas, fail-closed presentation, manifests, conservative Runna parsing, and provenance.

However, the current module is a transitional architecture. Before it becomes ARETE Core, four structural changes are mandatory:

1. provider-neutral authority;
2. Runna moved into canonical ingestion before presentation;
3. multidimensional source-health/coverage semantics;
4. separation of runtime athlete data from code/deployment lifecycle.

These are foundational changes, not optional cleanup.

---

# 12. Next audit dependency

The next module should be **Canonical Data Model + Plan/Actual Association**, because F-01, F-02, F-06, and F-12 cannot be resolved correctly without defining canonical entities and association cardinality first.
