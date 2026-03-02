# Milestone 05 — Naming Convention Migration (Repo, Roles, Variables, Tags)

## Objective

Achieve full td-paas naming compliance for repository identity, role identifiers, variable prefixes, and tags.

## Scope (from `milestones.md`)

- Repository naming, role names, and variable prefixes.

## Tasks (authoritative)

- Decide and approve final naming map (old → new).
- Rename roles from `lin_*` to `td-paas-<domain>-<component>` style.
- Migrate variable prefixes role-by-role.
- Normalize tags toward `td-paas`, domain, component tags.
- Keep compatibility shims/aliases temporarily where needed.

## Dependencies

- Milestones 3 and 4 complete.

## Exit Criteria

- No legacy role names remain in active playbooks.
- Variable names in defaults/vars follow role-prefix rules.
- Tags align with naming conventions; `always` removed unless justified.

## Risk

- High (wide reference impact across playbooks, roles, inventory, job templates).

---

## Before vs After Comparison

### Structure/Identity Delta

| Area | Before | After |
|---|---|---|
| Repository naming | `pmaas-dev_jboss8_fix_cluster` | td-paas-compliant repository name (approved in naming map) |
| Role directory naming | `roles/lin_*` model | `roles/td-paas-<domain>-<component>` model |
| Variable namespace | Mixed `lin_*` plus generic vars | role-prefixed variables aligned to role ID |
| Tag taxonomy | mixed tags (`precheck`, `suse`, `always`, etc.) | normalized tags with td-paas + domain + component |

### File Delta (example mapping for planning)

| Before | After (proposed target style) |
|---|---|
| `roles/lin_pc/` | `roles/td-paas-linux-precheck/` |
| `roles/lin_patch/` | `roles/td-paas-linux-patch/` |
| `roles/lin_post/` | `roles/td-paas-linux-postcheck/` |
| `roles/lin_hc/` | `roles/td-paas-linux-healthcheck/` |

### Code Delta (example patterns)

**Before**

```yaml
roles:
  - { role: lin_patch, tags: [ patch, suse, redhat ] }

lin_patch_reboot_timeout: 3600
```

**After**

```yaml
roles:
  - { role: td-paas-linux-patch, tags: [ td-paas, linux, patch ] }

td_paas_linux_patch_reboot_timeout: 3600
```

---

## Implementation Notes for SME/SRE/Architect Review

- Naming map approval is a formal gate; do not start renaming before sign-off.
- Compatibility aliases should be temporary and tracked with removal date.
- AWX/Tower job templates, inventories, and role references must be migrated in lockstep.

## Deliverables

- Approved old→new naming matrix.
- Renamed roles and migrated variable namespace.
- Updated tags and references across playbooks/docs/job templates.
