# Phase 0 Audit 14 — Testing, Scientific Regression, Observability, and Release Governance

> Status: audit complete; no production implementation changes made  
> Date: 2026-08-15

## 1. Purpose

Testing in ARETE must protect not only software correctness but also scientific behavior, privacy boundaries and historical reproducibility. A green build is insufficient if a rule change silently changes coaching decisions across athlete states.

## 2. Current strengths worth preserving

The personal ARETE already has a strong testing culture for an early-stage personal system.

Current repository tests include:

```text
core
pipeline
schema
integration
Runna parsing/overlay
running evidence
rule priority
service worker
worker/deployment/runtime publish
browser regression
```

The current CI also runs Node tests, ARETE contract/schema tests, TypeScript, ESLint, frontend tests, production/preview builds, Cloudflare dry-runs, production dependency audit, and browser offline/responsive regression.

This discipline is a major asset and should be expanded rather than replaced.

## 3. Critical findings

### 3.1 Software tests do not yet equal scientific regression tests

A rule can remain schema-valid and unit-tested while changing recommendations in an unintended way. ARETE needs standardized athlete scenarios whose expected decisions are reviewed whenever the evidence/ruleset changes.

### 3.2 Ruleset and decision replay must be first-class

A historical DecisionRecord should be reproducible from:

```text
canonical-state snapshot/reference
ruleset version
evidence/policy versions
goal/constraint snapshot
```

Later rule changes should not rewrite what the system decided historically.

### 3.3 Provider-contract tests need realistic versioned fixtures

Provider schemas, APIs and exports can change. Adapters need sanitized/synthetic representative fixtures, schema versions and explicit failure tests rather than only happy-path examples.

### 3.4 Invariants deserve dedicated tests

Some ARETE principles are more important than individual feature outputs and should be tested as invariants:

```text
plan/actual never overwrite each other
later plan revision cannot alter earlier assessment baseline
missing data cannot become observed zero
provider identity cannot define Core ontology
unconfirmed association cannot become adherence fact
AI cannot mutate plans directly
user A cannot read user B data
provider deletion removes/re-canonicalizes dependent data correctly
```

### 3.5 Security/privacy tests must join normal CI

Multi-user authorization, deletion, export and provider-token isolation cannot be a one-time manual checklist.

### 3.6 Observability must not become a privacy leak

Operational logging is necessary for sync failures, matching ambiguity and decision pipelines, but raw workout notes, symptom text, photos, tokens and provider payloads should not be copied into general logs/error tracking.

### 3.7 Alpha quality metrics should measure errors, not engagement

For the first users, optimize:

```text
ingestion success/failure
source completeness
association ambiguity/error
calculation error
rule/precedence error
attention false-positive/false-negative feedback
explanation clarity
privacy/security incidents
```

not screen time or notification counts.

### 3.8 Schema migration needs compatibility tests

Canonical models will evolve during alpha. Migrations need fixtures from prior schema versions, explicit transformations, rollback/recovery thinking and prevention of silent historical corruption.

### 3.9 Dependency security is necessary but license/provenance scanning is also required

The current `npm audit` is useful, but open-source release governance also needs dependency license inventory and third-party provenance checks. Security and licensing are different gates.

## 4. Audit verdict

**PRESERVE THE EXISTING TEST CULTURE AND EXPAND IT INTO A MULTI-LAYER ASSURANCE SYSTEM.**

ARETE's most distinctive required addition is scientific/decision regression testing.

## 5. Required test layers

```text
unit
schema
adapter/provider contract
canonicalization/reconciliation
integration
session association
domain-engine assessment
rule evaluation
precedence/conflict
scientific regression
privacy/deletion
multi-user authorization
security
AI validation boundary
UI/accessibility
data migration/replay
release/license/provenance
```

## 6. Scientific regression principle

When a ruleset changes, run a fixed scenario library and produce a diff such as:

```text
ruleset 1.4 → 1.5
42 scenarios unchanged
3 intentionally changed
0 unexpected changed
```

Every intentional decision change should reference the evidence/policy/ADR that justified it.

## 7. Scenario library

Must cover at least:

```text
strength-only novice/experienced
endurance-only
hybrid with different primary goals
minimal-data user
high-data user
missing/stale data
ambiguous matching
symptom escalation
recovery disagreement
maintenance goal
progression signal
no-change decision
provider disconnect/deletion
```

Use synthetic data only in the public repository.

## 8. Observability events

Useful minimized events include:

```text
SyncAttempt
SchemaValidationResult
CanonicalizationResult
AssociationResult
RuleEvaluationSummary
DecisionEvaluation
AttentionEpisodeChange
DeletionJobResult
```

Each can record IDs/status/timing/reason codes without raw sensitive content.

## 9. Release governance

A public release should require three independent approvals/gates:

```text
engineering
scientific
legal/privacy/security
```

One passing gate cannot compensate for another failure.

## 10. Required tests before first alpha participant

- cross-user authorization negative tests;
- data export/delete test;
- provider token separation;
- schema migration/replay test;
- DecisionRecord deterministic replay;
- core invariants;
- critical safety/precedence scenarios;
- AI plan-mutation bypass negative test;
- dependency security scan;
- third-party license/provenance inventory;
- no real personal data/secrets scan in public repo.
