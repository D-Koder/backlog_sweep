# Project Rules — Backlog_sweep

## What this repo is
Fresh repo, no existing codebase yet. Used for two kinds of work:
1. **Recurring content task** — weekly world news digest
2. **Ongoing dev work** — refactor / research tasks as they get added,
   tech stack not fixed yet (pick sensibly per task, note the choice
   in the PR description)

## When this runs
- Scheduled overnight via GitHub Actions (~1 hour before weekly Claude
  usage reset).
- Hard stop conditions for this run (since live usage can't be checked
  mid-run):
  - **Max 2 backlog items completed**, even if time/usage remains
  - **Max 3 hours** wall-clock (enforced by the workflow's job timeout)
  - If a Claude usage limit is hit mid-task, the run just stops there —
    no special handling needed, this happens naturally

## Boundaries
- No restricted files/folders (nothing exists yet).
- No extra confirmation needed for any action — free to install
  dependencies, create files/folders, restructure as needed.

## Verification before opening a PR
- Run lint if a linter is configured for whatever language was used.
- No fixed test command yet (empty repo) — if a task adds code, add a
  basic test alongside it and run it. If a task is pure content
  (e.g. the news digest), lint isn't applicable — just proofread for
  broken links/formatting before opening the PR.

## Priorities (highest → lowest)
1. Recurring: weekly world news digest (see below)
2. ♻️ Refactor tasks in the backlog
3. 🔬 Research tasks in the backlog
4. any other extra

## Recurring task: Weekly world news digest
- Compile interesting world news from the past week.
- Format: short, easy to read, skimmable — headline + 2-3 sentence
  summary per item, not full articles.
- Save as a new markdown file: `digests/YYYY-MM-DD.md`
- Counts as one of the 2 backlog items for the run.

## PR rules
- Never auto-merge. Always open a PR for review.
- Target branch: `develop` (subject to change — check this file is
  still current before each run, in case it's been updated).

## After each run
- Update `CLAUDE_BACKLOG.md`: mark completed items, move anything
  in-progress to "In Review".
- Summarize in the PR description: what was done, what's left, any
  risks or things worth a human double-check.
- Before the run's hard deadline, write `SWEEP_SUMMARY.md` at the repo
  root (what got done / attempted / left) and commit it — this is
  enforced by the workflow, not optional.
