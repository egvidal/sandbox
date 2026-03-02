# Milestone 02 — Secrets and Credentials Remediation

## Objective

Eliminate plaintext credentials from repository-tracked YAML and move to secure secret injection.

## Scope (from `milestones.md`)

- Root playbooks and role defaults containing controller/password variables.

## Tasks (authoritative)

- Replace plaintext values with variable references only.
- Move secrets to vault or secret manager-backed vars.
- Introduce required-variable assertions for secret inputs.
- Update README with secure variable injection instructions.

## Candidate Files (from `milestones.md`)

- `pmaas-dev_jboss8_fix_cluster/cluster-group-cleanup.yml`
- `pmaas-dev_jboss8_fix_cluster/delete-too-old-inventory.yml`
- `pmaas-dev_jboss8_fix_cluster/roles/lin_conn/defaults/main.yml`
- `pmaas-dev_jboss8_fix_cluster/roles/lin_hc/defaults/main.yml`
- `pmaas-dev_jboss8_fix_cluster/roles/lin_meta_cluster/defaults/main.yml`

## Dependencies

- Milestone 1 recommended first.

## Exit Criteria

- Secret scan across repo shows no hardcoded passwords/tokens.
- All secret-dependent runs use vault/external variables.

## Risk

- Low to medium (runtime failures if secret wiring is incomplete).

---

## Before vs After Comparison

### Structure Delta

| Area | Before | After |
|---|---|---|
| Secret source | Secrets defined in tracked YAML | Secrets sourced from vault/external secure backend |
| Runtime contract | Secret vars may be optional/implicit | Secret vars are validated early with assertions |
| Operational guidance | Security usage partially implicit | README includes secure injection/run patterns |

### File Delta (current observed vs target)

| File | Before (observed) | After (target) |
|---|---|---|
| `cluster-group-cleanup.yml` | `controller_password: "my-password"` | `controller_password: "{{ td_paas_controller_password }}"` (value supplied outside repo) |
| `delete-too-old-inventory.yml` | `controller_password: "my-password"` | externalized secret reference only |
| `roles/lin_conn/defaults/main.yml` | `controller_password: "my-password"` | no plaintext; default removed or replaced with secure var reference |
| `roles/lin_hc/defaults/main.yml` | `controller_password: "my-password"` | no plaintext; secure runtime input |
| `roles/lin_meta_cluster/defaults/main.yml` | hardcoded controller password | no plaintext; secure runtime input |

### Code Delta (example pattern)

**Before**

```yaml
controller_password: "my-password"
```

**After**

```yaml
controller_password: "{{ td_paas_controller_password }}"
```

And in role/play validation:

```yaml
- name: Validate required secret inputs
  ansible.builtin.assert:
    that:
      - td_paas_controller_password is defined
      - td_paas_controller_password | length > 0
    fail_msg: "Controller password must be provided via vault/external vars"
```

---

## Implementation Notes for SME/SRE/Architect Review

- All secret-bearing defaults should be treated as policy violations.
- This milestone must include both code change and operational handoff documentation.
- Validate in CI with secret scan and in a dry run with vault-provided variables.

## Deliverables

- Secret-free tracked YAML.
- Secret validation tasks.
- Updated secure run instructions in README.
