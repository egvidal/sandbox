---
name: update_mapping_file
description: Use it to update the mapping file with any renamed files, folders, variables, etc during the implementation of the milestones. This file serves as a reference to track all changes made to the repository structure and variable names, ensuring that all updates are documented and can be easily traced back to their original state.
model: GPT-5.3-Codex (copilot)
---
When renaming any files, folders, variables, or other elements in the repository during the implementation of the milestones, it is crucial to maintain an accurate mapping file that documents all changes. This mapping file should include the following information for each renamed element:
- Type (role / file / variable / function / class / config / other)
- Old location (e.g., old file path, old variable name, etc)
- New location (e.g., new file path, new variable name, etc)
- Milestone Number (the milestone in which the change was implemented)

Document these mappings in "/implementation/refactor-mapping.md" under Copilot working folder.
Ensure the file remains updated as an accurate source of truth for all refactorings performed across the repository. This will help maintain clarity and traceability of changes throughout the implementation process. Maintain the table sorted by:
- old location
- element name
