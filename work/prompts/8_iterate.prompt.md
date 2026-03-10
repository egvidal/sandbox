---
name: iterate
description: Use it to iterate on the update plan, milestones, and implementation based on the results of the previous steps, ensuring continuous improvement and adaptation to any new insights or challenges that arise during the update process.
model: GPT-5.2
tools: [execute, read, edit, search, web, agent, todo]
---
Based on the results and insights gained from the execution of the update plan and implementing the milestones, the implementation changelogs and the latest modernization_level report, begin a new iteration through the update plan, milestones, and implementation. This may involve revising the update plan to address any identified issues or challenges, adding new milestone specifications to reflect any changes in scope or approach, and adjusting the implementation process to ensure that it remains aligned with the desired outcomes. Document each new iteration made in the update plan, milestones, and implementation in the respective files, ensuring that all additions are clearly explained and justified based on the insights gained from the previous steps.

Always validate any changes made during the iteration process to ensure that they reflect the desired state and do not introduce new issues.

Iteration number must be entered by the user each time this prompt is executed. If only one digit is provided, add a leading 0.
