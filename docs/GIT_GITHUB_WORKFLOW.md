# Git & GitHub Workflow — Phase 7

**Phase:** Phase 7 — Git & GitHub Integration
**Repository:** already existed prior to this phase (initialized during the Phase 3 push — see `prompts/Git&Github-ProjectPushGuide.txt` for that original walkthrough)
**Remote:** `origin` → https://github.com/AmeerHamzaAsgher/QADemoAPITesting
**Branch:** `main`

This document describes the day-to-day Git/GitHub workflow for this project going forward. It complements (does not replace) `prompts/Git&Github-ProjectPushGuide.txt`, which documents the original one-time repository setup and first push during Phase 3.

---

## Git Initialization

Already done (Phase 3). To verify a repository exists rather than re-initializing:
```bash
git status
```
If this prints branch/tracking info instead of `fatal: not a git repository`, the repo already exists — **never run `git init` again** on an existing repository; it is safe (git detects an existing `.git/` and just reinitializes config, doing no harm), but is unnecessary and was not needed in this phase.

## Branch

This project uses a single long-lived branch:
```bash
git branch -vv
# * main <sha> [origin/main] <last commit subject>
```
`main` is both the local working branch and the one tracked against `origin/main`. No feature branches have been introduced yet (out of scope for this phase).

## Status

Always check status before staging anything:
```bash
git status
```
Shows: current branch, whether it's ahead/behind/up-to-date with `origin/main`, modified tracked files, and untracked files. This project's `.gitignore` (see below) keeps `git status` output focused on real project changes — `node_modules/` and generated `reports/*` never appear as noise.

## Add (staging)

Review before staging blindly:
```bash
git add --dry-run -A .      # preview exactly what `git add -A .` would stage, without staging anything
git add -A .                # stage it
git status                  # confirm what's now staged
git diff --cached --stat    # summary of staged changes, file-by-file line counts
```
`git add --dry-run` first is the safest habit — it lets you catch anything unexpected (an accidentally-created temp file, a credential-looking value) before it ever touches the staging area.

## Commit

```bash
git commit -m "Descriptive summary of what changed and why"
```
Commit message conventions used in this project:
- Present-tense, descriptive subject line naming the phase(s)/feature covered.
- A body listing the specific changes grouped by phase when a commit spans multiple phases (as the Phase 4-7 commit in this project does — see `git log`).
- Never amend or force-rewrite a commit that has already been pushed (see "Remote" below).

## Remote

```bash
git remote -v
```
Expected output for this project:
```text
origin  https://github.com/AmeerHamzaAsgher/QADemoAPITesting (fetch)
origin  https://github.com/AmeerHamzaAsgher/QADemoAPITesting (push)
```
Both fetch and push point to the same, existing repository. This project never creates a new remote or repository — it always reuses this one.

## Push

```bash
git push            # ordinary push once upstream is already tracked (it is, after the Phase 3 `-u` push)
git push -u origin main   # only needed the very first time a local branch is connected to a remote one
```
**Never use `git push --force` / `--force-with-lease`** on this repository unless explicitly instructed and the reason is fully understood — it can overwrite remote history other people (or your future self) rely on. This project has never needed a force-push.

## Pull

To bring in any changes made directly on GitHub (e.g. via the web UI) before continuing local work:
```bash
git pull
```
Run this before starting new work if there's any chance the remote has commits the local repo doesn't (e.g. after using the GitHub web editor, or if working from a second machine).

## Log

```bash
git log --oneline --decorate -10     # quick recent history with branch/tag pointers
git log --oneline --decorate --all   # full history across all branches
```
Use this to confirm a commit exists locally and, after a `fetch`/`pull`, to confirm `HEAD -> main` and `origin/main` point at the same commit (fully synchronized).

## Branch Management

Not yet needed for this project (single-branch workflow). If a future phase introduces feature branches:
```bash
git checkout -b feature/some-change
# ... work, commit ...
git push -u origin feature/some-change
```
then merge back into `main` via a pull request on GitHub (preferred over a local merge, for reviewability) — this pattern is not yet in use here but documented for when it becomes relevant.

## GitHub Workflow (this project's process)

```text
Work happens locally in Postman/scripts/docs
              ↓
git status  (see what changed)
              ↓
git add --dry-run -A .  (preview staging)
              ↓
git add -A .  (stage)
              ↓
git diff --cached --stat  (review staged changes)
              ↓
git commit -m "..."  (checkpoint locally)
              ↓
git push  (publish to GitHub)
              ↓
Verify on github.com and via `git status` / `git log`
```

Each phase of this project (Phase 1 through the current phase) represents one logical unit of work; ideally each gets its own commit (see `prompts/Git&Github-ProjectPushGuide.txt`'s "Recommended Commit History" section for the intended per-phase pattern). In practice, Phases 4 through 7 of this project were completed across a single working session before the next push, so they were committed together in one comprehensive, clearly-labeled commit rather than reconstructed into four artificial after-the-fact commits — the commit message groups the changes by phase for readability. Going forward, committing at the end of each phase (rather than batching several) is the preferred practice.

## Safe Handling of Secrets

**Never commit:** real passwords, API keys, access tokens, refresh tokens, client secrets, session cookies, or production credentials.

This project's actual safeguards:
1. **Postman environment secret-typed variables** (`authToken`, `refreshToken`, `password`) are always committed **empty** — the collection references them as `{{authToken}}` etc., never with a literal value baked in. This was true from Phase 3 onward and re-verified in this phase.
2. **`.gitignore`** excludes `node_modules/`, `.env` / `.env.local`, and generated Newman reports (`reports/*`, keeping only a tracked `.gitkeep` placeholder) — none of these should ever be staged.
3. **Pre-commit secret scan (manual, this phase):** before staging, the working tree was searched for common secret patterns (`password=`, `api_key=`, `client_secret=`, `access_token=`, `refresh_token=` followed by a literal, non-`{{variable}}` value) across every tracked file type. Result: clean — no matches.
4. If a real credential is ever needed locally (e.g. to exercise the authenticated Postman workflows), it belongs in the Postman environment's variable **value** field only, on the developer's own machine, and should **never** be staged/committed — treat the environment file's tracked version as a template with safe/empty defaults, matching how it has always been kept in this repository.

If a secret is ever accidentally committed, the fix is **not** a simple new commit removing it (the value remains in history) — it requires history rewriting (`git filter-repo` or equivalent) and credential rotation. This has not been needed in this project; prevention (steps 1-3 above) is the actual strategy in use.

---

## This Phase's Execution Log

```text
1. git status          → confirmed: existing repo, branch main, tracking origin/main, up to date,
                          with modified/untracked files representing Phases 4-7's work
2. git branch -vv       → confirmed: * main <sha> [origin/main] "Complete Phase 3 Postman API implementation"
3. git remote -v        → confirmed: origin already points to
                          https://github.com/AmeerHamzaAsgher/QADemoAPITesting (fetch + push)
4. git log --oneline    → confirmed: 1 prior commit, no history was deleted or altered
5. Secret scan          → clean, no matches (see "Safe Handling of Secrets" above)
6. .gitignore reviewed  → tightened (reports/* + tracked .gitkeep instead of ignoring the whole
                          reports/ directory outright) - see docs/NEWMAN_API_AUTOMATION.md
7. git add --dry-run -A → previewed exactly 24 files to be staged; confirmed no node_modules/ or
                          generated report files among them
8. git add -A           → staged
9. git diff --cached --stat → reviewed: 25 files changed (24 staged + this doc), no unexpected
                          binary/large files, no secret-looking values
10. git commit -m "..."  → created (see git log for the exact message)
11. git push             → pushed to origin/main
12. git status           → confirmed clean working tree, up to date with origin/main
```

See the end of the Phase 7 chat report for the exact commit hash, push confirmation, and final `git status` output.
