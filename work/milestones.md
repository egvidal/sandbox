# Implementation Milestones (Easiest → Hardest)

## Goal

Implement all required changes identified in `analysys.md` with controlled risk, clear checkpoints, and increasing complexity from mechanical updates to deep functional refactors.

## Ordering logic

Milestones are ordered by:

1. Lowest blast radius and easiest rollback first.
2. High-impact safety controls early (secrets/lint gates).
3. Deep behavior-changing refactors last (patching and cluster control flow).

---

## Milestone 1 — Baseline Tooling and Safety Net (Very Easy)

### Outcome

Establish repeatable quality gates before refactoring.

### Scope

- Add/standardize lint and validation tooling.
- Add dependency pinning scaffolding.

### Tasks

- Add `collections/requirements.yml` with pinned versions.
- Add `roles/requirements.yml` with pinned versions.
- Add/refresh lint configs (`.ansible-lint`, `.yamllint`) if missing.
- Add a CI job (or local task script) to run:
  - `yamllint`
  - `ansible-lint`
  - selected `ansible-playbook --check --diff`

### Dependencies

- None.

### Exit criteria

- Lint jobs run successfully in pipeline (or documented local execution path).
- Dependency files are present and version-pinned.

### Risk

- Low (non-functional changes).

---

## Milestone 2 — Secrets and Credentials Remediation (Easy)

### Outcome

No plaintext credentials remain in repository-tracked YAML.

### Scope

- Root playbooks and role defaults containing controller/password variables.

### Tasks

- Replace plaintext values with variable references only.
- Move secrets to vault or secret manager-backed vars.
- Introduce required-variable assertions for secret inputs.
- Update README with secure variable injection instructions.

### Candidate files

- `cluster-group-cleanup.yml`
- `delete-too-old-inventory.yml`
- `roles/lin_conn/defaults/main.yml`
- `roles/lin_hc/defaults/main.yml`
- `roles/lin_meta_cluster/defaults/main.yml`

### Dependencies

- Milestone 1 recommended first.

### Exit criteria

- Secret scan across repo shows no hardcoded passwords/tokens.
- All secret-dependent runs use vault/external variables.

### Risk

- Low to medium (runtime failures if secret wiring is incomplete).

---

## Milestone 3 — Mechanical Standards Sweep: FQCN + Booleans + Play Declarations (Easy-Medium)

### Outcome

Repository conforms to baseline style rules with minimal behavior changes.

### Scope

- All playbooks and role task files.

### Tasks

- Convert short module/action names to FQCN (`ansible.builtin.*`).
- Convert `yes/no` to `true/false`.
- Ensure all plays declare explicit `hosts`, `become`, `gather_facts`.
- Replace `local_action` with explicit `delegate_to: localhost` patterns.

### Dependencies

- Milestone 2 completed (to avoid touching same files twice for security fields).

### Exit criteria

- `ansible-lint` no longer reports FQCN and truthy-value violations.
- All root playbooks pass syntax check.

### Risk

- Medium (large mechanical touch set; mitigated by staged PRs).

---

## Milestone 4 — Repository Structure Alignment (Medium)

### Outcome

Repository matches target layout and is easier to operate.

### Scope

- Directory and orchestration organization.

### Tasks

- Create `playbooks/` and move root playbooks into it.
- Add `group_vars/` and `host_vars/` and move environment-specific values out of role defaults.
- Keep role implementations under `roles/`.
- Update docs and runbooks for new paths.

### Dependencies

- Milestone 3 (so moved files are already style-compliant).

### Exit criteria

- All execution examples use `playbooks/` paths.
- Operational runs succeed from new layout.

### Risk

- Medium (path/reference updates can break job templates if missed).

---

## Milestone 5 — Naming Convention Migration (Repo, Roles, Variables, Tags) (Medium-Hard)

### Outcome

Naming model fully aligned with td-paas conventions.

### Scope

- Repository naming, role names, and variable prefixes.

### Tasks

- Decide and approve final naming map (old → new).
- Rename roles from `lin_*` to `td-paas-<domain>-<component>` style.
- Migrate variable prefixes role-by-role.
- Normalize tags toward `td-paas`, domain, component tags.
- Keep compatibility shims/aliases temporarily where needed.

### Dependencies

- Milestones 3 and 4 (stable base first).

### Exit criteria

- No legacy role names remain in active playbooks.
- Variable names in defaults/vars follow role-prefix rules.
- Tags align with naming conventions; `always` removed unless justified.

### Risk

- High (wide reference impact across playbooks, roles, inventory, job templates).

---

## Milestone 6 — Thin Playbook Refactor (Hard)

### Outcome

Playbooks become orchestration-only; business logic resides in reusable roles.

### Scope

- Heavy pre_tasks and inline logic from root playbooks.

### Tasks

- Move parsing/derivation blocks from `pre-check.yml`, `patch.yml`, and related playbooks into role task files.
- Add role-level input validation (`ansible.builtin.assert`) and clear failure messages.
- Keep playbook role ordering explicit and stable.

### Dependencies

- Milestone 5 naming stability.

### Exit criteria

- Root playbooks primarily contain play metadata + role calls.
- Role entrypoints expose clear contracts and required vars.

### Risk

- High (behavior moves between layers; requires regression testing).

---

## Milestone 7 — Shell/Command Hardening in Critical Paths (Very Hard)

### Outcome

Idempotency and determinism significantly improved in patching/cluster workflows.

### Scope

- Shell-heavy files, especially patch and cluster operations.

### Priority hotspots

- `roles/lin_patch/tasks/patch.yml`
- `roles/lin_patch/tasks/main.yml`
- `roles/lin_pc/tasks/main.yml`
- `roles/lin_post/tasks/main.yml`
- `roles/lin_cluster/tasks/oracle/*`
- `roles/lin_cluster/tasks/pacemaker/*`

### Tasks

- Replace shell/command with declarative modules wherever possible.
- For unavoidable shell/command:
  - define strict `changed_when` / `failed_when`
  - add `creates`/`removes` where applicable
  - enforce safe shell execution options for pipelines
- Remove ambiguous side effects and improve observability.

### Dependencies

- Milestone 6 complete (logic location stabilized first).

### Exit criteria

- Second-run idempotency materially improved for patched test scope.
- Command tasks are justified and semantically bounded.

### Risk

- Very high (touches operational core logic).

---

## Milestone 8 — Reporting and Test Standardization (Hard)

### Outcome

Reliable verification and reporting after refactors.

### Scope

- Role tests, reporting consistency, custom module validation.

### Tasks

- Normalize role test naming (`tests/test.yml` vs `tests/main.yml`) and make execution uniform.
- Add smoke/idempotency checks for core roles.
- Validate custom modules under `library/` and remove/archive obsolete duplicates (`oracle_info_orig.py`).
- Ensure reporting templates still match post-refactor variable names.

### Dependencies

- Milestones 5–7.

### Exit criteria

- Core role tests execute consistently.
- Reporting artifacts are generated and valid for all major workflows.

### Risk

- High (integration-level verification).

---

## Milestone 9 — Final Compliance Gate and Production Readiness (Most Challenging)

### Outcome

Repository is release-ready and standards-compliant end-to-end.

### Scope

- Full-system validation and sign-off.

### Tasks

- Run full lint and selected integration runs in check mode and normal mode.
- Validate:
  - no plaintext secrets,
  - naming conventions enforced,
  - idempotency on repeat runs,
  - documentation accuracy.
- Freeze migration compatibility aliases and remove temporary bridges.
- Publish versioned release notes.

### Dependencies

- All prior milestones complete.

### Exit criteria

- Compliance checklist passes with no critical exceptions.
- Operations team can execute all standard workflows from updated docs.

### Risk

- Very high (final integration and cutover).

---

## Suggested Delivery Cadence

- Milestones 1–3: quick wins and baseline compliance (fast iteration).
- Milestones 4–6: structural and naming migration (controlled batches).
- Milestones 7–9: deep refactor + validation (longest phase).

---

## Recommended Work Packaging

- Use one pull request per milestone (or per subdomain in Milestones 5–7).
- For high-risk milestones (6–9), split by role family:
  - connectivity/reporting
  - precheck/postcheck
  - patch
  - cluster/oracle

This sequencing minimizes rework and keeps rollback paths simple while progressively increasing technical depth.
