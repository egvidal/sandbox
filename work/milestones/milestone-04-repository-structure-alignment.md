# Milestone 04 — Repository Structure Alignment

## Objective

Align repository layout with the target Ansible operating model and improve maintainability.

## Scope (from `milestones.md`)

- Directory and orchestration organization.

## Tasks (authoritative)

- Create `playbooks/` and move root playbooks into it.
- Add `group_vars/` and `host_vars/` and move environment-specific values out of role defaults.
- Keep role implementations under `roles/`.
- Update docs and runbooks for new paths.

## Dependencies

- Milestone 3 complete (moved files are already style-compliant).

## Exit Criteria

- All execution examples use `playbooks/` paths.
- Operational runs succeed from new layout.

## Risk

- Medium (path/reference updates can break job templates if missed).

---

## Before vs After Comparison

### Structure Delta

| Area | Before | After |
|---|---|---|
| Playbook location | Top-level playbooks at repo root (e.g., `pre-check.yml`, `patch.yml`) | Centralized under `playbooks/` |
| Environment-specific vars | Mixed into role defaults/playbooks | Moved to `group_vars/` and `host_vars/` |
| Operational docs | Paths reference root-level playbooks | Docs updated for `playbooks/` execution model |

### File Delta (expected moves/additions)

| Before | After |
|---|---|
| `pmaas-dev_jboss8_fix_cluster/pre-check.yml` | `pmaas-dev_jboss8_fix_cluster/playbooks/pre-check.yml` |
| `pmaas-dev_jboss8_fix_cluster/patch.yml` | `pmaas-dev_jboss8_fix_cluster/playbooks/patch.yml` |
| `pmaas-dev_jboss8_fix_cluster/post-check.yml` | `pmaas-dev_jboss8_fix_cluster/playbooks/post-check.yml` |
| No `group_vars/` | `pmaas-dev_jboss8_fix_cluster/group_vars/` introduced |
| No `host_vars/` | `pmaas-dev_jboss8_fix_cluster/host_vars/` introduced |

### Code/Reference Delta

| Before Pattern | After Pattern |
|---|---|
| Job templates/scripts calling root files directly | Calls target `playbooks/*.yml` |
| Role defaults carrying environment values | `group_vars`/`host_vars` hold environment values |

---

## Implementation Notes for SME/SRE/Architect Review

- Maintain temporary compatibility wrappers/symlinks only if cutover constraints require them.
- Update AWX/Tower template paths in same change window or controlled rollout phase.
- Include rollback instructions (restore old path mapping) for operational resilience.

## Deliverables

- New structure in place (`playbooks/`, `group_vars/`, `host_vars/`).
- Updated runbooks/README execution commands.
- Validated operational run from new path topology.
