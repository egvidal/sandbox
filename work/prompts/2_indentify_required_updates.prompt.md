---
name: indentify_required_updates
description: Use it for analyzing the necessary changes to the repository structure and existing files based on the best practices and naming conventions outlined in the specified documents.
model: GPT-5.3-Codex (copilot)
---
Based on "ansible-best-practices" and "naming-conventions" instructions files included in this workspace, analyze what changes should be made to the repo structure and each existing file. Document the analysis in "Copilot_{REPO_SHORTNAME}/repo_required_updates.md".

Variable naming scope rule (mandatory):
- Recommend renaming ONLY variables that are DEFINED within each role (source-of-truth in roles/<role>/defaults/main.yml or roles/<role>/vars/main.yml), adding the role prefix per naming conventions.
- Variables defined externally must NOT be renamed (AAP inventory vars, AAP survey vars, job template extra vars, credential vars, play-level/global integration vars).
- explicitly separate:
  1) "Role-internal vars to rename"
  2) "External vars to preserve unchanged"