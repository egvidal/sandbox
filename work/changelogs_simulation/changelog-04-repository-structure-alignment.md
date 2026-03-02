# Changelog — Milestone 04: Repository Structure Alignment

## Goal of the Milestone

Align repository layout with td-paas Ansible structure conventions and improve operational discoverability.

## Files Changed

- `pmaas-dev_jboss8_fix_cluster/playbooks/` (new directory; migrated playbooks)
- `pmaas-dev_jboss8_fix_cluster/group_vars/` (new)
- `pmaas-dev_jboss8_fix_cluster/host_vars/` (new)
- Migrated playbooks from repo root to `playbooks/`:
  - `pre-check.yml`, `patch.yml`, `post-check.yml`, `health-check.yml`, `conn-check.yml`, `reporting.yml`, `sos-report.yml`, etc.
- `pmaas-dev_jboss8_fix_cluster/README.md` and runbooks/docs references
- AWX/Tower job template path mappings (configuration update)

## Changes Implemented

- Introduced `playbooks/` as canonical orchestration entrypoint location.
- Moved environment-specific variable definitions from role defaults/playbook vars into `group_vars` and `host_vars`.
- Updated documentation, scripts, and template references to the new path model.
- Preserved role implementation logic under `roles/` without redesign.

## Expected Impact

- Reduces cognitive load for operations and onboarding.
- Improves variable scoping discipline and environment separation.
- Reduces long-term maintenance cost by enforcing a predictable project layout.

## Post Implementation Validation Performed

- Executed end-to-end dry runs using `playbooks/*` entrypoints.
- Validated AWX/Tower job templates resolve updated playbook paths.
- Verified variable precedence after migration to `group_vars`/`host_vars`.
- Confirmed docs/runbooks command examples match actual repository structure.

## Rollback Plan

- Restore prior root playbook locations from migration commit.
- Repoint AWX/Tower templates to legacy paths.
- Temporarily maintain dual-path compatibility wrappers if rollback window overlaps active operations.

## Next Steps

- Begin Milestone 05 naming convention migration after structure stabilization sign-off.
- Freeze new file path conventions as architectural baseline.

> Note: This changelog represents a simulated implementation record for governance planning and review.
