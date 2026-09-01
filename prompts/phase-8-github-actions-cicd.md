# PHASE 8 — GITHUB ACTIONS CI/CD

## Objective

Integrate the existing Newman-based QA-Demo API automation framework with GitHub Actions so that API tests execute automatically in CI.

Project:

`D:\API Testing\Newman API Testing\QADemoAPITesting`

Use the existing GitHub repository and existing Newman framework.

---

## Instructions

1. Read all previous phase prompts and documentation from Phase 1 through Phase 7.

2. Inspect the existing project before making changes.

3. Verify that:

   * Postman collection exists
   * Postman environment exists
   * Newman works locally
   * `package.json` exists
   * `npm test` works locally
   * Git repository exists
   * GitHub remote exists

4. Do NOT redesign the existing API automation framework.

5. Create:

`.github/workflows/api-tests.yml`

6. Configure GitHub Actions to:

   * Trigger on push
   * Trigger on pull request
   * Run on a supported Ubuntu runner
   * Checkout the repository
   * Install the required Node.js version
   * Install project dependencies
   * Execute `npm test`

7. Use the existing npm/Newman test command instead of duplicating the Newman command unnecessarily.

8. Ensure Newman exit codes correctly determine GitHub Actions success/failure.

9. Configure test reporting.

10. Preserve Newman output and make useful reports available as GitHub Actions artifacts.

11. Do NOT commit generated reports to the repository.

12. Ensure `.gitignore` remains correct.

13. Handle secrets securely.

Never hardcode:

* API keys
* passwords
* authentication tokens
* client secrets
* private credentials

14. If the API requires secrets, use GitHub Actions Secrets or appropriate environment variables.

15. Do not expose secrets in logs.

16. If the current QA-Demo API does not require secrets, do not create unnecessary GitHub secrets.

17. Configure the workflow so that a failed Newman test causes the GitHub Actions job to fail.

18. Configure dependency caching where appropriate.

19. Use clear workflow and job names.

Example:

```yaml
name: QA-Demo API Tests
```

20. Keep the workflow simple, maintainable, and professional.

---

# WORKFLOW REQUIREMENTS

The workflow should conceptually perform:

```text
Checkout
   ↓
Setup Node.js
   ↓
Install Dependencies
   ↓
Run Newman
   ↓
Generate Reports
   ↓
Upload Reports
```

Use the existing project commands wherever possible.

---

# TRIGGERS

Configure at minimum:

```text
push
pull_request
```

Do not add scheduled execution unless specifically justified.

---

# NODE.JS

Use the Node.js version appropriate for the existing project.

Inspect `package.json` and the local environment before choosing the version.

Do not arbitrarily upgrade dependencies.

---

# DEPENDENCIES

Prefer:

```text
npm ci
```

when `package-lock.json` exists and is valid.

Otherwise use an appropriate installation method and document why.

Do not commit `node_modules`.

---

# NEWMAN

Use the existing Newman implementation.

The workflow should execute:

```text
npm test
```

rather than duplicating configuration unnecessarily.

The Newman command must use the existing:

```text
QA-Demo Project
QA-Demo Environment
```

---

# REPORTING

Generate useful CI reports.

Reports should be uploaded as GitHub Actions artifacts when available.

Do not commit generated reports into Git.

Recommended concept:

```text
reports/
    newman/
```

Use an appropriate Newman reporter already used by the project.

Do not introduce unnecessary reporting dependencies.

---

# FAILURE HANDLING

The workflow must fail when API tests fail.

Expected behavior:

```text
Newman PASS
    ↓
GitHub Actions PASS

Newman FAIL
    ↓
GitHub Actions FAIL
```

Do not hide Newman failures using commands that force a zero exit code.

---

# SECURITY

Never expose credentials.

Do not place secrets directly inside:

```text
api-tests.yml
package.json
Postman collection
Postman environment
README.md
```

Use GitHub Secrets when required.

If environment configuration needs secrets, inject them at runtime.

---

# DOCUMENTATION

Create:

`docs/GITHUB_ACTIONS_CICD.md`

Document:

* GitHub Actions overview
* Workflow architecture
* Trigger configuration
* Node.js setup
* Dependency installation
* Newman execution
* Test failure behavior
* Reports
* Artifacts
* Secrets
* Local vs CI execution
* Troubleshooting

Update:

`README.md`

Add a CI/CD section explaining how the API tests execute automatically through GitHub Actions.

---

# WORKFLOW FILE

Create:

`.github/workflows/api-tests.yml`

Keep it readable and commented where useful.

Use clear names for:

* Workflow
* Jobs
* Steps

---

# LOCAL VALIDATION

Before pushing:

1. Run:

`npm test`

2. Confirm local Newman tests pass.

3. Validate the YAML workflow syntax/structure.

4. Review Git diff.

5. Confirm no secrets exist in the workflow.

6. Confirm reports are ignored by Git.

---

# GIT

After implementation:

Run:

`git status`

Review:

`git diff`

Stage only appropriate files.

Create a professional commit.

Push to the existing GitHub repository.

Do NOT force-push.

Do NOT delete history.

---

# GITHUB VALIDATION

After pushing:

1. Open GitHub.
2. Navigate to Actions.
3. Locate:

`QA-Demo API Tests`

4. Verify the workflow starts.

5. Verify:

   * Checkout succeeds
   * Node.js setup succeeds
   * Dependencies install
   * Newman executes
   * Tests execute
   * Reports are generated
   * Artifacts are uploaded
   * Job succeeds when tests pass

---

# FAILURE TEST

Where safely possible, temporarily introduce a controlled test failure locally or in a safe branch to confirm:

```text
Newman failure
      ↓
non-zero exit code
      ↓
GitHub Actions failure
```

Do not leave intentional failures in the main branch.

Restore the project to the passing state afterward.

---

# CI/CD ARCHITECTURE

The final architecture should be:

```text
Developer
    │
    │ git push
    ▼
GitHub Repository
    │
    ▼
GitHub Actions
    │
    ├── Checkout
    │
    ├── Node.js
    │
    ├── npm ci
    │
    └── npm test
            │
            ▼
          Newman
            │
            ▼
    QA-Demo Postman Collection
            │
            ▼
        API Tests
            │
       ┌────┴────┐
       ▼         ▼
     PASS       FAIL
       │         │
       ▼         ▼
   CI PASS    CI FAIL
       │
       ▼
   Test Reports
```

---

# RESTRICTIONS

Do NOT:

* Replace Newman
* Replace Postman
* Create a different test framework
* Add Docker unnecessarily
* Add deployment pipelines
* Deploy the application
* Modify API endpoints
* Modify API behavior
* Remove Phase 4 tests
* Remove Phase 5 workflows
* Remove Newman automation
* Commit secrets
* Force-push
* Create a second GitHub repository

This phase is specifically for GitHub Actions CI/CD integration.

---

# COMPLETION CRITERIA

Phase 8 is complete when:

* `.github/workflows/api-tests.yml` exists
* GitHub Actions workflow is valid
* Workflow triggers on push
* Workflow triggers on pull request
* Node.js is configured
* Dependencies install successfully
* `npm test` executes
* Newman executes successfully
* Existing Postman collection executes
* Existing environment works
* Phase 4 tests execute
* Phase 5 workflows execute
* Newman failures produce CI failures
* Reports are generated
* Reports are available as GitHub artifacts
* Secrets are protected
* Documentation is complete
* Workflow has been successfully executed on GitHub

At completion provide:

* Workflow name
* Workflow file
* Trigger configuration
* Node.js version
* npm command
* Newman command/configuration
* Test count
* Passed tests
* Failed tests
* Report location
* Artifact name
* Git commit
* GitHub Actions run result
* Final project structure

Then STOP.
