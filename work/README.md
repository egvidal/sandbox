# Agent prompts

- @workspace analyze repo structure and each playbook functionality, understand the purpose of this solution and document it on new file "work/existing_solution.md"


- based on "ansible-best-practices.md" and "naming-conventions.md", analyze what changes should be made to the repo structure and each existing file. Document the analysis in "work/analysis.md"


- now build a detailed plan for implementing the required changes described in "work/analysis.md", setting milestones in order, from easiest to hardest (or simplest to most challenging). Document on "work/milestones.md"


- now generate one md file per milestone under "work/milestones/". Each file must include all details (for that milestone) extracted from "work/milestones.md" and also a comparison between the before and after state of all the code/files/structure being changed. Make it accurate and professional, since this is to be shown to the SME/SRE/Architect.


- update milestones files adding instructions to make sure every change to be performed is accurate. If collections and roles are being included in requirements.yml, make sure the src url is accurate and points to the most current link. Also, make sure the versions being pinned responds to the latest stable version for each. if roles are to be used, make sure role names are exact. Do not make assumptions and validate the accuracy of each action before changing anything. After completing the milestone implementation, verify everything reflects the desired state.


- now simulate an implementation for each milestone. For that, create one changelog md file per each under "work/changelogs_simulation" in which you must detail: Goal of the milestone, files changed, changes implemented, expected impact, post implementation validation performed, rollback plan, and next steps.


- Let's start with execution. Implement and verify milestone #1 and properly document all changes under "work/implementation/changelog# < changelog-nr > _ < changelog-name > .md", using "work/changelog_sample.md" as a template.
