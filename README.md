# Academic Research Suite Traffic Codex

Codex skill package for academic research workflows in network traffic
classification and traffic-security papers.

This repository is a domain-adapted fork of the Codex package
[Imbad0202/academic-research-skills-codex](https://github.com/Imbad0202/academic-research-skills-codex),
which is the Codex-native sibling of
[Imbad0202/academic-research-skills](https://github.com/Imbad0202/academic-research-skills)
for Claude Code.

The active skill in this fork is:

```text
skills/academic-research-suite-traffic/
```

The original upstream Codex skill is still present at
`skills/academic-research-suite/` for comparison. The old upstream README is
kept as `UPSTREAM_README.md`.

## What Changed

This fork keeps the upstream ARS Codex workflows and adds a traffic-classification
domain overlay:

```text
skills/academic-research-suite-traffic/
  SKILL.md
  manifest.json
  agents/openai.yaml
  references/traffic-classification-domain.md
  ars/
  codex/
```

The overlay is derived from
[GSTovey/Traffic-Classification-Paper-LLM-Wiki](https://github.com/GSTovey/Traffic-Classification-Paper-LLM-Wiki)
at snapshot `bff8f9e283a37cb62ed8f28f7a7b6243463a8ee4`.

The full PDF library and parsed paper corpus are not vendored. The skill carries
a compact field guide for:

- encrypted traffic classification and analysis
- traffic representation learning and traffic foundation models
- website fingerprinting attacks and defenses
- malicious/encrypted traffic detection and anomaly detection
- VPN, Tor, DoH, QUIC, encrypted tunnels, app fingerprinting, and IoT traffic
- few-shot, semi-supervised, open-set, domain-shift, and robustness evaluation

## Install In Codex

Install from this fork:

```bash
python3 "$HOME/.codex/skills/.system/skill-installer/scripts/install-skill-from-github.py" \
  --repo GSTovey/academic-research-skills-suite-traffic-codex \
  --ref main \
  --path skills/academic-research-suite-traffic \
  --method git
```

Open a new Codex conversation after installation so the skill registry refreshes.

Verify with:

```text
/skills
```

Expected skill name:

```text
academic-research-suite-traffic
```

## Usage

Invoke the skill explicitly:

```text
Use $academic-research-suite-traffic with ars-reviewer to review this encrypted
traffic classification paper. Focus on leakage, SII shortcuts, benchmark
validity, baselines, ablations, macro/micro metrics, and reproducibility.
```

Plain ARS aliases are also supported inside the single Codex skill:

```text
ars-reviewer review this website fingerprinting paper
ars-lit-review traffic foundation models for encrypted traffic classification
ars-plan a paper about per-flow evaluation for encrypted traffic representation
ars-full start a research-to-paper pipeline for robust malicious traffic detection
```

If slash-prefixed input reaches the model, these work too:

```text
/ars-reviewer
/ars-plan
/ars-full
```

If the Codex client intercepts slash commands, use the plain alias form.

## Workflows

`deep-research`

Use for research question refinement, literature review, systematic review,
fact-checking, and evidence synthesis in traffic-security topics.

`academic-paper`

Use for paper planning, outline, drafting, abstract, revision, citation checks,
format conversion, and disclosure. The traffic overlay pushes the writer to
handle dataset validity, leakage controls, baselines, metrics, and claim
calibration.

`academic-paper-reviewer`

Use for manuscript review and simulated peer review. For traffic-classification
papers, the reviewer must explicitly audit:

- per-packet vs per-flow/session/time/environment splits
- SII leakage through MAC, IP, ports, TCP sequence/ack numbers, timestamps, and
  flow identifiers
- dataset encryption quality and obsolete-cipher/plaintext contamination
- macro F1, per-class recall, open-set metrics, and class imbalance
- shallow expert-feature baselines versus deep/foundation models
- modality ablations for payload, length, direction, timing, graph, and session
  representations
- robustness to packet loss, duplication, reordering, padding, delay, NAT,
  VPN/Tor/proxy changes, temporal drift, and adversarial evasion
- code/data availability and reproducibility

`academic-pipeline`

Use for end-to-end research-to-paper work with staged checkpoints, integrity
gates, review, revision, finalization, and process records.

`experiment-agent`

Use for benchmark design, reproducibility validation, and experiment planning.
Recommended experiments include SII masking, split comparison, modality
ablations, modern TLS 1.3 benchmarks versus legacy datasets, and robustness
stress tests.

## Field Assumptions

The overlay encodes review priors from the user's knowledge base. It treats the
following as high-risk claims unless the manuscript proves otherwise:

- high accuracy on legacy "encrypted" datasets without checking plaintext or
  obsolete cipher contamination
- per-packet splits for flow/session-level classification
- models using SII or implicit flow identifiers
- encrypted-payload semantic claims without TLS/cipher and leakage controls
- deep representation models compared only against weak baselines
- micro F1 or accuracy reported without macro/per-class analysis
- robustness claims without realistic network perturbations or environment shift

These are not automatic rejection rules. They are mandatory review questions.

## Provenance

- Fork repository: [GSTovey/academic-research-skills-suite-traffic-codex](https://github.com/GSTovey/academic-research-skills-suite-traffic-codex)
- Codex upstream: [Imbad0202/academic-research-skills-codex](https://github.com/Imbad0202/academic-research-skills-codex)
- Claude Code upstream: [Imbad0202/academic-research-skills](https://github.com/Imbad0202/academic-research-skills)
- Domain knowledge base: [GSTovey/Traffic-Classification-Paper-LLM-Wiki](https://github.com/GSTovey/Traffic-Classification-Paper-LLM-Wiki)

License follows the upstream package: CC BY-NC 4.0.
