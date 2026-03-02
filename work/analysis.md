# Analysis of Required Changes (Based on Standards)

## Scope

This analysis compares `pmaas-dev_jboss8_fix_cluster` against:

- `td-paas-standards-main/ansible-best-practices.md`
- `td-paas-standards-main/naming-conventions.md`

Coverage includes:

- Repository structure
- Root-level playbooks/config/docs
- Role entry files (`defaults/main.yml`, `vars/main.yml`, `tasks/main.yml`, `meta/main.yml`)
- High-impact task-pattern findings across role task files

---

## Executive Summary

The repository is functionally rich, but it is **not aligned** with td-paas standards in four critical areas:

1. **Naming model mismatch** (repo and role naming do not follow `td-paas-*` scheme).
2. **Structure mismatch** (playbooks are at root; no `playbooks/`; no dependency pinning files).
3. **Ansible style/lint mismatch** (many non-FQCN action calls; legacy booleans `yes/no`; heavy `shell`/`command`).
4. **Security/operability gaps** (plaintext controller passwords in defaults and playbooks; use of `always` tags).

---

## 1) Required Repository Structure Changes

### 1.1 Rename / reorganize

- Rename repository from `pmaas-dev_jboss8_fix_cluster` to a td-paas compliant name.
  - Suggested: `td-paas-linux-jboss8-cluster-playbooks`.
- Move root playbooks into `playbooks/` directory (thin orchestration only).
- Keep implementations in roles, but migrate role names from `lin_*` to td-paas scheme.
  - Example mapping:
    - `lin_pc` -> `td-paas-linux-precheck`
    - `lin_patch` -> `td-paas-linux-patch`
    - `lin_post` -> `td-paas-linux-postcheck`

### 1.2 Add missing standard files

- Add `collections/requirements.yml` and pin all collections (e.g., `awx.awx`, `community.general` if used).
- Add `roles/requirements.yml` for external role dependencies.
- Add `group_vars/` and `host_vars/` for environment-specific values currently hardcoded.

### 1.3 Keep playbooks thin

- Root/playbook logic currently performs substantial processing in `pre_tasks` and inline tasks.
- Move most data parsing and state derivation into role tasks to keep playbooks orchestration-only.

---

## 2) Cross-Cutting Changes Required in Existing Files

### 2.1 Naming and variable model

- Update variable prefixes from `lin_*` to role ID prefixes compliant with td-paas role naming.
- Avoid ambiguous generic vars (`report_directory`, `local_report_directory`, `days`, `dry_run`) unless role-prefixed.
- Standardize booleans as `true/false` (replace `yes/no`).

### 2.2 FQCN consistency

- Replace non-FQCN actions everywhere, e.g.:
  - `set_fact` -> `ansible.builtin.set_fact`
  - `stat` -> `ansible.builtin.stat`
  - `file` -> `ansible.builtin.file`
  - `copy` -> `ansible.builtin.copy`
  - `fetch` -> `ansible.builtin.fetch`
  - `slurp` -> `ansible.builtin.slurp`
  - `meta` -> `ansible.builtin.meta`
  - `include_role` / `include_tasks` / `import_tasks` -> `ansible.builtin.*`

### 2.3 Shell/command hardening and idempotency

- Replace shell commands with declarative modules where possible.
- When shell/command is unavoidable:
  - Add explicit `changed_when` and `failed_when`.
  - Add `creates:`/`removes:` where possible.
  - Use `set -euo pipefail` with `executable: /bin/bash` where pipelines are needed.

### 2.4 Secrets and controller credentials

- Remove plaintext credentials from all defaults/playbooks.
- Move credentials to vault/external secret backend.
- Keep only variable references in role defaults/playbooks.

### 2.5 Tag policy

- Remove/limit `always` tag usage unless strictly required by operational design.

---

## 3) File-by-File Recommendations (Root Files)

## `ansible.cfg`

- Change booleans to lowercase `true/false` style consistency (`host_key_checking`, `pipelining`).
- Keep tuning values, but add comments for rationale where non-default behavior is expected in prod.

## `README.md`

- Update naming references to new repo/role names.
- Add prerequisites and explicit dependency pinning references.
- Add execution examples using `playbooks/` paths.

## `cleaning.yml`

- Use `ansible.builtin.file` (currently short module name).
- Add explicit `become:` in play.
- Keep as thin playbook if moved to `playbooks/cleaning.yml`.

## `cluster-group-cleanup.yml`

- Replace short action `set_fact` with FQCN.
- Remove hardcoded controller credentials.
- Rename vars with role/purpose prefix (avoid generic `check_ssl`).

## `conn-check.yml`

- Convert `gather_facts: no` to `false`.
- Convert `validate_certs: no` to `false`.
- Move imported task paths behind role invocation where possible to keep playbook thin.

## `delete-too-old-inventory.yml`

- Remove hardcoded credentials.
- Rename generic vars (`days`, `dry_run`) with contextual prefixes.
- Validate critical inputs with `ansible.builtin.assert` before delete logic.

## `elk-install.yml`

- Add explicit `become` and `gather_facts` declarations in all plays.
- Remove plaintext credentials (`kibana_password` etc.) from playbook vars.
- Replace `enabled: yes` and `daemon_reload: yes` with `true`.
- Evaluate splitting into a dedicated role set (`elasticsearch/logstash/kibana`) for reuse.

## `health-check.yml`

- Convert `gather_facts: yes` to `true`.
- Keep as orchestration-only and delegate logic to role (already mostly compliant).

## `meta-cluster.yml`

- Convert booleans to `true/false`.
- Avoid unused vars (`local_healthcheck_report` appears not used in playbook body).
- Move controller data entirely to role defaults + vault/group_vars.

## `patch.yml`

- Convert `gather_facts: yes` to `true`.
- Add explicit `become` in play.
- Move pre-check JSON parsing from playbook into role pre-task file if possible.

## `post-check.yml`

- Convert `gather_facts: yes` to `true`.
- Keep thin orchestration (already close).

## `pre-check.yml`

- Convert `gather_facts: yes` to `true`.
- Replace `local_action` with `delegate_to: localhost` + FQCN module style.
- Move heavy pre_tasks logic into role to reduce playbook complexity.

## `remove-package.yml`

- Major hardening needed:
  - Replace short module names (`package_facts`, `copy`, `fetch`) with FQCN.
  - Replace shell parsing (`yum history`, `rpm -q`, `jq`) with safer module-driven approach where feasible.
  - Ensure robust `changed_when`/`failed_when` on shell tasks.
  - Replace hardcoded values (`snow_change_id`, package name defaults) with external vars.

## `reporting.yml`

- Convert `gather_facts: no` to `false`.
- Replace short action `setup:` with `ansible.builtin.setup`.
- Remove `always` tag usage unless essential.

## `sos-report.yml`

- Convert `gather_facts: yes` to `true`.
- Keep orchestration thin (already mostly compliant).

---

## 4) Role Entry-File Recommendations

## `roles/lin_conn/*`

- `defaults/main.yml`: remove plaintext `controller_password`; `validate_certs: no` -> `false`.
- `tasks/main.yml`: use `ansible.builtin.import_tasks`.
- Role rename required (`lin_conn` to td-paas compliant role ID).

## `roles/lin_cluster/*`

- Defaults variables (`gfs2_*`) should be prefixed with final td-paas role prefix.
- `tasks/main.yml` should use FQCN imports.
- Ensure all subtask files use FQCN for `include_tasks` and core actions.

## `roles/lin_hc/*`

- `defaults/main.yml` currently mixes concerns (HC thresholds + controller credentials + cluster group values).
  - Split controller/cluster orchestration vars into dedicated role(s).
- Remove plaintext credentials from defaults.
- Convert booleans and enforce prefixed names aligned to new role ID.

## `roles/lin_meta_cluster/*`

- `defaults/main.yml` contains plaintext controller password and generic names (`check_ssl`).
- Rename variables and move secrets to vault.
- Ensure FQCN in `tasks/main.yml` (`set_fact` currently short form).

## `roles/lin_patch/*`

- `tasks/main.yml` has many short-form core actions (`stat`, `set_fact`, `slurp`, `meta`, etc.) to convert to FQCN.
- `tasks/patch.yml` has extensive shell usage (`yum` commands, ownership fixes).
  - Prefer package/file modules where possible.
  - Add strict changed/failed semantics where shell remains necessary.
- Validate inputs with assertions before patch operations.

## `roles/lin_pc/*`

- `defaults/main.yml` uses legacy booleans (`yes`). Convert to `true/false`.
- `tasks/main.yml` uses short-form actions (`set_fact`, `shell`) and needs FQCN updates.
- Extract ad-hoc edited logic comments into structured changelog/commit history; remove inline date-author notes.

## `roles/lin_post/*`

- `defaults/main.yml` includes `lin_pc_*` variables; this indicates naming leakage between roles.
  - Rename to post role prefix and separate precheck vs postcheck ownership.
- Convert legacy booleans to `true/false`.
- Convert short-form actions in `tasks/main.yml` to FQCN.

## `roles/lin_reporting/*`

- `defaults/main.yml` uses `yes/no`; convert to `true/false`.
- `tasks/main.yml` uses `always` tag; remove unless mandatory.
- Ensure role variables are consistently prefixed and scoped.

## `roles/lin_sos_report/*`

- `defaults/main.yml` uses `yes/no`; convert to `true/false`.
- `tasks/main.yml` import syntax should use FQCN.
- Ensure command execution tasks have robust idempotency signaling.

---

## 5) Additional Existing Files to Address

## `library/oracle_info.py`, `library/oracle_info_orig.py`, `library/pcs_cluster_info.py`, `library/xls_write.py`

- Keep only active modules; remove or archive `*_orig.py` duplicates.
- Add linting/tests for custom modules and verify docs/examples in role README.

## `.travis.yml` files under roles

- Migrate CI references to current CI system if Travis is no longer used.
- Enforce `ansible-lint`, `yamllint`, and optional `--check --diff` pipeline.

## `tests/test.yml` / `tests/main.yml` in roles

- Normalize test naming and ensure all roles have at least smoke idempotency checks.

---

## 6) Suggested Migration Plan (Low-Risk Order)

1. **Security first**: remove plaintext credentials; migrate to vault/group_vars.
2. **Naming baseline**: decide final repo/role IDs and variable prefixes.
3. **Structure move**: introduce `playbooks/`, dependency requirements files, group_vars/host_vars.
4. **FQCN + boolean sweep**: mechanical updates across all YAML.
5. **Shell hardening**: refactor highest-risk shell tasks in patch/cluster roles.
6. **CI/lint gate**: enforce standards to prevent regressions.

---

## 7) Priority Hotspots (Immediate Attention)

- `roles/lin_patch/tasks/patch.yml` (very high shell density + patch critical path).
- `roles/lin_pc/tasks/main.yml` and `roles/lin_post/tasks/main.yml` (cluster control flow + short-form modules).
- `cluster-group-cleanup.yml`, `delete-too-old-inventory.yml`, `roles/lin_meta_cluster/defaults/main.yml`, `roles/lin_hc/defaults/main.yml`, `roles/lin_conn/defaults/main.yml` (plaintext credentials).
- `roles/lin_reporting/defaults/main.yml`, `roles/lin_pc/defaults/main.yml`, `roles/lin_post/defaults/main.yml`, `roles/lin_sos_report/defaults/main.yml` (legacy boolean syntax).

---

## Final Assessment

The codebase can be made standards-compliant without functional redesign, but it requires a structured remediation pass across naming, structure, security, and lint/idempotency semantics. The largest effort is in the `lin_patch`, `lin_pc`, and `lin_post` roles due to task complexity and command-heavy implementation.
