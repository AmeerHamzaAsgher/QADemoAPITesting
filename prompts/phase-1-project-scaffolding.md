# ROLE

Act as a **Senior API Automation Architect and Postman Framework Engineer**.

I am starting a professional API testing automation project for the QA Demo application.

The project root MUST be:

```text
D:\API Testing\Newman API Testing\QADemoAPITesting
```

Application under test:

```text
https://qademo.com/
```

---

# PHASE 1 OBJECTIVE

Create ONLY the initial professional project structure for the API automation framework.

**Phase 1 is strictly limited to:**

* Project folder structure
* Prompt management structure
* Postman Collection
* Postman Environment
* Basic project documentation
* Basic configuration/scaffolding required for future API automation

Do NOT implement API requests or API test automation yet.

The purpose of Phase 1 is to establish a clean, professional foundation that will be extended in later phases.

---

# IMPORTANT — SAVE THIS PROMPT

The prompt being used for this Phase 1 setup MUST be saved inside the project itself.

Create:

```text
D:\API Testing\Newman API Testing\QADemoAPITesting\prompts\phase-1-project-scaffolding.md
```

Save the complete contents of this prompt into that file.

The prompt file should contain the complete Phase 1 instructions, not a shortened summary.

This allows the project to maintain a history of the instructions used to build it.

Also create:

```text
D:\API Testing\Newman API Testing\QADemoAPITesting\prompts\README.md
```

The prompts README should explain that this directory contains the prompts used to build and maintain the QA-Demo API testing project.

---

# REQUIRED PROJECT STRUCTURE

Create the following:

```text
QADemoAPITesting/
│
├── prompts/
│   ├── phase-1-project-scaffolding.md
│   └── README.md
│
├── postman/
│   ├── collections/
│   ├── environments/
│   └── data/
│
├── docs/
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

Do not create unnecessary directories.

If the project directory already exists, inspect it first and preserve existing work.

---

# PROMPT MANAGEMENT STRUCTURE

The `prompts` directory is an important part of this project.

Use it to maintain all Claude/AI instructions associated with the project.

For Phase 1, create:

```text
prompts/
│
├── phase-1-project-scaffolding.md
└── README.md
```

In future phases, prompts can be added such as:

```text
prompts/
├── phase-1-project-scaffolding.md
├── phase-2-api-discovery.md
├── phase-3-api-request-implementation.md
├── phase-4-api-test-automation.md
├── phase-5-data-chaining.md
├── phase-6-newman-automation.md
├── phase-7-git-github.md
├── phase-8-github-actions.md
└── phase-9-advanced-api-automation.md
```

DO NOT create these future prompt files now.

Only create the Phase 1 prompt file.

---

# POSTMAN COLLECTION

Create a Postman collection named EXACTLY:

```text
QA-Demo Project
```

Save/export it under:

```text
QADemoAPITesting/postman/collections/
```

Recommended filename:

```text
QA-Demo Project.postman_collection.json
```

The collection must use a valid Postman Collection v2.1 format.

---

# COLLECTION CONTENT — PHASE 1 ONLY

Do NOT create actual API requests yet.

Do NOT create fake endpoints.

Do NOT create sample GET/POST/PUT/PATCH/DELETE requests.

Create only a professional initial folder structure.

Use:

```text
QA-Demo Project
│
├── 01 - Authentication
├── 02 - API Requests
├── 03 - Negative Tests
└── 04 - Cleanup
```

These are initial placeholder folders only.

They will be expanded/restructured after API discovery.

Do not assume that QA-Demo has specific resources such as:

```text
Users
Products
Orders
Customers
```

unless they are actually discovered later.

---

# POSTMAN ENVIRONMENT

Create a Postman environment named EXACTLY:

```text
QA-Demo Environment
```

Save/export it under:

```text
QADemoAPITesting/postman/environments/
```

Recommended filename:

```text
QA-Demo Environment.postman_environment.json
```

The environment must be a valid Postman environment JSON file.

---

# ENVIRONMENT VARIABLES

Create only foundational variables.

Include:

```text
baseUrl
apiUrl
authToken
refreshToken
```

Configure:

```text
baseUrl = https://qademo.com/
```

Leave:

```text
apiUrl
authToken
refreshToken
```

empty or as safe placeholders.

IMPORTANT:

Do NOT guess the API URL.

Do not automatically assume:

```text
https://qademo.com/api
https://qademo.com/api/v1
https://api.qademo.com
```

The actual API base URL will be discovered in Phase 2.

---

# COLLECTION VARIABLES

Do not create unnecessary collection variables during Phase 1.

Collection variables will be introduced after API discovery when their purpose is known.

---

# API REQUESTS

Do NOT create any actual API requests.

Do not implement:

* GET
* POST
* PUT
* PATCH
* DELETE
* HEAD
* OPTIONS

requests yet.

These will be implemented in later phases based on actual API discovery.

---

# SCRIPTS

Do NOT create:

* Pre-request scripts
* Test scripts
* Authentication scripts
* Token extraction scripts
* Response validation scripts
* Data chaining scripts

These belong to later phases.

---

# DOCUMENTATION

Create:

```text
docs/PROJECT_PHASES.md
```

Document the planned roadmap:

```text
Phase 1
Project scaffolding
Collection
Environment
Prompt management

Phase 2
Application/API discovery
Endpoint inventory
Authentication analysis
Request/response analysis

Phase 3
API request implementation
GET
POST
PUT
PATCH
DELETE
etc.

Phase 4
API test automation
Assertions
Status-code validation
Schema validation
Negative testing
Boundary testing

Phase 5
Data chaining
Dynamic test data
Authentication/token management
Test data lifecycle

Phase 6
Newman automation
Command-line execution
Reports
npm scripts

Phase 7
Git/GitHub integration

Phase 8
GitHub Actions CI/CD

Phase 9
Advanced API automation
Reporting
Environment management
Advanced validations
```

Do not implement future phases.

Only document the roadmap.

---

# PROMPTS README

Create:

```text
prompts/README.md
```

It should explain:

* Purpose of the prompts directory
* Prompt naming convention
* Relationship between prompts and project phases
* That prompts are version-controlled project assets
* That each major phase should have its own prompt

Example:

```text
prompts/
│
├── phase-1-project-scaffolding.md
├── phase-2-api-discovery.md
├── phase-3-api-request-implementation.md
└── ...
```

Do not create future prompt files yet.

---

# MAIN README

Create:

```text
README.md
```

Include:

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
Phase 1 - Project Scaffolding
```

## Postman Collection

```text
QA-Demo Project
```

## Postman Environment

```text
QA-Demo Environment
```

## Purpose

Explain that this project will eventually become a professional API automation framework using:

* Postman
* Newman
* Git
* GitHub
* GitHub Actions
* CI/CD

Clearly state that Phase 1 contains only the initial project foundation.

---

# GITIGNORE

Create a professional `.gitignore` appropriate for a Postman/Newman/API automation project.

Consider:

```text
node_modules/
.env
*.log
reports/
```

Do NOT blindly ignore:

```text
postman/collections/
postman/environments/
prompts/
docs/
```

These are important project assets and should normally be version-controlled.

---

# REPORTS

Create:

```text
reports/
```

Leave it empty.

Newman reports will be generated in a later phase.

---

# TESTS

Create:

```text
tests/
```

Do not add API tests yet.

---

# SCRIPTS

Create:

```text
scripts/
```

Do not add automation scripts yet.

---

# GITHUB

Create:

```text
.github/
└── workflows/
```

Do NOT create GitHub Actions workflows yet.

---

# IMPORTANT RULES

## RULE 1 — DO NOT INVENT API INFORMATION

Do not assume:

* API base URL
* endpoints
* authentication
* users
* products
* request bodies
* response schemas
* credentials
* tokens

These will be discovered later.

---

## RULE 2 — DO NOT EXPLORE THE APPLICATION YET

Although the application URL is provided:

```text
https://qademo.com/
```

do NOT perform full API discovery during Phase 1.

The application/API discovery will be Phase 2.

---

## RULE 3 — DO NOT CREATE FAKE REQUESTS

The collection should contain folders only.

No fake GET, POST, PUT, PATCH, or DELETE requests.

---

## RULE 4 — DO NOT IMPLEMENT NEWMAN

Do not install or configure Newman.

Do not create:

```text
package.json
```

unless it is genuinely required for the basic Phase 1 scaffolding.

Prefer not to create it at this stage.

---

## RULE 5 — DO NOT IMPLEMENT CI/CD

Do not create:

* GitHub Actions workflows
* npm automation
* Newman pipeline
* CI/CD scripts

These belong to later phases.

---

## RULE 6 — PRESERVE EXISTING WORK

If:

```text
D:\API Testing\Newman API Testing\QADemoAPITesting
```

already exists:

1. Inspect it.
2. Preserve existing files.
3. Do not delete existing work.
4. Add only what is necessary for Phase 1.
5. Do not overwrite files unnecessarily.

---

# VALIDATION

After creation, verify the complete structure.

Expected result:

```text
QADemoAPITesting/
│
├── prompts/
│   ├── phase-1-project-scaffolding.md
│   └── README.md
│
├── postman/
│   ├── collections/
│   │   └── QA-Demo Project.postman_collection.json
│   │
│   ├── environments/
│   │   └── QA-Demo Environment.postman_environment.json
│   │
│   └── data/
│
├── docs/
│   └── PROJECT_PHASES.md
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

Validate:

### Collection

* Name is exactly `QA-Demo Project`
* Valid Postman Collection v2.1 JSON
* Correct location
* Contains only initial folders
* Contains no fake API requests

### Environment

* Name is exactly `QA-Demo Environment`
* Valid Postman environment JSON
* Correct location
* `baseUrl` = `https://qademo.com/`
* `apiUrl` is not guessed
* `authToken` is empty/placeholder
* `refreshToken` is empty/placeholder

### Prompt

Confirm:

```text
prompts/phase-1-project-scaffolding.md
```

contains the complete Phase 1 prompt.

### Documentation

Confirm:

```text
README.md
docs/PROJECT_PHASES.md
prompts/README.md
```

exist and correctly describe the project.

---

# FINAL RESPONSE

After completing Phase 1, provide a concise report containing:

## 1. Project Location

```text
D:\API Testing\Newman API Testing\QADemoAPITesting
```

## 2. Project Structure

Show the final directory tree.

## 3. Created Files

List all created files.

## 4. Postman Collection

```text
QA-Demo Project
```

## 5. Postman Environment

```text
QA-Demo Environment
```

## 6. Prompt

Confirm:

```text
prompts/phase-1-project-scaffolding.md
```

was created and contains the complete Phase 1 prompt.

## 7. Current Phase

```text
Phase 1 - Project Scaffolding
```

## 8. Not Implemented

Explicitly confirm that these have NOT been implemented:

* API discovery
* API endpoints
* GET requests
* POST requests
* PUT requests
* PATCH requests
* DELETE requests
* Test scripts
* Authentication automation
* Data chaining
* Newman
* npm
* GitHub Actions
* CI/CD

## 9. Validation

Confirm that the Postman collection and environment JSON files are valid and ready for Phase 2.

STOP after Phase 1.

Do not automatically continue to Phase 2.
