---
name: analyze_repo
description: Use it to understand what is the repo for and how it works.
model: GPT-5.3-Codex (copilot)
---
@workspace Based on the available repository, create a Copilot working folder under parent, named "Copilot_work_for_{REPO_SHORTNAME}", where *REPO_SHORTNAME* is the acronym of the repository being analyzed. Ask for confirmation before creating that folder. 

Then, analyze the repo structure and each and every playbook and role functionality. Understand what is it for and how it works. Document the analysis in "repo_analysis.md" under Copilot working folder.
