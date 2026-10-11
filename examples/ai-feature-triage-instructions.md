# Feature triage instructions for <project>

Copy this file to `.github/ai-feature-triage-instructions.md` in a repository that uses the `feature-triage`
workflow. The triage appends it to its prompt at run time, read from the default branch. It is optional: delete it
and the triage grades "Fit" from the README alone.

## Project context

<!-- What the project is for and who uses it, specific enough to judge whether a request is in scope. -->

## Out of scope

These were requested before and decided against. A request for one of them grades as low fit; link the earlier
issue where there is one.

- **<feature>.** <why not, in one line>.

## Architecture notes

<!-- Where things live and what the hard limits are, so the triage spends fewer turns looking. For example:
`src/core/` holds business logic with no I/O; storage is SQLite only, so anything that needs another database is
size L or larger. -->
