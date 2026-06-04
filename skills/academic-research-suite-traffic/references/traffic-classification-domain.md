# Traffic Classification Domain Overlay

Use this reference whenever ARS is applied to network traffic classification,
encrypted traffic analysis, website fingerprinting, malicious traffic detection,
traffic representation learning, or traffic foundation models.

Source snapshot: `GSTovey/Traffic-Classification-Paper-LLM-Wiki` at
`bff8f9e283a37cb62ed8f28f7a7b6243463a8ee4` (2026-06-03). The source knowledge
base contains 79 papers, structured Chinese paper notes, concept/method/task
pages, comparison tables, claims, and contradiction records. This file is a
compact domain overlay, not a full copy of the knowledge base.

## Field Scope

Treat the user's home field as network traffic security research, with emphasis
on:

- encrypted traffic classification and analysis
- traffic representation learning and traffic foundation models
- website fingerprinting attacks and defenses
- malicious/encrypted traffic detection and anomaly detection
- VPN, Tor, DoH, QUIC, encrypted tunnels, app fingerprinting, IoT traffic
- few-shot, semi-supervised, open-set, domain-shift, and robustness settings

When a paper is in this scope, evaluate it against computer security/networking
standards, not generic social-science review criteria.

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

## High-Value Prior Evidence

These are field priors from the user's knowledge base. Use them as review
questions and literature anchors, not as automatic verdicts.

- Dataset false prosperity: ISCX-VPN and USTC-TFC contain large fractions of
  unencrypted traffic according to SoK 2025, so high scores on old "encrypted"
  datasets may reflect plaintext or obsolete crypto artifacts.
- SII shortcut learning: strong identifying information such as MAC, IP, ports,
  TCP sequence/ack numbers, timestamps, and flow identifiers can dominate model
  performance. Papers must disclose whether these fields are present, removed,
  anonymized, or masked.
- Split leakage: per-packet splits can leak flow identity across train/test.
  For flow/session tasks, per-flow or stricter temporal/domain splits are the
  default expected evaluation.
- Payload learnability is conditional: claims that encrypted payload bytes carry
  semantic patterns must specify encryption protocol/cipher, TLS version, field
  selection, and leakage controls. Strong TLS 1.3/AES-GCM settings require extra
  skepticism.
- Metric choice matters: micro F1 and accuracy can hide poor minority-class
  performance. Macro F1, per-class recall, confusion matrices, and open-set
  metrics are often necessary.
- Traditional baselines remain strong: RF/XGBoost/LightGBM with well-designed
  expert features can outperform deep representations under stricter evaluation.
  Missing these baselines is a serious comparison flaw.
- Multimodal claims need ablations: payload, packet length, direction, timing,
  graph, and session-level modalities must be independently ablated and tested
  under leakage-controlled settings.
- Robustness claims need realistic perturbations: packet loss, duplication,
  reordering, delay, padding, fragmentation, NAT, VPN/Tor/proxy changes,
  temporal drift, environment shift, and adversarial evasion should match the
  threat model.

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

Strong objections:

- method depends on fields unavailable in the claimed deployment scenario
- paper claims encrypted-payload semantics without leakage controls
- deep model is compared only against weak or misconfigured baselines
- no ablation separates payload/length/timing/header/SII contributions
- no temporal/environment split despite deployment or robustness claims
- open-set or unknown detection uses closed-set metrics only
- "real-time" claims lack throughput, latency, memory, and hardware details

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

For traffic-classification papers, include these sections in addition to normal
ARS review output:

1. Leakage and shortcut-learning audit.
2. Dataset/encryption validity audit.
3. Split and metric validity audit.
4. Baseline and ablation adequacy.
5. Robustness and deployment realism.
6. Reproducibility and code/data availability.
7. Claim calibration: distinguish demonstrated evidence, plausible inference,
   unsupported extrapolation, and contradicted field prior.

Use verdict language conservatively:

- `Major flaw` when reported performance may be explained by leakage or SII.
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
- Robust website fingerprinting under adaptive attack/defense and drift.
- Few-shot/open-set malicious traffic detection under realistic deployment shift.
- Explainable and lightweight traffic classification for operational security.

## Experiment Planning Guidance

For `experiment-agent`, prefer experiments that isolate confounders:

- per-packet vs per-flow vs temporal/environment split comparison
- SII masking and header-field ablation
- payload-only, length-only, timing-only, direction-only, and multimodal ablation
- old datasets vs TLS 1.3 modern datasets
- macro F1, balanced accuracy, per-class recall, AUROC/AUPRC for imbalanced data
- shallow expert-feature baselines vs deep/foundation models under identical splits
- robustness to packet loss, duplication, reordering, padding, delay, and drift
- code/data reproducibility: seeds, preprocessing scripts, feature extraction,
  model checkpoints, and hardware/runtime records

## Personal Knowledge Base Integration

If the user provides or references the local knowledge base, first read:

- `00-dashboard/project-overview.md`
- `00-dashboard/research-map.md`
- `00-dashboard/open-questions.md`
- `00-dashboard/paper-registry.md`
- `08-comparisons/dataset-comparison-table.md`
- `08-comparisons/method-comparison-table.md`
- `09-claims/claims-index.md`
- `09-claims/contradictions.md`

For a specific cited paper, prefer its structured note under `03-paper-notes/`
before reading full parsed markdown or PDFs. Do not ingest or quote full PDFs
unless the user explicitly asks for source-level verification.
