---
name: create_planning
description: Use it to create a plan for updating the repository based on the analysis of the existing structure and the required updates.
model: GPT-5.2
---
Based on the analysis documented in "repo_analysis.md" and "repo_required_updates.md", create a detailed plan for updating the repository applying Ansible standards and best practices in a controlled, milestone-based rollout that prioritizes:
1. Low-risk changes first.
2. Core lifecycle safety before broad refactors.
3. Measurable quality improvement without breaking runtime behavior.
 
The plan must include the following sections:
1. Summary of the current state of the repository.
2. List of required updates and changes to be made.
3. Step-by-step plan (set small, measurable milestones that can be safely implemented).
 
Document the plan in "repo_update_plan.md" under "/docs/code_modernization/" folder within the repository.
