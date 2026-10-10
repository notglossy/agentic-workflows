# Review instructions for <project>

Copy this file to `.github/ai-review-instructions.md` in a repository that uses the `pr-review` workflow. The
review appends it to its prompt at run time, read from the PR head. It is optional: delete it and the review
runs with the shared prompt only.

## Project context

<!-- A few lines the reviewer cannot infer from the code: what the project is, who uses it, trust boundaries,
target runtimes. -->

## Settled decisions

These were raised by earlier reviews, checked, and decided. They are not findings. Do not repeat one unless the
PR changes the code it describes so that the decision no longer holds; if it does, say what changed.

### <area or file>

- **<decision>.** <why it is intentional, naming the function or file it relies on>.
