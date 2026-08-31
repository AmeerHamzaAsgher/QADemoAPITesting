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
Phase 3 - Postman API Request Implementation
```

See [`docs/API_DISCOVERY.md`](docs/API_DISCOVERY.md) for the full API discovery report, [`docs/api-inventory.json`](docs/api-inventory.json) for the machine-readable endpoint inventory, and [`docs/POSTMAN_IMPLEMENTATION.md`](docs/POSTMAN_IMPLEMENTATION.md) for the Phase 3 implementation matrix.

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

Phase 1 established the initial project foundation — folder structure, an empty Postman collection with placeholder folders, a Postman environment with foundational variables, and basic documentation. Phase 2 performed API discovery and analysis of the QA Demo application; the discovered API base URL, endpoint inventory, authentication mechanism, request/response structures, and a Postman implementation plan are documented in `docs/API_DISCOVERY.md` and `docs/api-inventory.json`. Phase 3 implemented the discovered APIs as actual Postman requests (24 requests across 6 resource-based folders) in the `QA-Demo Project` collection, using the Phase 2 findings as the source of truth — see `docs/POSTMAN_IMPLEMENTATION.md`. No automated test assertions, Newman integration, or CI/CD have been set up yet. See `docs/PROJECT_PHASES.md` for the full roadmap and `prompts/` for the instructions used to build each phase.

## Project Structure

```text
QADemoAPITesting/
│
├── prompts/
│   ├── phase-1-project-scaffolding.md
│   ├── phase-2-api-discovery.md
│   ├── phase-3-api-request-implementation.md
│   └── README.md
│
├── postman/
│   ├── collections/
│   │   └── QA-Demo Project.postman_collection.json
│   ├── environments/
│   │   └── QA-Demo Environment.postman_environment.json
│   └── data/
│
├── docs/
│   ├── PROJECT_PHASES.md
│   ├── API_DISCOVERY.md
│   ├── api-inventory.json
│   └── POSTMAN_IMPLEMENTATION.md
│
├── tests/
│
├── scripts/
│
├── reports/
│
├── .github/
│   └── workflows/
│
├── .gitignore
│
└── README.md
```
