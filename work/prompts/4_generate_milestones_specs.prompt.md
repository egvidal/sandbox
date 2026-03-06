---
name: generate_milestones_specs
description: Use it to set milestones for updating the repository based on the plan created in the previous step.
model: GPT-5.2
---
Based on the milestones listed in the update plan documented as "Copilot_{REPO_SHORTNAME}/repo_update_plan.md", generate milestone technical specifications in "Copilot_{REPO_SHORTNAME}/milestones/milestone#{NUMBER}.md". Each file must contain all of these sections:
- "Goal of the milestone"
- "Changes to be implemented"
- "Files in scope"
- "Expected impact"

Each milestone must be documented in detail. Make sure to explain the rationale behind each change. Why is it necessary? How does it contribute to the overall improvement of the repository? What are the expected benefits and potential risks associated with the change?
