# Repository instructions for coding agents

## Commits and release impact

Write commit messages in English using Conventional Commits. Keep each commit focused on one coherent change so its release impact is clear to readers and changelog tools such as git-cliff.

- Use `fix(scope): specific outcome` for a backward-compatible bug fix (Patch).
- Use `feat(scope): specific outcome` for a backward-compatible new capability (Minor).
- Mark any incompatible change as Major with `!` after the type or scope, for example `feat(scope)!: specific outcome`. Add a `BREAKING CHANGE: ...` footer that states what changed and how existing users should migrate.
- Use an appropriate type such as `docs`, `test`, `refactor`, or `chore` for work without a user-facing feature or bug fix. Do not label a change `fix` or `feat` solely to force a release.

The subject must name the affected behavior or contract. Avoid generic subjects such as `fix: address review feedback`. Add a body that explains what changed, why it was needed, and the relevant compatibility or migration impact. State `Release impact: Patch`, `Minor`, `Major`, or `None` in the body; the Conventional Commit type and breaking marker must agree with it. For a commit covering several closely related files, list the concrete changes in the body. Keep any `Co-authored-by` trailers after the body and use the actual contributor's identity.

Example:

```text
fix(issue-templates): capture coding agent session evidence

Require a session link in guidance bug reports. Accept a redacted transcript
when the agent cannot provide a shareable URL, so maintainers can reproduce
the failure without exposing private content.

Release impact: Patch; the form adds a required field but changes no runtime API.
```
