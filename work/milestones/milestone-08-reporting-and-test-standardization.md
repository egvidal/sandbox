# Milestone 08 — Reporting and Test Standardization

## Objective

Ensure reliable validation and consistent reporting after structural and behavioral refactors.

## Scope (from `milestones.md`)

- Role tests, reporting consistency, custom module validation.

## Tasks (authoritative)

- Normalize role test naming (`tests/test.yml` vs `tests/main.yml`) and make execution uniform.
- Add smoke/idempotency checks for core roles.
- Validate custom modules under `library/` and remove/archive obsolete duplicates (`oracle_info_orig.py`).
- Ensure reporting templates still match post-refactor variable names.

## Dependencies

- Milestones 5–7.

## Exit Criteria

- Core role tests execute consistently.
- Reporting artifacts are generated and valid for all major workflows.

## Risk

- High (integration-level verification).

---

## Before vs After Comparison

### Structure Delta

| Area | Before | After |
|---|---|---|
| Role test entrypoint | Mixed conventions (`tests/test.yml` and `tests/main.yml`) | Standardized and documented test entrypoint convention |
| Custom module hygiene | Duplicate legacy module file present (`library/oracle_info_orig.py`) | obsolete duplicate archived/removed with active module path validated |
| Reporting compatibility | Variable rename risk may break templates | templates validated against updated variable schema |

### File Delta (expected)

| File/Area | Before | After |
|---|---|---|
| `roles/*/tests/` | inconsistent naming and invocation | unified test naming + runner documentation |
| `pmaas-dev_jboss8_fix_cluster/library/oracle_info_orig.py` | stale duplicate present | removed or archived outside active execution path |
| `roles/lin_reporting/templates/*.j2` and related role templates | may reference pre-migration variable names | aligned with post-migration variable namespace |

### Code/Execution Delta

| Before Pattern | After Pattern |
|---|---|
| Role test execution requires role-specific guesswork | Single documented test execution approach across roles |
| Reporting validity checked manually | Structured post-run template/schema verification |

---

## Implementation Notes for SME/SRE/Architect Review

- This milestone is where cross-role integration confidence is rebuilt after high-risk refactors.
- Preserve report schema compatibility requirements for downstream consumers.
- Capture test evidence artifacts for governance review.

## Deliverables

- Unified role test framework conventions.
- Validated/clean `library/` module set.
- Reporting compatibility test results for key workflows.
