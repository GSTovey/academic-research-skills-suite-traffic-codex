# Traffic Classification Domain Overlay

Use this reference whenever ARS is applied to network traffic classification,
encrypted traffic analysis, website fingerprinting, malicious traffic detection,
traffic representation learning, or traffic foundation models.

Base snapshot: `GSTovey/Traffic-Classification-Paper-LLM-Wiki` at
`4b527216102ef25819fa0773ab076d6166876175` (2026-10-12), with the
local working-tree sources reviewed on 2026-11-04. The 22 selected primary-text
passage reviews are summarized in `traffic-classification-evidence-update-2026.md`
and pinned by hashes and locators in `traffic-evidence-ledger.json`.
The base commit alone does not reproduce this working-tree snapshot. The source knowledge base
contains structured Chinese paper notes, concept/method/task pages, comparison
tables, claims, contradictions, and research-front records. This file is a
compact domain overlay, not a full copy of the knowledge base.

## Field Scope

Treat the user's home field as network traffic security research, with emphasis
on:

- encrypted traffic classification and analysis
- traffic representation learning and traffic foundation models
- website fingerprinting attacks and defenses
- malicious/encrypted traffic detection and anomaly detection
- VPN, Tor, DoH, QUIC, encrypted tunnels, app fingerprinting, IoT traffic
- few-shot, semi-supervised, open-set, domain-shift, continual, and robustness
  settings, including trace-level, multi-flow, and in-network observation

When a paper is in this scope, evaluate it against computer security/networking
standards, not generic social-science review criteria.

## Establish the Task Before Choosing a Method

Recover from the supplied material: observation point and direction; visible
outer/inner protocol; packet/flow/session/trace decision unit; label provenance;
data available at each decision time; split groups; and permitted target-domain
labels, unlabeled traffic, or calibration samples. Ask only if an unresolved
field changes the requested result. Keep fixed-cohort benchmarks, open-set
recognition, low-base-rate alerting, and online stopping as distinct settings.

Use task-matched mechanisms: per-flow sequences for flow evidence, MIL for bag
labels, graph models when relational context is available, factorized decisions
for composite targets, and stateful models for incremental processing. Compare
with a suitable simple baseline. Architecture names and venue rank do not
establish novelty or superiority.

## Canonical Research Map

Core chain:

`network traffic data -> encrypted traffic analysis -> traffic representation learning/foundation models -> threat detection/classification -> deployable security monitoring`

Main task families:

- traffic classification: application, service, protocol, VPN/Tor, app, website
- malicious traffic detection: malware, C2, DDoS, intrusion, evasion attacks
- website fingerprinting: Tor/anonymous traffic attack and defense
- traffic representation: packet, flow, burst, session, graph, image/audio-like views
- tunnel detection and encrypted DNS/DoH analysis

Main method families:

- traditional ML with expert features: RF, XGBoost, LightGBM, Markov chains
- sequence models: RNN/GRU/LSTM, CNN, temporal models
- transformer pretraining: ET-BERT, YaTC, TrafficFormer, packet/flow transformers
- graph models: flow interaction graphs, byte-level traffic graphs, GNN/GAT
- contrastive/self-supervised learning and masked autoencoding
- multimodal fusion: payload bytes + packet length/direction/timing
- Mamba/state-space, MoE, LLM-adapted traffic models
- few-shot, meta-learning, open-set, test-time adaptation, label-noise learning
- factorized decisions for composite targets, multiple-instance learning for
  trace-level labels, and resource-aware data-plane inference

## High-Value Prior Evidence

These are field priors from the user's knowledge base. Use them as review
questions and literature anchors, not as automatic verdicts.

- Dataset false prosperity: ISCX-VPN and USTC-TFC contain large fractions of
  unencrypted traffic according to SoK 2025, so high scores on old "encrypted"
  datasets may reflect plaintext or obsolete crypto artifacts.
- SII shortcut learning: strong identifying information such as MAC, IP, ports,
  TCP sequence/ack numbers, timestamps, and flow identifiers can dominate model
  performance. Papers must disclose whether these fields are present, removed,
  anonymized, or masked. Also inspect identifiers used to build graphs or split
  groups. A legitimately available operational field is not automatically label
  leakage; test whether the claimed transfer survives changed identities.
- Split leakage: per-packet splits can leak flow identity across train/test.
  For flow/session tasks, per-flow or stricter temporal/domain splits are the
  default expected evaluation.
- Payload learnability is conditional: claims that encrypted payload bytes carry
  semantic patterns must specify encryption protocol/cipher, TLS version, field
  selection, and leakage controls. Separate ciphertext content from record
  lengths, headers, handshake fields, and padding. Successful byte input alone
  does not demonstrate learning plaintext semantics from ciphertext.
- Metric choice matters: micro F1 and accuracy can hide poor minority-class
  performance. Macro F1, per-class recall, confusion matrices, and open-set
  metrics are often necessary.
- Traditional baselines remain strong: RF/XGBoost/LightGBM with well-designed
  expert features can outperform deep representations under stricter evaluation.
  Include a task-matched simple baseline where comparison claims require it,
  and audit its shortcuts too. Frozen probing, random initialization, and full
  fine-tuning answer different questions about representation and final accuracy.
- Multimodal claims need ablations: payload, packet length, direction, timing,
  graph, and session-level modalities must be independently ablated and tested
  under leakage-controlled settings.
- Robustness claims need realistic perturbations: packet loss, duplication,
  reordering, delay, padding, fragmentation, NAT, VPN/Tor/proxy changes,
  temporal drift, environment shift, and adversarial evasion should match the
  threat model.
- Observation/target alignment is a separate validity check: identify the
  observable outer layer, trace completeness, task unit, composite labels, and
  whether a flat classifier is being compared fairly with factorized decisions.
- Match generalization tests to the claim: unseen classes for open-set rejection;
  later data for temporal transfer; new environments for environment transfer;
  label-budget variation for sample efficiency. State adaptation access and
  calibration separately. One of these evaluations does not establish the others.
- Deployment claims must include parsing and feature-extraction cost, memory,
  hardware, latency distribution, throughput, and control/data-plane placement
  relevant to the claimed system. Include waiting, graph construction, smoothing,
  state update, or expert review where used.

## Dataset Review Checklist

For every reviewed manuscript, extract a dataset table with:

- dataset name, year, collection period, task, label source, class count, sample
  unit, scale, and availability
- whether samples are packets, flows, sessions, bursts, traces, or CSV features
- protocol and encryption details: TLS version, cipher suite, QUIC, VPN, Tor,
  DoH, plaintext fraction, obsolete cipher usage
- whether SII fields are present, removed, anonymized, masked, or accidentally
  recoverable
- train/validation/test split unit: packet, flow, session, user, app, website,
  time, network, environment, or device
- class balance and long-tail treatment
- duplicate removal, near-duplicate flow handling, and leakage prevention
- whether datasets are public, private, synthetic, or partially released
- label provenance and failure/timeout retention; group IDs before windowing,
  resampling, augmentation, or overlapping prefixes are generated
- filtering/selection at each stage, sample retention by class/source, and
  whether test distribution or graph context was used during fitting

Common datasets and review posture:

- ISCX-VPN / ISCX-Tor / USTC-TFC / Cross-Platform Application: useful legacy
  baselines, but scrutinize plaintext fraction, obsolete ciphers, and shortcut
  leakage.
- CSTNET-TLS 1.3 and CipherSpectrum: stronger modern-encryption baselines, but
  still check label source, realism, class count, and collection bias.
- CIC-IDS-2017 / CIC-DDoS-2019 / DoHBrw-2020 / CIC-IOT2023: useful security
  datasets, but distinguish encrypted-traffic claims from generic IDS claims.
- DF95 and related WF traces: validate closed/open-world assumptions, defense
  generation, time drift, and adaptive attacker assumptions.
- Private TB-scale corpora: require release plan, reproducibility substitute,
  sampling description, ablations, and credible external validation.

For recent evidence patterns and their source anchors, read
`traffic-classification-evidence-update-2026.md`.

## Method Review Checklist

Evaluate whether the paper clearly defines:

- input representation: raw bytes, packet length, direction, timing, header
  fields, flow graph, image/audio-like transform, statistics, or multimodal mix
- processing pipeline: parsing, truncation/padding, tokenization, masking,
  augmentation, normalization, pretraining, fine-tuning, classifier head
- output granularity: packet, flow, session, app, website, malware family,
  attack type, unknown/open-set label
- threat model: passive observer, enterprise monitor, adversary with defense
  knowledge, evasion attacker, deployment constraints
- novelty boundary: new representation, architecture, loss, data scale,
  evaluation protocol, or deployment system

Potential validity issues; tie severity to the central claim and actual evidence:

- method depends on fields unavailable in the claimed deployment scenario
- paper claims encrypted-payload semantics without leakage controls
- deep model is compared only against weak or misconfigured baselines
- no ablation separates payload/length/timing/header/SII contributions
- no temporal/environment split despite deployment or robustness claims
- open-set or unknown detection uses closed-set metrics only
- “real-world” or “semantic” claims ignore observation-layer limits or label
  composition
- "real-time" claims lack throughput, latency, memory, and hardware details

For online claims, inspect preprocessing as well as model causality: full-trace
duration normalization, future graph neighbors, unobserved termination, and
later flows can invalidate a prefix evaluation. Bidirectional processing within
an already closed window can be valid with its collection delay disclosed.
Report all-candidate and prefix-reachable cohorts separately, and evaluate the
whole stopping policy's false alarms. Capture-time replay alone does not prove
arrival-order or real-delay behavior.

For low-FPR claims, report FP counts, negative denominators, independent groups,
fixed threshold selection, and precision at stated prevalence. Distinguish
empirical operating points, nominal IID confidence bounds, and robustness
certificates. Details and formulas are in the 2026 evidence reference.

## Reviewer Persona Guidance

For `academic-paper-reviewer` full mode, configure the panel like this unless
the user asks otherwise:

- EIC: security/networking venue editor focused on contribution, threat model,
  reproducibility, and benchmark credibility.
- Methodology reviewer: ML systems/security evaluator focused on leakage,
  splits, metrics, baselines, ablations, statistics, and robustness.
- Domain reviewer: encrypted traffic analysis expert focused on datasets,
  protocols, task assumptions, and prior work positioning.
- Perspective reviewer: deployment/privacy reviewer focused on feasibility,
  operational constraints, data governance, and ethical/security impact.
- Devil's Advocate: shortcut-learning skeptic focused on SII, dataset artifacts,
  per-packet leakage, obsolete crypto, and overclaimed generalization.

## Review Output Requirements

For traffic-classification papers, cover applicable findings within the requested
ARS review format; combine overlapping sections rather than duplicating them:

1. Leakage and shortcut-learning audit.
2. Dataset/encryption validity audit.
3. Split and metric validity audit.
4. Baseline and ablation adequacy.
5. Robustness and deployment realism.
6. Reproducibility and code/data availability.
7. Claim calibration: distinguish demonstrated evidence, plausible inference,
   unsupported extrapolation, and contradicted field prior.

Use verdict language conservatively:

- `Major flaw` when identified leakage or unavailable information materially
  undermines the central result; mere suspicion calls for verification.
- `Major revision` when the contribution may be valid but needs stricter splits,
  stronger baselines, or missing ablations.
- `Minor revision` for presentation, missing details, or limited extra checks
  that do not affect the main claim.
- `Reject / desk reject risk` when the central claim depends on an invalid
  benchmark, impossible deployment assumptions, or fabricated/unsupported
  citation claims.

## Research And Writing Guidance

For `deep-research` and `academic-paper`, use the knowledge-base structure as
the default literature organization:

- concept pages: encrypted traffic analysis, traffic classification,
  representation learning, foundation models, few-shot traffic learning,
  malicious traffic detection, anomaly detection, tunnel detection, website
  fingerprinting
- method pages: transformer, contrastive learning, GNN, multimodal fusion,
  pretraining/fine-tuning, self-supervised learning, CNN, state-space models
- comparison pages: datasets, methods, open-source registry, motivation
  patterns, narrative patterns
- claims pages: core claims and contradictions

Suggested research-gap families:

- How to evaluate encrypted traffic representation learning without leakage.
- Whether modern encrypted payload carries learnable signal beyond length/timing.
- How to build reliable modern encrypted-traffic benchmarks.
- When multimodal traffic foundation models outperform expert-feature baselines.
- When multimodal gains survive alignment cost, strict splits, and protocol drift.
- Robust website fingerprinting under adaptive, multi-tab, many-to-many, and
  drift-aware attack/defense.
- Few-shot/open-set malicious traffic detection with calibration under realistic
  deployment shift.
- How factorized decisions and trace-level models behave when only an outer
  observation is available.
- Explainable and lightweight traffic classification for operational security.

## Experiment Planning Guidance

For `experiment-agent`, choose experiments that discriminate the current
scientific claim from its strongest alternative explanation. This menu does not
require every experiment for every paper:

- per-packet vs per-flow vs temporal/environment split comparison
- SII masking and header-field ablation
- payload-only, length-only, timing-only, direction-only, and multimodal ablation
- old datasets vs TLS 1.3 modern datasets
- macro F1, balanced accuracy, per-class recall, AUROC/AUPRC for imbalanced data
- shallow expert-feature baselines vs deep/foundation models under identical splits
- robustness to packet loss, duplication, reordering, padding, delay, and drift
- unknown-class rejection, calibration, cross-environment transfer, adaptive
  attack/defense, and composite-target factorization when those claims apply
- end-to-end preprocessing and data-plane cost, not only neural inference time
- code/data reproducibility: seeds, preprocessing scripts, feature extraction,
  model checkpoints, and hardware/runtime records

## Personal Knowledge Base Integration

If the user provides or references the local knowledge base, use its AGENTS.md
and registry to locate the relevant note and source. Select relevant context from:

- `00-dashboard/project-overview.md`
- `00-dashboard/research-map.md`
- `00-dashboard/open-questions.md`
- `00-dashboard/paper-registry.md`
- `08-comparisons/dataset-comparison-table.md`
- `08-comparisons/method-comparison-table.md`
- `09-claims/claims-index.md`
- `09-claims/contradictions.md`

For a specific cited paper, prefer its structured note under `03-paper-notes/`
for navigation, then inspect the parsed text or PDF passages supporting important
mechanisms, numbers, and scope. L3 labels and coverage reports are not proof that
every claim has been audited. Inspect a rendered PDF when extraction affects a
necessary table, formula, or figure. Keep unknowns explicit and retain the
source's version and locator.

Use Claims and research fronts as candidate syntheses. Do not import venue/time
weights as probabilities of scientific truth, or count dependent papers as
independent support. Do not modify consensus records during ordinary skill use.
Respect personal-paper isolation; only explicitly promoted public papers may
enter this evidence map through their standard-paper records.
