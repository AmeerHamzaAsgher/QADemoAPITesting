# PHASE 5 — API DATA CHAINING & WORKFLOW AUTOMATION

## ROLE

Act as a **Senior QA Automation Architect, API Automation Engineer, Postman Expert, and Test Framework Designer**.

You are continuing the existing professional API testing project.

Application Under Test:

```text
https://qademo.com/
```

Project:

```text
D:\API Testing\Newman API Testing\QADemoAPITesting
```

The following phases are already completed:

```text
Phase 1 — Project Scaffolding
Phase 2 — API Discovery
Phase 3 — Postman API Request Implementation
Phase 4 — API Test Automation
```

Your responsibility is to implement:

```text
PHASE 5 — API DATA CHAINING & WORKFLOW AUTOMATION
```

---

# 1. PRIMARY OBJECTIVE

Convert appropriate independent Postman requests into realistic, dependent API workflows using dynamic data.

The target architecture is:

```text
Create Resource
      ↓
Capture Generated Data
      ↓
Store Variable
      ↓
Use Variable in Next Request
      ↓
Read Resource
      ↓
Update Resource
      ↓
Validate Update
      ↓
Delete Resource
      ↓
Validate Cleanup
```

Do not use hardcoded resource IDs where the API allows dynamic creation and retrieval.

---

# 2. READ ALL PREVIOUS PHASES FIRST

Before modifying anything, read:

```text
prompts/phase-1-project-scaffolding.md
prompts/phase-2-api-discovery.md
prompts/phase-3-api-request-implementation.md
prompts/phase-4-api-test-automation.md
```

Then read:

```text
docs/API_DISCOVERY.md
docs/api-inventory.json
docs/POSTMAN_IMPLEMENTATION.md
docs/API_TEST_AUTOMATION.md
```

These documents are the source of truth.

Preserve all valid work from Phases 1–4.

Do not redesign the project unnecessarily.

---

# 3. INSPECT THE EXISTING POSTMAN COLLECTION

Inspect:

```text
postman/collections/QA-Demo Project.postman_collection.json
```

and:

```text
postman/environments/QA-Demo Environment.postman_environment.json
```

Understand:

* Existing requests
* Existing folders
* Existing tests
* Existing variables
* Existing scripts
* Existing authentication
* Existing request bodies
* Existing endpoints
* Existing test data

Do not overwrite valid Phase 4 assertions.

---

# 4. DO NOT INVENT API WORKFLOWS

Only create data-chaining workflows where the actual application supports them.

For example, do not assume that:

```text
POST /users
GET /users/{id}
PUT /users/{id}
DELETE /users/{id}
```

exists merely because this is a conventional REST pattern.

Use:

```text
docs/API_DISCOVERY.md
docs/api-inventory.json
```

as the source of truth.

---

# 5. IDENTIFY CHAINABLE ENDPOINTS

Analyze the API inventory and identify workflows such as:

```text
CREATE → READ
CREATE → UPDATE
CREATE → DELETE
CREATE → READ → UPDATE → DELETE
LOGIN → AUTHENTICATED REQUEST
CREATE PARENT → CREATE CHILD
```

Only implement workflows supported by the actual application.

Document endpoints that cannot be chained.

---

# 6. VARIABLE STRATEGY

Use Postman variables appropriately.

Understand the distinction between:

```text
Environment variables
Collection variables
Local variables
Global variables
```

Prefer collection/environment variables according to their intended scope.

Do not create unnecessary global variables.

---

# 7. DYNAMIC RESOURCE ID

When a POST request returns a generated identifier, capture it dynamically.

Example:

```javascript
const response = pm.response.json();

pm.test("Response contains generated ID", function () {
    pm.expect(response).to.have.property("id");
});

pm.collectionVariables.set("createdResourceId", response.id);
```

Use the actual response property discovered from the API.

Do not assume the identifier is always named:

```text
id
```

---

# 8. NEVER HARD-CODE GENERATED IDs

Avoid:

```text
/users/123
/users/456
/users/999
```

when the API dynamically generates IDs.

Prefer:

```text
/users/{{createdResourceId}}
```

This makes the workflow reusable.

---

# 9. VARIABLE EXTRACTION

Extract useful dynamic values from responses where required.

Examples:

```text
Resource ID
User ID
Order ID
Token
Reference number
Created timestamp
```

Only extract values required by subsequent requests.

Do not create unnecessary variables.

---

# 10. AUTHENTICATION CHAINING

If the API has authentication:

```text
Login
  ↓
Capture token
  ↓
Store token
  ↓
Use token in authenticated requests
```

Example:

```javascript
const response = pm.response.json();

pm.test("Authentication token exists", function () {
    pm.expect(response).to.have.property("token");
});

pm.environment.set("authToken", response.token);
```

Use the actual authentication response structure.

Never print tokens to the Postman Console.

---

# 11. DYNAMIC TEST DATA

Replace hardcoded reusable test data with dynamic or variable-based values where appropriate.

For example:

```javascript
const timestamp = Date.now();

pm.collectionVariables.set(
    "testUserEmail",
    `qa.user.${timestamp}@example.com`
);
```

Only use dynamic data where the API accepts it.

Do not generate random values that violate business rules.

---

# 12. UNIQUE TEST DATA

Where uniqueness is required, generate unique values.

Example:

```javascript
const uniqueValue = `QA_${Date.now()}`;

pm.collectionVariables.set("uniqueName", uniqueValue);
```

Avoid:

```text
Test User
Test User
Test User
```

if the API requires unique values.

---

# 13. PRE-REQUEST SCRIPTS

Use pre-request scripts for test-data preparation.

Examples:

```text
Generate unique email
Generate unique username
Generate timestamp
Prepare request variables
Prepare dynamic identifiers
```

Keep pre-request scripts simple and maintainable.

Do not move response validation into pre-request scripts.

---

# 14. POST-RESPONSE SCRIPTS

Use post-response scripts for:

```text
Response validation
Variable extraction
Business assertions
Chaining preparation
```

Example:

```javascript
const response = pm.response.json();

pm.test("Resource was created", function () {
    pm.expect(pm.response.code).to.be.oneOf([200, 201]);
});

pm.collectionVariables.set("resourceId", response.id);
```

Use actual expected status codes.

---

# 15. CREATE → READ WORKFLOW

Where supported, implement:

```text
POST Create
     ↓
Capture ID
     ↓
GET by ID
     ↓
Validate returned resource
```

The GET request must use:

```text
{{resourceId}}
```

rather than a hardcoded identifier.

---

# 16. CREATE → UPDATE WORKFLOW

Where supported:

```text
POST Create
     ↓
Capture ID
     ↓
PUT/PATCH
     ↓
Validate update
```

The update request must reference the dynamically captured ID.

---

# 17. UPDATE VALIDATION

After updating a resource, validate that the requested change is reflected.

Example:

```javascript
const response = pm.response.json();

pm.test("Updated value is correct", function () {
    pm.expect(response.name)
        .to.eql(pm.collectionVariables.get("updatedName"));
});
```

Use the actual response structure.

---

# 18. UPDATE → READ WORKFLOW

Where appropriate:

```text
UPDATE
   ↓
GET
   ↓
Verify persisted change
```

This provides stronger validation than checking only the PUT/PATCH response.

Only implement this where the API behavior supports it.

---

# 19. CREATE → DELETE WORKFLOW

Where supported:

```text
POST
 ↓
Capture ID
 ↓
DELETE
 ↓
Validate deletion
```

Use the dynamically generated ID.

Do not use hardcoded IDs.

---

# 20. DELETE → READ VALIDATION

Where the API supports verification after deletion:

```text
DELETE
 ↓
GET deleted resource
 ↓
Verify expected not-found/deleted behavior
```

Use the actual expected response discovered during API testing.

Do not automatically assume:

```text
404
```

unless the API actually behaves that way.

---

# 21. CRUD END-TO-END WORKFLOW

Where the API supports complete CRUD:

```text
CREATE
  ↓
READ
  ↓
UPDATE
  ↓
READ
  ↓
DELETE
  ↓
VERIFY DELETION
```

Implement this as a professional workflow.

Each stage must contain meaningful assertions.

---

# 22. WORKFLOW FOLDER ORGANIZATION

Organize the collection logically.

Possible structure:

```text
QA-Demo Project
│
├── Authentication
│
├── Resources
│
├── Positive Tests
│
├── Negative Tests
│
└── Workflows
    ├── Create → Read
    ├── Create → Update
    └── CRUD Lifecycle
```

Do not duplicate requests unnecessarily.

Use the existing collection architecture where possible.

---

# 23. COLLECTION VARIABLES

Use collection variables for workflow-specific values when appropriate.

Examples:

```text
createdResourceId
updatedResourceId
testResourceName
testResourceEmail
```

Remove temporary variables after workflow completion when appropriate.

---

# 24. ENVIRONMENT VARIABLES

Continue using:

```text
QA-Demo Environment
```

for environment-specific configuration.

Examples:

```text
baseUrl
authToken
environment-specific configuration
```

Do not move environment-specific configuration into hardcoded request URLs.

---

# 25. VARIABLE SCOPE

Use the narrowest appropriate scope.

General preference:

```text
Local
   ↓
Collection
   ↓
Environment
   ↓
Global
```

Avoid global variables unless genuinely necessary.

---

# 26. VARIABLE CLEANUP

After a workflow completes, determine whether temporary variables should be removed.

Example:

```javascript
pm.collectionVariables.unset("createdResourceId");
```

Only remove variables when doing so does not break later requests or debugging.

---

# 27. DATA CLEANUP

Test workflows should avoid leaving unnecessary test data behind.

Preferred lifecycle:

```text
Create test data
     ↓
Validate
     ↓
Update
     ↓
Validate
     ↓
Delete
     ↓
Validate cleanup
```

Where deletion is supported.

If cleanup cannot be performed, document the limitation.

---

# 28. FAILURE HANDLING

Design workflows so that failures are understandable.

For example:

```text
CREATE FAILED
    ↓
Do not execute dependent requests
```

Do not allow a failed CREATE to cause confusing failures in every downstream request.

Where Postman Runner execution requires conditional flow, use appropriate Postman control mechanisms.

---

# 29. REQUEST CHAINING

Where appropriate, use Postman's request execution flow to connect dependent requests.

Possible mechanisms include:

```javascript
pm.execution.setNextRequest("Request Name");
```

Use this carefully.

Do not create unnecessary execution loops.

Ensure workflows have a clear termination point.

---

# 30. AVOID INFINITE LOOPS

Never create a workflow such as:

```text
Request A
   ↓
Request B
   ↓
Request A
   ↓
Request B
```

without a deliberate termination condition.

Every workflow must eventually finish.

---

# 31. NEGATIVE WORKFLOWS

Where useful, implement workflows such as:

```text
Create resource
   ↓
Use invalid ID
   ↓
Verify error
```

or:

```text
Login
   ↓
Invalid credentials
   ↓
Verify authentication failure
```

Only where supported by the application.

---

# 32. AUTHENTICATED WORKFLOW

If applicable:

```text
Authenticate
    ↓
Capture token
    ↓
Authenticated GET
    ↓
Authenticated POST
    ↓
Authenticated UPDATE
    ↓
Authenticated DELETE
```

Do not expose the token.

---

# 33. TEST DATA ISOLATION

Avoid relying on data created manually by another person.

Prefer:

```text
Workflow creates its own test data
        ↓
Workflow validates it
        ↓
Workflow cleans it up
```

This improves repeatability.

---

# 34. REPEATABILITY

A Phase 5 workflow should be capable of being run repeatedly without requiring manual modification of IDs.

The following should work:

```text
Run #1
Create → Read → Update → Delete

Run #2
Create → Read → Update → Delete

Run #3
Create → Read → Update → Delete
```

Avoid dependencies on previous manual runs.

---

# 35. IDEMPOTENCY CONSIDERATIONS

Understand whether repeated requests produce:

```text
same result
```

or:

```text
new resource
```

or:

```text
duplicate/conflict
```

Document the behavior.

Do not assume POST is idempotent.

---

# 36. PRESERVE PHASE 4 ASSERTIONS

Do not remove or weaken existing Phase 4 tests.

For example, if Phase 4 validates:

```text
Status code
Required fields
Response schema
Business rules
```

Phase 5 should retain those assertions.

Data chaining extends the framework; it does not replace the existing test automation.

---

# 37. SECURITY REQUIREMENTS

Never expose:

```text
Passwords
API keys
Access tokens
Refresh tokens
Client secrets
Session cookies
```

Do not use:

```javascript
console.log(pm.environment.get("authToken"));
```

Do not commit real credentials.

---

# 38. DOCUMENT DATA FLOW

Create/update:

```text
docs/API_DATA_CHAINING.md
```

Document:

* Workflow architecture
* Variables
* Variable scope
* Data extraction
* Authentication chaining
* CRUD workflows
* Cleanup strategy
* Dynamic test data
* Request execution order
* Known limitations

---

# 39. CREATE WORKFLOW COVERAGE MATRIX

In:

```text
docs/API_DATA_CHAINING.md
```

create:

| Workflow        | Requests | Dynamic Data | Validation | Cleanup | Status |
| --------------- | -------- | ------------ | ---------- | ------- | ------ |
| Create → Read   |          |              |            |         |        |
| Create → Update |          |              |            |         |        |
| Create → Delete |          |              |            |         |        |
| Full CRUD       |          |              |            |         |        |
| Authentication  |          |              |            |         |        |

Only include workflows actually supported.

---

# 40. UPDATE API INVENTORY

Review:

```text
docs/api-inventory.json
```

If appropriate, record:

```text
chainable
workflow
dynamicData
```

Do not destroy the original discovery information.

---

# 41. UPDATE POSTMAN DOCUMENTATION

Review:

```text
docs/POSTMAN_IMPLEMENTATION.md
```

Document the transition from:

```text
Independent API requests
```

to:

```text
Data-driven API workflows
```

---

# 42. UPDATE API TEST AUTOMATION DOCUMENTATION

Review:

```text
docs/API_TEST_AUTOMATION.md
```

Add a reference to the Phase 5 data-chaining layer.

Preserve the Phase 4 implementation history.

---

# 43. UPDATE README

Update:

```text
README.md
```

to show:

```text
Phase 1 — Completed
Phase 2 — Completed
Phase 3 — Completed
Phase 4 — Completed
Phase 5 — In Progress/Completed
```

---

# 44. UPDATE PROMPTS README

Update:

```text
prompts/README.md
```

to include:

```text
phase-1-project-scaffolding.md
phase-2-api-discovery.md
phase-3-api-request-implementation.md
phase-4-api-test-automation.md
phase-5-api-data-chaining.md
```

---

# 45. VALIDATE VARIABLES

Before execution, verify that:

```text
baseUrl
authentication variables
dynamic IDs
test data variables
```

are correctly resolved.

No unexpected:

```text
{{undefinedVariable}}
```

values should remain.

---

# 46. RUN WORKFLOWS IN POSTMAN

Run the appropriate workflow(s) through the Postman Collection Runner.

Verify:

```text
Request A
   ↓
Request B
   ↓
Request C
   ↓
Request D
```

executes in the intended order.

---

# 47. VERIFY DYNAMIC DATA

During execution verify that generated values are actually changing.

For example:

```text
Run 1:
createdResourceId = 101

Run 2:
createdResourceId = 102
```

The exact values depend on the API.

The important requirement is that IDs are extracted dynamically rather than hardcoded.

---

# 48. VERIFY CRUD LIFECYCLE

For supported resources verify:

```text
CREATE
  ✓

READ
  ✓

UPDATE
  ✓

READ updated resource
  ✓

DELETE
  ✓

VERIFY deletion
  ✓
```

---

# 49. VERIFY REPEATABILITY

Run the workflow at least twice.

Confirm:

```text
Run 1 → PASS
Run 2 → PASS
```

without manually changing IDs.

---

# 50. VERIFY CLEANUP

After workflow execution, verify that temporary test data has been cleaned up where deletion is supported.

Do not leave unnecessary test records.

---

# 51. VERIFY PHASE 4 REGRESSION

Run the existing Phase 4 tests again.

Confirm that data chaining has not broken:

```text
Status assertions
Response assertions
Schema assertions
Negative tests
Authentication tests
```

---

# 52. DO NOT IMPLEMENT NEWMAN

Do NOT:

```text
Install Newman
Create Newman scripts
Create package.json for Newman
Create npm test commands
```

unless such files already existed independently.

Newman belongs to a later phase.

---

# 53. DO NOT IMPLEMENT CI/CD

Do not implement:

```text
GitHub Actions
CI/CD workflows
Scheduled API execution
Deployment automation
```

These belong to later phases.

---

# 54. FINAL PROJECT STRUCTURE

Expected structure:

```text
QADemoAPITesting/
│
├── prompts/
│   ├── phase-1-project-scaffolding.md
│   ├── phase-2-api-discovery.md
│   ├── phase-3-api-request-implementation.md
│   ├── phase-4-api-test-automation.md
│   ├── phase-5-api-data-chaining.md
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
│   ├── API_DISCOVERY.md
│   ├── api-inventory.json
│   ├── POSTMAN_IMPLEMENTATION.md
│   ├── API_TEST_AUTOMATION.md
│   └── API_DATA_CHAINING.md
│
├── tests/
├── scripts/
├── reports/
│
├── .github/
├── .gitignore
└── README.md
```

---

# 55. PHASE 5 ACCEPTANCE CRITERIA

Phase 5 is complete only when:

* Appropriate chainable APIs have been identified
* Dynamic IDs are captured
* Dynamic IDs are reused
* Hardcoded generated IDs are eliminated where possible
* Dynamic test data is implemented where useful
* Authentication chaining is implemented where applicable
* CRUD workflows are implemented where supported
* Dependent requests execute in the correct order
* Existing Phase 4 assertions remain intact
* Temporary test data is cleaned up where possible
* Workflows are repeatable
* Workflows have meaningful assertions
* No sensitive information is exposed
* No infinite request loops exist
* Documentation is updated
* README is updated
* Prompt README is updated
* Phase 5 workflows have been executed successfully

---

# 56. FINAL REPORT

At completion, provide:

## Phase

```text
Phase 5 — API Data Chaining & Workflow Automation
```

## Collection

```text
QA-Demo Project
```

## Environment

```text
QA-Demo Environment
```

## Workflows

Report:

```text
Total chainable workflows:
Create → Read:
Create → Update:
Create → Delete:
Full CRUD:
Authentication:
Other:
```

## Dynamic Data

Report:

```text
Dynamic IDs:
Dynamic test data:
Authentication tokens:
Other extracted values:
```

## Execution

Report:

```text
Total workflow executions:
Passed:
Failed:
Skipped:
```

## Cleanup

Report:

```text
Automatic cleanup:
Partial cleanup:
Cleanup limitations:
```

## Documentation

Report:

```text
Files created:
Files modified:
```

## Known Limitations

Clearly identify any workflows that could not be implemented and why.

---

# 57. STRICT STOP CONDITION

STOP after Phase 5.

Do NOT proceed to:

```text
Newman
npm automation
CI/CD
GitHub Actions
```

Those will be implemented in later phases.

The final Phase 5 architecture should be:

```text
                    API
                     │
                     ▼
             Postman Collection
                     │
          ┌──────────┴──────────┐
          │                     │
     API Requests          Test Assertions
          │                     │
          └──────────┬──────────┘
                     ▼
             Dynamic Variables
                     │
                     ▼
             Data Chaining
                     │
                     ▼
              API Workflows
                     │
                     ▼
              CRUD Lifecycle
                     │
                     ▼
              Test Validation
                     │
                     ▼
               Test Cleanup
```

The collection must be ready for the next phase:

```text
PHASE 6 — NEWMAN AUTOMATION
```

Do not implement Phase 6 during this phase.
