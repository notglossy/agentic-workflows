# agentic-workflows

Shared [gh-aw](https://github.github.com/gh-aw/) workflows for notglossy repositories.

| Workflow | What it does |
|---|---|
| [`pr-review`](workflows/pr-review.md) | Reviews same-repo, non-draft PRs for bugs, security and over-engineering. Copilot CLI in gh-aw's sandbox, inference via OpenRouter (BYOK). Posts inline comments plus one `COMMENT` review. |
| [`feature-triage`](workflows/feature-triage.md) | Grades a feature request issue for feasibility when a maintainer adds the `triage:evaluate` label. Posts one scorecard comment and applies `feasibility/*` and `size/*` labels. Same sandbox and OpenRouter setup as `pr-review`. |

## Add to a repository

The steps below install `pr-review`; for `feature-triage`, substitute its name and see
[Feature triage](#feature-triage) for the label it needs.

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
gh aw update pr-review     # 3-way merge keeps local edits, then recompiles (likewise feature-triage)
git add .github && git commit -m "ci: update AI PR review"
```

## Feature triage

A maintainer adds the `triage:evaluate` label to an issue; anyone with write access can. gh-aw removes the
label as the run starts, so adding it again re-runs the triage, which hides the previous scorecard as outdated and
replaces stale grade labels. It never runs on its own when an issue is opened: anyone can open an issue, and that
would let any visitor spend `AI_API_KEY` credits.

Create the trigger label once per repository. The grade labels are created on first use.

```sh
gh label create triage:evaluate --repo notglossy/<repo> --color 5319e7 --description "Run AI feasibility triage"
```

Optional, per repository:

- `.github/ai-feature-triage-instructions.md`: project scope, rejected ideas and architecture notes, appended to
  the prompt at run time. Start from [`examples/ai-feature-triage-instructions.md`](examples/ai-feature-triage-instructions.md).
- The shared `AI_MODEL` variable applies here too.

### Issue template

[`issue-templates/feature-request.yml`](issue-templates/feature-request.yml) is an issue form whose sections the
triage prompt knows by name. `gh aw add` does not install it. To share it across every repository on the account,
put it in a public `notglossy/.github` repository at `.github/ISSUE_TEMPLATE/feature-request.yml`. GitHub then
offers it in any notglossy repository that has no `.github/ISSUE_TEMPLATE/` folder of its own. The fallback is all
or nothing: a repository with even one template of its own sees none of the shared ones, so copy the file into
that repository's folder instead.
