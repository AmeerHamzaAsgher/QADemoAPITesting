# QA-Demo API Testing

## Project

```text
QA-Demo API Testing
```

## Application

```text
https://qademo.com/
```

## Project Location

```text
D:\API Testing\Newman API Testing\QADemoAPITesting
```

## Current Phase

```text
Phase 7 - Git & GitHub Integration
```

```text
Phase 1   — Completed
Phase 2   — Completed
Phase 3   — Completed
Phase 4   — Completed
Phase 5   — Completed
Phase 5.1 — Completed
Phase 6   — Completed
Phase 7   — Completed
```

See [`docs/API_DISCOVERY.md`](docs/API_DISCOVERY.md) for the full API discovery report, [`docs/api-inventory.json`](docs/api-inventory.json) for the machine-readable endpoint inventory, [`docs/POSTMAN_IMPLEMENTATION.md`](docs/POSTMAN_IMPLEMENTATION.md) for the Phase 3 implementation matrix, [`docs/API_TEST_AUTOMATION.md`](docs/API_TEST_AUTOMATION.md) for the Phase 4 test coverage and execution report, [`docs/API_DATA_CHAINING.md`](docs/API_DATA_CHAINING.md) for the Phase 5 workflow architecture and execution evidence, [`docs/POSTMAN_UI_ARCHITECTURE.md`](docs/POSTMAN_UI_ARCHITECTURE.md) for the Phase 5.1 collection navigation guide and regression proof, [`docs/NEWMAN_API_AUTOMATION.md`](docs/NEWMAN_API_AUTOMATION.md) for the Phase 6 command-line execution guide, and [`docs/GIT_GITHUB_WORKFLOW.md`](docs/GIT_GITHUB_WORKFLOW.md) for the Phase 7 Git/GitHub workflow.

## Postman Collection

```text
QA-Demo Project
```

## Postman Environment

```text
QA-Demo Environment
```

## Using the Project

### 1. Postman (interactive / manual testing)
1. Open Postman → **Import** → select both `postman/collections/QA-Demo Project.postman_collection.json` and `postman/environments/QA-Demo Environment.postman_environment.json`.
2. Select **QA-Demo Environment** from the environment dropdown (top-right).
3. Browse the collection's 8 folders (`01 - Authentication` through `07 - Negative Tests`, plus `08 - Workflows`) — every request's description explains its purpose and lists the exact assertions it runs (`Tests:` section). See `docs/POSTMAN_UI_ARCHITECTURE.md` for the full navigation guide.
4. To exercise a Phase 5 chained workflow (e.g. the fully-live Cart CRUD lifecycle), open `08 - Workflows` → run a sub-folder via the Collection Runner, or click **Send** on its first request and let `pm.execution.setNextRequest(...)` auto-advance through the chain.

### 2. Newman (command-line automation)
```bash
npm install   # one-time setup - installs Newman + the HTML reporter locally
npm test      # runs the full suite (main requests + all 4 Phase 5 workflows), generates HTML reports in reports/
```
See [`docs/NEWMAN_API_AUTOMATION.md`](docs/NEWMAN_API_AUTOMATION.md) for individual npm scripts, direct `newman run` commands, HTML/CLI reporting, exit-code behavior, and troubleshooting (including a documented Newman-specific compatibility finding about workflow chaining).

### 3. Git / GitHub
```bash
git status                  # see what's changed
git add --dry-run -A .      # preview staging
git add -A .                # stage
git diff --cached --stat    # review staged changes
git commit -m "..."         # checkpoint locally
git push                    # publish to GitHub (origin/main)
```
See [`docs/GIT_GITHUB_WORKFLOW.md`](docs/GIT_GITHUB_WORKFLOW.md) for the full workflow, branch/remote conventions, and how secrets are safely kept out of version control.

## Purpose

This project will eventually become a professional API automation testing framework for the QA Demo application, built using:

* Postman
* Newman
* Git
* GitHub
* GitHub Actions
* CI/CD

Phase 1 established the initial project foundation — folder structure, an empty Postman collection with placeholder folders, a Postman environment with foundational variables, and basic documentation. Phase 2 performed API discovery and analysis of the QA Demo application; the discovered API base URL, endpoint inventory, authentication mechanism, request/response structures, and a Postman implementation plan are documented in `docs/API_DISCOVERY.md` and `docs/api-inventory.json`. Phase 3 implemented the discovered APIs as actual Postman requests (24 requests across 6 resource-based folders) in the `QA-Demo Project` collection, using the Phase 2 findings as the source of truth — see `docs/POSTMAN_IMPLEMENTATION.md`. Phase 4 converted those requests into an automated test suite: all 38 requests (the original 24 plus 14 dedicated negative/boundary requests in a new `07 - Negative Tests` folder) now carry live-verified `pm.test()` assertions covering status codes, response time, headers, Content-Type, required fields, data types, response structure, CRUD behavior, authentication, negative/boundary scenarios, error responses, and JSON schema — see `docs/API_TEST_AUTOMATION.md`. Phase 5 added a new `09 - Workflows` folder chaining requests into stateful, dynamic-data-driven workflows (dynamic id capture/reuse, authentication chaining, CRUD lifecycles, and cleanup) without modifying any of the 38 existing requests or their assertions — see `docs/API_DATA_CHAINING.md`. Phase 5.1 reorganized the collection's presentation (renaming that folder to `08 - Workflows` after removing one empty leftover folder, adding a `Tests:` summary to every Phase 3/4 request's description, and writing a professional collection description) with zero functional change — see `docs/POSTMAN_UI_ARCHITECTURE.md`. Phase 6 added command-line execution via Newman: a `package.json` with `npm test` and per-workflow scripts, HTML reporting (`newman-reporter-htmlextra`) into `reports/`, and verified non-zero exit codes on failure — see `docs/NEWMAN_API_AUTOMATION.md` (including a documented Newman-specific compatibility finding about `pm.execution.setNextRequest(null)` stopping an entire run, and how this project's scripts work around it). Phase 7 reviewed and pushed the completed project to the existing GitHub repository (no new repo, no history rewrite, no force-push), tightened `.gitignore`, verified no secrets are tracked, and documented the ongoing Git/GitHub workflow — see `docs/GIT_GITHUB_WORKFLOW.md`. No CI/CD has been set up yet. See `docs/PROJECT_PHASES.md` for the full roadmap and `prompts/` for the instructions used to build each phase.

## Project Structure

```text
QADemoAPITesting/
│
├── prompts/
│   ├── phase-1-project-scaffolding.md
│   ├── phase-2-api-discovery.md
│   ├── phase-3-api-request-implementation.md
│   ├── phase-4-api-test-automation.md
│   ├── phase-5-api-data-chaining.md
│   ├── phase-5.1-postman-ui-organization.md
│   ├── phase-6-newman-automation.md
│   ├── phase-7-github-integration.md
│   └── README.md
│
├── postman/
│   ├── collections/
│   │   └── QA-Demo Project.postman_collection.json
│   ├── environments/
│   │   └── QA-Demo Environment.postman_environment.json
│   ├── data/
│   └── backups/
│       └── phase-5-pre-5.1/   (Phase 5 collection/environment snapshot, untouched)
│
├── docs/
│   ├── PROJECT_PHASES.md
│   ├── API_DISCOVERY.md
│   ├── api-inventory.json
│   ├── POSTMAN_IMPLEMENTATION.md
│   ├── API_TEST_AUTOMATION.md
│   ├── API_DATA_CHAINING.md
│   ├── POSTMAN_UI_ARCHITECTURE.md
│   ├── NEWMAN_API_AUTOMATION.md
│   └── GIT_GITHUB_WORKFLOW.md
│
├── tests/
│
├── scripts/
│
├── reports/                    (generated Newman HTML reports; gitignored except .gitkeep)
│
├── .github/
│   └── workflows/
│
├── node_modules/                (gitignored; created by `npm install`)
├── package.json                 (Newman/npm scripts)
├── package-lock.json
├── .gitignore
│
└── README.md
```
