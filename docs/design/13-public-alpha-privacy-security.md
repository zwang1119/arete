# Design Proposal 13 — Public Alpha, Privacy, Security, and Compliance

> Status: proposed architecture; no production implementation yet  
> Date: 2026-08-15  
> Scope: first 1–3 external users

## 1. Objective

Create the smallest public-alpha architecture that can safely support multiple users while preserving ARETE Core's scientific and auditability guarantees.

The alpha is intentionally not a scale test. It is a correctness/generalization test.

## 2. Core privacy/security entities

```text
User
AlphaParticipantProfile
ProviderConnection
ConsentRecord
CollectionNoticeVersion
DataGrant
RetentionPolicy
DataSubjectRequest
DeletionJob
DeletionReceipt
SecurityAuditEvent
ProcessorRegistry
```

These are governance concepts; exact database tables can be decided later.

## 3. User and tenant boundary

Every user-owned runtime entity must be scoped to an internal `user_id`/tenant identity.

Examples:

```text
PlannedSession
PerformedSession
SourceObservation
BodyMeasurement
SymptomObservation
DecisionRecord
ProviderConnection
AITrace
```

The server/data-access layer enforces ownership. The UI must never be the security boundary.

Background jobs and webhook handlers must also carry the correct tenant context.

## 4. Authentication and authorization

For alpha:

- use a mature authentication solution rather than custom password cryptography;
- enforce authorization on every server-side read/write;
- use short-lived/session mechanisms and secure cookies/tokens appropriate to the chosen stack;
- provide account revocation/logout;
- do not share one personal ARETE credential among testers.

Administrator/support access, if any, must be explicit and logged.

## 5. Collection and consent model

Each capability/integration declares:

```text
what data are collected
why they are collected
source of data
how they are used
whether they are required or optional
retention/deletion behavior
third-party processors involved
```

Store which notice/consent version the user saw.

For data collected indirectly from providers, design notification around applicable New Zealand IPP3A requirements and any provider-specific obligations.

Do not use broad “consent to everything” as the product architecture.

## 6. Data minimization

The default rule:

> If a field is not needed for an enabled capability or a justified audit/legal purpose, do not collect it.

Examples:

- route geometry is unnecessary for many training decisions and can be omitted;
- photographs are not needed in the first alpha;
- full raw provider payloads should not be retained merely because storage is cheap, especially when provider terms restrict retention;
- AI receives only task-relevant context.

## 7. ProviderConnection

```text
id
user_id
provider
scopes_granted
created_at
last_used_at
status
terms_review_version
retention_policy_ref
```

Secrets/tokens are stored in a dedicated secret/encrypted credential mechanism appropriate to the platform, never in the public Git repository.

## 8. Provider-scoped dependency lineage

Every provider-derived canonical/derived object should be able to answer:

```text
which SourceObservation(s) contributed to this value/claim?
```

This supports:

- correction;
- re-canonicalization;
- provider deletion/revocation;
- audit;
- legal/provider-term compliance.

A deletion job can traverse:

```text
ProviderConnection
→ SourceObservations
→ provider-dependent canonical fields
→ derived metrics/claims/AI traces where required
```

If a canonical entity also has independent lawful evidence from another source, re-canonicalize rather than necessarily deleting the real-world entity.

## 9. Data subject controls

Minimum alpha user controls:

```text
view connected sources
export data ARETE is permitted to export
correct relevant user-entered/context data
disconnect provider
request/delete account and ARETE-controlled personal data
```

Record deletion outcome/limitations in a `DeletionReceipt` without retaining deleted sensitive content.

## 10. Retention

Do not create one universal retention period.

Retention policy is data-class/source-specific and documents:

```text
purpose
retention period or condition
provider restrictions
user expectations
legal requirements
deletion trigger
```

Provider terms can be stricter than ARETE's desired product retention.

## 11. Cross-border processors

Maintain a ProcessorRegistry for services that may receive personal information:

```text
hosting/database
email/auth
AI model provider
analytics/error tracking
backups
```

For each:

```text
purpose
data categories
region/transfer mechanism
contract/privacy review date
retention
delete capability
```

New Zealand Privacy Act cross-border obligations must be checked for the actual architecture.

## 12. Analytics and logs

Prefer product/operational analytics that do not require health/training content.

Good examples:

```text
page/error event
sync duration
schema validation failure
association ambiguity count
```

Avoid logging:

```text
full workout notes
symptom text
photos
access tokens
raw provider payloads
```

unless a specific secure diagnostic workflow requires it.

No athlete health/training data should be sold or used for targeted advertising.

## 13. Strava adapter release design

Before enabling Strava for another user:

1. re-read the then-current API Agreement and Policy;
2. document permitted purpose and scopes;
3. design webhook/refresh behavior within rate limits;
4. comply with current cache/retention restrictions;
5. implement disconnect/deletion path;
6. ensure user-only display/disclosure requirements;
7. confirm ARETE's functionality does not breach non-replication/competition restrictions;
8. run provider-specific compliance tests.

Do not use Strava API as the assumed permanent source of truth for lifelong training history.

## 14. Apple Health future design

Not required for first alpha.

If added:

```text
iOS companion
→ per-type HealthKit permission
→ local read/normalization
→ explicit selected upload
→ ARETE source observation
```

Request only necessary types at a relevant moment. Respect limited/changed authorization. Do not treat lack of returned data as proof that the user denied permission or has no health data.

## 15. Clinical/general-wellness boundary

Alpha copy/functionality should describe:

```text
training analysis
performance tracking
recovery context
training-related symptom awareness
```

Avoid claims of:

```text
diagnosis
treatment
injury prevention guarantees
disease detection
clinical clearance
```

A future shift toward clinical functionality triggers a new regulatory/legal architecture review, not merely a disclaimer update.

## 16. Alpha participant model

Suggested first cohort:

```text
A — strength-only, minimal data
B — endurance-only, minimal data
C — simple hybrid, optional
```

Do not choose only highly instrumented users similar to the personal ARETE case.

Collect only the feedback needed to identify:

```text
incorrect facts
incorrect matches
incorrect calculations
incorrect confidence
incorrect recommendations
unclear explanations
unnecessary attention
missing attention
integration failures
privacy/security friction
```

## 17. Alpha gates

### Before User 1

```text
multi-user auth/authorization tested
private runtime store selected
privacy/collection notice available
export/delete pathways exist
secrets management exists
provider terms reviewed for enabled integrations
basic security scan/tests pass
AI processor/data handling reviewed if AI enabled
```

### Before User 4+

Repeat legal/privacy/security review, operational incident review, provider-capacity/terms review and architecture bottleneck assessment rather than assuming a 1–3 user design can simply scale.

## 18. Success criteria

The first alpha succeeds if it reveals and reduces errors while preserving trust. It does not need high engagement. A quiet product that correctly answers a few important training questions is preferable to an engaging product that confidently overstates what it knows.
