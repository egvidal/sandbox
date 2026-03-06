---
name: simulate_execution
description: Use it to simulate the execution of the update plan and identify any potential issues or challenges that may arise during the actual implementation.
model: GPT-5.3-Codex (copilot)
---
Based on the update plan documented in "Copilot_{REPO_SHORTNAME}/repo_update_plan.md" and the milestones under "Copilot_{REPO_SHORTNAME}/milestones/", simulate the execution of the plan by going through each milestone and task. Be precise and thorough, following the documented steps closely. Identify any potential issues or challenges that may arise during the actual implementation. Document each simulation results in "Copilot_{REPO_SHORTNAME}/simulations/changelog_milestone#{NUMBER}.md", including:
- "Goal of the milestone"
- "Files changed" (full list)
- "Changes implemented"
- "Expected impact"
- "Post implementation validation" (linting, testing, etc.)
- "Rollback Notes"
- "Identified issues and potential solutions"
- "Next steps"

Use code snippets to clearly illustrate sample files or changes in repository structure, presenting a before-and-after comparison for clarity. Ensure the snippets correctly reflect both, previous state and the implemented changes. Do not make assumptions and base your documentation on verifiable facts. Make sure to redact or obfuscate any sensitive information such as passwords, API keys, and personally identifiable information (PII) in all code snippets and examples. DO NOT EXPOSE ANY REAL CREDENTIALS OR SENSITIVE DATA IN THE SIMULATION DOCUMENTATION. Always use placeholders or dummy values when referencing credentials or sensitive information in your documentation.

For collections and roles being included in requirements.yml, make sure all SCM source URLs are complete, accurate and points to the most current GitHub links. Also, make sure the versions being pinned responds to the latest stable version. Make sure to include only roles and collections which are actually used in the playbooks. If an external role source cannot be found, look for the most updated archived version and update the requirements.yml accordingly. Be accurate with roles names, do not make assumptions and validate the accuracy of each action before changing anything. After completing each milestone implementation, verify everything reflects the desired state.

If the structure of the repository is changed, show the before and after structure, and explain the rationale behind the changes.
