# Phase 0 Audit 13 — Public Alpha, Privacy, Security, and Compliance

> Status: audit complete; no production implementation changes made  
> Date: 2026-08-15  
> Scope: initial 1–3 external-user alpha and the architecture needed not to create avoidable release risk

## 1. Purpose

The public alpha is not a scientific efficacy trial and not a scaled consumer launch. Its purpose is to determine whether one provider-neutral ARETE Core can serve a small number of users with different capabilities without leaking personal assumptions, corrupting data, or creating unjustified training decisions.

Because ARETE may hold training, body, symptom, recovery and wearable data, “only friends are testing it” is not a reason to relax privacy or security fundamentals.

## 2. Current strategic strengths

The project already has several protective decisions:

- public ARETE is separate from the private personal-data repository;
- provider-specific capabilities are intended to be optional;
- sensitive integrations such as Apple Health are not alpha prerequisites;
- provenance and deletion lineage have already been identified as Core concerns;
- no production personal data should enter the public repository;
- the initial product is positioned as training/general-wellness software, not diagnostic or therapeutic software;
- the alpha is intentionally small.

These should remain hard constraints.

## 3. Critical findings

### 3.1 A public repository is not a public-user runtime architecture

The current personal model of storing operational data in Git and rebuilding/deploying presentation artifacts is unsuitable as the long-term multi-user runtime. The public alpha needs user-scoped application storage, authentication, authorization, deletion and audit behavior.

GitHub remains appropriate for:

```text
code
schemas
rules
evidence reviews
ADRs
synthetic fixtures
documentation
```

It should not be the normal datastore for alpha participants' sensitive records.

### 3.2 Multi-user isolation is a release blocker

Every runtime data object must have an owner/tenant relationship and every read/write path must enforce it. A user must never be able to obtain another user's sessions, body measurements, symptoms, provider tokens, decisions or AI context through an ID guess, cache mistake, export bug or background job.

**Verdict: authorization/isolation tests are mandatory before the first external participant.**

### 3.3 Privacy by design is required from the first participant

For a New Zealand-operated alpha, the Privacy Act 2020 is directly relevant to how personal information is collected, stored, used and shared. Since 1 May 2026, IPP3A also imposes notification duties for relevant indirect collection. Whether the Health Information Privacy Code applies depends on whether the operator/function meets the definition of a health agency; this must be assessed rather than assumed.

The architecture therefore needs, from the beginning:

```text
purpose specification
collection notice
per-integration disclosure
access/export
correction
deletion
retention
security
cross-border/vendor review
```

### 3.4 Provider terms are part of system design

A technically valid API integration can still be legally/contractually unsuitable.

Strava's API Policy effective 1 June 2026 imposes unusually restrictive storage/cache and deletion requirements. ARETE must not assume that Strava API data can become an unrestricted permanent longitudinal archive. Provider-derived objects need dependency lineage so provider/user revocation can remove data and dependent personal data as required by the then-current agreement/policy.

**Verdict: Strava integration requires a provider-specific compliance design before public alpha use.**

User-controlled file import/export (for example FIT/TCX/GPX where the user has lawful rights to the file) remains strategically important for portability and for reducing dependence on provider API terms.

### 3.5 Apple Health is not required for alpha

HealthKit is an Apple-platform framework with fine-grained per-type permission and privacy restrictions. If ARETE later supports Apple Health, the preferred architecture remains a minimal iOS companion that requests only needed types and respects revocation/limited access.

Do not block the alpha on this integration.

### 3.6 Health/fitness data should never become an advertising asset

Apple explicitly restricts advertising/marketing/data-mining use of HealthKit-derived data. Independently of Apple, ARETE's product values and trust model strongly favor a broader rule: athlete health/training data should not be sold or repurposed for targeted advertising.

**Verdict: recommended project-level prohibition.**

### 3.7 Clinical claims can change regulatory obligations

The initial alpha should remain a general-wellness/training tool. FDA's 2026 general-wellness guidance is one example of why intended use and disease-related claims matter to software-device status. New Zealand and any other target jurisdictions require their own assessment.

Avoid claims that ARETE diagnoses, treats, prevents or rules out injury/disease.

### 3.8 Physique photos are high-cost data for an early alpha

Photos increase privacy, storage, deletion, access-control and AI-processing risk. They are not necessary to validate the Core architecture.

**Verdict: do not make physique-photo upload a default alpha feature. Add only when a specific test objective justifies it and privacy controls are ready.**

### 3.9 External users are alpha testers, not evidence of efficacy

One to three users can reveal:

```text
hidden assumptions
schema failures
matching failures
missing-data behavior
UX confusion
provider integration problems
false/missing attention
```

They cannot establish that ARETE improves performance, prevents injury, or provides clinically valid coaching.

### 3.10 Alpha success metrics should be correctness-oriented

Early success should emphasize:

```text
data integrity
matching integrity
calculation integrity
rule integrity
explainability
false attention
missing attention
privacy/security incidents
```

not screen time, DAU, engagement or notification volume.

## 4. Legal/compliance review summary

### New Zealand privacy

The Privacy Act 2020 principles cover collection purpose, notice, security, access, correction, retention, use/disclosure and overseas disclosure. IPP3A has applied since 1 May 2026 to relevant indirect collection. If a hosted model, analytics provider or other processor receives personal information overseas, the cross-border-disclosure position must be reviewed rather than assumed safe.

### Health Information Privacy Code

The HIPC applies to identifiable health information handled by health agencies and was amended in 2026 to incorporate indirect-collection rules. ARETE should obtain release-specific advice on whether its operator/function falls within scope instead of self-classifying based merely on the sensitivity of the data.

### Strava

Current API terms require explicit attention to permitted use, caching/retention, revocation/deletion, display restrictions and non-replication/competition conditions. This is a provider-integration release gate.

### Apple Health

HealthKit requires fine-grained user authorization and strong privacy disclosures. HealthKit-derived data cannot be used for advertising or sold to data brokers; third-party disclosure is tightly constrained.

### General-wellness boundary

Initial product language and functionality should remain focused on exercise/training/wellness and avoid disease diagnosis/treatment/prevention claims. A disclaimer does not override actual intended use or functionality.

This document is engineering governance, not jurisdiction-specific legal advice.

## 5. Security minimums before external users

Required baseline:

```text
secure authentication
server-side authorization for every user-owned object
strong tenant isolation
TLS/secure transport
secure secret/token storage
least-privilege provider scopes
CSRF/XSS/injection protections appropriate to stack
input/schema validation
rate limiting where appropriate
webhook signature/verification where available
safe background-job ownership context
sensitive-log minimization
dependency/vulnerability scanning
backups/restore plan for stored authoritative data
account/export/delete pathways
provider token revocation handling
```

Encryption-at-rest choices should be appropriate to the selected datastore/cloud platform and threat model; do not invent custom cryptography.

## 6. Recommended alpha scope

Deliberately small:

```text
Participant A: strength-only
Participant B: endurance-only
Participant C: simple hybrid (optional)
```

Prefer low-friction, lawful ingestion methods first:

```text
manual/CSV strength import where needed
FIT/TCX/GPX endurance import
one or two reviewed provider integrations only when necessary
```

Do not add Garmin API, Apple Health, photos, nutrition, social/community or broad wearable integrations merely to impress alpha users.

## 7. Error taxonomy

Every alpha issue should be classified:

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
```

This makes early failures useful evidence rather than an unstructured bug list.

## 8. Audit verdict

**PROCEED WITH A 1–3 USER PRIVATE ALPHA ONLY AFTER MINIMUM MULTI-USER, PRIVACY, DELETION AND SECURITY GATES EXIST.**

The alpha should validate architectural generalization and product usefulness, not scientific efficacy.

## 9. Release blockers for first external participant

- unresolved cross-user authorization path;
- real personal data stored in the public repository;
- secrets committed to Git;
- no privacy/collection notice;
- no delete/export mechanism for data ARETE controls;
- provider API use without current terms review;
- no provider-scoped deletion lineage where required;
- diagnostic/treatment claims not reviewed;
- unreviewed sensitive third-party AI/data processor use;
- ability for AI/UI to bypass canonical decision authority.
