# Phase 0 Audit 12 — Athlete Lens, Coach Lens, and Attention UX

> Status: audit complete; no production implementation changes made  
> Date: 2026-08-15

## 1. Purpose

The interface should help the athlete take the next useful action while allowing a reviewer/coach to inspect the reasoning. It must not turn every available metric into a demand for attention.

## 2. Current strengths worth preserving

The current product rules already contain a strong principle: Athlete Attention should surface only information likely to change current or next behavior. Athlete and Coach lenses are separated, and the presentation layer has explicit confidence, attention, data-health and reasoning structures.

This is one of ARETE's strongest product ideas.

## 3. Critical findings

### 3.1 Presentation schema currently leaks provider assumptions

Types still expose sources such as Xunji, Strava, Runna and GitHub plan directly in core presentation contracts. This will not scale to public users and conflicts with capability-based architecture.

**Verdict:** UI should usually speak in domain/role language; provider identity is shown only when useful for provenance/debugging.

### 3.2 Data health needs behavioral wording, not generic freshness

“Fresh” can still mean incomplete. The athlete needs language such as:

```text
training still syncing
all expected records received
last checked at X
plan source unavailable
```

rather than a green badge that implies completeness.

### 3.3 Athlete Lens should not expose uncertainty as unexplained technical noise

Terms such as `eligibility_uncertain`, `source conflict`, `coverage`, or evidence tiers are useful internally but should be translated into behavior-relevant language.

Coach Lens can retain full technical detail.

### 3.4 Progress wording needs the new claim-specific evidence gate

UI labels such as `improving` or `established trend` must only render when a domain-specific ProgressClaim supports them. Presentation code must not generate improvement language itself.

### 3.5 Attention needs deduplication and persistence semantics

A persistent stable issue should not produce a “new” alert every refresh. The system needs alert episode identity, acknowledgement/resolution semantics and escalation/de-escalation logic.

### 3.6 Capability-based navigation is required

Strength-only users should not see empty Running/Recovery modules. Missing integrations should not dominate the homepage.

### 3.7 Coach Lens must distinguish fact, interpretation and decision

A reviewer should be able to inspect:

```text
Observed
Interpreted
Decided
Not known
```

without reading raw JSON.

## 4. Audit verdict

**KEEP THE TWO-LENS PRODUCT MODEL; REBUILD PRESENTATION AROUND CAPABILITIES, DECISION RELEVANCE, AND PROGRESSIVE DISCLOSURE.**

## 5. Required tests

1. strength-only user sees no endurance clutter;
2. no wearable does not produce recovery error cards;
3. same persistent low-level issue does not repeatedly alert;
4. safety escalation changes Athlete Attention;
5. stale/incomplete sync is explained accurately;
6. Coach Lens can drill into source/evidence/rules;
7. Athlete Lens never shows unsupported “improved” language;
8. ambiguous session match requests correction only when it matters;
9. accessibility/keyboard/screen-reader behavior for critical status;
10. provider names are optional provenance, not navigation architecture.
