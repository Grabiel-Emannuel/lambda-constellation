---
name: pr-comments
description: Read all feedback on a GitHub pull request and create a local, immutable correction plan from its review comments.
---

# Plan corrections from PR comments

Use this skill when asked to run `/pr-comments` or to turn pull request review feedback into a correction plan. The output is an English Markdown file under `docs/plans/`; do not implement the fixes as part of this skill.

1. Identify the PR from a number or URL in the request, or from the current branch with `gh pr view`. If more than one PR matches and the intended one is unclear, ask which PR to use.
2. Read every page of the PR's review bodies, inline review comments (including replies), and conversation comments. Use `gh api --paginate` for `repos/{owner}/{repo}/pulls/{number}/reviews`, `repos/{owner}/{repo}/pulls/{number}/comments`, and `repos/{owner}/{repo}/issues/{number}/comments`. Include comments on outdated diffs and resolved threads in the review; record their status when available. Do not rely on `gh pr view` alone, which can omit inline comments.
3. Inspect the files and surrounding context mentioned by the feedback without changing them. Separate requested corrections from discussion, questions, and already addressed points. Group duplicate comments, but preserve a link to each source comment. Do not invent a solution where the reviewer has only identified a problem; mark any needed decision as open.
4. Create `docs/plans/REVIEW.md` if it does not exist. Planning files are snapshots: never overwrite, append to, or otherwise edit an existing file under `docs/plans/`. If `REVIEW.md` already exists, create a new file named `REVIEW-PR-<number>-<UTC timestamp>.md` using a timestamp that makes the path unique. Ensure `/docs/plans/` is in the repository's `.gitignore`; add that rule only if missing. Do not stage or commit the plan file.
5. In the plan, identify the PR and link to it. For each actionable theme, cite the relevant review comment URLs, state the requested change, name the affected files where known, and list concrete implementation and verification steps. Include unresolved questions and a short completion checklist. If no correction is requested, say so and cite the comments reviewed. Write the plan in English, matching the repository's documentation language.
6. Verify the plan exists and `git check-ignore -v` confirms it is ignored. Compare `git status --short` with the initial working tree state to confirm you did not change unrelated files. Report the plan path and a concise summary of findings. Leave the plan unchanged after creation.
