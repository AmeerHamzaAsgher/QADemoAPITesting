# GitHub Actions CI/CD — Phase 8

**Phase:** Phase 8 — GitHub Actions CI/CD
**Workflow:** `QA-Demo API Tests`
**Workflow file:** `.github/workflows/api-tests.yml`
**Repository:** https://github.com/AmeerHamzaAsgher/QADemoAPITesting (existing, verified in Phase 7 — not recreated)

---

## GitHub Actions Overview

GitHub Actions runs the project's existing `npm test` command automatically, on GitHub's infrastructure, every time code is pushed or a pull request is opened against `main`. It does not replace anything from Phases 3-6 — it is purely an automatic trigger for the same Postman collection, environment, and Newman commands a developer already runs locally (see `docs/NEWMAN_API_AUTOMATION.md`). No new testing logic, framework, or Postman content was introduced in this phase.

---

## Workflow Architecture

```text
push to main / pull_request → main
              │
              ▼
   GitHub Actions runner (ubuntu-latest)
              │
   ┌──────────┼─────────────────────────────┐
   │          │                             │
Checkout   Set up Node.js 20.x         (npm cache restored
repository (actions/setup-node,         from package-lock.json
           cache: npm)                  hash, if present)
   │          │                             │
   └──────────┴─────────────────────────────┘
              │
              ▼
        npm ci   (installs newman + newman-reporter-htmlextra
                   from package-lock.json - exact, reproducible versions)
              │
              ▼
        npm test  (== the exact same command used locally)
              │
        ┌─────┴──────┐
        │            │
   test:main    test:workflow-auth/cart/products/admin
   (folders     (Phase 5 chained workflows, run as separate
   01-07)        Newman invocations - see the setNextRequest
                 compatibility note in docs/NEWMAN_API_AUTOMATION.md)
        │            │
        └─────┬──────┘
              ▼
     Newman exit code
        ┌─────┴──────┐
        ▼            ▼
      0 (pass)     non-zero (fail)
        │            │
        ▼            ▼
   job succeeds   job fails
              │
              ▼
   Upload reports/ as the "newman-reports" artifact (always, pass or fail)
```

---

## Trigger Configuration

```yaml
on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
```

- **`push` to `main`** — runs the full suite on every commit that lands on the main branch.
- **`pull_request` targeting `main`** — runs the full suite against the merge of a proposed change before it's merged, catching regressions before they reach `main`.
- No `schedule` trigger was added — the phase instructions explicitly say not to add scheduled execution without specific justification, and none exists here (this is a demo/portfolio project, not a monitored production service).
- Scoped to `main` specifically because that is the only branch this repository currently uses (confirmed in Phase 7's `git branch -vv`); if feature branches are introduced later, the `branches:` filters can be broadened.

> **Note on `on:` in YAML:** some generic YAML linters (e.g. plain PyYAML) parse the bare word `on` as the boolean `true` under YAML 1.1's implicit-boolean rules (the well-known "Norway problem"). This is **not** an issue for GitHub Actions itself — GitHub's workflow parser is schema-aware and always treats `on` as the trigger-configuration key, exactly as used here and in virtually every published GitHub Actions example. No functional risk; documented here only so a future contributor isn't confused by a generic linter's false positive.

---

## Node.js Setup

```yaml
- uses: actions/setup-node@v4
  with:
    node-version: '20.x'
    cache: 'npm'
```

**Version chosen: Node 20.x (an actively-maintained LTS release).** The local development machine used throughout this project runs Node v24.19.0 (a Current, non-LTS release — confirmed via `node --version` in Phase 6/8). Rather than pinning CI to that same fast-moving Current version, **20.x** was chosen deliberately because:
- It is a stable, actively-supported LTS release well within Newman 6.x's supported range (Newman/its dependency tree has no requirement for a Node version newer than 20).
- LTS releases are the standard recommendation for CI reliability — they change less often and are far less likely to introduce a surprise CI break unrelated to this project's own code.
- `package.json` declares `"engines": { "node": ">=18.0.0" }`, and 20.x comfortably satisfies that floor.

This is a deliberate, documented choice, not an arbitrary version pin.

`cache: 'npm'` enables GitHub's built-in dependency caching, keyed off `package-lock.json`'s hash — subsequent runs with an unchanged lockfile restore `~/.npm`'s cache instead of re-downloading every package, meaningfully speeding up `npm ci`.

---

## Dependency Installation

```yaml
- run: npm ci
```

`npm ci` (not `npm install`) was used because `package-lock.json` exists and is valid (created and committed in Phase 6) — `npm ci` installs the **exact** locked versions and is faster and more deterministic than `npm install` in a CI context, which is exactly what a reproducible pipeline needs. `node_modules/` is never committed (`.gitignore`) and is rebuilt fresh on every run.

---

## Newman Execution

```yaml
- run: npm test
```

The workflow deliberately does **not** duplicate or re-specify the Newman command — it invokes the exact same `npm test` script defined in `package.json` (Phase 6), which itself runs:
1. The main suite (`newman run ... --folder "01 - Authentication" ... --folder "07 - Negative Tests"`) — 38 requests, 126 assertions.
2. Each of the 4 Phase 5 workflow sub-folders as **separate** Newman invocations (`Workflow 01` through `Workflow 04`) — this is required because `pm.execution.setNextRequest(null)` stops an entire Newman run, not just a folder (see `docs/NEWMAN_API_AUTOMATION.md` for the full explanation); this compatibility handling lives entirely in `package.json` from Phase 6 and needed no changes for CI.

Both the collection (`postman/collections/QA-Demo Project.postman_collection.json`) and environment (`postman/environments/QA-Demo Environment.postman_environment.json`) used are the exact same, already-committed files used for local execution — nothing is regenerated or reconfigured for CI.

---

## Test Failure Behavior

```text
Any pm.test() assertion fails
         ↓
that Newman invocation exits with code 1
         ↓
the `&&`-chained npm script stops at that stage
         ↓
`npm test` (the shell process GitHub Actions ran) exits non-zero
         ↓
the "Run API test suite" step is marked failed by GitHub Actions
         ↓
the job (and therefore the whole workflow run) is marked failed
```

Nothing in this workflow suppresses or overrides Newman's exit code (no `continue-on-error: true`, no `|| true`, no `exit 0` wrapper) — a real API regression will fail CI exactly as it fails a local `npm test` run. This was verified in Phase 6 by deliberately forcing an assertion failure (`--env-var "apiUrl=<broken-url>"`) and confirming both raw `newman run` and `npm run test:main` exited with code 1; GitHub Actions' `run:` step semantics (any non-zero exit from the shell command fails the step) are standard, well-documented platform behavior that applies identically here — re-proving it via a throwaway broken commit on `main` was judged unnecessary risk for a guarantee this well-established, and the phase instructions explicitly say not to leave an intentional failure on `main`.

---

## Reports & Artifacts

`npm test` generates 5 HTML reports (via `newman-reporter-htmlextra`, already configured in Phase 6) into `reports/`:
```text
reports/newman-main-report.html
reports/newman-workflow-01-authentication-report.html
reports/newman-workflow-02-cart-report.html
reports/newman-workflow-03-products-report.html
reports/newman-workflow-04-admin-report.html
```

```yaml
- name: Upload Newman HTML reports
  if: always()
  uses: actions/upload-artifact@v4
  with:
    name: newman-reports
    path: reports/
    if-no-files-found: warn
    retention-days: 30
```

- **`if: always()`** — this step runs whether the test step passed or failed, so a failing run's partial reports (whatever HTML files *did* get generated before the failure) are still uploaded for debugging, not lost.
- **Artifact name:** `newman-reports` — download it from the "Artifacts" section of any workflow run's summary page.
- **`if-no-files-found: warn`** — logs a warning instead of failing the whole job in the unlikely case no report exists (e.g. `npm ci` itself failed before any test ran).
- **`retention-days: 30`** — a reasonable, non-indefinite retention window for a demo/portfolio project.
- Reports are **never committed to the repository** — `.gitignore` already excludes `reports/*` (keeping only a tracked `.gitkeep` placeholder, from Phase 6); this workflow does not change that.

---

## Secrets

**No GitHub Actions secrets were created**, because none are required. This project's target application (`https://qademo.com/`) has never had valid test/admin credentials available (documented since Phase 2's Discovery Limitations) — the collection's `username`/`password`/`authToken` environment variables are committed **empty** and referenced only as `{{variable}}` placeholders, never as literal values, in the collection, `package.json`, this workflow file, and `README.md`. Consistent with the phase instructions' rule #16 ("If the current QA-Demo API does not require secrets, do not create unnecessary GitHub secrets"), none were added.

If this project later obtains real test credentials, the correct pattern (not implemented here, since it isn't needed yet) would be:
1. Add them as **GitHub Actions Secrets** (repository Settings → Secrets and variables → Actions), never as workflow-file literals.
2. Inject them at runtime via `--env-var` on the `newman run`/`npm test` invocation, e.g. `--env-var "username=${{ secrets.QA_USERNAME }}" --env-var "password=${{ secrets.QA_PASSWORD }}"`, or by exporting them as environment variables consumed by a modified npm script.
3. Never `echo` or otherwise print a secret value in a workflow step (GitHub automatically masks known secret values in logs, but this project also simply never introduces one).

---

## Local vs. CI Execution

| | Local | CI (GitHub Actions) |
| --- | --- | --- |
| Command | `npm test` | `npm test` (identical) |
| Node.js | Whatever is installed locally (v24.19.0 during this project's development) | Node 20.x LTS (`actions/setup-node`) |
| Dependency install | `npm install` (Phase 6 setup) | `npm ci` (exact lockfile versions, faster, deterministic) |
| Collection/Environment | Same committed files | Same committed files (no divergence) |
| Reports | Written to local `reports/` directory, opened manually in a browser | Written to `reports/` in the runner's filesystem, then uploaded as the `newman-reports` artifact for download |
| Result | Exit code in the terminal (`$?` / `%errorlevel%` / `$LASTEXITCODE`) | Job status shown in the GitHub Actions UI (green check / red X) |

Both paths execute byte-identical test logic against the byte-identical Postman collection — CI is not a separate/parallel test suite, it is automated execution of the same one.

---

## Troubleshooting

| Symptom | Likely cause / fix |
| --- | --- |
| Workflow doesn't appear under the Actions tab | Confirm the file is at exactly `.github/workflows/api-tests.yml` and was actually pushed (`git log --stat` should show it); GitHub only detects workflow files on the default branch or in a PR that modifies them. |
| "Run API test suite" step fails immediately with a Node/npm error | Check the "Set up Node.js" and "Install dependencies" step logs first — a broken `package-lock.json` or an npm registry outage would fail there, before Newman ever runs. |
| Job fails with real assertion failures | This is the workflow working correctly — a real API regression should fail CI. Download the `newman-reports` artifact and open the relevant HTML report for the exact failing assertion and response. |
| No `newman-reports` artifact appears | Check whether the "Upload Newman HTML reports" step itself ran (it has `if: always()`, so it should even on failure) and whether `reports/` was empty (would only happen if `npm ci` failed before Newman ever started). |
| A pull request's checks show as "Expected — Waiting for status" indefinitely | The workflow's `pull_request` trigger only fires for PRs targeting `main`; confirm the PR's base branch is `main`. |

---

## Restrictions Honored

Per the phase's explicit restrictions, this phase did **not**: replace Newman or Postman, introduce a different test framework, add Docker, add a deployment pipeline, deploy the application, modify any API endpoint or API behavior, remove any Phase 4 test or Phase 5 workflow, remove any existing Newman automation, commit any secret, force-push, or create a second GitHub repository.
