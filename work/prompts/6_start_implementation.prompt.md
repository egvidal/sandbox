---
name: start_implementation
description: Use it to start implementing the first milestone from the implementation plan.
model: GPT-5.3-Codex (copilot)
---
Based on the update plan documented in "Copilot_{REPO_SHORTNAME}/repo_update_plan.md" and the milestones under "Copilot_{REPO_SHORTNAME}/milestones/", start implementing the first milestone by following the tasks outlined in the milestone description. Document the implementation process in the actual repo, under "/changelogs/changelog_milestone#{NUMBER}.md", including:
- a Timestamp
- "Goal of the milestone"
- "Files changed" (full list)
- "Changes implemented" (as snippets with explanations)
- "Expected impact"
- "Validation performed".
  Execute required linting commands and provide results as:
    - Linting command executed
    - Results (PASSED/FAILED)
- "Rollback Notes"
- "Identified issues and potential solutions"
- "Next steps"

Use code snippets to clearly illustrate sample files or repository structure changes, presenting a before-and-after comparison for clarity. Ensure all documented changes are accurate and the snippets correctly reflect both, previous state and the implemented changes. Always redact or obfuscate sensitive information such as passwords, API keys, and personally identifiable information (PII) in all code snippets and examples.

For collections and roles being included in requirements.yml, make sure all SCM source URLs are complete, accurate and points to the most current GitHub links. Also, make sure the versions being pinned responds to the latest stable version. Make sure to include only roles and collections which are actually used in the playbooks. If an external role source cannot be found, look for the most updated archived version and update the requirements.yml accordingly. Be accurate with roles names, do not make assumptions and validate the accuracy of each action before changing anything. After completing each milestone implementation, verify everything reflects the desired state.

If the structure of the repository is changed, show the before and after structure, and explain the rationale behind the changes.

Update existing documentation if necessary, ensuring that it is clear, concise, and accurately reflects the changes made in the code and structure.