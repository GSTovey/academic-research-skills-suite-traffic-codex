# Changelog

All notable changes to the Codex package are documented here.

## [0.1.9-traffic.3] - 2026-11-04

### Changed
- Grounded the traffic overlay in selected primary-text passages from 22 papers,
  recording source and passage hashes, section/line locators, mechanisms,
  evidence limits, and derived skill rules in a portable ledger.
- Added conditional guidance for header shortcuts, transductive graphs,
  filtering, noisy labels, multi-teacher supervision, trace-level labels,
  causal prefixes, stopping policies, and low-base-rate false alarms.
- Replaced blanket checklist and model-family judgments with claim-specific
  controls. Writing-only requests do not initiate experiments.
- Clarified that the corpus includes uncommitted local sources; neither the
  base commit nor L3 metadata alone verifies all evidence. Upstream ARS remains
  pinned and KB consensus records are unchanged.
- Made optional adapter manifest paths relative to the traffic skill so its
  validation does not silently depend on the uninstalled generic sibling.

## Unreleased

### Added
- Added a 2026 traffic-classification evidence update reference synthesized
  from the updated Traffic_Papers corpus and the 2024--2026 top-venue deep-
  analysis coverage. It adds operational checks for observation-layer
  alignment, open-world calibration, drift, adaptive attack/defense,
  trace-level semantics, and end-to-end deployment cost.
- Added an optional Codex full-runtime adapter profile under
  `skills/academic-research-suite/codex/`, including deterministic route
  planning, Codex agent-team templates, a disabled-by-default hook pack, and
  adapter quality gates. Default ARS Codex behavior remains inline role-prompt
  execution.

### Security
- Added Codex security boundaries for untrusted research inputs, cross-model
  consent, local adapter filesystem handling, and fixed-host bibliographic API
  lookups.

### Changed
- Updated the traffic overlay provenance to the 2026-10-12 knowledge-base
  commit and marked the adapter as `0.1.9-traffic.2`.
- Kept pending or unknown literature records out of field-consensus guidance.

## [0.1.9] - 2026-06-01

### Changed
- Vendored upstream ARS from `96b82e82142dc95f117595c207d3e150b078e411` (`v3.9.4.2`) to `4c38571798da4b1ed604ec2c1e01a6f66a7de5a7` (`v3.10.0` plus release-manifest alignment).
- Added ARS v3.10 runtime content, including the triangulation policy layer, eval harness/gold sets, Schema 11 commitment-ledger refactor, domain-evidence/version-family updates, and scoped-write guard scripts.
- Added newly vendored upstream `README.zh-CN.md`, `README.ja-JP.md`, `evals/`, `conftest.py`, and new `ars-*` command recipes.
- Kept Codex-specific overlays: single root router skill, `WORKFLOW.md` vendored workflow entry files, Codex setup/architecture docs, nested-path lint patches, excluded showcase PDFs, and macOS Bash 3.2 audit wrapper compatibility.
- Clarified beginner install instructions by using `python3` in command
  examples and documenting the `python` fallback when it points to Python 3.
- Added community acknowledgements for beginner-install feedback and issue
  discussion support.

## [0.1.8] - 2026-05-19

### Changed
- Vendored upstream ARS from `74413a42571867abece7b8b76f7a24ac472ab2a0` (`v3.9.0`) to `96b82e82142dc95f117595c207d3e150b078e411` (`v3.9.4.2`).
- Added ARS v3.9.1 client hardening, v3.9.2 phase-boundary routing discipline, v3.9.3 shared client utilities, and v3.9.4/v3.9.4.1 temporal verification runtime content.
- Kept Codex-specific overlays: single root router skill, `WORKFLOW.md` vendored workflow entry files, Codex setup/architecture docs, nested-path lint patches, and macOS Bash 3.2 audit wrapper compatibility.

### Notes
- Upstream v3.9.4.2 changes only `.github` CI/release-gate files, which are intentionally excluded from this Codex package. The manifest still pins the exact v3.9.4.2 commit for provenance.

## [0.1.7] - 2026-05-17

### Changed
- Aligned the Codex package with upstream ARS `v3.9.0`.
