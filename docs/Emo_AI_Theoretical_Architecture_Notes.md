# EMO-AI Theoretical Architecture Notes

## Foundational Premise: Emotion as Multidimensional State

**Emotion is not adequately represented as only a discrete label or a single scalar. EMO-AI treats affect as a position and trajectory within a multidimensional, subject-relative state space.**

The motivating problem is not that dimensional emotion models do not exist. They do. The circumplex tradition models affect primarily through valence and arousal, and the Pleasure-Arousal-Dominance (PAD) model uses three numerical dimensions. Appraisal and component-process theories also model emotion as a dynamic process rather than a static label.

EMO-AI therefore does **not** claim novelty merely from using multiple axes, positive/negative direction, appraisal, personalization, or temporal change. Its research hypothesis is the usefulness of a particular integration:

- a fixed higher-dimensional bounded affect representation,
- independently preservable directional activation within each dimension,
- a subject-relative adaptive baseline or local zero,
- explicit displacement from that baseline,
- temporal persistence, decay, reinforcement, variability, instability, and inertia,
- appraisal-driven state updates,
- confidence and uncertainty,
- provenance-preserving evidence and memory,
- semantic/context organization that remains separate from affective state,
- atomic state snapshots,
- observer-window-aware measurement,
- versioned local-first state transport,
- and guarded feedback that prevents observation, inference, generated output, or memory from silently becoming truth.

---

# 1. Epistemic Core: Observation Is Not Truth

The first rule of EMO-AI is epistemic rather than emotional:

> **No observation, inference, memory, semantic cluster, or generated output is treated as truth solely because it exists in the system.**

Every claim retains provenance, time, uncertainty, validation status, and supporting or contradicting evidence.

The preferred pipeline is:

```text
observation
    ↓
provenance capture
    ↓
validation / corroboration
    ↓
confidence + uncertainty
    ↓
accepted evidence
    ↓
appraisal / inference
```

Not:

```text
observation → truth → state update
```

### Minimum epistemic states

1. **Observed** — supplied, measured, retrieved, or otherwise detected.
2. **Validated** — passed source-appropriate integrity or plausibility checks.
3. **Corroborated** — independently supported by another allowed source or observation.
4. **Derived** — computed or inferred from evidence.
5. **Generated** — produced by a model as explanation, hypothesis, prediction, or response.
6. **Confirmed** — sufficiently supported for the current operational purpose, with confidence still retained.

“Confirmed” is not metaphysical truth. It is an operational status under stated evidence and confidence.

### Core anti-feedback law

**An inference may reference evidence. It may not silently promote itself into evidence.**

Likewise:

```text
observation ≠ truth
semantic cluster ≠ emotion
inferred emotion ≠ observation
model response ≠ evidence
memory retrieval ≠ independent corroboration
repetition ≠ confirmation
```

---

# 2. Canonical Affect Representation

## 2.1 Six-dimensional research space

Conceptually, EMO-AI represents affect as six modeled dimensions:

```text
E(t) = [e1(t), e2(t), e3(t), e4(t), e5(t), e6(t)]
```

The dimensions are model variables, not literal physical Cartesian axes. The six-dimensional geometry remains a research hypothesis and must outperform simpler baselines before being treated as justified.

## 2.2 Non-canceling directional activation

A single signed scalar can hide simultaneous opposing activation. EMO-AI therefore preserves two directional activation channels for each dimension:

```text
A_i+(t) ∈ [0,1]
A_i-(t) ∈ [0,1]
```

For six dimensions, the full instantaneous activation representation contains twelve bounded activation values:

```text
A(t) = [A1+, A1-, A2+, A2-, ... A6+, A6-]
```

A signed summary may be derived when useful:

```text
S_i(t) = A_i+(t) - A_i-(t)
```

but the pair must be retained because subtraction is lossy.

Example:

```text
A_i+ = 0.85
A_i- = 0.75
S_i   = 0.10
```

A summary of `0.10` alone would falsely imply weak activation, while the paired representation correctly preserves a highly conflicted state.

Therefore:

> **Opposing affective activation may coexist. Derived net position must never replace the underlying directional channels.**

---

# 3. Subject-Relative Baseline as an Estimated Attractor

## 3.1 Baseline is latent, not directly observed

The baseline is best treated as a **latent, slowly varying subject state** inferred from repeated validated observations.

A system can observe evidence of return behavior, but the attractor itself must be estimated.

Represent the subject baseline as a vector:

```text
B(t) = [b1(t), b2(t), b3(t), b4(t), b5(t), b6(t)]
```

Each component represents the estimated resting or recurrent reference for its dimension.

## 3.2 Local zero

The common local zero is created after normalization relative to the baseline vector:

```text
D(t) = E(t) - B(t)
```

or, for paired directional channels, by an equivalent baseline-relative transform defined for each channel.

This means all dimensions share a common **normalized origin** of zero displacement even though their raw baseline coordinates need not be numerically identical.

```text
same local zero ≠ same raw baseline value
```

The architecture should **not** require all six raw dimensions to converge to one identical numerical coordinate. Coherence comes from a shared subject-relative reference frame, not forced equality among heterogeneous dimensions.

## 3.3 Attractor interpretation

The attractor hypothesis is:

> Across sufficiently stable contexts, state trajectories should exhibit recurrent movement toward a subject-specific baseline region.

This is empirically testable. Some dimensions may ultimately show context-dependent attractors, slow nonstationarity, or no useful single equilibrium. EMO-AI should allow the data to reject a single-attractor assumption.

## 3.4 Fast state, slow baseline

A general time-scale separation should hold:

```text
fast process: current affective activation
slow process: baseline / disposition estimate
```

Baseline adaptation should require sustained, repeated, validated, sufficiently confident evidence. It should move much more slowly than immediate state so that a temporary episode does not become a permanent personality shift.

Candidate baseline update gates include:

- minimum observation duration,
- minimum evidence quality,
- minimum confidence,
- repeated return behavior,
- context stability,
- absence of unresolved contradiction,
- and hysteresis before changing the baseline estimate.

---

# 4. Atomic State Snapshot Requirement

State and baseline must not be read piecemeal while either is changing.

A consumer calculating relative expression from state sampled at one moment and baseline sampled at another can create a synthetic displacement that never actually existed.

EMO-AI therefore defines an **atomic affect snapshot**.

```text
AffectSnapshot {
  subject_id,
  state_epoch,
  sequence,
  captured_at,
  model_version,
  schema_version,
  baseline_version,
  baseline_vector,
  directional_activation[6][2],
  derived_signed_state[6],
  uncertainty,
  confidence,
  evidence_ids,
  provenance_summary,
  validation_status
}
```

The baseline vector and all state channels used for a calculation must come from the same logical snapshot.

> **No derived relative-state calculation may combine components from different state epochs unless the transformation explicitly models that temporal difference.**

---

# 5. Observer-Window Measurement

Different observers can produce different estimates of the same underlying latent process because they sample different signals, at different resolutions, over different windows.

A consultant may perceive a stable ten-minute interaction pattern while a local model detects second-to-second drift. Neither estimate is automatically truth, and neither is invalid merely because it differs from the other.

```text
ObservationRecord {
  observer_id,
  observer_type,
  subject_id,
  window_start,
  window_end,
  temporal_resolution,
  observed_features,
  provenance,
  confidence,
  validation_status
}
```

The correct interpretation is:

> **Multiple observers produce estimates of the same latent subject process through different observational windows. Their disagreement is data to reconcile, not proof that one observer is necessarily wrong.**

Observer agreement, systematic observer bias, temporal resolution, and predictive accuracy should all be measurable.

---

# 6. Temporal Dynamics and EmoEcho

EmoEcho should be treated as a family of temporal parameters rather than a single “emotional memory” value.

Affective meaning depends not only on state position but on temporal behavior:

```text
state position
+ directional activation
+ direction of change
+ rate of change
+ variability
+ instability
+ inertia / persistence
+ decay / reinforcement
+ adaptive baseline
= affective trajectory
```

### Variability

```text
SD_i = within-subject standard deviation
```

### Instability

```text
RMSSD_i = sqrt(mean((E_i(t) - E_i(t-1))^2))
```

### Inertia

A first approximation can use lag-1 autoregression:

```text
E_i(t) = α_i + φ_i E_i(t-1) + error
```

### Decay / reinforcement

Decay constants must be attached to the state component and time reference they govern. Different dimensions or directional channels may decay at different rates.

Different decay rates are not themselves synchronization errors. They become problematic when a state is interpreted without the correct timestamp, version, or decay rule.

### Cross-dimensional coupling

Cross-dimensional coupling may exist:

```text
E_i(t+1) = ... + β_ij E_j(t)
```

but coupling coefficients must be learned or tested rather than assumed.

---

# 7. Oscillation Containment and Controlled Propagation

Oscillation around baseline is not automatically error. It may be meaningful affective dynamics, sensor noise, inference noise, or a mixture.

Simply representing oscillation does **not** guarantee that it cannot propagate into other layers.

EMO-AI therefore separates transient measurement from downstream accepted state:

```text
raw observations
      ↓
transient state candidates
      ↓
temporal filter / window analysis
      ↓
validation + uncertainty
      ↓
atomic accepted state snapshot
      ↓
controlled downstream consumers
```

### No direct write rule

Layers may pass references, evidence, and approved state transitions, but one layer should not directly overwrite another layer's internal truth state.

```text
transient oscillation
    ≠ baseline update
    ≠ durable memory
    ≠ independent evidence
```

A downstream layer may consume oscillation statistics—such as variability, frequency, amplitude, persistence, or phase—but only through an explicitly defined interface.

This is the practical containment mechanism for the earlier “three-body” concern: **containment must be enforced by information-flow rules, not assumed from representation alone.**

---

# 8. Coupled Feedback and the “Three-Body” Analogy

The three-body problem remains a useful metaphor for the concern that multiple interacting dynamic layers can become difficult to predict. It is not a mathematical equivalence.

EMO-AI does not currently demonstrate chaotic dynamics or sensitive dependence on initial conditions.

The engineering concern is:

> Coupled feedback among observation, appraisal, affective state, memory, reasoning, and generated output can amplify early inference errors unless provenance, authority, and update pathways are constrained.

```text
E(t+1) = F(E(t), A(t), C(t), X(t), U(t)) + εE
M(t+1) = G(M(t), X(t), Q(t)) + εM
R(t)   = H(E(t), M(t), C(t))
```

where:

- `X(t)` = validated external evidence,
- `C(t)` = contextual/semantic information,
- `A(t)` = appraisal result,
- `E(t)` = affective state,
- `M(t)` = memory state,
- `Q(t)` = explicitly approved derived information eligible for memory,
- `R(t)` = model response or expression.

The critical rule is:

```text
R(t) must not automatically become X(t+1)
```

If generated output later re-enters the system, its provenance remains model-generated unless independently validated.

---

# 9. Provenance-Isolated Memory

Durable memory promotion must depend on more than salience.

```text
memory_eligibility =
    provenance_quality
  × validation_strength
  × confidence
  × significance
  × corroboration
  × temporal_relevance
```

The precise function is a research question.

> **Significance alone cannot convert speculation into fact.**

Memory should preserve:

- source evidence identifiers,
- observation windows,
- validation history,
- human corrections,
- uncertainty,
- alternative interpretations,
- and the model/version that produced any derived claim.

---

# 10. Semantic Clustering as Context, Not Emotion

A candidate semantic-context pipeline is:

```text
high-dimensional embeddings
        ↓
optional dimensional reduction
        ↓
DBSCAN / HDBSCAN
        ↓
candidate semantic clusters
        ↓
cluster validation / stability
        ↓
derived contextual evidence
        ↓
appraisal
```

DBSCAN can identify dense regions and leave noise unassigned. HDBSCAN can be attractive where density varies and cluster persistence matters.

UMAP may help clustering, but it does not perfectly preserve density and can introduce apparent separations.

```text
UMAP projection ≠ ground-truth geometry
cluster assignment ≠ ground-truth concept
semantic cluster ≠ affective state
```

The theory should compare clustering of original embeddings, PCA-reduced representations, UMAP-reduced representations, multiple seeds, and multiple clustering parameters before treating clusters as useful.

A cluster enters appraisal only as **derived contextual evidence with provenance and confidence**.

---

# 11. Local-First State Authority and Versioned Transport

## 11.1 Keep high-resolution state local where practical

A local-first design reduces latency, limits exposure of sensitive state, and avoids reconstructing a rapidly changing twelve-channel affect representation from independently timed network calls.

The local runtime may hold the full current state while a backend receives compressed semantic or AIL-style state digests.

However:

> **Local storage by itself does not solve synchronization.**

Every transported digest must be versioned and ordered.

## 11.2 Minimum state digest envelope

```text
StateDigest {
  subject_id,
  state_epoch,
  sequence,
  captured_at,
  sent_at,
  schema_version,
  model_version,
  baseline_version,
  context_id,
  ail_payload,
  confidence_summary,
  uncertainty_summary,
  provenance_summary,
  validation_status
}
```

Where appropriate, integrity protection may also include a hash, MAC, signature, or authenticated transport layer.

### Ordering rule

A consumer must be able to detect:

- stale messages,
- duplicates,
- missing sequence numbers,
- out-of-order delivery,
- baseline-version mismatch,
- model-version mismatch,
- and conflicting branches created while offline.

```text
value without timestamp/version ≠ reconstructable state
```

## 11.3 Digest, not destructive compression

AIL shorthand is useful as a transport and reasoning language, but it must not become the only retained representation if doing so destroys information needed for later reconstruction or validation.

The authoritative local record should preserve the richer snapshot and evidence trail.

---

# 12. Multi-Agent Portability: One Subject, Multiple Views

The canonical baseline belongs to the **subject**, not to VERA, a client application, a traveller application, or another agent.

```text
                 Canonical subject model
                 B_subject + state history
                          │
          ┌───────────────┼───────────────┐
          ▼               ▼               ▼
     observer/agent A observer/agent B observer/agent C
       context A         context B         context C
       window A          window B          window C
```

Each agent may maintain a synchronized local snapshot or contextual projection, but agents should not independently invent permanent subject baselines.

> **One logical subject baseline may have many synchronized replicas, but baseline mutation must follow a defined authority and reconciliation protocol.**

Possible strategies include:

- single-writer baseline authority,
- explicitly elected local authority,
- server-mediated reconciliation,
- or validated multi-writer reconciliation with conflict records.

Blind averaging of conflicting agent baselines is not sufficient.

When offline branches occur, reconciliation should preserve both histories until the conflict is validated and resolved.

---

# 13. Confidence, Uncertainty, and Contradiction

A multidimensional system should not force false precision.

```text
StateEstimate {
  value,
  confidence,
  provenance,
  timestamp,
  observation_window,
  supporting_evidence_ids,
  contradicting_evidence_ids,
  validation_status
}
```

The system should distinguish:

- **state uncertainty** — uncertainty about current affective state,
- **model uncertainty** — uncertainty arising from inadequate model fit,
- **evidence uncertainty** — ambiguity or unreliability in input,
- **semantic uncertainty** — ambiguity in cluster/context interpretation,
- **observer uncertainty** — uncertainty associated with observational window, modality, or observer bias.

Contradictory observations should generally widen uncertainty or preserve multiple hypotheses before forcing a state coordinate or baseline change.

---

# 14. Scientific Prior Art and Positioning

EMO-AI should be positioned as a research architecture built in conversation with established affective science rather than as a replacement for it.

Dimensional affect models, appraisal theories, dynamic-process models, affective inertia, attractor concepts, and nonlinear systems language all have prior art.

A defensible EMO-AI contribution claim is therefore narrower:

> **EMO-AI investigates whether a bounded higher-dimensional affect representation with independently preservable directional activation, subject-relative estimated attractors, temporal dynamics, atomic state capture, provenance-governed evidence, semantic-context separation, and versioned local-first multi-agent transport improves state tracking, appraisal, prediction, and reasoning.**

That integrated architecture—not the existence of any single component—is the research contribution to test.

---

# 15. Falsifiable Research Program

### H1 — Higher-dimensional state utility

**Hypothesis:** The fixed EMO state representation predicts held-out human judgments or downstream behavior better than lower-dimensional baselines such as valence/arousal or PAD.

**Failure condition:** If simpler models perform as well or better after controlling for complexity, the extra dimensions are not justified.

### H2 — Non-canceling channel utility

**Hypothesis:** Preserving opposing directional activations separately improves mixed-state representation and downstream prediction compared with a single signed scalar.

**Failure condition:** If paired channels add no measurable information after controlling for complexity, the representation should be simplified.

### H3 — Adaptive local-zero / attractor utility

**Hypothesis:** Subject-relative displacement from an estimated baseline improves within-person state estimation compared with absolute coordinates alone.

**Failure condition:** If the adaptive baseline adds no reproducible predictive or calibration benefit, it should be simplified or removed.

### H4 — Atomic snapshot integrity

**Hypothesis:** Atomic capture of baseline plus all state channels reduces impossible or internally inconsistent derived states under concurrent updates.

**Failure condition:** If non-atomic reads produce no measurable inconsistency under realistic update rates, the implementation requirement can be relaxed.

### H5 — Observer-window model

**Hypothesis:** Explicitly modeling observer window and temporal resolution improves reconciliation and calibration across human and machine observers.

**Failure condition:** If observer metadata does not improve agreement or prediction, the additional complexity is not justified.

### H6 — EmoEcho temporal utility

**Hypothesis:** Explicit temporal parameters improve next-state prediction, appraisal consistency, or human-rated continuity compared with memoryless state estimation.

**Failure condition:** If persistence/decay parameters do not improve held-out performance, the temporal layer requires revision.

### H7 — Provenance isolation safety

**Hypothesis:** Preventing generated/derived outputs from being counted as independent observations reduces confidence drift and recursive inference error.

**Test:** Compare identical agents with and without provenance isolation under repeated retrieval and self-reference scenarios.

### H8 — Semantic clustering utility

**Hypothesis:** Validated semantic clusters improve appraisal relevance or memory retrieval without materially increasing false associations.

**Failure condition:** If clustering adds noise, instability, or spurious relationships, use direct retrieval or a different representation.

### H9 — Layer isolation versus unrestricted coupling

**Hypothesis:** Explicitly constrained information flow produces better calibration and lower error amplification than unrestricted cross-layer feedback.

### H10 — Versioned local-first transport

**Hypothesis:** State epochs, sequence numbers, timestamps, and baseline/model versions materially reduce stale-state and desynchronization errors compared with unversioned semantic transport.

### H11 — Subject-owned baseline portability

**Hypothesis:** A single logical subject baseline with contextual observer views produces greater cross-agent consistency than independently learned agent-specific baselines.

---

# 16. Proposed Architectural Relationship

```text
                    RAW OBSERVATIONS
             provenance + timestamp + source
                           │
                           ▼
                VALIDATION / CORROBORATION
                           │
                           ▼
                 ACCEPTED EVIDENCE LAYER
                           │
              ┌────────────┴────────────┐
              ▼                         ▼
      SEMANTIC CONTEXT PATH       DIRECT APPRAISAL PATH
 embeddings / optional clustering        │
 validation + confidence                 │
              └────────────┬────────────┘
                           ▼
                     APPRAISAL ENGINE
                           │
                           ▼
              TRANSIENT STATE CANDIDATES
                           │
               temporal filtering/window
                           │
                           ▼
                 ATOMIC STATE SNAPSHOT
       6 dimensions × 2 directional channels
       + subject baseline vector + uncertainty
                           │
                           ▼
                     EMOECHO DYNAMICS
        variability / instability / inertia / decay
                           │
              ┌────────────┴────────────┐
              ▼                         ▼
         MEMORY GATE              STATE DIGEST
 provenance + validation      epoch + seq + versions
              │                         │
              ▼                         ▼
       DURABLE MEMORY             REMOTE AGENTS
              │                  contextual replicas
              └────────────┬────────────┘
                           ▼
                  REASONING / ATTENTION
                           │
                           ▼
                   EXPRESSION POLICY
                           │
                           ▼
                     MODEL OUTPUT
                           │
                 NOT automatically evidence
```

---

# 17. Current Theory Status

**Classification:** theoretical / research architecture  
**Canonical affect representation:** six dimensions with independently preserved positive/negative directional activation  
**Baseline:** subject-owned latent baseline vector / estimated attractor  
**Local zero:** normalized zero-displacement origin relative to subject baseline  
**State integrity:** atomic snapshot requirement  
**Observer model:** multiple observational windows over one latent subject process  
**Temporal layer:** EmoEcho decomposed into measurable affect-dynamic parameters  
**Semantic organization:** optional embeddings + validated density-based clustering  
**Transport:** local-first high-resolution state + versioned semantic/AIL digests  
**Multi-agent rule:** one logical subject baseline, many contextual replicas/views  
**Primary epistemic safeguard:** observation is not truth; generated or derived information cannot silently self-promote into evidence  
**Three-body language:** analogy for coupled-feedback risk only; formal chaos is not claimed  
**Novelty position:** the contribution to test is the integrated architecture, not the individual existence of multidimensional affect, appraisal, attractors, temporal dynamics, or distributed state.

---

# 18. Selected References and Validation Anchors

1. Russell, J. A. (1980). *A circumplex model of affect.* Journal of Personality and Social Psychology, 39(6), 1161–1178. DOI: 10.1037/h0077714.
2. Posner, J., Russell, J. A., & Peterson, B. S. (2005). *The circumplex model of affect: An integrative approach to affective neuroscience, cognitive development, and psychopathology.* Development and Psychopathology, 17(3), 715–734. DOI: 10.1017/S0954579405050340.
3. Mehrabian, A. (1995). *Framework for a comprehensive description and measurement of emotional states.* Genetic, Social, and General Psychology Monographs, 121(3), 339–361. PMID: 7557355.
4. Scherer, K. R. (2009). *The dynamic architecture of emotion: Evidence for the component process model.* Cognition and Emotion, 23(7), 1307–1351. DOI: 10.1080/02699930902928969.
5. Scherer, K. R. (2005). *A systems approach to appraisal mechanisms in emotion.* Neural Networks, 18(4), 317–352. PMID: 15936172.
6. Dejonckheere, E. et al. (2023). *Measuring affect dynamics: An empirical framework.* Open-access PMC record PMC9918585.
7. Ong, A. D. et al. (2026). *Seven Challenges in Affective Inertia Research.* Open-access PMC record PMC12798691.
8. UMAP documentation. *Using UMAP for Clustering* and FAQ: clustering is possible with care; density is not perfectly preserved and false tears can occur.
9. HDBSCAN documentation. HDBSCAN integrates DBSCAN-style clustering over varying epsilon values and selects persistent clusters, supporting variable-density structure.
10. Shumailov, I. et al. (2024). *AI models collapse when trained on recursively generated data.* Nature, 631, 755–759. DOI: 10.1038/s41586-024-07566-y. Included as related evidence for source-data provenance, not as direct proof of runtime EMO memory behavior.
