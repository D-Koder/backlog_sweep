# Claude Backlog Sweep

Runs Claude Code unattended overnight to work through your project
backlog before your Claude Pro weekly usage resets.

## ⚠️ Read this before relying on it

- There's no reliable way for a script to check "how much weekly usage
  is left" mid-run — `/usage` is interactive-only. So instead of a live
  number, this uses **time windows + a task cap** as the safety limit.
- **You are the source of truth for the reset time.** It lives in
  `config/reset-schedule.txt`. Nothing here reads it from Claude
  automatically — check Settings → Usage yourself and put it in that
  file.

---

## One-time setup (10 minutes)

### 1. Get a Claude Code OAuth token (works with Pro, no API key needed)
On your machine, with Claude Code installed:
```
claude setup-token
```
Keep the printed token for step 3.

### 2. Push this folder to a GitHub repo

### 3. Add the token as a repo secret
Repo → Settings → Secrets and variables → Actions → New repository secret
- Name: `CLAUDE_CODE_OAUTH_TOKEN`
- Value: the token from step 1

### 4. Install the Claude GitHub App
https://github.com/apps/claude — grants the Contents/Issues/Pull
requests permissions the workflow needs.

### 5. Set your reset time
Open `config/reset-schedule.txt` and put in the exact date + time of
your next weekly reset (from Settings → Usage), **Melbourne local
time**, format `DD/MM/YYYY HH:MMAM` or `DD/MM/YYYY HH:MMPM`, e.g.
`18/08/2026 10:10PM`. A placeholder is already in there — confirm or
correct it. Daylight saving is handled automatically, no need to
adjust for it yourself.

### 6. Fill in `CLAUDE.md` and `CLAUDE_BACKLOG.md`
See `INTAKE_TEMPLATE.md` for questions to answer for each project
before the first unattended run.

---

## How a run works

1. **Every 15 minutes**, a lightweight check job runs (a few seconds,
   does nothing most of the time). It reads `config/reset-schedule.txt`
   and asks: "is now between 3 hours and 10 minutes before that reset?"
2. If **no** → the job exits immediately, nothing else runs.
3. If **yes** → the real sweep starts:
   - Claude Code reads `CLAUDE.md` (rules) and `CLAUDE_BACKLOG.md`
     (priority list), works top-down, highest value first, up to
     2 items.
   - Tests/lints each change, opens a PR (never auto-merges).
   - Updates `CLAUDE_BACKLOG.md`.
4. **10 minutes before the reset**, Claude stops starting new work,
   writes `SWEEP_SUMMARY.md` (what got done / attempted / still
   queued), commits it, and ends.
5. A workflow step posts that summary directly into the GitHub Actions
   run's "Summary" tab — one page to check, no digging through logs.
6. A hard 190-minute job timeout is the last-resort backstop if Claude
   somehow doesn't self-stop.
7. On success, the workflow **auto-advances** `config/reset-schedule.txt`
   by +7 days and commits it — so next week just works, assuming your
   reset stays on the same weekly cadence. If it ever shifts, edit the
   file again with the new value.

## Updating the reset time later
Just edit `config/reset-schedule.txt` with the new date/time (same
`DD/MM/YYYY HH:MMAM/PM` format), commit, and push. Next poll picks it
up — no workflow file changes needed.

## A cost note on the 15-minute check
If this repo is **private**, GitHub Actions free-tier minutes are
capped (2,000/month). The check job is cheap (a few seconds) but adds
up to roughly 30–60 min/month on its own. Public repos have no cap.

## Files in this repo

| File | Purpose |
|---|---|
| `CLAUDE.md` | Rules Claude follows every sweep |
| `CLAUDE_BACKLOG.md` | The prioritized task list |
| `INTAKE_TEMPLATE.md` | Questions to answer before adding a new project/task |
| `config/reset-schedule.txt` | Your weekly reset time — the only thing you need to keep current |
| `.github/workflows/backlog-sweep.yml` | The check + sweep automation |
