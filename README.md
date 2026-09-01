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
Phase 6 - Newman API Automation
```

```text
Phase 1   — Completed
Phase 2   — Completed
Phase 3   — Completed
Phase 4   — Completed
Phase 5   — Completed
Phase 5.1 — Completed
Phase 6   — Completed
```

See [`docs/API_DISCOVERY.md`](docs/API_DISCOVERY.md) for the full API discovery report, [`docs/api-inventory.json`](docs/api-inventory.json) for the machine-readable endpoint inventory, [`docs/POSTMAN_IMPLEMENTATION.md`](docs/POSTMAN_IMPLEMENTATION.md) for the Phase 3 implementation matrix, [`docs/API_TEST_AUTOMATION.md`](docs/API_TEST_AUTOMATION.md) for the Phase 4 test coverage and execution report, [`docs/API_DATA_CHAINING.md`](docs/API_DATA_CHAINING.md) for the Phase 5 workflow architecture and execution evidence, [`docs/POSTMAN_UI_ARCHITECTURE.md`](docs/POSTMAN_UI_ARCHITECTURE.md) for the Phase 5.1 collection navigation guide and regression proof, and [`docs/NEWMAN_API_AUTOMATION.md`](docs/NEWMAN_API_AUTOMATION.md) for the Phase 6 command-line execution guide.

## Running the Tests (Newman)

```bash
npm install   # one-time setup - installs Newman + the HTML reporter locally
npm test      # runs the full suite (main requests + all 4 Phase 5 workflows), generates HTML reports in reports/
```

See [`docs/NEWMAN_API_AUTOMATION.md`](docs/NEWMAN_API_AUTOMATION.md) for individual npm scripts, direct `newman run` commands, exit-code behavior, and troubleshooting.

## Postman Collection

```text
QA-Demo Project
```

## Postman Environment

```text
QA-Demo Environment
```

## Purpose

This project will eventually become a professional API automation testing framework for the QA Demo application, built using:

* Postman
* Newman
* Git
* GitHub
* GitHub Actions
* CI/CD

Phase 1 established the initial project foundation — folder structure, an empty Postman collection with placeholder folders, a Postman environment with foundational variables, and basic documentation. Phase 2 performed API discovery and analysis of the QA Demo application; the discovered API base URL, endpoint inventory, authentication mechanism, request/response structures, and a Postman implementation plan are documented in `docs/API_DISCOVERY.md` and `docs/api-inventory.json`. Phase 3 implemented the discovered APIs as actual Postman requests (24 requests across 6 resource-based folders) in the `QA-Demo Project` collection, using the Phase 2 findings as the source of truth — see `docs/POSTMAN_IMPLEMENTATION.md`. Phase 4 converted those requests into an automated test suite: all 38 requests (the original 24 plus 14 dedicated negative/boundary requests in a new `07 - Negative Tests` folder) now carry live-verified `pm.test()` assertions covering status codes, response time, headers, Content-Type, required fields, data types, response structure, CRUD behavior, authentication, negative/boundary scenarios, error responses, and JSON schema — see `docs/API_TEST_AUTOMATION.md`. Phase 5 added a new `09 - Workflows` folder chaining requests into stateful, dynamic-data-driven workflows (dynamic id capture/reuse, authentication chaining, CRUD lifecycles, and cleanup) without modifying any of the 38 existing requests or their assertions — see `docs/API_DATA_CHAINING.md`. Phase 5.1 reorganized the collection's presentation (renaming that folder to `08 - Workflows` after removing one empty leftover folder, adding a `Tests:` summary to every Phase 3/4 request's description, and writing a professional collection description) with zero functional change — see `docs/POSTMAN_UI_ARCHITECTURE.md`. Phase 6 added command-line execution via Newman: a `package.json` with `npm test` and per-workflow scripts, HTML reporting (`newman-reporter-htmlextra`) into `reports/`, and verified non-zero exit codes on failure — see `docs/NEWMAN_API_AUTOMATION.md` (including a documented Newman-specific compatibility finding about `pm.execution.setNextRequest(null)` stopping an entire run, and how this project's scripts work around it). No CI/CD has been set up yet. See `docs/PROJECT_PHASES.md` for the full roadmap and `prompts/` for the instructions used to build each phase.

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
│   └── NEWMAN_API_AUTOMATION.md
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
