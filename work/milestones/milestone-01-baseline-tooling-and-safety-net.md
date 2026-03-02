# Milestone 01 — Baseline Tooling and Safety Net

## Objective

Establish repeatable quality gates before refactoring.

## Scope (from `milestones.md`)

- Add/standardize lint and validation tooling.
- Add dependency pinning scaffolding.

## Tasks (authoritative)

- Add `collections/requirements.yml` with pinned versions.
- Add `roles/requirements.yml` with pinned versions.
- Add/refresh lint configs (`.ansible-lint`, `.yamllint`) if missing.
- Add a CI job (or local task script) to run:
  - `yamllint`
  - `ansible-lint`
  - selected `ansible-playbook --check --diff`

## Dependencies

- None.

## Exit Criteria

- Lint jobs run successfully in pipeline (or documented local execution path).
- Dependency files are present and version-pinned.

## Risk

- Low (non-functional changes).

---

## Before vs After Comparison

### Structure Delta

| Area | Before | After |
|---|---|---|
| Dependency pinning | No `collections/requirements.yml` and no `roles/requirements.yml` in repo root | Both files present with pinned versions |
| Lint policy | No guaranteed centralized lint policy | `.ansible-lint` and `.yamllint` established/standardized |
| Validation workflow | Validation is ad-hoc/manual | CI/local automation runs lint + selected check-mode validations |

### File Delta

| File | Before | After |
|---|---|---|
| `collections/requirements.yml` | Missing | Created with exact collection pins (e.g., `awx.awx` and others actually used) |
| `roles/requirements.yml` | Missing | Created with exact role pins (external role dependencies only) |
| `.ansible-lint` | Missing or non-standard | Present and aligned with td-paas standards |
| `.yamllint` | Missing or non-standard | Present and aligned with YAML policy |
| CI workflow/task runner | Lint/check not enforced | Pipeline/task script enforces baseline checks |

### Code/Execution Delta

| Before Pattern | After Pattern |
|---|---|
| Lint/check run manually and inconsistently | Single reproducible command path in CI/local scripts |
| Implicit dependency resolution | Deterministic dependency resolution from pinned files |

---

## Implementation Notes for SME/SRE/Architect Review

- This milestone is intentionally non-functional and should be merged first to reduce risk in later milestones.
- Version pinning must be explicit and reviewable to prevent drift.
- Any lint rule exceptions must be documented with rationale.

## Deliverables

- New pinning files and lint configs.
- CI/local validation workflow documentation.
- Baseline run evidence (lint/check command outputs).
