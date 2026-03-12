---
name: implement_milestone
description: Use it to implement a specific milestone from the implementation plan.
model: GPT-5.3-Codex (copilot)
---
Based on the update plan documented in "repo_update_plan.md", the milestones under "/milestones/" and the user input (milestone number), implement the desired milestone by following the tasks outlined in the milestone description. Document the implementation process in "changelog_milestone_#{NUMBER}.md" under "/docs/code_modernization/implementation/" folder within the repository, including:
- Iteration number: {nn}[space][space] (standard body text, after the top-line header)
- Date: {yyyy-mm-dd} (standard body text)
- Goal of the milestone
- Files changed
- Changes implemented
- Expected impact
- Validation performed.
  Execute required linting commands (following existing repo lint configuration) and provide results as:
    - Linting command executed
    - Results (PASSED/FAILED)
- Rollback Notes
- Identified issues and potential solutions
- Next steps

Use code snippets to clearly illustrate sample files or changes in repository structure, presenting a before-and-after comparison for clarity. Ensure the snippets correctly reflect both, previous state and the implemented changes. Do not make assumptions and base your documentation on verifiable facts. Make sure to redact or obfuscate any sensitive information such as passwords, API keys, and personally identifiable information (PII) in all code snippets and examples. DO NOT EXPOSE ANY REAL CREDENTIALS OR SENSITIVE DATA IN THE IMPLEMENTATION DOCUMENTATION. Always use placeholders or dummy values when referencing credentials or sensitive information in your documentation.

For collections and roles being included in requirements.yml, make sure all SCM source URLs are complete, accurate and points to the most current GitHub links. Also, make sure the versions being pinned responds to the latest stable version. Make sure to include only roles and collections which are actually used in the playbooks. If an external role source cannot be found, look for the most updated archived version and update the requirements.yml accordingly. Be accurate with roles names, do not make assumptions and validate the accuracy of each action before changing anything. After completing each milestone implementation, verify everything reflects the desired state.

If the structure of the repository is changed, show the before and after structure, and explain the rationale behind the changes.

For any files, folders or variable being renamed, provide an old-to-new mapping and execute instructions in ["update_mapping_file"](update_mapping_file.prompt.md).

If a decision based on the options detailed within the milestones or repo_update_plan (if any) is to be made during implementation, ask for user input.

Update existing documentation if necessary, ensuring that it is clear, concise, and accurately reflects the changes made in the code and structure.

Iteration number and milestone number must be entered by the user each time this prompt is executed (agent asks for iteration number first, and then for milestone number). If only one digit is provided, add a leading 0.