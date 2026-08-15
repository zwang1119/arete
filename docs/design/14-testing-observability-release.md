# Design Proposal 14 — Testing, Scientific Regression, Observability, and Release Governance

> Status: proposed architecture; no production implementation yet  
> Date: 2026-08-15

## 1. Objective

Make ARETE changes reviewable at four levels simultaneously:

```text
software behavior
scientific behavior
privacy/security behavior
historical reproducibility
```

## 2. Test-domain entities

```text
AthleteScenario
ProviderContractFixture
ExpectedInvariant
ScientificRegressionCase
DecisionSnapshot
RulesetReleaseDiff
```

These are conceptual test artifacts and do not need to become production tables.

## 3. Layered test architecture

### Layer 1 — Pure units

Examples:

```text
unit conversions
e1RM implementations
zone calculations
rolling summaries
schedule utilities
```

### Layer 2 — Schemas and contracts

Validate canonical/provider/domain objects and explicit unknown/null behavior.

### Layer 3 — Provider adapters

For each provider/file format:

```text
representative fixture
edge fixture
schema/version metadata
missing fields
changed fields
invalid payload
privacy-stripped payload
```

Adapters fail explicitly rather than silently fabricating defaults.

### Layer 4 — Canonicalization and reconciliation

Test:

```text
multiple sources → one performed session
field-level lineage
conflicting sources
provider deletion/re-canonicalization
measurement-regime changes
```

### Layer 5 — Plan/Actual association

Test 1:1, 1:n, n:1, rescheduled, replacement, ambiguous and no-plan cases.

### Layer 6 — Domain engines

Strength, Endurance, Recovery, Symptoms, Hybrid and Progress each receive dedicated scientific fixtures and expected assessments.

### Layer 7 — Rule/precedence tests

Given exact canonical state + goals + ruleset:

```text
expected applicable rules
expected suppressed rules
expected selected action
expected reason codes
```

### Layer 8 — Scientific regression

A curated scenario library is rerun for every material ruleset/evidence-policy change.

### Layer 9 — Privacy/security

Include:

```text
cross-user read/write denial
IDOR-style negative tests
provider-token isolation
export/delete
cascade deletion
least-privilege scope behavior
log redaction
webhook authenticity
```

### Layer 10 — UI/accessibility

Test critical attention, uncertainty, missing-data states, capability-based navigation, keyboard/screen-reader labels, responsive/offline behavior where relevant.

## 4. Invariant suite

Critical invariant examples:

```text
INV-001 Plan never overwritten by actual
INV-002 Actual never reconstructed from plan when missing
INV-003 Historical assessment uses pre-execution valid plan revision
INV-004 Unknown is never silently converted to zero/normal
INV-005 Provider names do not determine Core semantics
INV-006 Unconfirmed match cannot produce adherence claim
INV-007 Progress claim requires its evidence gate
INV-008 Safety precedence beats progression when materially relevant
INV-009 AI cannot directly mutate plan
INV-010 User cannot access another user's objects
INV-011 Provider deletion lineage is executable
INV-012 No decision changes without a ruleset/evidence/policy version change
```

Add new invariants whenever a serious bug reveals a class of failure.

## 5. Scientific scenario format

Conceptual example:

```yaml
scenario_id: HYBRID-KEY-RUN-001
athlete:
  primary_goal: 5k_performance
  maintenance_goal: lower_body_strength
state:
  tomorrow: key_intervals
  today: optional_lower_body_volume
  symptom: none
expected:
  decision: reduce_or_remove_optional_lower_body_volume
  protected_goal: 5k_performance
  prohibited:
    - cancel_key_run
```

The exact expected decision must itself be evidence/policy-reviewed; tests should not fossilize a bad rule merely because it is old.

## 6. Ruleset release process

A material rule change creates a candidate ruleset version.

CI produces:

```text
old ruleset
new ruleset
scenario decision diff
reason-code diff
attention diff
plan-mutation eligibility diff
```

Categorize every change:

```text
expected_unchanged
intentional_change
unexpected_change
```

Release requires zero unresolved unexpected changes.

## 7. Decision replay

DecisionRecord should retain references sufficient to reconstruct:

```text
canonical state as-of
ruleset version
goal/constraint snapshot
policy/evidence versions
```

Replay is for reproducibility/debugging. Historical records remain historical even if today's newer rules would decide differently.

## 8. Migration testing

Every canonical-schema migration should have:

```text
old fixture
migration
new schema validation
semantic invariant checks
id/provenance preservation
```

Never run destructive migration logic on the only copy of authoritative data without backup/recovery strategy.

## 9. Observability design

Operational events are structured and minimized.

Example:

```text
SyncAttempt
  user_pseudonymous_id
  provider
  status
  latency
  error_class
  no sensitive payload

AssociationResult
  candidate_count
  status
  confidence_class
  reason_codes
```

Logs should use pseudonymous/internal identifiers where useful and avoid raw notes, route geometry, photos, tokens and symptom narratives by default.

## 10. Alpha quality dashboard

Track correctness/reliability rather than engagement:

```text
sync success rate
incomplete-source events
association ambiguity rate
manual correction rate
schema failures
rule conflicts
unexpected scientific-regression changes
attention feedback
export/delete success
security/privacy incidents
```

User feedback can tag attention/recommendations as:

```text
useful
not_relevant
wrong_fact
wrong_interpretation
wrong_action
unclear
```

These labels are product-quality evidence, not scientific ground truth.

## 11. Error taxonomy

Standardize:

```text
DATA
MATCHING
DOMAIN
SCIENCE
RULE
PRECEDENCE
AI
UX
INTEGRATION
PRIVACY
SECURITY
MIGRATION
```

Every serious incident should create a regression fixture or invariant when feasible.

## 12. Security and supply-chain CI

The eventual public repository should automate, as appropriate:

```text
dependency vulnerability scanning
secret scanning
static analysis/lint/typecheck
license inventory
third-party notice/provenance checks
build reproducibility checks
```

Do not assume one scanner establishes security or license compliance.

## 13. Release gates

### Engineering gate

```text
all required tests pass
no unresolved migration/data integrity issue
operational rollback/recovery known
```

### Scientific gate

```text
scientific regression reviewed
rules/evidence versions documented
no unsupported claim introduced
```

### Privacy/security/legal gate

```text
provider terms reviewed
privacy/data flows reviewed
security blockers closed
license/provenance reviewed
```

All three are required.

## 14. Acceptance criteria

ARETE should be able to answer after any release:

> What changed in code, what changed in scientific behavior, which athlete scenarios changed decision, why that change was intentional, and whether privacy/security/provider obligations were rechecked?

If this cannot be reconstructed, the release process is not yet mature enough for wider rollout.
