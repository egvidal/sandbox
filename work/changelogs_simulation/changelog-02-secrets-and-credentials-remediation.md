# Changelog — Milestone 02: Secrets and Credentials Remediation

## Goal of the Milestone

Eliminate plaintext credentials from tracked YAML and enforce secure secret injection at runtime.

## Files Changed

- `pmaas-dev_jboss8_fix_cluster/cluster-group-cleanup.yml`
- `pmaas-dev_jboss8_fix_cluster/delete-too-old-inventory.yml`
- `pmaas-dev_jboss8_fix_cluster/roles/lin_conn/defaults/main.yml`
- `pmaas-dev_jboss8_fix_cluster/roles/lin_hc/defaults/main.yml`
- `pmaas-dev_jboss8_fix_cluster/roles/lin_meta_cluster/defaults/main.yml`
- `pmaas-dev_jboss8_fix_cluster/group_vars/all/vault.yml` (new, encrypted)
- `pmaas-dev_jboss8_fix_cluster/README.md`

## Changes Implemented

- Replaced hardcoded secret values with variable references sourced from vault/external secret backend.
- Added `ansible.builtin.assert` checks in relevant roles/playbooks to fail fast if required secrets are missing.
- Standardized secret variable naming for controller credentials and token-like values.
- Updated operational documentation for secure variable provisioning and vault usage.

## Expected Impact

- Removes credential exposure risk from source control and artifact replication.
- Strengthens deployment reliability by validating secret prerequisites early.
- Aligns repository with security and compliance controls expected by SRE and architecture governance.

## Post Implementation Validation Performed

- Ran repository secret scan to confirm removal of hardcoded passwords/tokens in tracked YAML.
- Validated playbook startup behavior with and without secrets injected (expected fail-fast on missing secrets).
- Verified encrypted vault file decryption in controlled execution context.
- Confirmed no regression in AWX/Tower variable resolution flow using secure credentials.

## Rollback Plan

- Revert secret-externalization changes and assertions in one coordinated rollback if runtime injection path fails.
- Restore previous variable sources only in a temporary emergency branch (never reintroduce plaintext in main branch).
- Apply immediate compensating controls: short-lived credentials and forced rotation.

## Next Steps

- Proceed with Milestone 03 mechanical standards sweep (FQCN/booleans/play declarations).
- Add automated secret scanning to CI as a non-bypass gate for mainline merges.

> Note: This changelog represents a simulated implementation record for governance planning and review.
