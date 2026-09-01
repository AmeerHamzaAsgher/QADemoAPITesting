# Prompts

This directory contains the prompts used to build and maintain the QA-Demo API Testing project.

## Purpose

Every significant phase of this project is driven by a written instruction set (a "prompt") given to the AI assistant (Claude) performing the work. Storing these prompts inside the repository gives the project a durable, version-controlled history of *why* and *how* it was built — not just *what* the resulting code and configuration look like.

## Naming Convention

Prompts are named after the project phase they belong to:

```text
phase-<number>-<short-description>.md
```

Examples:

```text
phase-1-project-scaffolding.md
phase-2-api-discovery.md
phase-3-api-request-implementation.md
phase-4-api-test-automation.md
phase-5-data-chaining.md
phase-6-newman-automation.md
phase-7-git-github.md
phase-8-github-actions.md
phase-9-advanced-api-automation.md
```

## Relationship to Project Phases

This project is built incrementally, one phase at a time (see `docs/PROJECT_PHASES.md` for the full roadmap). Each phase has a single corresponding prompt file that captures the complete instructions used for that phase — objectives, constraints, and rules. The prompt is written and saved *before or during* the phase's execution, so the reasoning behind the resulting files is always traceable.

* **Phase 1** (`phase-1-project-scaffolding.md`) established the project structure, the initial Postman collection/environment, and base documentation.
* **Phase 2** (`phase-2-api-discovery.md`) — **completed** — discovered and documented the actual QA-Demo API: base URL, authentication mechanism, endpoint inventory, request/response structures, and a Postman implementation plan. Results are in `docs/API_DISCOVERY.md` and `docs/api-inventory.json`.
* **Phase 3** (`phase-3-api-request-implementation.md`) — **completed** — implemented the discovered APIs as actual Postman requests (24 requests across 6 resource-based folders) in the `QA-Demo Project` collection, using Phase 2's findings as the source of truth. No test assertions were added. Results are in `docs/POSTMAN_IMPLEMENTATION.md`.
* **Phase 4** (`phase-4-api-test-automation.md`) — **completed** — converted the Phase 3 requests into an automated test suite: 38 requests (24 main + 14 dedicated negative/boundary requests) now carry live-verified `pm.test()` assertions for status codes, response structure, headers, data types, authentication, negative/boundary scenarios, and JSON schema. No dynamic data chaining, Newman, or CI/CD were implemented. Results are in `docs/API_TEST_AUTOMATION.md`.
* **Phase 5** (`phase-5-api-data-chaining.md`) — **completed** — added a new `09 - Workflows` folder (renumbered to `08 - Workflows` in Phase 5.1) chaining requests into 4 stateful workflows with dynamic id capture/reuse, authentication chaining, CRUD lifecycles, and automatic cleanup, without modifying any of the 38 existing Phase 3/4 requests or assertions. No Newman, npm automation, or CI/CD were implemented. Results are in `docs/API_DATA_CHAINING.md`.
* **Phase 5.1** (`phase-5.1-postman-ui-organization.md`) — **completed** — reorganized the Postman collection's presentation only (request/folder descriptions, a "Tests:" summary on every Phase 3/4 request, a professional collection description, and removal of one empty leftover folder with renumbering). Zero functional change - verified by a field-level diff (method/url/header/body/auth/scripts) and by re-running the Phase 4/5 live-verification harnesses with identical results. Results are in `docs/POSTMAN_UI_ARCHITECTURE.md`.
* **Phase 6** (`phase-6-newman-automation.md`) — **completed** — added command-line execution via Newman: `package.json` with `npm test` and per-workflow scripts, HTML reporting into `reports/`, and verified exit-code behavior (0 on success, non-zero on failure, confirmed by deliberately forcing a failure). Discovered and documented a Newman-specific compatibility finding (`pm.execution.setNextRequest(null)` stops the entire run, not just a folder) and designed the npm scripts around it without changing any collection content. No CI/CD was implemented. Results are in `docs/NEWMAN_API_AUTOMATION.md`.
* **Phase 7** (`phase-7-github-integration.md`) — **completed** — reviewed the existing Git repository and GitHub remote (both pre-existing from the Phase 3 push - neither was recreated), tightened `.gitignore`, verified no secrets are tracked, and committed/pushed the outstanding Phase 4-7 work to `origin/main`. No force-push, no history rewrite, no new repository. No CI/CD was implemented. Results are in `docs/GIT_GITHUB_WORKFLOW.md`.

## Current Prompt Files

```text
prompts/
│
├── phase-1-project-scaffolding.md
├── phase-2-api-discovery.md
├── phase-3-api-request-implementation.md
├── phase-4-api-test-automation.md
├── phase-5-api-data-chaining.md
├── phase-5.1-postman-ui-organization.md
├── phase-6-newman-automation.md
├── phase-7-github-integration.md
└── README.md
```

## Version Control

Prompts are treated as first-class project assets, just like the Postman collection, environment files, and documentation. They are committed to source control and are never excluded via `.gitignore`.

## Adding New Prompts

Each major phase should have its own prompt file, added only when that phase begins. Do not create prompt files for future phases in advance.
