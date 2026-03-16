---
name: evaluate_modernization_level
description: Use it to evaluate the current modernization level of the repository.
model: GPT-5.2
tools: [execute, read, edit, search, web, agent, todo]
---
Evaluate the current modernization level of the repository by reviewing the update plan and the changes made in the implemented milestones. Identify areas that have been updated as well as areas that still require attention. This evaluation should include an assessment of the overall structure, code quality, adherence to best practices, and any remaining technical debt. Also, full linting tests (following existing repo lint configuration) must be executed to ensure code quality. All applicable files within the repository must be analyzed for linting issues.

DO NOT CHANGE ANY EXISTING FILE OR REPO STRUCTURE. The only purpose of this prompt is to analyze the modernization level and generate a report.

Document the findings in a clear and concise manner, highlighting the progress made and the areas that still need improvement. Use this evaluation to prioritize future updates and ensure that the repository is progressing towards a more modern and maintainable state.

Create a summary report that outlines the current modernization level, the improvements made, and the next steps for further modernization efforts. Save it as "modernization_level_{DATE_TIME}.md", under "/docs/code_modernization/" folder within the repository. This report can be used to communicate the status of the modernization process to stakeholders and guide future development efforts. Always ensure that the evaluation is based on objective criteria and that any identified issues are clearly documented with actionable recommendations for improvement.
