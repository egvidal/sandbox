# Milestone 03 — Mechanical Standards Sweep (FQCN + Booleans + Play Declarations)

## Objective

Bring repository-wide Ansible syntax/style into standards compliance with minimal functional behavior change.

## Scope (from `milestones.md`)

- All playbooks and role task files.

## Tasks (authoritative)

- Convert short module/action names to FQCN (`ansible.builtin.*`).
- Convert `yes/no` to `true/false`.
- Ensure all plays declare explicit `hosts`, `become`, `gather_facts`.
- Replace `local_action` with explicit `delegate_to: localhost` patterns.

## Dependencies

- Milestone 2 completed (avoid touching same files twice for security fields).

## Exit Criteria

- `ansible-lint` no longer reports FQCN and truthy-value violations.
- All root playbooks pass syntax check.

## Risk

- Medium (large mechanical touch set; mitigated by staged PRs).

---

## Before vs After Comparison

### Structure Delta

| Area | Before | After |
|---|---|---|
| Module invocation style | Mixed short names + FQCN | Consistent FQCN (`ansible.builtin.*`) |
| Boolean style | Mixed `yes/no` and `true/false` | `true/false` only |
| Play declaration consistency | `become` and fact policy not always explicit | Every play explicitly declares `hosts`, `become`, `gather_facts` |

### File Delta (representative)

| File | Before (observed) | After (target) |
|---|---|---|
| `pmaas-dev_jboss8_fix_cluster/pre-check.yml` | uses `local_action` and mixed short actions | explicit `delegate_to: localhost` + FQCN |
| `pmaas-dev_jboss8_fix_cluster/reporting.yml` | short `setup:` action and `gather_facts: no` | `ansible.builtin.setup` and `gather_facts: false` |
| `pmaas-dev_jboss8_fix_cluster/roles/lin_pc/defaults/main.yml` | `lin_pc_reboot: yes` etc. | `lin_pc_reboot: true` etc. |
| `pmaas-dev_jboss8_fix_cluster/roles/lin_reporting/defaults/main.yml` | `lin_report_*: no` values | `lin_report_*: false` values |

### Code Delta (example patterns)

**Before**

```yaml
- name: Check if file exists
  stat:
    path: /tmp/example

lin_pc_reboot: yes
```

**After**

```yaml
- name: Check if file exists
  ansible.builtin.stat:
    path: /tmp/example

lin_pc_reboot: true
```

---

## Implementation Notes for SME/SRE/Architect Review

- This is a broad, mechanical pass; enforce strict review template to avoid accidental functional changes.
- Recommended execution: split PRs by area (`root-playbooks`, `roles/lin_patch`, `roles/lin_cluster`, etc.).
- Lint baseline from Milestone 1 is mandatory for acceptance.

## Deliverables

- Standards-compliant YAML syntax/style across playbooks and roles.
- Lint proof for FQCN/truthy rule compliance.
- Syntax-check proof for root playbooks.
