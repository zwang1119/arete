# Design Proposal 15 — Physique Check-in Roadmap

> Status: approved product direction for V2 scope; V3 quantification remains experimental/research-only  
> Date: 2026-08-17  
> Companion to: `08-progress-physique-engine.md`, `09-goals-decision-engine.md`, `11-ai-explanation-layer.md`, `13-public-alpha-privacy-security.md`

## 1. Decision

ARETE should add a physique check-in capability, but it must not be implemented as:

```text
photo → AI decides which muscle is weak → AI rewrites training
```

The approved direction is:

```text
standardized physique check-in
→ photo quality / comparability gate
→ qualitative longitudinal visual assessment
→ combine with body measurements, training exposure, performance, goals, recovery and symptoms
→ structured coaching suggestion
→ deterministic decision checks
→ smallest justified plan change, if any
→ later check-in evaluates the outcome
```

V2 should implement the reliable feedback loop. V3 may research automated body-shape quantification only after independent validation and governance gates are met.

## 2. First-principles problem

The problem is not “score a physique from a photo.” The useful question is:

> Is the athlete’s body changing in a way that is relevant to the athlete’s stated physique goals, and is there enough evidence to justify changing training?

A single photo contains useful morphology information but is heavily confounded by pose, camera geometry, lighting, clothing, pump, glycogen, hydration and body-fat changes. Therefore:

- longitudinal comparability is more valuable than one-off aesthetic scoring;
- visual appearance and functional capacity are separate domains;
- visual evidence can identify a relative development priority, but cannot establish muscular strength, neuromuscular function or diagnosis;
- training progression is supporting context, not proof of hypertrophy;
- a visual priority does not automatically imply that more training volume is appropriate;
- `maintain` is a valid decision when evidence for change is insufficient.

## 3. Critical semantic boundary

ARETE must keep these concepts separate.

### 3.1 Visual development

A standardized photo may support claims such as:

```text
lateral-deltoid development appears below the athlete's current physique target
lat width appears improved relative to the previous comparable check-in
left-right visual asymmetry is possible but uncertain because torso rotation differs
```

Preferred goal-relative states may include:

```text
priority
below_target
on_target
strong
dominant
insufficient_data
```

These are relative coaching descriptions, not biological measurements.

### 3.2 Functional capacity

A photo alone must not produce claims such as:

```text
weak glute medius
poor scapular control
weak left leg
rotator-cuff dysfunction
```

Functional capacity requires appropriate performance, task, symptom or clinical evidence. Visual appearance must never silently become a strength or diagnostic claim.

## 4. V2 scope — Physique Check-in V1

### 4.1 Standardized capture

Minimum useful views:

```text
front_relaxed
side_relaxed
back_relaxed
```

Optional separately versioned views may include:

```text
front_posed
back_posed
```

Relaxed and posed images are different measurement regimes and must not be compared as if equivalent.

### 4.2 Capture protocol

Where available, record metadata such as:

```text
view
capture_time
camera/device
camera orientation
camera height
camera distance or reproducible position marker
framing
lighting setup
pose type
clothing state/category
pump status
training-before-photo status
user-reported protocol deviations
```

The product should encourage a repeatable protocol, for example similar time of day, same location, same camera/lens, same camera position, same lighting, same clothing category and no intentional pump before comparison photos. These are measurement-control principles, not universal biological requirements.

### 4.3 Capture quality gate

Use lightweight pose estimation primarily to reduce measurement noise, not to infer hypertrophy.

Candidate checks:

```text
whole body visible
body centered
expected view detected
shoulder/hip orientation within acceptable capture tolerance
major torso rotation detected
framing sufficiently similar to reference
pose sufficiently similar for longitudinal comparison
```

The UI should use language such as:

```text
suitable for comparison
comparison limited by pose difference
retake recommended
```

Do not display pseudo-precise “measurement accuracy” percentages unless independently validated.

### 4.4 Previous-photo alignment

V2 should support an optional previous-photo/silhouette overlay during capture.

Purpose:

- improve reproducibility of feet, hip, shoulder and head position;
- reduce avoidable perspective/framing changes;
- improve user understanding of standardized capture.

This is expected to have high value relative to implementation cost.

### 4.5 Longitudinal visual assessment

The primary V2 visual task is not absolute scoring. It is structured comparison between protocol-compatible observations.

A candidate assessment object should retain fields such as:

```text
region_or_muscle_group
current_goal_relative_status
change_vs_reference
confidence
supporting_views
comparison_quality
limitations
model/provider/version
assessment_time
```

Example:

```text
region_or_muscle_group: lateral_deltoids
current_goal_relative_status: below_target
change_vs_reference: possible_improvement
confidence: moderate
supporting_views: [front_relaxed, back_relaxed]
limitations: [minor_lighting_difference]
```

The system must support abstention:

```text
insufficient_data
comparison_withheld
cannot_reliably_assess_from_available_views
```

### 4.6 Goal awareness

Physique evaluation is not universal aesthetics scoring. It must be relative to the athlete’s declared goal hierarchy and constraints.

The relevant context may include:

```text
primary/secondary/maintenance goals
current training phase
sport demands
body-mass goal
performance priorities
injury/symptom constraints
available training time
```

A muscle group may be visually smaller yet correctly remain a low priority if increasing it conflicts with a more important goal.

### 4.7 Evidence fusion

Visual evidence is one evidence stream. Before a consequential training change, the Coach/Decision layer should consider relevant convergent evidence such as:

```text
standardized visual trend
body-mass trend
waist and relevant circumference measurements
current exercise selection
recent training exposure / volume distribution
performance/progression trajectory
recovery and local fatigue
symptoms or movement constraints
goal relevance
recent prior interventions and outcomes
```

Do not implement an arbitrary universal rule such as “two of three signals means add sets.” The executable policy must be claim-specific, evidence-governed and testable.

### 4.8 AI authority boundary

The vision/AI layer may produce a structured candidate assessment or candidate action, for example:

```text
candidate_action: consider_small_increase_in_lateral_delt_stimulus
rationale:
  - persistent goal-relative visual priority
  - current training exposure leaves plausible room for adjustment
required_rule_checks:
  - goal conflict
  - local symptoms
  - recovery/fatigue
  - current volume/exercise distribution
  - recent progression
```

It may not directly mutate a plan.

Required authority path remains:

```text
validated observations
→ Progress/Physique assessment
→ StructuredSuggestion / candidate action
→ Decision Engine validation
→ DecisionRecord
→ PlanMutationRequest
→ authority/conflict checks
→ user confirmation where required
→ PlannedSessionRevision
```

### 4.9 Minimum effective change

When a plan change is justified, prefer the smallest intervention that tests the coaching hypothesis while preserving other goals and recovery capacity.

Examples may include:

```text
reallocate a small number of weekly sets
change exercise selection while keeping total fatigue similar
change exercise order or frequency before adding large volume
maintain the current plan when the target region is already improving
```

The subsequent check-in should explicitly evaluate whether the intervention produced the intended response.

## 5. V2 non-goals

V2 must not ship the following as authoritative features:

```text
precise body-fat percentage inferred from ordinary photos
precise muscle-mass estimates from ordinary photos
single-muscle hypertrophy percentages
centimeter-equivalent measurements inferred from image pixels without validated calibration
strength/function diagnosis from visual appearance
automated injury diagnosis
universal physique score
AI-controlled automatic plan rewriting
```

Statements such as “lateral deltoid +4.8%” or “body fat 12.7%” create false precision unless a validated measurement method genuinely supports that precision.

## 6. Suggested V2 architecture

Keep capture, interpretation and decision authority separate:

```text
Physique Capture UI
    ↓
Capture Quality / Pose Adapter
    ↓
Physique Photo Observation
    ↓
MeasurementProtocol + comparability metadata
    ↓
Progress & Physique Engine
    ↓
VisualDevelopmentAssessment / ProgressClaim
    ↓
AI StructuredSuggestion (optional)
    ↓
Decision Engine
    ↓
DecisionRecord
    ↓
PlanMutationRequest (only if justified)
```

Possible implementation concepts are not yet frozen schema. Reuse existing canonical concepts where possible rather than creating a parallel physique-only ontology.

## 7. Open-source review

External projects are references, not authorities. Every dependency remains subject to exact-version license, maintenance, security, privacy and compatibility review before adoption.

### 7.1 MediaPipe — strongest V2 engineering candidate

Repository: `google-ai-edge/mediapipe`  
Observed license: Apache-2.0.

Useful for:

- lightweight pose landmarks;
- capture alignment/quality assistance;
- cross-platform or on-device inference patterns.

ARETE use level:

```text
Level 3 candidate — direct dependency only after a focused implementation spike and dependency review
```

Primary first-principles value: reduce photo-protocol noise. It should not become the source of physique-development claims merely because pose landmarks are available.

### 7.2 TensorFlow.js models / pose detection — V2 browser candidate

Repository: `tensorflow/tfjs-models`  
Observed license: Apache-2.0.

Useful when a web implementation benefits from client-side pose estimation.

ARETE should compare this against MediaPipe on the actual target browsers/devices for:

```text
mobile Safari compatibility
latency
bundle size
battery/thermal behavior
landmark stability
maintenance burden
privacy characteristics
```

Do not retain two production pose stacks merely for optionality; select the simplest one that passes the spike.

### 7.3 MMPose — benchmark/research reference

Repository: `open-mmlab/mmpose`  
Observed license: Apache-2.0.

Useful as:

- a broad pose-estimation benchmark toolbox;
- an offline comparator if lightweight V2 pose quality is questioned;
- research reference for richer keypoint/mesh approaches.

Default ARETE use:

```text
Level 1–2 — concept/benchmark or independent implementation reference
```

Do not introduce a Python/GPU pose stack into V2 production unless it solves a demonstrated problem that the lightweight path cannot.

### 7.4 SHAPY — V3 scientific/research reference

Repository: `muelea/shapy`  
Purpose: research code for 3D body-shape regression using metric and semantic attributes.

GitHub repository metadata does not expose a standard SPDX license. Therefore treat the code/model/data as research-only reference until exact reuse rights for every required component are independently reviewed.

Potential learning value:

- image-to-body-shape methodology;
- virtual anthropometry concepts;
- validation design for shape and measurements.

Default ARETE use:

```text
Level 1 — research/concept reference only
```

### 7.5 4D-Humans / HMR2 — V3 research candidate

Repository: `shubham-goel/4D-Humans`  
Observed repository code license: MIT.

Useful for studying RGB-image-to-human-mesh reconstruction.

However, repository code license does not automatically clear external body models, checkpoints, datasets or downstream dependencies. The complete dependency/license chain must be reviewed separately.

Default ARETE use:

```text
V3 experimental spike, not V2 production
```

### 7.6 SMPL-Anthropometry — V3 measurement research candidate

Repository: `DavidBoja/SMPL-Anthropometry`  
Observed repository license: MIT.

Useful for studying how anthropometric measurements can be derived from SMPL/SMPL-X-type meshes.

This is potentially valuable only after a validated and legally compatible body-shape representation exists. It does not by itself solve image-to-body measurement.

Default ARETE use:

```text
Level 1–2 research reference for V3
```

### 7.7 Sapiens — research watchlist only

Repository: `facebookresearch/sapiens`  
GitHub reports a non-standard/custom license (`NOASSERTION`).

Useful capabilities include high-resolution human-centric tasks such as pose and segmentation. Because the license is non-standard and the data domain is sensitive human imagery, it should remain research-only until an explicit legal/privacy/license review establishes compatibility.

Default ARETE use:

```text
Level 1 — architecture/research reference only
```

## 8. V2 implementation spike

Before selecting the pose/capture dependency, test the smallest realistic prototype on target devices.

Compare at minimum:

```text
MediaPipe vs a suitable TFJS pose path
```

Test cases should include:

- front, side and back relaxed views;
- minor torso rotation;
- camera-height mismatch;
- different framing;
- partial-body crop;
- typical indoor lighting variation;
- iPhone/Safari target environment where relevant.

Spike output should be an ADR containing:

```text
problem
candidate implementations
measured latency/stability
privacy/data-flow implications
license/dependency findings
chosen implementation
rejected alternatives
```

## 9. V3 research scope — Automated Physique Quantification

V3 may research a separate experimental capability such as:

```text
human/body-part segmentation
pose normalization
silhouette analysis
3D body-shape reconstruction
virtual anthropometry
morphology metrics
longitudinal uncertainty modeling
```

Potential research pipeline:

```text
standardized photo
→ pose/segmentation
→ normalized representation
→ optional 3D body-shape estimator
→ optional anthropometric estimator
→ uncertainty + model/version metadata
→ experimental longitudinal observation
```

No such output becomes authoritative merely because a model returns a number.

## 10. Promotion gates from V3 research to Core

Automated quantification should not enter Core until it demonstrates all materially relevant gates.

### 10.1 Repeatability

Repeated standardized captures of the same athlete under near-identical conditions must show sufficiently low variation for the intended claim.

### 10.2 Sensitivity to protocol error

Quantify the effect of realistic changes in:

```text
pose
camera distance
camera height
camera/lens
lighting
clothing
pump state
```

### 10.3 Error relative to meaningful change

A metric is not useful for longitudinal coaching if expected measurement error is similar to or larger than the change ARETE wants to detect.

### 10.4 Incremental decision value

Demonstrate that automated quantification materially improves decisions beyond the simpler V2 evidence set:

```text
standardized photos + bodyweight + waist/circumference + training/performance context
```

If it does not change decisions or improve confidence enough to justify complexity, do not ship it.

### 10.5 Calibration and subgroup robustness

Assess whether performance materially degrades across relevant body sizes, body-composition ranges, skin tones, sex/body-shape patterns, camera conditions or clothing states. Do not infer robustness from benchmark headline accuracy alone.

### 10.6 License/provenance/privacy

Every code component, model weight, body model, dataset dependency and external service must have compatible rights and acceptable data handling. Sensitive photo processing should prefer local/on-device processing when practical and valuable.

### 10.7 Model/version governance

Any promoted quantitative model must retain:

```text
model/provider/version
input protocol/version
calibration/validation version
uncertainty or quality state
measurement regime
```

A model change creates a potential measurement-regime change and requires regression analysis.

## 11. Privacy and security requirements

Physique photos are sensitive athlete data.

V2 must design for:

- explicit voluntary capture/upload;
- purpose limitation;
- minimum required retention;
- authenticated private access;
- tenant/user isolation;
- secure transport/storage where stored;
- user delete/export/correction pathways;
- deletion lineage for derived observations where appropriate;
- no raw photo content in normal application logs, analytics or debugging telemetry;
- no unrelated photo inclusion in AI context;
- clear review of hosted-model/vendor handling before external use.

On-device pose/quality processing is preferred when it materially reduces sensitive-data exposure without unacceptable implementation cost.

## 12. V2 acceptance criteria

Physique Check-in V1 is not complete unless all of the following are true:

1. A user can create protocol-labeled front/side/back physique observations.
2. The system can explicitly mark photos as comparable, limited or unsuitable for longitudinal comparison.
3. Capture quality assistance does not claim unvalidated measurement precision.
4. Previous-reference alignment is available or the implementation documents why an equally effective alternative was chosen.
5. AI visual output is structured, goal-relative, confidence-qualified and able to abstain.
6. Visual appearance cannot generate an authoritative functional-weakness or diagnostic claim.
7. A physique assessment can incorporate relevant non-photo evidence without treating correlated metrics as independent proof.
8. AI cannot directly mutate the training plan.
9. Any consequential plan change passes the ordinary DecisionRecord → PlanMutationRequest pathway.
10. `maintain` remains a valid outcome.
11. Photo storage/access/deletion behavior passes privacy and tenant-isolation tests.
12. Scientific-behavior regression fixtures include at least:
    - apparent visual change caused mainly by pose/lighting;
    - visually lagging muscle with already-high stimulus and poor recovery;
    - visually lagging muscle that conflicts with the primary performance goal;
    - improving muscle where the correct action is maintain;
    - insufficient/comparison-incompatible photos;
    - AI candidate suggestion rejected by deterministic checks.

## 13. Version recommendation

### V2 — approve

Ship:

```text
standardized photos
capture protocol metadata
pose/capture quality assistance
previous-photo alignment
qualitative longitudinal visual assessment
goal-relative development priorities
confidence + limitations + abstention
fusion with measurements/training/performance/recovery context
structured candidate actions
Decision Engine validation
smallest justified plan adjustment
```

### V3 — defer pending validation

Research only:

```text
body-part segmentation as a quantitative signal
pose-normalized morphology metrics
3D reconstruction
virtual anthropometry
photo-derived body-composition estimates
single-region quantitative hypertrophy metrics
```

The default V3 question is not “can a model output this number?” It is:

> Is this measurement repeatable, valid enough for the intended longitudinal claim, legally usable, privacy-compatible, and incrementally useful for coaching decisions?

If not, retain the simpler V2 system.

## 14. Governing principle

The physique feature should make ARETE better at learning whether training is producing the intended body change. It should not turn ARETE into an aesthetic-rating app or an unvalidated body scanner.

> Standardize first. Compare longitudinally. Infer cautiously. Fuse evidence. Change the plan only when the decision is justified.
