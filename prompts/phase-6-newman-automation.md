# PHASE 6 — NEWMAN API AUTOMATION

## Objective

Convert the existing `QA-Demo Project` Postman collection into a professional command-line API automation framework using Newman.

Project:

`D:\API Testing\Newman API Testing\QADemoAPITesting`

Collection:

`postman/collections/QA-Demo Project.postman_collection.json`

Environment:

`postman/environments/QA-Demo Environment.postman_environment.json`

---

## Instructions

1. Read all Phase 1–5.1 prompts and documentation before making changes.

2. Preserve the existing Postman collection and environment functionality completely.

3. Verify Node.js and npm are installed.

4. Verify Newman is installed. If not, install it appropriately.

5. Execute the existing collection using Newman with the existing environment.

6. Confirm that:

   * Collection loads successfully
   * Environment loads successfully
   * Variables resolve correctly
   * Phase 4 tests execute
   * Phase 5 data-chaining workflows execute
   * Expected tests pass

7. Create a professional Node.js/Newman project configuration using `package.json`.

8. Add useful npm scripts, including a primary API test command.

Example:

`npm test`

which should execute the Newman test suite.

9. Configure Newman execution using the existing collection and environment. Do not hardcode environment-specific URLs or credentials.

10. Add useful Newman CLI options such as:

* CLI reporter
* concise test output
* appropriate exit-code behavior

11. Add HTML reporting where appropriate using a suitable Newman reporter.

12. Create a reports directory for generated Newman reports.

13. Update `.gitignore` so generated reports and other temporary files are not unnecessarily committed.

14. Create/update:

`docs/NEWMAN_API_AUTOMATION.md`

Document:

* What Newman is
* Installation
* Project setup
* Newman command
* npm scripts
* Collection/environment execution
* Reports
* Exit codes
* Troubleshooting
* How to execute the complete API suite

15. Update `README.md` with the Phase 6 Newman execution instructions.

16. Run the Newman suite directly.

17. Run the same suite using:

`npm test`

18. Verify both executions produce the expected results.

19. Intentionally verify failure handling where practical: Newman must return a non-zero exit code when an assertion fails.

20. Do not modify API behavior merely to make Newman pass.

---

## Restrictions

Do NOT implement:

* GitHub Actions
* CI/CD
* Deployment
* Scheduled automation
* Docker
* Cloud execution

Those belong to later phases.

Do NOT remove existing Postman tests.

Do NOT change Phase 5 data chaining unless required to fix an actual Newman compatibility issue.

Do NOT commit real credentials or secrets.

---

## Expected structure

Maintain the existing project and add the Newman-related files as appropriate:

`postman/`
`docs/`
`prompts/`
`reports/`
`package.json`
`.gitignore`
`README.md`

---

## Completion criteria

Phase 6 is complete when:

* Newman is installed and working
* Collection executes successfully through Newman
* Environment loads successfully
* Dynamic variables work
* Phase 4 tests execute
* Phase 5 workflows execute
* `npm test` executes the Newman suite
* Newman reports are generated
* Failed assertions produce a non-zero exit code
* Documentation is complete
* No CI/CD implementation has been added

At the end, provide:

* Newman version
* Newman command used
* npm command used
* Total requests
* Total tests
* Passed tests
* Failed tests
* Report locations
* Files created/modified
* Final project structure

Then STOP.
