# Milestone 07 — Shell/Command Hardening in Critical Paths

## Objective

Improve idempotency, determinism, and operational safety in patching and cluster control paths.

## Scope (from `milestones.md`)

- Shell-heavy files, especially patch and cluster operations.

## Priority Hotspots (authoritative)

- `pmaas-dev_jboss8_fix_cluster/roles/lin_patch/tasks/patch.yml`
- `pmaas-dev_jboss8_fix_cluster/roles/lin_patch/tasks/main.yml`
- `pmaas-dev_jboss8_fix_cluster/roles/lin_pc/tasks/main.yml`
- `pmaas-dev_jboss8_fix_cluster/roles/lin_post/tasks/main.yml`
- `pmaas-dev_jboss8_fix_cluster/roles/lin_cluster/tasks/oracle/*`
- `pmaas-dev_jboss8_fix_cluster/roles/lin_cluster/tasks/pacemaker/*`

## Tasks (authoritative)

- Replace shell/command with declarative modules wherever possible.
- For unavoidable shell/command:
  - define strict `changed_when` / `failed_when`
  - add `creates`/`removes` where applicable
  - enforce safe shell execution options for pipelines
- Remove ambiguous side effects and improve observability.

## Dependencies

- Milestone 6 complete.

## Exit Criteria

- Second-run idempotency materially improved for patched test scope.
- Command tasks are justified and semantically bounded.

## Risk

- Very high (touches operational core logic).

---

## Before vs After Comparison

### Structure/Behavior Delta

| Area | Before | After |
|---|---|---|
| Patch execution semantics | Command-heavy with variable changed semantics | Declarative where possible, explicit change/failure semantics otherwise |
| Cluster operations | Mixed shell patterns and side effects | Controlled tasks with explicit guardrails and bounded outcomes |
| Observability | Harder to reason from command output | Deterministic task outcomes and audit-friendly status signals |

### File Delta (targeted modernization)

| File Area | Before (observed pattern) | After (target pattern) |
|---|---|---|
| `roles/lin_patch/tasks/patch.yml` | repeated `shell: yum -y update ...` variants | use package modules or wrap commands with strict `changed_when`/`failed_when` and clear justification |
| `roles/lin_cluster/tasks/oracle/*` | shell-driven `srvctl`/`crsctl` flow | guarded command blocks with explicit retries, failure gates, and idempotency hints |
| `roles/lin_post/tasks/main.yml` | shell/command + implicit changes | explicit outcome semantics and safer command wrappers |

### Code Delta (example pattern)

**Before**

```yaml
- name: Update packages
  ansible.builtin.shell: yum -y update httpd*
```

**After (when module available)**

```yaml
- name: Ensure target packages are latest
  ansible.builtin.package:
    name: "{{ td_paas_linux_patch_packages }}"
    state: latest
```

**After (when command unavoidable)**

```yaml
- name: Execute vendor-specific command safely
  ansible.builtin.shell: |
    set -euo pipefail
    <command>
  args:
    executable: /bin/bash
  register: cmd_result
  changed_when: "'already up-to-date' not in cmd_result.stdout"
  failed_when: cmd_result.rc != 0
```

---

## Implementation Notes for SME/SRE/Architect Review

- This milestone should be delivered in narrow PR slices by subsystem.
- Require controlled test environments for cluster and Oracle operations.
- Preserve existing business policy while improving execution semantics.

## Deliverables

- Hardened patch/cluster task flows.
- Command-usage justification register.
- Idempotency evidence (first/second run comparison).
