# agentic-workflows

Shared [gh-aw](https://github.github.com/gh-aw/) workflows for notglossy repositories.

| Workflow | What it does |
|---|---|
| [`pr-review`](workflows/pr-review.md) | Reviews same-repo, non-draft PRs for bugs, security and over-engineering. Copilot CLI in gh-aw's sandbox, inference via OpenRouter (BYOK). Posts inline comments plus one `COMMENT` review. |

## Add to a repository

```sh
gh aw add notglossy/agentic-workflows/pr-review@main
gh aw compile pr-review --approve                   # approve the new AI_API_KEY secret (first install only)
gh aw update pr-review                              # writes .github/aw/actions-lock.json (container pins)
gh secret set AI_API_KEY --repo notglossy/<repo>    # OpenRouter key
git add .github .gitattributes && git commit -m "ci: add AI PR review"
```

It also writes `.gitattributes` (lock files marked generated), `.github/skills/agentic-workflows/` (a skill for
coding agents) and `.vscode/settings.json`; keep or drop them as you like. The files the review needs:

- `.github/workflows/pr-review.md`: the source, with a `source:` field pointing back here
- `.github/workflows/pr-review.lock.yml`: the compiled workflow Actions runs (never edit by hand)
- `.github/aw/actions-lock.json`: pinned action SHAs for reproducible compiles

Optional, per repository:

- `.github/ai-review-instructions.md`: project context and settled decisions, appended to the prompt at run time.
  Start from [`examples/ai-review-instructions.md`](examples/ai-review-instructions.md).
- `AI_MODEL` repository variable: any OpenRouter model id with tool calling and streaming (default
  `google/gemini-3.8-flash`). If it costs more than the `models.default-ai-credits-pricing` rate, raise that too.

## Update

`main` is the release channel. `gh aw add` records the commit it installed as a SHA in the workflow's `source:`
field, and `gh aw update` then moves to the newest commit on `main` that is at least 3 days old (gh-aw's cooldown).
Tags are not followed: putting a tag in `source:` makes `update` look for GitHub Releases instead. So push finished
changes to `main`, and work on a branch until then. In each repository:

```sh
gh aw update pr-review     # 3-way merge keeps local edits, then recompiles
git add .github && git commit -m "ci: update AI PR review"
```
