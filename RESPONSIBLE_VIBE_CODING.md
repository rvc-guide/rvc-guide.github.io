# Introduction

The rise of AI coding assistants has shifted the balance of effort in software development.
In the past, *creating* a high-quality pull request (PR) required significant human effort, while *reviewing* it was comparatively lighter.
Now, with large language models (LLMs) able to generate code on demand, creating a PR has become cheap and fast - but reviewing and validating those changes is more labor-intensive than ever.
In practice, this means maintainers often face PRs that are "almost correct" but still riddled with subtle bugs requiring painstaking review. An AI tool can overwhelm the team's capacity for reviews.

Nevertheless, outright banning AI-generated contributions is neither practical nor desirable.
AI tools can boost productivity if used responsibly. The focus for an open-source community should be on maintaining the same high-quality standards and collaborative process, regardless of how a contribution was produced. [Research](https://dev.to/goern/ai-generated-code-quality-in-open-source-cce#:~:text=Evidence,inevitably%20reproducing%20these%20flaws) has shown that developers using AI assistance can unintentionally introduce more security vulnerabilities (while paradoxically becoming overconfident in the code's correctness).
This risk highlights the importance of due diligence and human oversight in AI-assisted code development.
Rather than rejecting AI involvement, we must become "AI realists" - leveraging AI's benefits while doubling down on transparency, thorough review, and education to uphold our standards.

While many of the following practices - such as creating small, focused PRs and avoiding unrelated changes - have been best practices in collaborative software development for decades, AI-assisted coding amplifies the challenges they address. The speed and volume of AI-generated code can overwhelm reviewers, making adherence to these long-standing guidelines even more critical.

The following guidelines aim to help contributors use AI responsibly in OS projects, thereby minimizing the review burden on maintainers while maintaining, if not raising, the usual quality bar.
These hands-on recommendations are not meant to be normative but should be used as inspiration for your project specific needs.

# Rules for AI-Assisted PR Submissions
* **Small and Focused PRs**: Contributors are strongly encouraged to create multiple small and focused PRs instead of single, monolithic monster PRs.
  As a general guideline, a PR should aim to touch as few files as possible.
  If, for example, you need to modify more than twenty files, this is a strong indication that you should split your PR into multiple ones, possibly based on each other.
  There are, of course, exceptions to this rule of thumb, but these should be justified in the PR comment.
* **Stay on target – avoid irrelevant code changes**: AI models sometimes get "distracted", modifying parts of the codebase that you didn't ask to change (like drive-by "improvements" that are not needed).
  Such changes can confuse reviewers and bloat your PR.
  Avoid touching code that is not relevant to the task at hand. Each PR should have a laser-focused diff - if you notice unrelated edits (no matter how well-intentioned by the AI), strip them out.
* **Do a thorough self-review and test your changes**: Never treat an AI-generated patch as _ready to go_ without reviewing it yourself.
  Before requesting a peer review, inspect and verify the code yourself.
  This means reading through the diff carefully, understanding every change, and checking that it solves the problem without breaking anything else.
  Try to run the code end-to-end, and run all relevant unit tests or sample scenarios.
  If the project has automated test suites or linters, run those tools locally to catch obvious issues.
  _You are responsible for the code you submit, whether you or an AI wrote it, and AI tools are no substitute for your own review of code quality, correctness, style, security, and licensing_ (from [OpenInfra AI policy](https://openinfra.org/legal/ai-policy/#:~:text=As%20a%20contributor%2C%20you%20are,correctness%2C%20style%2C%20security%2C%20and%20licensing)).
* **Challenge the LLM**: Often, the model will deliver an overly complex answer, so you should challenge it with additional questions about the change.
  If there is a more straightforward approach, weigh the pros and cons of each approach. Introduce checkpoints for states that you are happy with and make intermediate commits.
  Don't hesitate to rollback to the checkpoint if the LLM has driven itself into a corner and try again from there with a fresh context.
* **Requests-In-Progress (RIP) Limit**: To prevent the "spamming" of _Pull Requests_ (GitHub) or _Merge Requests_ (GitLab) and to maintain focus on the most important contributions, a Requests-In-Progress (RIP) limit is proposed. This limit means that only a fixed and negotiated number (e.g., 3) PRs might be open from a single author, which are currently under review (not counting DRAFT PRs).
  This metric is similar to the [Work-In-Progress (WIP) limit](https://www.atlassian.com/agile/kanban/wip-limits#:~:text=WIP%20limits%20improve%20throughput%20and,valuable%20tool%20in%20agile%20development) known from Kanban.
  In agile terms, *WIP limits improve throughput by reducing the amount of work "nearly done" and forcing focus on a smaller set of tasks*. This technique helps avoid "PR spam".
  PR spam is not only noisy and annoying for the limited number of reviewers but also encourages contributors to *finish* and polish one change before moving on to the next.
  If your project does not enforce a RIP-Limit it's still a good practice to keep the number of your own open requests small.
* **Add an AI Attribution**: Add an attribution to AI-generated content in your PR and commit comments. You can do it in free form or stick to some proposed conventions:
  In a commit message, or in a pull request/merge request description field, identify the code assistant that you used, perhaps elaborating on how it was used. You may wish to use a trailer like `Assisted-by:` for AI-assisted contributions or `Generated-by:` for fully AI-generated content.
  Alternatively, the [AI Attribution Toolkit](https://aiattribution.github.io/) has a proposal that results in detailed attribution lines like [AIA PAI Nc Hin R o3 v1.0](https://aiattribution.github.io/statements/AIA-PAI-Nc-Hin-R-?model=o3-v10).
  The syntax of the AI Attribution Toolkit is not an established standard and might be too complex and too detailed for many open source communities.
* **Assume AI assistance and openly share prompting techniques**: These days, it's safe to assume AI assistance is the norm rather than the exception.
  Contributors and reviewers are encouraged to share and discuss their prompting techniques openly.
  Be aware that most of the code you encounter was also generated by AI and may contain elusive bugs.

# Project automation for the Vibe Coding/Review Workflow

To further support and streamline the AI coding and review workflow, we recommend that any OS project that supports AI-assisted coding should implement lightweight rules:

* **PROMPTING.md**: The project can include a top-level `PROMPTING.md` file that contains instructions and best practices for prompting AI when coding for the project.
  It's different from `CONTRIBUTING.md` insofar as it can be directly included into the context of an LLM query.
  `CONTRIBUTING.md` often also contains information that is not relevant for coding tasks (such as governance rules or development environment setup), which might dilute the context and distract the LLM.
  `PROMPTING.md` essentially encodes the project's guidelines into a format that can be easily incorporated into an AI prompt.
  Simply add it to the context when querying your IDE's model.
  This [technique](https://www.reddit.com/r/SoftwareEngineering/comments/1kjwiso/maintaining_code_quality_with_widespread_ai/#:~:text=5,or%20alike%20across%20the%20team) might be helpful to unify prompt rules across the team and share good prompts, so that everyone's AI assistance operates with the same guidance.
  The `PROMPTING.md` file would be updated over time as we learn which instructions yield the best results from various AI models for our project.

Example of a possible `PROMPTING.md`:

```
* Focus on the task at hand and don't change anything beside this
* Only do minimal changes to fullfill the task at hand
* Use best practices when creating tests, don't repeat boilerplate
  code literally but extract them into functions.
* Follow Dont-Repeat-Yourself principles
* Comments should provide meaningful insights into the code.
  Avoid filler comments that simply describe the next step, as they
  create unnecessary clutter, same goes for docstrings.
* Prefer comments to clarify surprising behavior and/or relationships
  between parts of the code rather than explain what the next line
  of code does.
* Catching exceptions, prefer using a specific exception type rather
  than a broad catch-all like `Exception`.
* Error messages should be prefixed with "Failed to ..."
* 4 spaces for indentation rather than tab
```

To some degree `PROMPTING.md` is similar to [`CLAUDE.md`](https://docs.anthropic.com/en/docs/claude-code/memory#how-claude-looks-up-memories) or [`AGENTS.MD`](https://agentsmd.net/AGENTS.md) but model-agnostic and targeted for humans.
You can mix-and-match those files to support multiple tools, but they should all contain the same set or rules.
Leveraging tool-specific project-prompts has the advantage that they are automatically picked up and does not have to be added manually by the users.

* **GitHub PR Template Modifications**: The GitHub PR template could be modified to include the following field:

```
- [ ] AI-assisted: <add your AI attibution>
```
