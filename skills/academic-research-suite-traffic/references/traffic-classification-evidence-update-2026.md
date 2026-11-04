# Traffic Classification Evidence Update (2026)

Use this reference after `traffic-classification-domain.md` when the task uses
recent 2024--2026 traffic-security literature, the user's updated knowledge
base, or claims about modern deployment. It is an evidence map for routing and
review decisions, not a replacement for source verification.

## Provenance and evidence boundary

This update synthesizes the user's Traffic_Papers working corpus through the
2026-11-03 deep-analysis coverage report. The strongest reusable evidence comes
from the L3 analyses of 28 traffic-security papers from CCS, NDSS, IEEE S&P,
and USENIX Security (2024--2026), together with the previously audited SoK,
Sweet Danger, and related benchmark papers. A separate 2026 INFOCOM/TDSC/TIFS/
TON intake batch is recorded as candidate evidence; entries marked
`pending_review`, `unknown`, or summary-only must not be promoted to field
consensus or used as verified numeric evidence.

Treat every synthesis below as conditional on the paper's observation point,
label source, split unit, protocol, and threat model. Preserve the distinction
between an author claim, a replicated result, a cross-paper inference, and an
open hypothesis.

## Evidence updates that change default review behavior

| Evidence pattern | Recent anchors in the corpus | Operational rule |
|---|---|---|
| Benchmark and shortcut failures recur across models | *SoK: Decoding the Enigma of Encrypted Network Traffic Classifiers* (S&P 2025); *The Sweet Danger of Sugar* (SIGCOMM 2025); *Bias in the Shadows* (arXiv 2026); the 2026 replicability and label-provenance records | Treat a high score as conditional until SII, implicit flow identifiers, encryption validity, duplicate handling, and split unit are exposed. Require a frozen-encoder or representation-transfer test when a paper claims useful pretraining. |
| Representation gains depend on modality, protocol, and evaluation protocol | MM4flow (CCS 2025); TrafficFormer (S&P 2025); NetMamba+/MambaTIG/HyTMTC; HF-Transformer; BPF-DAG; the multimodal preprocessing and alignment analyses | Do not infer that Transformer, Mamba, graph, multimodal, or generative pretraining is intrinsically superior. Compare matched inputs, matched budgets, frozen and fine-tuned settings, and payload/length/direction/timing/header ablations. Record any alignment tax or single-modality degradation. |
| Open-world, drift, and few-shot settings are now first-class tasks | FEC-OSL; PP-OpenNet; graph-based open-world classification; WF-TFC; cross-domain few-shot WF; Drift-Aware Adaptive WF; practical few-shot multi-tab WF | Separate closed-set classification from unknown rejection, calibration, discovery, and adaptation. Require unseen class, time, user, website, environment, or browser splits when the claim uses open-world, continual, or real-world language. |
| WF attack/defense results are threat-model dependent | CountMamba, Swallow, GAPDiS, Palette, Cease/FRUGAL, Stinger, TrapFlow, multi-tab and real-world WF analyses | State attacker knowledge, defense transform, target scope, trace truncation, multi-tab composition, and drift explicitly. Test adaptive and cross-defense transfer; do not call a defense robust from a single closed-world attack. |
| Robustness is moving from informal perturbations to calibrated or adversarial tests | CertTA; hard-label black-box evasion; Beyond RTT; universal live black-box perturbations; DualGuard and label/noise/drift studies | Distinguish certified robustness, empirical perturbation robustness, adversarial evasion, and drift adaptation. Report the perturbation budget, feasibility constraints, calibration target, and failure cases separately. |
| Deployment claims require a systems path, not only model latency | SketchFeature; Proteus; in-network obfuscation and inference systems; FENIX/Synecdoche and related programmable-data-plane work | Report capture/parser cost, feature-extraction memory, pipeline stages, p50/p95 latency, throughput, hardware, and control/data-plane placement. Model-only throughput cannot stand in for end-to-end deployment throughput. |
| The observation layer and target semantics must be aligned | Tracegram's trace-level MIL framing; Motion in the Clear; proxy/encapsulated-TLS analyses; Huma; NestTor's factorized Tor-over-Proxy decisions | Define whether the unit is packet, flow, burst, session, trace, or multi-flow and identify what the observer can actually see. Do not infer inner protocol state from an outer capture without an explicit observability argument. For composite labels, test factorized decisions against a flat classifier. |
| Real-world privacy leakage is broader than website labels | VR behavior reconstruction, DNS cache-timing and browser-configuration studies, Tor/proxy and encrypted-tunnel work | Treat user-behavior, browser, device, and environment inference as separate privacy outcomes. Do not collapse protocol identification, application identification, website inference, and behavior reconstruction into one “traffic classification” claim. |

## Required extraction schema for recent papers

For each paper, record these fields before comparing methods or results:

1. **Observation**: vantage point, directionality, outer/inner layer, available
   headers, packet loss/reordering, and whether the trace is complete.
2. **Unit and target**: packet/flow/burst/session/trace/multi-flow; label source;
   composite versus atomic target; known versus unknown classes.
3. **Signal groups**: payload bytes, length, direction, timing, header/context,
   graph/session relations, and SII. Mark each group as retained, randomized,
   masked, or unavailable.
4. **Protocol and environment**: TLS/QUIC/VPN/Tor/DoH version, cipher or
   encryption status, client/browser/device, collection time, and network site.
5. **Evaluation**: split unit, duplicate control, class balance, macro/per-class
   metrics, open-set/calibration metrics, temporal/environment transfer, and
   confidence intervals or repeated runs.
6. **Comparison fairness**: shallow expert-feature baselines, frozen versus
   unfrozen encoder, equal input prefix, parameter/compute budget, and matching
   preprocessing.
7. **Operational path**: preprocessing, feature extraction, hardware, memory,
   latency, throughput, code/data/checkpoint status, and what is unavailable.
8. **Boundary**: demonstrated evidence, author interpretation, plausible
   inference, counterevidence, and unresolved verification items.

## Workflow-specific instructions

### Deep research and literature review

Build a matrix grouped by task and observation layer before grouping by model
name. Synthesize only after aligning split unit, encryption validity, label
provenance, and threat model. Treat the 2026 intake batch as a discovery queue
until the structured note and source location support the claim.

### Manuscript review

Add these checks to the existing overlay checklist:

- observation/target alignment and trace completeness;
- atomic versus composite labels and factorized versus flat decisions;
- open-world rejection, calibration, and unknown-class protocol;
- temporal, environment, user, browser, and cross-wrapper transfer;
- adaptive attack/defense and perturbation feasibility;
- end-to-end deployment cost, not just neural inference time;
- whether a claimed semantic leak is actually a protocol, environment, or SII
  shortcut.

### Experiment planning

Use a staged protocol:

1. establish a leakage-controlled reference with per-flow/session and temporal
   splits, frozen and fine-tuned encoders, and shallow baselines;
2. run signal-group ablations and SII randomization before architecture ablations;
3. add unknown, drift, cross-environment, and adversarial evaluations that match
   the stated deployment threat model;
4. report end-to-end preprocessing and system cost, with uncertainty and failed
   conditions.

A result from one stage cannot be used as evidence for a stronger claim in a
later stage without the additional protocol.

### Paper writing and research-to-paper pipelines

Frame the contribution around the task and observable evidence first, then the
model. Use “under [split/protocol/observation]” in result claims. Keep benchmark
validity, representation quality, deployment feasibility, and security impact
as separate claims. Do not use “real-world,” “robust,” “generalizable,”
“open-world,” or “semantic” without naming the evaluated shift or threat model.

## Research-front prompts supported by the update

Use these as candidate questions, not settled conclusions:

- Under modern TLS 1.3/QUIC and strict flow/session splits, which observable
  signal groups remain stable across time, environment, and client?
- When does a multimodal model's complementarity outweigh its alignment tax and
  extra preprocessing cost?
- Can open-set calibration and unknown rejection remain reliable under drift and
  low base rates rather than only closed-set accuracy?
- Which WF defenses remain effective against adaptive, multi-tab, drift-aware,
  and many-to-many attackers?
- Can factorized decisions improve low-FPR detection when the outer observation
  hides multiple inner conditions, and when does the factorization fail?
- What is the smallest reproducible pipeline that preserves security evidence at
  line rate, including parsing and feature extraction?

## Do not over-generalize

Do not merge these findings into a single claim that “deep learning fails” or
“pretraining works.” The updated corpus supports task- and protocol-conditioned
conclusions: shallow models can remain strong under strict controls; some
representations survive transfer or cross-domain tests; open-world and drift
results are not interchangeable; and deployment evidence is distinct from
benchmark accuracy. Keep unresolved or summary-only records at `pending_review`
until their source locations and counterevidence are checked.
