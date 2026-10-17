---
name: create-english-pr
description: Create or update an English GitHub pull request for this repository, including an English documentation review and the project's four-part PR body.
---

# Create an English pull request

Use this skill when asked to open or update a pull request for Lambda Constellation. Keep the PR title, body, and any new or edited documentation in English.

## Workflow

1. Inspect the working tree, current branch, target branch, relevant changes, and any existing PR. Preserve unrelated work. Use `main` as the PR base unless the user specifies another base.
2. Review the implementation and run checks that actually apply to the change. Record the commands and results. Do not mark an evaluation criterion complete unless it was verified.
3. Check the language of **all tracked, human-written repository documentation**, including `README.md`, `AGENTS.md`, `CONTRIBUTING.md`, `CHANGELOG.md`, `docs/`, and GitHub issue or PR templates. Also check any new or edited documentation outside those paths. Use `git ls-files` to inventory files; inspect prose rather than relying only on an automated language guess. Exclude generated output, dependencies, code identifiers, and quoted examples that must retain their original wording. Translate non-English documentation that belongs to the requested change; report unrelated existing exceptions in the PR instead of silently claiming that everything is in English.
4. If needed, create a focused branch from the appropriate base. Stage only intended files. Use an English semantic commit subject (for example, `feat: add framework issue templates`). When Codex contributed to the commit, include `Co-authored-by: Codex <codex@openai.com>` in its trailer. For revisions to the same PR's own commit, amend it instead of adding a fixup commit when safe; preserve other authors' work and use `--force-with-lease` when pushing a rewritten commit.
5. Push the branch, then create or update the GitHub PR against the base branch. Use an English title and the body format below. If `gh pr edit` fails, the GitHub REST API via `gh api` is an acceptable fallback. Verify the published PR title, body, base, and head after updating it.

## PR body

Write these four numbered sections in English, with details specific to the actual change:

```markdown
## 1. Proposal

Explain the problem and intended outcome. Link the issue when one exists (`Closes #123` only if this PR resolves it; otherwise `Refs #123`). If there is no issue, say so.

## 2. What was implemented

Summarize the concrete changes and their scope.

## 3. Evaluation criteria met

- [x] State a criterion that was verified and how it was satisfied.
- [ ] State any criterion still open, if applicable.

## 4. How to test locally

Give reproducible commands or steps from the repository root, with prerequisites and any relevant limitations. Include checks actually run; distinguish steps that require a merged PR or GitHub UI.
```

Before finishing, confirm the PR accurately describes the pushed commit and disclose any documentation language exceptions or unrun checks.
