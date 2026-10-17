---
name: pr-comments
description: Read GitHub pull request feedback and maintain one local correction plan per worktree.
---

# Plan corrections from PR comments

Use this skill when asked to run `/pr-comments` or to turn pull request review feedback into a correction plan. Maintain the English Markdown plan at `docs/plans/REVIEW.md`; do not implement fixes as part of this skill.

1. Identify the PR from a number or URL in the request, or from the current branch with `gh pr view`. If more than one PR matches and the intended one is unclear, ask which PR to use.
2. Read `docs/plans/REVIEW.md` if it exists. Keep its relevant open items and source links. Check the affected files to determine which items have been fixed; mark those completed with brief evidence rather than deleting their history. If the file covers another PR, preserve its entries and label each PR clearly.
3. Read every page of the PR's review bodies, inline review comments (including replies), and conversation comments. Use `gh api --paginate` for `repos/{owner}/{repo}/pulls/{number}/reviews`, `repos/{owner}/{repo}/pulls/{number}/comments`, and `repos/{owner}/{repo}/issues/{number}/comments`. Include comments on outdated diffs and resolved threads in the review; record their status when available. Do not rely on `gh pr view` alone, which can omit inline comments.
4. Inspect the files and surrounding context mentioned by feedback without changing them. Separate requested corrections from discussion, questions, and already addressed points. Group duplicates while preserving a link to each source comment. Match source comment URLs or IDs against existing entries, so repeated runs do not add the same request again. Add newly discovered requests and update the status of existing items. Do not invent a solution where the reviewer has only identified a problem; mark any needed decision as open.
5. Create or update only `docs/plans/REVIEW.md`. For each actionable theme, cite the relevant review comment URLs, state the requested change, name affected files where known, and list concrete implementation and verification steps. Record open and completed items distinctly, with a short completion checklist. If no correction is requested, say so and cite the comments reviewed. Ensure `/docs/plans/` is in `.gitignore`; add that rule only if missing. Do not stage or commit the plan file.
6. Verify the plan exists and `git check-ignore -v` confirms it is ignored. Compare `git status --short` with the initial working tree state to confirm you did not change unrelated files. Report the plan path and a concise summary of new, open, and completed items.
