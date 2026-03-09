# Milestone 4 — Normalize playbook hygiene (mechanical sweep)

## Goal of the milestone

Make the root playbooks consistent, predictable, and lint-friendly by standardizing play-level metadata and boolean style without changing runtime logic.

## Changes to be implemented

- Ensure every play explicitly declares `hosts`, `become`, and `gather_facts`.
  - Rationale: play-level execution semantics should be explicit; relying on implicit defaults increases risk and makes reviews harder.
  - Benefit: predictable privilege and fact-gathering behavior across the workflow.
  - Risk: if any play previously relied on implicit defaults, behavior may change; mitigate by setting values to match current intent.
- Normalize YAML booleans to `true`/`false` (replace `yes`/`no`).
  - Rationale: the repo standards require `true/false`; this removes lint noise and ambiguity.
  - Benefit: consistent style across roles and playbooks.
  - Risk: very low; mechanical change.
- Keep playbooks orchestration-focused and avoid refactors in this milestone.
  - Rationale: this milestone is intentionally low-risk and should not alter the patching workflow logic.

## Files in scope

- Root playbooks:
  - `pmaas-dev_jboss8_fix_cluster/cleaning.yml`
  - `pmaas-dev_jboss8_fix_cluster/cluster-group-cleanup.yml`
  - `pmaas-dev_jboss8_fix_cluster/conn-check.yml`
  - `pmaas-dev_jboss8_fix_cluster/delete-too-old-inventory.yml`
  - `pmaas-dev_jboss8_fix_cluster/elk-install.yml`
  - `pmaas-dev_jboss8_fix_cluster/health-check.yml`
  - `pmaas-dev_jboss8_fix_cluster/meta-cluster.yml`
  - `pmaas-dev_jboss8_fix_cluster/patch.yml`
  - `pmaas-dev_jboss8_fix_cluster/post-check.yml`
  - `pmaas-dev_jboss8_fix_cluster/pre-check.yml`
  - `pmaas-dev_jboss8_fix_cluster/remove-package.yml`
  - `pmaas-dev_jboss8_fix_cluster/reporting.yml`
  - `pmaas-dev_jboss8_fix_cluster/sos-report.yml`

## Expected impact

- Expected benefits:
  - Root entrypoints become easier to lint, review, and maintain.
  - Privilege/facts behavior becomes explicit and consistent across stages.
- Potential risks:
  - A small number of plays may need careful selection of `become` and `gather_facts` to preserve existing behavior (e.g., localhost/controller-only plays).
