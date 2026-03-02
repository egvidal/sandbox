# Milestone 06 — Thin Playbook Refactor

## Objective

Make playbooks orchestration-only and move business logic into reusable roles.

## Scope (from `milestones.md`)

- Heavy `pre_tasks` and inline logic from root playbooks.

## Tasks (authoritative)

- Move parsing/derivation blocks from `pre-check.yml`, `patch.yml`, and related playbooks into role task files.
- Add role-level input validation (`ansible.builtin.assert`) and clear failure messages.
- Keep playbook role ordering explicit and stable.

## Dependencies

- Milestone 5 naming stability.

## Exit Criteria

- Root playbooks primarily contain play metadata + role calls.
- Role entrypoints expose clear contracts and required vars.

## Risk

- High (behavior moves between layers; requires regression testing).

---

## Before vs After Comparison

### Structure Delta

| Area | Before | After |
|---|---|---|
| Playbook complexity | Playbooks include substantial data parsing and control logic | Playbooks are thin orchestrators calling roles |
| Validation location | Validation and assumptions partially implicit in playbooks | Role entrypoints contain explicit assertions/contracts |
| Responsibility boundaries | Blended orchestration + implementation | Clear separation: playbook orchestration, role implementation |

### File Delta (representative)

| File | Before (observed) | After (target) |
|---|---|---|
| `pmaas-dev_jboss8_fix_cluster/pre-check.yml` | extensive `pre_tasks` parsing healthcheck/report data | minimal preflight only; main logic in precheck role tasks |
| `pmaas-dev_jboss8_fix_cluster/patch.yml` | precheck report and exclusion logic in playbook | role-owned parsing/eligibility logic |
| `pmaas-dev_jboss8_fix_cluster/playbooks/*.yml` | mixed patterns | consistent thin-playbook pattern |

### Code Delta (example pattern)

**Before**

```yaml
pre_tasks:
  - name: Decode base64 file
    ansible.builtin.set_fact:
      healthcheck_file: "{{ healthcheck_b64.content | b64decode }}"
  - name: Get health-check status
    ansible.builtin.set_fact:
      lin_pc_healthcheck: "{{ healthcheck_file | json_query('status') }}"
```

**After**

```yaml
roles:
  - role: td-paas-linux-precheck
```

Role side:

```yaml
- name: Validate required precheck inputs
  ansible.builtin.assert:
    that:
      - td_paas_linux_precheck_report_directory is defined
```

---

## Implementation Notes for SME/SRE/Architect Review

- Treat this as an architecture change, not a syntax cleanup.
- Preserve existing execution order and conditional semantics while moving logic.
- Require regression evidence for standalone and clustered paths.

## Deliverables

- Thin root playbooks.
- Role contracts and validations documented.
- Regression matrix proving behavior parity.
