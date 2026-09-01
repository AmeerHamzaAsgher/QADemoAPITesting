# Newman API Automation — Phase 6

**Phase:** Phase 6 — Newman API Automation
**Collection:** `QA-Demo Project`
**Environment:** `QA-Demo Environment`
**Application:** https://qademo.com/
**Source of truth:** the Postman collection/environment as finalized in Phase 5.1 (`docs/POSTMAN_UI_ARCHITECTURE.md`)

---

## What is Newman?

Newman is Postman's official command-line collection runner. It executes a Postman collection (`.postman_collection.json`) against a Postman environment (`.postman_environment.json`) exactly as the Postman desktop app would — running every request, evaluating every `pm.test()` assertion, and resolving every `{{variable}}` — but from a terminal, with no GUI required. That makes it the bridge between "a collection someone runs by hand in Postman" and "a test suite a script (or later, a CI pipeline) can run automatically and get a pass/fail exit code from."

---

## Installation

Newman and the HTML report generator are declared as **project devDependencies** in `package.json` (not relied upon as a global install), so anyone cloning this repository gets the exact same tooling:

```bash
npm install
```

This installs, locally into `node_modules/`:
- `newman` (^6.2.2 — verified installed version: **6.2.2**)
- `newman-reporter-htmlextra` (^1.23.1 — adds the `htmlextra` HTML report format)

No global `npm install -g newman` is required or assumed; all `npm run` scripts resolve the locally installed binary automatically (npm always checks `node_modules/.bin` first).

**Prerequisites verified for this project:** Node.js v24.19.0, npm 11.17.0 (both already present in this environment; no version pin was added since Newman 6.x has no unusual Node version requirement).

---

## Project Setup

```text
QADemoAPITesting/
├── package.json              ← npm project definition, all test scripts
├── package-lock.json         ← exact dependency versions (committed, for reproducible installs)
├── node_modules/              ← installed by `npm install` (gitignored)
├── postman/
│   ├── collections/QA-Demo Project.postman_collection.json
│   └── environments/QA-Demo Environment.postman_environment.json
└── reports/                   ← generated Newman HTML reports land here (gitignored except .gitkeep)
```

---

## A Newman-Specific Compatibility Finding (important — read before running the whole collection)

The Phase 5 workflows (`08 - Workflows`) use `pm.execution.setNextRequest(...)` to chain requests, and each workflow's gated/terminal step calls `pm.execution.setNextRequest(null)` to end the chain (see `docs/API_DATA_CHAINING.md`). **`setNextRequest(null)` stops the entire run, not just the current folder** — this is standard Postman/Newman runner behavior, not a bug in this collection, but it has a real consequence for Newman specifically:

> If you run the **whole collection** (or the whole `08 - Workflows` folder) in a single `newman run` with no valid test credentials configured, `Workflow 01 - Authentication Chaining`'s Login step fails (as expected/documented), calls `setNextRequest(null)`, and **the entire Newman run stops right there** — `Workflow 02` (the flagship, fully-executable Cart lifecycle), `Workflow 03`, and `Workflow 04` never get a chance to run, even though they don't depend on Workflow 01 at all.

This was verified directly:
```text
newman run <collection> -e <environment> --folder "08 - Workflows"
→ ran exactly 1 request (Workflow 01's Login), then stopped. 15 other requests never executed.
```

**The fix used in this project:** each of the 4 workflow sub-folders is run as its **own separate `newman run` invocation** (via `--folder "<exact sub-folder name>"`), so each workflow's own `setNextRequest(null)` only ever terminates its own run. The 7 non-chained resource/negative-test folders (`01`-`07`, 38 requests, no `setNextRequest` calls anywhere in them) are safely run together in a single invocation. This is exactly what `npm test` does — see below. No collection content was changed to work around this; it is purely an execution-strategy decision.

---

## Newman Commands

### Run the main suite directly (folders `01`-`07`, 38 requests, no data chaining)
```bash
newman run "postman/collections/QA-Demo Project.postman_collection.json" \
  -e "postman/environments/QA-Demo Environment.postman_environment.json" \
  --folder "01 - Authentication" --folder "02 - Products" --folder "03 - Cart" \
  --folder "04 - Orders" --folder "05 - Admin" --folder "06 - Images" --folder "07 - Negative Tests" \
  -r cli,htmlextra --reporter-htmlextra-export reports/newman-main-report.html
```

### Run a single Phase 5 workflow directly
```bash
newman run "postman/collections/QA-Demo Project.postman_collection.json" \
  -e "postman/environments/QA-Demo Environment.postman_environment.json" \
  --folder "Workflow 02 - Cart CRUD Lifecycle" \
  -r cli,htmlextra --reporter-htmlextra-export reports/newman-workflow-02-cart-report.html
```
(Substitute the sub-folder name for `Workflow 01 - Authentication Chaining`, `Workflow 03 - Products List to Detail`, or `Workflow 04 - Admin Product Stock Update (Requires Authentication)` to run the others individually.)

Both commands use the **existing, unmodified** collection/environment files from Phase 3–5.1 — no environment-specific URLs or credentials are hardcoded on the command line; `{{apiUrl}}` etc. resolve from `QA-Demo Environment`.

---

## npm Scripts

Defined in `package.json`:

| Script | What it does |
| --- | --- |
| `npm test` | **Primary command.** Runs the main suite, then all 4 workflows in sequence (5 Newman invocations total). Stops at the first failing stage (`&&` chaining) and its exit code reflects that failure. |
| `npm run test:main` | Just the main suite (folders `01`-`07`, 38 requests). Generates `reports/newman-main-report.html`. |
| `npm run test:cli` | Same as `test:main` but CLI output only, no HTML report — useful for a fast local check. |
| `npm run test:workflows` | All 4 Phase 5 workflows, in sequence, without the main suite. |
| `npm run test:workflow-auth` / `test:workflow-cart` / `test:workflow-products` / `test:workflow-admin` | Run one specific workflow in isolation. Each generates its own HTML report. |

`npm test` is the command a QA engineer (or, in a later phase, a CI pipeline) should run to execute the complete suite:
```bash
npm test
```

---

## Collection / Environment Execution — Verified Results

Every command above was actually executed against the live `https://qademo.com/api` during this phase, using the exact same environment file used throughout Phases 3-5.1 (no values hardcoded, no values changed).

| Run | Requests | Test scripts | Assertions | Failed | Exit code |
| --- | --- | --- | --- | --- | --- |
| Main suite (`01`-`07`) | 38 | 76 | 126 | 0 | 0 |
| Workflow 01 - Authentication Chaining | 1 (of 2 - gated, see below) | 1 | 3 | 0 | 0 |
| Workflow 02 - Cart CRUD Lifecycle | 8 (all) | 16 | 25 | 0 | 0 |
| Workflow 03 - Products List to Detail | 2 (all) | 4 | 6 | 0 | 0 |
| Workflow 04 - Admin Stock Update | 1 (of 4 - gated, see below) | 1 | 2 | 0 | 0 |
| **Total** | **50 executed / 54 in collection** | **98** | **162** | **0** | **all 0** |

**Confirmed working:**
- Collection loads successfully (Newman parses the v2.1 JSON with no errors).
- Environment loads successfully (`{{apiUrl}}`, `{{sessionId}}`, etc. all resolve — no `{{undefinedVariable}}` ever appeared in a request URL/body/header in any run).
- Phase 4 tests execute — all 126 assertions across the 38 main-suite requests ran and passed.
- Phase 5 data-chaining workflows execute — Workflow 02 ran its full 8-step Create→Read→Update→Read→Delete→Read→Cleanup chain via `pm.execution.setNextRequest`, with every dynamically-captured variable (`wf_productId`, `wf_sessionId`, quantities, etc.) resolving correctly; Workflow 03's 2-step chain ran correctly.
- Workflows 01 and 04 correctly executed their Login step, correctly asserted the documented "no credentials configured" failure shape, and correctly terminated via `setNextRequest(null)` without attempting their downstream authenticated steps — this is the expected, by-design outcome documented since Phase 5 (`docs/API_DATA_CHAINING.md`, Known Limitations), not a Newman defect.

Only 4 of the 54 collection requests did not execute in this pass (Workflow 01's step 2, Workflow 04's steps 2-4) — all 4 are gated behind valid login credentials that are not available in this environment, exactly as documented since Phase 5. Every request that *could* run without credentials ran and passed.

---

## Reports

HTML reports (via `newman-reporter-htmlextra`) are written to `reports/`:
```text
reports/newman-main-report.html
reports/newman-workflow-01-authentication-report.html
reports/newman-workflow-02-cart-report.html
reports/newman-workflow-03-products-report.html
reports/newman-workflow-04-admin-report.html
```
Open any of these directly in a browser for a request-by-request, assertion-by-assertion breakdown (searchable, filterable, with request/response bodies). A CLI summary (via the built-in `cli` reporter) is always printed to the terminal at the same time — every `npm run test:*` / `npm test` invocation uses `-r cli,htmlextra` so you get both without re-running.

`reports/` is `.gitignore`d (`reports/*` with `!reports/.gitkeep`) — generated report files are never committed, but the empty directory structure is preserved via a tracked `.gitkeep` placeholder, so a fresh clone always has somewhere for reports to land.

---

## Exit Codes

Newman follows the standard CLI convention and this was explicitly verified in this phase, not just assumed:

```text
Exit code 0  → all assertions passed (verified: main suite, all 4 workflow runs)
Exit code 1  → one or more assertions failed (verified: see below)
```

**Verification performed:** the main suite was deliberately re-run with `--env-var "apiUrl=<intentionally-broken-url>"` (a harmless, temporary CLI override — no file was modified) to force real request failures. Both a raw `newman run ...` invocation and an `npm run test:main -- --env-var ...` invocation were confirmed to exit with code **1** and print a full assertion-failure breakdown, proving the exit code correctly propagates both directly from Newman and through an npm script wrapper. No collection or API behavior was changed to produce this — it was reverted immediately after the check (the override only exists on that one command line, never in a file).

This means `npm test` is safe to use as a pass/fail gate: it returns 0 only when every stage's every assertion passed, and non-zero the moment anything fails (later phases, e.g. CI/CD, can rely on this without additional wiring).

---

## Troubleshooting

| Symptom | Likely cause / fix |
| --- | --- |
| `newman: command not found` | Run `npm install` first; use `npm run test:main` (not a bare `newman` command) so the locally-installed binary is used, or install Newman globally with `npm install -g newman`. |
| A request shows `{{someVariable}}` literally in the URL/body instead of a resolved value | The `-e` flag (environment file) was omitted, or the wrong environment was passed. Always include `-e "postman/environments/QA-Demo Environment.postman_environment.json"`. |
| Only Workflow 01's Login request ran when you tried to run the whole `08 - Workflows` folder (or the whole collection) in one command | Expected — see "A Newman-Specific Compatibility Finding" above. Run each workflow sub-folder separately (`npm run test:workflow-*`), or use `npm test` which already does this correctly. |
| Auth-gated requests/workflows show `401`/`400` and "no credentials configured" style test names | Expected in this environment — no valid test/admin credentials exist for `https://qademo.com/`. Populate `username`/`password` in `QA-Demo Environment` with valid credentials to exercise the authenticated success paths (see `docs/API_DISCOVERY.md`, Discovery Limitations). |
| `npm install` prints deprecation/vulnerability warnings | These come from Newman's own transitive dependency tree (e.g. an old `uuid`/`har-validator` pulled in by Newman or the reporter), not from anything added by this project. Running `npm audit fix --force` was deliberately **not** done in this phase, since it can pull in breaking major-version changes to Newman itself — out of scope for Phase 6. |
| HTML report file didn't appear in `reports/` | Confirm the `reports/` directory exists (it's tracked via `.gitkeep`) and that the command actually completed (check the exit code) — `newman-reporter-htmlextra` only writes the file at the end of a successful run. |

---

## How to Execute the Complete API Suite

```bash
# one-time setup
npm install

# run everything (main suite + all 4 Phase 5 workflows), with HTML reports
npm test

# then open any of these in a browser to inspect results in detail:
#   reports/newman-main-report.html
#   reports/newman-workflow-01-authentication-report.html
#   reports/newman-workflow-02-cart-report.html
#   reports/newman-workflow-03-products-report.html
#   reports/newman-workflow-04-admin-report.html
```

`npm test`'s exit code (`echo $?` on macOS/Linux, `echo %errorlevel%` on Windows cmd, `$LASTEXITCODE` in PowerShell) tells you, unambiguously, whether the whole suite passed.
