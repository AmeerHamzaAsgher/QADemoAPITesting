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

## Current Prompt Files

```text
prompts/
│
├── phase-1-project-scaffolding.md
├── phase-2-api-discovery.md
├── phase-3-api-request-implementation.md
└── README.md
```

## Version Control

Prompts are treated as first-class project assets, just like the Postman collection, environment files, and documentation. They are committed to source control and are never excluded via `.gitignore`.

## Adding New Prompts

Each major phase should have its own prompt file, added only when that phase begins. Do not create prompt files for future phases in advance.
