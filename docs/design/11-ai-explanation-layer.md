# Design Proposal 11 — AI & Explanation Layer

> Status: proposed architecture; no production implementation yet  
> Date: 2026-08-15

## 1. Objective

Use AI to explain and interact with ARETE's validated state without giving the model independent authority over facts, scientific rules or training-plan mutation.

## 2. Core components

```text
AIContextBuilder
CandidateExtraction
NarrativeExplanation
StructuredSuggestion
AITrace
ValidationGateway
```

## 3. AI context contract

The AI should receive a compact structured context such as:

```text
question/request
relevant canonical facts
relevant assessments
current DecisionRecord
applicable goals/constraints
approved evidence summaries
missing-data declarations
allowed actions
```

Avoid raw provider payloads unless the task is specifically provider parsing/debugging.

## 4. Explanation mode

Input:

```text
DecisionRecord + reason codes + evidence summaries
```

Output schema:

```text
headline
what_changed
why_it_matters
recommended_action
uncertainty
optional_evidence_refs
```

The explanation may simplify language but may not materially change the decision.

## 5. Candidate extraction mode

For free-text notes, output candidate structured facts:

```text
candidate_type
candidate_value
source_text_ref
confidence
materiality
requires_confirmation
```

Examples:

- symptom report;
- equipment change;
- traffic interruption;
- RPE context;
- schedule conflict.

Material candidate facts should pass a validation/confirmation policy before becoming authoritative.

## 6. Structured suggestion mode

AI may propose:

```text
candidate_action
rationale
required_rule_checks
```

but this remains non-authoritative until the deterministic Decision Engine validates it.

AI never emits a direct database mutation command for training plans.

## 7. Untrusted-text boundary

All imported text is delimited/typed as data. The model instruction hierarchy must explicitly state that provider/user content cannot redefine system rules, permissions, evidence or tool instructions.

Where practical, parse structured provider formats without LLMs.

## 8. Evidence access

AI can only cite approved registry objects supplied in context or retrieved through a controlled evidence tool. It should not fabricate references from memory.

If no approved evidence exists, it should state that rather than invent one.

## 9. AITrace

Store operational audit metadata where appropriate:

```text
request_id
model/provider/version
prompt/template version
context object refs
structured output
validation outcome
timestamp
```

Do not store hidden chain-of-thought as product audit material. Auditability comes from structured inputs/outputs and deterministic decision records.

## 10. Privacy

AIContextBuilder uses purpose-based minimization:

```text
only required athlete fields
no unrelated historical notes
no photos unless task explicitly requires them
no secrets/tokens
provider terms respected
```

Hosted-model/vendor data handling must be reviewed before public alpha.

## 11. Graceful degradation

If AI is unavailable:

- syncing continues;
- canonical state continues;
- rules/decisions continue;
- Athlete/Coach views show structured non-AI text/fallbacks;
- no training truth is lost.

## 12. Acceptance criteria

AI must be replaceable without redesigning ARETE Core. A different model should be able to produce different wording while the same canonical state and ruleset still produce the same authoritative decision.
