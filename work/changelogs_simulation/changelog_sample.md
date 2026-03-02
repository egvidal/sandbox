# Changelog: Deduplication Phase 2 (Cautious Slice)

Date: 2026-02-18
Scope: Phase 2 partial implementation (NodeJS import gating only)

## Goal of This Slice

Start Phase 2 with the lowest-risk behavioral gate:

1. Run NodeJS-specific health-check tasks only when NodeJS capability is enabled by profile.

This follows the recommendation in Phase 1 changelog to begin with `roles/lin_hc/tasks/main.yml` before larger refactors.

## Changes Implemented

Modified:

1. `roles/lin_hc/tasks/main.yml`

Details:

1. Added profile capability guard on NodeJS task import:
   - `import_tasks: nodejs_check.yml`
   - `when: lin_profile_caps.cap_nodejs | default(false)`
2. Added profile capability guard on NodeJS VM status role import:
   - `import_role: name: lin_nodejs_vmstatus_precheck`
   - `when: lin_profile_caps.cap_nodejs | default(false)`

## Expected Runtime Impact

1. For `workflow_profile=nodejs_reporting`:
   - NodeJS checks and VM status precheck role continue to run in `lin_hc`.
2. For non-NodeJS profiles (`jboss_fix`, `jboss_cluster`, `mongodb`):
   - NodeJS-specific imports in `lin_hc` are skipped.
3. If profile context is missing:
   - Guard defaults to `false`, so NodeJS imports are skipped.

## Why This Is Safe

1. Only one role task file was changed.
2. No top-level playbook names changed.
3. No removal of shared vars or role entrypoints.
4. No changes to patch/pre/post role flows in this slice.

## Validation Performed

Attempted full Ansible syntax matrix:

1. Still blocked by environment Ansible interpreter issue (`.venv` points to missing `python3.14`, and system Python has no Ansible module).

Fallback validation completed:

1. YAML parse check for modified file:
   - `roles/lin_hc/tasks/main.yml`
2. Result: parse successful.

## Rollback Plan

To revert this Phase 2 slice:

1. Remove `when: lin_profile_caps.cap_nodejs | default(false)` from:
   - `import_tasks: nodejs_check.yml`
   - `import_role: name: lin_nodejs_vmstatus_precheck`
2. Keep Phase 1 bootstrap/profile files unchanged.

## Next Step

Continue Phase 2 cautiously:

1. Gate NodeJS/Mongo/JBoss branches inside `roles/lin_patch/tasks/patch.yml` using `lin_profile_caps`.
2. Validate per profile after each small gated block.
