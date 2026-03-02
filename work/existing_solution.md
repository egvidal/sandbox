# Existing Solution Analysis — Playbook Structure & Functionality

## 1) Solution shape (high level)

The repository uses a **role-oriented orchestration model**:

- Root playbooks orchestrate workflow steps (thin controllers), aligned with standards in [td-paas-standards-main/ansible-best-practices.md](../td-paas-standards-main/ansible-best-practices.md).
- Implementation is delegated to role task files under [roles/](roles/).
- Reporting is consolidated through [roles/lin_reporting/tasks/main.yml](roles/lin_reporting/tasks/main.yml).

---

## 2) Playbook-by-playbook analysis

## [cleaning.yml](cleaning.yml)

- **Structure:** Root orchestration playbook (not expanded in excerpts).
- **Functionality:** Expected environment/report cleanup operations before or after runs.

## [cluster-group-cleanup.yml](cluster-group-cleanup.yml)

- **Structure:** Root orchestration playbook (not expanded in excerpts).
- **Functionality:** Expected cluster group state cleanup/reset actions.

## [conn-check.yml](conn-check.yml)

- **Structure:** Multi-play flow:
  1. Server-side connectivity checks via imported tasks.
  2. Localhost report generation and host disabling.
  3. Localhost host re-enable flow by tag.
- **Functionality:** Connectivity validation and operational host state management.
- **Observed task imports:**
  - [`roles/lin_conn/tasks/get-connectivity.yml`](roles/lin_conn/tasks/get-connectivity.yml)
  - [`roles/lin_conn/tasks/generate-report.yml`](roles/lin_conn/tasks/generate-report.yml)
  - [`roles/lin_conn/tasks/disable-host.yml`](roles/lin_conn/tasks/disable-host.yml)
  - [`roles/lin_conn/tasks/enable-host.yml`](roles/lin_conn/tasks/enable-host.yml)
- **Outputs:** JSON/TXT/CSV reports generated in local report directory by [`roles/lin_conn/tasks/generate-report.yml`](roles/lin_conn/tasks/generate-report.yml), plus optional Logstash upload.

## [delete-too-old-inventory.yml](delete-too-old-inventory.yml)

- **Structure:** Root orchestration playbook (not expanded in excerpts).
- **Functionality:** Expected retention cleanup for old inventory artifacts.

## [elk-install.yml](elk-install.yml)

- **Structure:** Root orchestration playbook (not expanded in excerpts).
- **Functionality:** Expected ELK components setup/integration.

## [health-check.yml](health-check.yml)

- **Structure:** Likely calls health-check role pipeline.
- **Functionality:** Host health validation before/after patch windows.
- **Role task chain reference:** [roles/lin_hc/tasks/main.yml](roles/lin_hc/tasks/main.yml)
  Includes install/service/cluster/ntp/uptime/repo/disk/package-cache/memory/cpu/salt/mount/process/network/log/report task files.

## [meta-cluster.yml](meta-cluster.yml)

- **Structure:** Root orchestration playbook (not expanded in excerpts).
- **Functionality:** Expected cluster metadata and orchestration coordination.

## [patch.yml](patch.yml)

- **Structure:** Patch execution orchestration delegated to patch role.
- **Functionality:** Pre-patch prep, patch execution, conditional reboot, post-package tasks, host report generation.
- **Key behavior from [`roles/lin_patch/tasks/main.yml`](roles/lin_patch/tasks/main.yml):**
  - Reads precheck report.
  - Reads cluster state file and decodes content.
  - Runs patch block for:
    - standalone hosts, or
    - cluster nodes with valid cluster/node status.
  - Conditional reboot based on `lin_patch_packages_changed`.

## [post-check.yml](post-check.yml)

- **Structure:** Post-check orchestration through role task sequence.
- **Functionality:** Maintenance normalization, app start (conditional), sos report (conditional), patch verification, reporting.
- **Key behavior from [`roles/lin_post/tasks/main.yml`](roles/lin_post/tasks/main.yml):**
  - Runs when standalone or cluster is active.
  - Updates host status fact (`patched`/`failed`/`not_patched`) in guarded block.

## [pre-check.yml](pre-check.yml)

- **Structure:** Pre-check orchestration through role task sequence.
- **Functionality:** For standalone and eligible cluster nodes: optional sos, app shutdown, maintenance prep, reboot path, host report.
- **Key behavior references:**
  - [roles/lin_pc/tasks/main.yml](roles/lin_pc/tasks/main.yml)
  - [roles/lin_pc/tasks/main_current.yml](roles/lin_pc/tasks/main_current.yml)
  - [roles/lin_pc/tasks/main_backup.yml](roles/lin_pc/tasks/main_backup.yml)
- **Cluster integration:** Uses [`lin_cluster` role contract](roles/lin_cluster/README.md), especially `stop-and-disable.yml` outputs (`cl_status_before_host_patching`, `lin_cluster_status`, `lin_cluster_info`).

## [remove-package.yml](remove-package.yml)

- **Structure:** Root orchestration playbook (not expanded in excerpts).
- **Functionality:** Expected package removal workflow.

## [reporting.yml](reporting.yml)

- **Structure:** Aggregator playbook for report pipelines.
- **Functionality:** Consolidates preparation + HC + precheck + patch + postcheck + summary + XLS.
- **Role entrypoint:** [roles/lin_reporting/tasks/main.yml](roles/lin_reporting/tasks/main.yml).

## [sos-report.yml](sos-report.yml)

- **Structure:** Root orchestration playbook (not expanded in excerpts).
- **Functionality:** Expected sosreport collection workflow (also conditionally invoked from pre/post role flows).

---

## 3) Role contracts that drive playbook behavior

## [roles/lin_cluster/README.md](roles/lin_cluster/README.md)

- Defines cluster plugin structure (`stop-and-disable.yml`, `start-and-enable.yml`, `host-report.yml`).
- Exposes key output vars consumed by pre/patch/post flows:
  - `cl_status_before_host_patching`
  - `lin_cluster_status`
  - `lin_cluster_info`

## [roles/lin_conn/README.md](roles/lin_conn/README.md)

- Defines conn-check execution pattern and tags:
  - `ping/check/test`
  - `generate_report/generate/report`
  - `disable_host/disable`
  - `enable_host/enable`

---

## 4) End-to-end functional sequence (observed)

1. **Connectivity gate** via [conn-check.yml](conn-check.yml) and lin_conn tasks.
2. **Pre-check** via [pre-check.yml](pre-check.yml), including cluster node stop/disable when needed.
3. **Patch** via [patch.yml](patch.yml), with cluster-status-aware execution and conditional reboot.
4. **Post-check** via [post-check.yml](post-check.yml), service/app normalization and validation.
5. **Reporting consolidation** via [reporting.yml](reporting.yml) and [roles/lin_reporting/tasks/main.yml](roles/lin_reporting/tasks/main.yml).

---

```markdown
<!-- filepath: pmaas-dev_jboss8_fix_cluster/existing_solution.md -->
# Existing Solution Analysis — Playbook Structure & Functionality

## 1) Solution shape (high level)

The repository uses a **role-oriented orchestration model**:

- Root playbooks orchestrate workflow steps (thin controllers), aligned with standards in [td-paas-standards-main/ansible-best-practices.md](../td-paas-standards-main/ansible-best-practices.md).
- Implementation is delegated to role task files under [roles/](roles/).
- Reporting is consolidated through [roles/lin_reporting/tasks/main.yml](roles/lin_reporting/tasks/main.yml).

---

## 2) Playbook-by-playbook analysis

## [cleaning.yml](cleaning.yml)
- **Structure:** Root orchestration playbook (not expanded in excerpts).
- **Functionality:** Expected environment/report cleanup operations before or after runs.

## [cluster-group-cleanup.yml](cluster-group-cleanup.yml)
- **Structure:** Root orchestration playbook (not expanded in excerpts).
- **Functionality:** Expected cluster group state cleanup/reset actions.

## [conn-check.yml](conn-check.yml)
- **Structure:** Multi-play flow:
  1. Server-side connectivity checks via imported tasks.
  2. Localhost report generation and host disabling.
  3. Localhost host re-enable flow by tag.
- **Functionality:** Connectivity validation and operational host state management.
- **Observed task imports:**
  - [`roles/lin_conn/tasks/get-connectivity.yml`](roles/lin_conn/tasks/get-connectivity.yml)
  - [`roles/lin_conn/tasks/generate-report.yml`](roles/lin_conn/tasks/generate-report.yml)
  - [`roles/lin_conn/tasks/disable-host.yml`](roles/lin_conn/tasks/disable-host.yml)
  - [`roles/lin_conn/tasks/enable-host.yml`](roles/lin_conn/tasks/enable-host.yml)
- **Outputs:** JSON/TXT/CSV reports generated in local report directory by [`roles/lin_conn/tasks/generate-report.yml`](roles/lin_conn/tasks/generate-report.yml), plus optional Logstash upload.

## [delete-too-old-inventory.yml](delete-too-old-inventory.yml)
- **Structure:** Root orchestration playbook (not expanded in excerpts).
- **Functionality:** Expected retention cleanup for old inventory artifacts.

## [elk-install.yml](elk-install.yml)
- **Structure:** Root orchestration playbook (not expanded in excerpts).
- **Functionality:** Expected ELK components setup/integration.

## [health-check.yml](health-check.yml)
- **Structure:** Likely calls health-check role pipeline.
- **Functionality:** Host health validation before/after patch windows.
- **Role task chain reference:** [roles/lin_hc/tasks/main.yml](roles/lin_hc/tasks/main.yml)
  Includes install/service/cluster/ntp/uptime/repo/disk/package-cache/memory/cpu/salt/mount/process/network/log/report task files.

## [meta-cluster.yml](meta-cluster.yml)
- **Structure:** Root orchestration playbook (not expanded in excerpts).
- **Functionality:** Expected cluster metadata and orchestration coordination.

## [patch.yml](patch.yml)
- **Structure:** Patch execution orchestration delegated to patch role.
- **Functionality:** Pre-patch prep, patch execution, conditional reboot, post-package tasks, host report generation.
- **Key behavior from [`roles/lin_patch/tasks/main.yml`](roles/lin_patch/tasks/main.yml):**
  - Reads precheck report.
  - Reads cluster state file and decodes content.
  - Runs patch block for:
    - standalone hosts, or
    - cluster nodes with valid cluster/node status.
  - Conditional reboot based on `lin_patch_packages_changed`.

## [post-check.yml](post-check.yml)
- **Structure:** Post-check orchestration through role task sequence.
- **Functionality:** Maintenance normalization, app start (conditional), sos report (conditional), patch verification, reporting.
- **Key behavior from [`roles/lin_post/tasks/main.yml`](roles/lin_post/tasks/main.yml):**
  - Runs when standalone or cluster is active.
  - Updates host status fact (`patched`/`failed`/`not_patched`) in guarded block.

## [pre-check.yml](pre-check.yml)
- **Structure:** Pre-check orchestration through role task sequence.
- **Functionality:** For standalone and eligible cluster nodes: optional sos, app shutdown, maintenance prep, reboot path, host report.
- **Key behavior references:**
  - [roles/lin_pc/tasks/main.yml](roles/lin_pc/tasks/main.yml)
  - [roles/lin_pc/tasks/main_current.yml](roles/lin_pc/tasks/main_current.yml)
  - [roles/lin_pc/tasks/main_backup.yml](roles/lin_pc/tasks/main_backup.yml)
- **Cluster integration:** Uses [`lin_cluster` role contract](roles/lin_cluster/README.md), especially `stop-and-disable.yml` outputs (`cl_status_before_host_patching`, `lin_cluster_status`, `lin_cluster_info`).

## [remove-package.yml](remove-package.yml)
- **Structure:** Root orchestration playbook (not expanded in excerpts).
- **Functionality:** Expected package removal workflow.

## [reporting.yml](reporting.yml)
- **Structure:** Aggregator playbook for report pipelines.
- **Functionality:** Consolidates preparation + HC + precheck + patch + postcheck + summary + XLS.
- **Role entrypoint:** [roles/lin_reporting/tasks/main.yml](roles/lin_reporting/tasks/main.yml).

## [sos-report.yml](sos-report.yml)
- **Structure:** Root orchestration playbook (not expanded in excerpts).
- **Functionality:** Expected sosreport collection workflow (also conditionally invoked from pre/post role flows).

---

## 3) Role contracts that drive playbook behavior

## [roles/lin_cluster/README.md](roles/lin_cluster/README.md)
- Defines cluster plugin structure (`stop-and-disable.yml`, `start-and-enable.yml`, `host-report.yml`).
- Exposes key output vars consumed by pre/patch/post flows:
  - `cl_status_before_host_patching`
  - `lin_cluster_status`
  - `lin_cluster_info`

## [roles/lin_conn/README.md](roles/lin_conn/README.md)
- Defines conn-check execution pattern and tags:
  - `ping/check/test`
  - `generate_report/generate/report`
  - `disable_host/disable`
  - `enable_host/enable`

---

## 4) End-to-end functional sequence (observed)

1. **Connectivity gate** via [conn-check.yml](conn-check.yml) and lin_conn tasks.
2. **Pre-check** via [pre-check.yml](pre-check.yml), including cluster node stop/disable when needed.
3. **Patch** via [patch.yml](patch.yml), with cluster-status-aware execution and conditional reboot.
4. **Post-check** via [post-check.yml](post-check.yml), service/app normalization and validation.
5. **Reporting consolidation** via [reporting.yml](reporting.yml) and [roles/lin_reporting/tasks/main.yml](roles/lin_reporting/tasks/main.yml).

---
