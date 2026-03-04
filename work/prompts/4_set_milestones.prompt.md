---
name: set_milestones
description: Use it to set milestones for updating the repository based on the plan created in the previous step.
model: GPT-5.2
---
Based on the update plan documented in "work/Copilot_{REPO_SHORTNAME}/update_plan.md", set milestones for updating the repository. Each milestone should correspond to a specific update or group of related updates, and should include a clear description of the tasks to be completed, the expected outcome, and a comparison between before and after state of the code/files/structure being changed. Make it accurate and professional, since this is to be shown to the SME/SRE/Architect. Document each milestone in a new file named "work/Copilot_{REPO_SHORTNAME}/milestones/milestone_#{NUMBER}.md".