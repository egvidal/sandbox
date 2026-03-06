---
name: analyze_repo
description: Use it to understand what is the repo for and how it works.
model: GPT-5.3-Codex (copilot)
---
@workspace Based on the available repo, create a new folder under work/ named "Copilot_{REPO_SHORTNAME}", where *REPO_SHORTNAME* is the name abbreviation of the repository being analyzed. Ask for confirmation before creating that folder. Analyze repo structure and each playbook and role functionality. Understand what is it for and how it works. Document the analysis in "Copilot_{REPO_SHORTNAME}/repo_analysis.md"
