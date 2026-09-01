# PHASE 4 — API TEST AUTOMATION

## ROLE

Act as a **Senior QA Automation Architect, API Automation Engineer, Postman Expert, and Test Framework Designer**.

You are continuing the existing professional API testing project:

```text
Application Under Test:
https://qademo.com/
```

Project:

```text
D:\API Testing\Newman API Testing\QADemoAPITesting
```

The following phases have already been completed:

```text
Phase 1 — Project Scaffolding
Phase 2 — API Discovery & Analysis
Phase 3 — Postman API Request Implementation
```

Your responsibility is to execute **Phase 4 — API Test Automation**.

---

# 1. PHASE OBJECTIVE

Convert the Postman requests created during Phase 3 into a professional automated API test suite.

The target architecture is:

```text
API Discovery
      ↓
Postman Requests
      ↓
Automated Assertions
      ↓
Response Validation
      ↓
Business Validation
      ↓
Negative Testing
      ↓
Boundary Testing
      ↓
Reusable Test Framework
```

The resulting Postman collection must be suitable for future execution through Newman.

---

# 2. READ PREVIOUS PHASES FIRST

Before making any changes, read:

```text
prompts/phase-1-project-scaffolding.md
prompts/phase-2-api-discovery.md
prompts/phase-3-api-request-implementation.md
```

Then read:

```text
docs/API_DISCOVERY.md
docs/api-inventory.json
docs/POSTMAN_IMPLEMENTATION.md
```

These are the primary sources of truth.

Do not redesign the project unnecessarily.

Do not invent APIs.

Do not invent expected behavior.

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

* Existing folders
* Existing requests
* Existing variables
* Authentication
* Request bodies
* Query parameters
* Path parameters
* Existing scripts
* Existing descriptions

Preserve all valid Phase 3 work.

---

# 4. PHASE 4 SOURCE OF TRUTH

Use:

```text
docs/API_DISCOVERY.md
docs/api-inventory.json
```

to determine:

* Expected status codes
* Response structure
* Required fields
* Authentication behavior
* Validation behavior
* Error behavior
* CRUD behavior
* Business rules
* Query behavior
* Resource relationships

Do not assume conventional REST behavior.

For example, do not assume:

```text
POST = 201
DELETE = 204
```

unless the actual API behavior supports it.

---

# 5. TEST AUTOMATION PRINCIPLE

Every important Postman request should have meaningful automated validation.

Avoid meaningless tests such as:

```javascript
pm.test("Request completed", function () {
    pm.expect(pm.response).to.exist;
});
```

Tests must validate actual API behavior.

---

# 6. TEST NAMING STANDARD

Use professional test names.

Examples:

```text
Status code is 200
Response time is below threshold
Response Content-Type is JSON
Response contains required fields
User ID is a number
User email has valid format
Created resource matches request data
Unauthorized request is rejected
Invalid request returns validation error
Deleted resource is no longer accessible
```

Names should clearly explain what is being validated.

---

# 7. STATUS CODE VALIDATION

Add status-code assertions based on actual Phase 2 findings.

Example:

```javascript
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});
```

For multiple valid statuses, use an explicit list:

```javascript
pm.test("Status code is successful", function () {
    pm.expect([200, 201]).to.include(pm.response.code);
});
```

Only use statuses supported by the actual API behavior.

---

# 8. RESPONSE TIME VALIDATION

Add response-time validation where appropriate.

Example:

```javascript
pm.test("Response time is less than 2000 ms", function () {
    pm.expect(pm.response.responseTime).to.be.below(2000);
});
```

Use a reasonable threshold.

Do not create unrealistic performance requirements.

If the project documentation defines a different threshold, follow it.

---

# 9. CONTENT-TYPE VALIDATION

For JSON APIs, validate the response content type where appropriate.

Example:

```javascript
pm.test("Response Content-Type is JSON", function () {
    pm.expect(pm.response.headers.get("Content-Type"))
        .to.include("application/json");
});
```

Only apply this where the endpoint actually returns JSON.

---

# 10. RESPONSE BODY VALIDATION

Parse JSON responses where appropriate:

```javascript
const response = pm.response.json();
```

Validate meaningful fields.

Example:

```javascript
pm.test("Response contains user ID", function () {
    pm.expect(response).to.have.property("id");
});
```

Do not merely check that the response is valid JSON.

---

# 11. REQUIRED FIELD VALIDATION

Validate fields identified during Phase 2.

Example:

```javascript
pm.test("Response contains required fields", function () {
    pm.expect(response).to.have.property("id");
    pm.expect(response).to.have.property("name");
    pm.expect(response).to.have.property("email");
});
```

Use actual fields from the API.

---

# 12. DATA TYPE VALIDATION

Validate important field types.

Example:

```javascript
pm.test("User ID is a number", function () {
    pm.expect(response.id).to.be.a("number");
});
```

Possible types:

```text
string
number
boolean
array
object
```

Only validate types supported by the actual API.

---

# 13. NULL / EMPTY VALUE VALIDATION

Where fields are required, validate that they are not unexpectedly null or empty.

Example:

```javascript
pm.test("User name is not empty", function () {
    pm.expect(response.name).to.be.a("string").and.not.empty;
});
```

Use this only for fields where the API contract requires a value.

---

# 14. FORMAT VALIDATION

For fields with known formats, validate appropriately.

Examples:

* Email
* UUID
* Date
* URL
* Phone number

Example:

```javascript
pm.test("Email has valid format", function () {
    pm.expect(response.email).to.match(
        /^[^\s@]+@[^\s@]+\.[^\s@]+$/
    );
});
```

Do not add format assumptions unsupported by the API.

---

# 15. ARRAY VALIDATION

For list endpoints, validate that the response contains an array where appropriate.

Example:

```javascript
pm.test("Response is an array", function () {
    pm.expect(response).to.be.an("array");
});
```

Then validate important characteristics.

Example:

```javascript
pm.test("Response contains expected resource structure", function () {
    response.forEach(item => {
        pm.expect(item).to.have.property("id");
    });
});
```

Do not require a non-empty array if an empty result is a legitimate API response.

---

# 16. OBJECT STRUCTURE VALIDATION

For object responses, validate the expected structure.

Example:

```javascript
pm.test("Response has expected structure", function () {
    pm.expect(response).to.be.an("object");
    pm.expect(response).to.have.property("id");
});
```

Use actual API schemas.

---

# 17. HEADER VALIDATION

Validate important response headers where appropriate.

Examples:

```javascript
pm.test("Response contains Content-Type header", function () {
    pm.expect(pm.response.headers.has("Content-Type")).to.be.true;
});
```

For authentication-related APIs, validate relevant headers only when appropriate.

---

# 18. REQUEST/RESPONSE CONSISTENCY

For POST requests, validate that the response represents the created resource.

If the request contains:

```json
{
    "name": "{{userName}}"
}
```

and the response contains:

```json
{
    "name": "..."
}
```

validate the relationship where appropriate.

Example:

```javascript
const response = pm.response.json();

pm.test("Created name matches request", function () {
    pm.expect(response.name).to.eql(
        pm.variables.get("userName")
    );
});
```

Only implement this where the API behavior supports it.

---

# 19. CRUD VALIDATION

Where CRUD APIs exist, validate the lifecycle.

Example:

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
READ
```

Phase 4 should add assertions that validate each operation.

Do not implement complex cross-request data chaining unless required.

Advanced dynamic chaining belongs to Phase 5.

---

# 20. CREATE VALIDATION

For POST requests, validate:

* Correct status code
* Response format
* Required fields
* Generated identifier
* Returned resource
* Request/response consistency
* Important headers

Use actual API behavior.

---

# 21. READ VALIDATION

For GET requests, validate:

* Status code
* Response structure
* Required fields
* Data types
* Expected resource
* Collection/list structure
* Query behavior where applicable

---

# 22. UPDATE VALIDATION

For PUT/PATCH requests, validate:

* Correct status code
* Updated response
* Updated fields
* Required fields
* Data types
* Response structure

Where possible, verify that the requested change is reflected in the response.

---

# 23. DELETE VALIDATION

For DELETE requests, validate the actual API behavior.

Possible validations include:

```text
Expected status code
Response body
Response headers
Deletion confirmation
```

Do not assume the response must be `204`.

Use the actual API behavior discovered in Phase 2.

---

# 24. AUTHENTICATION TESTS

For authentication APIs, validate:

* Successful authentication
* Token presence where applicable
* Authentication failure
* Invalid credentials
* Missing authentication
* Expired/invalid token behavior where discoverable

Do not expose actual credentials or tokens.

---

# 25. NEGATIVE TESTING

Add negative tests for behaviors actually supported by the API.

Examples:

```text
Missing required field
Invalid field value
Invalid ID
Non-existent resource
Unauthorized request
Invalid authentication
Malformed request
Invalid query parameter
Invalid data type
```

Use actual API behavior.

Do not create negative tests based solely on generic REST assumptions.

---

# 26. NEGATIVE TEST ORGANIZATION

Where appropriate, create folders such as:

```text
Resource
├── Positive
│   ├── GET - ...
│   ├── POST - ...
│   └── PUT - ...
│
└── Negative
    ├── POST - Missing Required Field
    ├── GET - Invalid ID
    └── GET - Unauthorized
```

However, do not unnecessarily duplicate requests.

Choose the organization that best fits the actual API.

---

# 27. VALIDATION TEST ORGANIZATION

Use clear folders where helpful:

```text
Authentication
Positive Tests
Negative Tests
CRUD
Validation
```

Do not create empty folders merely to satisfy a template.

---

# 28. BOUNDARY TESTING

Where the API exposes constraints, test boundaries.

Examples:

```text
Minimum string length
Maximum string length
Minimum numeric value
Maximum numeric value
Empty string
Null
Invalid date
Invalid ID
Maximum page size
```

Only test constraints actually discovered.

---

# 29. QUERY PARAMETER TESTS

For APIs with query parameters, test appropriate cases:

```text
Valid parameter
Missing optional parameter
Invalid parameter
Boundary value
Multiple parameters
```

Use actual API behavior.

---

# 30. PATH PARAMETER TESTS

For resource endpoints, test appropriate path values:

```text
Valid ID
Invalid ID format
Non-existent ID
```

Only where relevant.

---

# 31. ERROR RESPONSE VALIDATION

For negative tests, validate the error response.

Examples:

```javascript
pm.test("Error response contains message", function () {
    const response = pm.response.json();
    pm.expect(response).to.have.property("message");
});
```

Use actual error structures.

Do not assume every API returns:

```json
{
    "message": "..."
}
```

---

# 32. JSON SCHEMA VALIDATION

Where the API structure is stable enough, introduce JSON schema validation.

Example:

```javascript
const schema = {
    type: "object",
    required: ["id", "name"],
    properties: {
        id: {
            type: "number"
        },
        name: {
            type: "string"
        }
    }
};

pm.test("Response matches schema", function () {
    pm.response.to.have.jsonSchema(schema);
});
```

Use schemas derived from the actual API.

Do not invent unnecessary schema restrictions.

---

# 33. REUSABLE TEST PATTERNS

Avoid unnecessary duplication.

Where a validation is common across multiple requests, determine whether it should be implemented at:

```text
Collection level
Folder level
Request level
```

Examples of common validations:

```text
Content-Type
Response time
Authentication
Common response headers
```

Request-specific validations should remain at request level.

---

# 34. COLLECTION-LEVEL TESTS

Use collection-level scripts only for truly common behavior.

Do not put endpoint-specific assertions at collection level.

Keep the framework understandable.

---

# 35. FOLDER-LEVEL TESTS

Use folder-level scripts when several requests share the same validation logic.

For example:

```text
Authentication
```

may share common authentication-related behavior.

Use this only when it improves maintainability.

---

# 36. ENVIRONMENT VARIABLES

Preserve the Phase 3 environment:

```text
QA-Demo Environment
```

Do not hardcode:

* Tokens
* Passwords
* API keys
* Environment-specific URLs

Use variables.

---

# 37. TEST DATA VARIABLES

Where appropriate, create safe test-data variables such as:

```text
testUserName
testUserEmail
testProductName
```

Use only variables actually needed by the API.

Do not create a large unnecessary variable list.

---

# 38. SECURITY

Never store:

```text
Real passwords
Real API keys
Real access tokens
Real refresh tokens
Client secrets
Session cookies
```

Use safe placeholders or environment variables.

Do not print sensitive tokens using:

```javascript
console.log(pm.environment.get("authToken"));
```

---

# 39. TEST SCRIPT QUALITY

All scripts must:

* Be readable
* Use meaningful test names
* Avoid unnecessary duplication
* Avoid hardcoded environment-specific values
* Avoid sensitive data
* Fail clearly when validation fails
* Use actual API contracts

Prefer:

```javascript
pm.test("User ID is numeric", function () {
    pm.expect(response.id).to.be.a("number");
});
```

over vague tests such as:

```javascript
pm.test("Test passed", function () {
    pm.expect(true).to.be.true;
});
```

---

# 40. DO NOT IMPLEMENT NEWMAN

Do NOT install or configure Newman.

Do NOT create:

```text
package.json
npm scripts
```

unless they already exist for an unrelated reason.

Newman belongs to Phase 6.

---

# 41. DO NOT IMPLEMENT CI/CD

Do not create or modify:

```text
.github/workflows/
```

Do not implement:

* GitHub Actions
* CI/CD
* Scheduled execution
* Deployment automation

These belong to later phases.

---

# 42. DO NOT IMPLEMENT ADVANCED DATA CHAINING

Do not build the complete dynamic data-chaining framework yet.

Phase 5 will handle:

```text
Create resource
      ↓
Capture ID
      ↓
Store variable
      ↓
Use ID in next request
      ↓
Update
      ↓
Delete
```

Phase 4 may prepare the tests for this, but do not build the complete chaining architecture.

---

# 43. CREATE PHASE 4 DOCUMENTATION

Create:

```text
docs/API_TEST_AUTOMATION.md
```

Document:

## Phase

```text
Phase 4 - API Test Automation
```

## Collection

```text
QA-Demo Project
```

## Environment

```text
QA-Demo Environment
```

## Test Strategy

Explain:

* Functional validation
* Status-code validation
* Response validation
* Schema validation
* Negative testing
* Boundary testing
* Authentication testing
* CRUD validation

## Assertion Strategy

Explain:

```text
Collection-level assertions
Folder-level assertions
Request-level assertions
```

## Test Coverage Matrix

Create:

| # | Resource | Method | Endpoint | Positive Tests | Negative Tests | Status |
| - | -------- | ------ | -------- | -------------- | -------------- | ------ |

Use actual APIs.

## Assertion Coverage

Document:

```text
Status code
Response time
Headers
Content type
Required fields
Data types
Business rules
Schema
Error responses
```

## Phase 5 Preparation

Document what will be handled in Phase 5.

---

# 44. UPDATE API INVENTORY

Review:

```text
docs/api-inventory.json
```

If useful, add test implementation metadata.

For example:

```json
{
    "postmanImplemented": true,
    "testsImplemented": true
}
```

Do not destroy the Phase 2 discovery information.

---

# 45. UPDATE POSTMAN IMPLEMENTATION DOCUMENT

Review:

```text
docs/POSTMAN_IMPLEMENTATION.md
```

Add a Phase 4 implementation status section if appropriate.

Do not rewrite the Phase 3 history.

---

# 46. UPDATE README

Update the root:

```text
README.md
```

to show:

```text
Current Phase:
Phase 4 - API Test Automation
```

Preserve the history:

```text
Phase 1 — Completed
Phase 2 — Completed
Phase 3 — Completed
Phase 4 — In Progress/Completed
```

---

# 47. UPDATE PROMPTS README

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
```

---

# 48. SAVE THE PHASE 4 PROMPT

Ensure this prompt is stored at:

```text
prompts/phase-4-api-test-automation.md
```

Do not store it elsewhere.

---

# 49. VALIDATE TESTS

Run the Postman collection in Postman.

Review the test results.

Confirm:

* Tests execute
* Tests have meaningful names
* Assertions pass where expected
* Intentional negative tests fail/pass according to expected behavior
* No scripts produce JavaScript errors
* No sensitive values are exposed

---

# 50. TEST SCRIPT ERROR CHECK

Inspect the Postman Console and collection results for:

```text
ReferenceError
TypeError
SyntaxError
JSON parsing errors
Undefined variables
Missing environment variables
```

Fix all genuine automation errors.

---

# 51. TEST COVERAGE

Compare:

```text
docs/api-inventory.json
```

against:

```text
QA-Demo Project
```

Calculate:

```text
Total discovered APIs
Total implemented requests
Total requests with automated tests
Total positive tests
Total negative tests
Total unresolved tests
```

Do not claim 100% coverage unless the evidence supports it.

---

# 52. PHASE 4 VALIDATION MATRIX

Create a final matrix:

| Area                    | Status    |
| ----------------------- | --------- |
| Status code validation  | PASS/FAIL |
| Response validation     | PASS/FAIL |
| Header validation       | PASS/FAIL |
| Content-Type validation | PASS/FAIL |
| Required fields         | PASS/FAIL |
| Data type validation    | PASS/FAIL |
| Schema validation       | PASS/FAIL |
| CRUD validation         | PASS/FAIL |
| Authentication tests    | PASS/FAIL |
| Negative tests          | PASS/FAIL |
| Boundary tests          | PASS/FAIL |
| Error validation        | PASS/FAIL |
| Security validation     | PASS/FAIL |

---

# 53. FINAL PROJECT STRUCTURE

Expected structure:

```text
QADemoAPITesting/
│
├── prompts/
│   ├── phase-1-project-scaffolding.md
│   ├── phase-2-api-discovery.md
│   ├── phase-3-api-request-implementation.md
│   ├── phase-4-api-test-automation.md
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
│   └── API_TEST_AUTOMATION.md
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

# 54. FINAL REPORT

After implementation, report:

## Phase

```text
Phase 4 - API Test Automation
```

## Collection

```text
QA-Demo Project
```

## Environment

```text
QA-Demo Environment
```

## API Coverage

```text
Total discovered:
Total requests:
Total requests with tests:
```

## Test Coverage

```text
Positive tests:
Negative tests:
Boundary tests:
Authentication tests:
CRUD tests:
Schema tests:
```

## Assertion Coverage

```text
Status codes:
Response time:
Headers:
Content-Type:
Required fields:
Data types:
Business rules:
Schemas:
Error responses:
```

## Test Execution

Report:

```text
Total tests:
Passed:
Failed:
Skipped:
```

Do not claim success if tests were not actually executed.

---

# 55. PHASE 4 COMPLETION CRITERIA

Phase 4 is complete only when:

* All eligible Phase 3 requests have appropriate automated tests
* Tests validate actual API behavior
* Positive scenarios are covered
* Important negative scenarios are covered
* Important validation/boundary scenarios are covered
* Authentication behavior is tested where applicable
* Response structures are validated
* Important headers are validated
* Meaningful assertions exist
* No JavaScript errors exist
* No secrets are exposed
* Postman collection executes successfully
* Documentation is updated
* Phase 5 has not been implemented prematurely

---

# 56. STRICT STOP CONDITION

STOP after Phase 4.

Do NOT proceed to:

* Phase 5 data chaining
* Newman
* npm automation
* GitHub Actions
* CI/CD

The next phase will be initiated separately.

The target result is:

```text
PHASE 3
Postman Requests
       ↓
PHASE 4
Automated API Tests
       ↓
Validated Postman Collection
       ↓
READY FOR DATA CHAINING
```
