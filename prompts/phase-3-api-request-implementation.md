# PHASE 3 — POSTMAN API REQUEST IMPLEMENTATION

## ROLE

Act as a **Senior API Automation Architect, Postman Expert, QA Automation Engineer, and API Test Framework Engineer**.

You are continuing an existing professional API testing project for:

```text
Application Under Test:
https://qademo.com/
```

Project root:

```text
D:\API Testing\Newman API Testing\QADemoAPITesting
```

The project has completed:

```text
Phase 1 — Project Scaffolding
Phase 2 — API Discovery & Analysis
```

Your responsibility in this phase is to take the **verified API information discovered during Phase 2** and implement the corresponding API requests in the Postman collection.

---

# 1. PHASE CHAIN

This project follows the following controlled development process:

```text
Phase 1
Project Scaffolding
        ↓
Phase 2
API Discovery & Analysis
        ↓
Phase 3
Postman API Request Implementation
        ↓
Phase 4
API Test Automation
        ↓
Phase 5
Data Chaining & Dynamic Test Data
        ↓
Phase 6
Newman Automation
        ↓
Phase 7
Git/GitHub
        ↓
Phase 8
GitHub Actions / CI/CD
```

You are executing **Phase 3 only**.

Do not automatically proceed to Phase 4 or later phases.

---

# 2. READ PHASE 1 FIRST

Before making any changes, read:

```text
prompts/phase-1-project-scaffolding.md
```

Understand the original project architecture and constraints.

---

# 3. READ PHASE 2

Then read:

```text
prompts/phase-2-api-discovery.md
```

This defines how the API discovery was performed.

---

# 4. READ THE PHASE 2 RESULTS

The most important sources for this phase are:

```text
docs/API_DISCOVERY.md
```

and:

```text
docs/api-inventory.json
```

Read both before implementing any Postman requests.

These files are the **primary source of truth** for Phase 3.

Do not rely on assumptions about REST APIs.

Do not create endpoints based on generic conventions.

Do not invent missing API information.

---

# 5. INSPECT THE EXISTING PROJECT

Before modifying anything, inspect:

```text
QADemoAPITesting/
```

including:

```text
prompts/
postman/
docs/
tests/
scripts/
reports/
.github/
README.md
.gitignore
```

Also inspect:

```text
postman/collections/
postman/environments/
```

Identify the existing:

```text
QA-Demo Project
```

collection and:

```text
QA-Demo Environment
```

environment.

Preserve valid Phase 1 and Phase 2 work.

---

# 6. PHASE 3 OBJECTIVE

The primary objective is:

```text
PHASE 2 API DISCOVERY
        ↓
API DEFINITIONS
        ↓
POSTMAN REQUESTS
        ↓
PROFESSIONAL POSTMAN COLLECTION
```

Implement the actual APIs discovered during Phase 2.

For each verified API, create an appropriate Postman request.

The collection must remain:

```text
QA-Demo Project
```

The environment must remain:

```text
QA-Demo Environment
```

---

# 7. STRICT NO-GUESSING RULE

Never invent:

* API endpoints
* API base URLs
* HTTP methods
* Request parameters
* Request fields
* Response fields
* Authentication mechanisms
* Headers
* Status codes
* Resource names
* API versions

If Phase 2 does not provide enough information to implement an endpoint confidently:

1. Investigate the application/API further if possible.
2. If still uncertain, do not invent the information.
3. Record the endpoint as unresolved in the implementation documentation.
4. Continue with verified APIs.

---

# 8. POSTMAN COLLECTION

Use the existing collection:

```text
QA-Demo Project
```

Expected location:

```text
postman/collections/QA-Demo Project.postman_collection.json
```

Do not create a second collection with a different name.

If the collection does not exist, create it using the exact name:

```text
QA-Demo Project
```

Use a valid Postman Collection v2.1 format.

---

# 9. COLLECTION ORGANIZATION

Organize the requests based on the actual API resources discovered in Phase 2.

Do not blindly use generic folders.

Use meaningful resource-based folders.

For example:

```text
QA-Demo Project
│
├── 01 - Authentication
│
├── 02 - Users
│
├── 03 - Products
│
├── 04 - Orders
│
└── 99 - Utility
```

The above resource names are examples only.

Replace them with the actual resources discovered in Phase 2.

If an API resource does not exist, do not create its folder.

---

# 10. REQUEST NAMING STANDARD

Use:

```text
HTTP_METHOD - Action
```

Examples:

```text
GET - Get All Users
GET - Get User By ID
POST - Create User
PUT - Update User
PATCH - Partially Update User
DELETE - Delete User
```

For authentication:

```text
POST - Login
POST - Refresh Token
POST - Logout
```

Use actual names based on the discovered API.

Request names should be:

* Clear
* Consistent
* Human-readable
* Unique where necessary

---

# 11. IMPLEMENT ALL VERIFIED APIS

For every API endpoint in the Phase 2 inventory that has sufficient information, create its corresponding Postman request.

For each request configure:

* HTTP method
* URL
* Path parameters
* Query parameters
* Headers
* Authentication
* Request body
* Appropriate variables
* Useful description

Do not implement automated assertions yet.

---

# 12. URL STRATEGY

Do not hardcode the API host repeatedly.

Use the Postman environment.

Prefer:

```text
{{apiUrl}}/actual/path
```

or the appropriate variable structure determined by Phase 2.

If Phase 2 determined that:

```text
{{baseUrl}}
```

is sufficient, use it appropriately.

Do not create redundant URL variables.

---

# 13. API BASE URL

Use the actual API base URL discovered during Phase 2.

For example, if Phase 2 verified:

```text
https://example.com/api
```

then requests should use:

```text
{{apiUrl}}/...
```

Do not replace the verified API base URL with:

```text
https://qademo.com/api
```

or another assumed value.

---

# 14. POSTMAN ENVIRONMENT

Use:

```text
QA-Demo Environment
```

located at:

```text
postman/environments/QA-Demo Environment.postman_environment.json
```

Preserve existing environment variables.

Use variables where appropriate.

Potential variables may include:

```text
baseUrl
apiUrl
authToken
refreshToken
```

and resource-specific variables such as:

```text
userId
productId
orderId
```

ONLY if they are actually required by the discovered API.

---

# 15. ENVIRONMENT VARIABLE DESIGN

Use environment variables for:

### Environment-specific configuration

```text
baseUrl
apiUrl
```

### Authentication state

```text
authToken
refreshToken
```

### Dynamic identifiers

Where appropriate:

```text
userId
productId
orderId
```

Do not create variables unnecessarily.

Do not duplicate the same value in multiple places.

---

# 16. COLLECTION VARIABLES

Use collection variables only when a value logically belongs to the collection rather than a specific environment.

Do not create duplicate environment and collection variables without a clear reason.

Prefer environment variables for:

* API URLs
* Tokens
* Environment-specific configuration

---

# 17. PATH PARAMETERS

Convert actual path parameters into Postman variables.

If the discovered endpoint is:

```text
/users/{id}
```

use something equivalent to:

```text
{{apiUrl}}/users/{{userId}}
```

Use meaningful variable names.

For example:

```text
{{userId}}
{{productId}}
{{orderId}}
```

rather than blindly using:

```text
{{id}}
```

---

# 18. QUERY PARAMETERS

For APIs with query parameters, configure them in Postman's Params section.

For example, if actually discovered:

```text
?page=1
&limit=10
&search=test
```

use appropriate Postman query parameters.

Document:

* Parameter name
* Value
* Required/optional status
* Purpose

Do not add undocumented parameters.

---

# 19. REQUEST HEADERS

Configure the headers required by the actual API.

Examples may include:

```text
Content-Type: application/json
Accept: application/json
Authorization: Bearer {{authToken}}
```

Only configure headers supported by Phase 2 findings.

Do not blindly add headers to every request.

Where common headers are shared, use collection/folder-level configuration where appropriate.

---

# 20. AUTHENTICATION

Implement the authentication mechanism discovered in Phase 2.

Possible mechanisms include:

* Bearer token
* JWT
* API key
* Basic authentication
* Cookie/session authentication
* OAuth
* CSRF protection
* Other custom mechanisms

Do not assume Bearer authentication.

Use the actual mechanism discovered.

Never hardcode real tokens.

---

# 21. AUTHENTICATION REQUESTS

If Phase 2 discovered login/authentication APIs, implement them under:

```text
01 - Authentication
```

For example:

```text
POST - Login
POST - Refresh Token
POST - Logout
```

Only create requests that actually exist.

Use safe variables for credentials.

Do not commit real credentials.

---

# 22. REQUEST BODY IMPLEMENTATION

For POST/PUT/PATCH requests, implement the actual request body discovered during Phase 2.

If the API expects JSON:

```text
Body
→ raw
→ JSON
```

Use the actual schema.

For example:

```json
{
  "actualField": "{{actualValue}}"
}
```

Do not invent fields.

Do not remove required fields.

Do not add arbitrary fields.

---

# 23. REQUEST BODY VARIABLES

Use variables where they provide real value.

Examples:

```json
{
  "name": "{{userName}}",
  "email": "{{userEmail}}"
}
```

Do not turn every field into a variable.

Use variables primarily for:

* Dynamic values
* Environment-specific values
* Reusable test data
* IDs
* Authentication information

---

# 24. REQUEST DESCRIPTIONS

Every implemented request should have a useful description.

The description should explain:

```text
Purpose:
What this API does.

Method:
HTTP method.

Endpoint:
Relative API endpoint.

Authentication:
Required/Not required.

Parameters:
Relevant path/query parameters.

Body:
Required request body, if applicable.

Expected response:
Brief response description.
```

Keep descriptions concise and professional.

---

# 25. RESPONSE EXAMPLES

Where practical, add sanitized Postman response examples based on actual Phase 2 findings.

Do not include:

* Real passwords
* Real access tokens
* API secrets
* Sensitive personal information

Use sanitized values.

---

# 26. STATUS CODE DOCUMENTATION

Document expected/observed status codes in the request description or response examples.

Use actual Phase 2 findings.

Examples:

```text
200 OK
201 Created
204 No Content
400 Bad Request
401 Unauthorized
403 Forbidden
404 Not Found
```

Do not assume a status code simply because it is conventional.

---

# 27. CRUD ORGANIZATION

Where actual APIs support CRUD, organize them logically.

For example:

```text
Users
│
├── GET - Get All Users
├── GET - Get User By ID
├── POST - Create User
├── PUT - Update User
├── PATCH - Partial Update User
└── DELETE - Delete User
```

Only implement operations that actually exist.

---

# 28. API DEPENDENCIES

Use the Phase 2 dependency information when organizing requests.

For example:

```text
Authentication
      ↓
Create Resource
      ↓
Resource ID
      ↓
Get Resource
      ↓
Update Resource
      ↓
Delete Resource
```

Do not implement automated chaining in this phase.

Phase 5 will handle dynamic data chaining.

---

# 29. TEST SCRIPTS — IMPORTANT

Do NOT implement the complete test automation framework yet.

Do not add comprehensive assertions such as:

```javascript
pm.test(...)
```

for every request.

Do not implement:

* Schema validation
* Business-rule assertions
* Response field assertions
* Advanced status validation
* Negative test automation
* Boundary testing

These belong to:

```text
Phase 4 — API Test Automation
```

Phase 3 should primarily create and configure the requests.

---

# 30. PRE-REQUEST SCRIPTS

Do not create complex pre-request scripts.

Only create a minimal pre-request script if it is genuinely required for the API request to function.

Document why it is required.

Complex dynamic data generation belongs to Phase 5.

---

# 31. NEGATIVE TESTS

Do not build the complete negative-testing suite.

Phase 2 discovery results may contain validation scenarios.

Preserve that information for Phase 4.

If a negative-test folder already exists, it may remain empty until Phase 4.

---

# 32. CLEANUP REQUESTS

If Phase 2 discovered cleanup/delete APIs, implement the corresponding requests.

Do not automatically execute destructive operations.

The requests should be available for future automation.

---

# 33. COLLECTION DESCRIPTION

Update the collection description to reflect:

```text
QA-Demo Project

Purpose:
Professional API testing collection for the QA-Demo application.

Application:
https://qademo.com/

Current Phase:
Phase 3 - Postman API Request Implementation

API definitions:
Derived from Phase 2 API Discovery.

Future phases:
Phase 4 - API Test Automation
Phase 5 - Data Chaining
Phase 6 - Newman
Phase 7 - Git/GitHub
Phase 8 - GitHub Actions / CI/CD
```

---

# 34. CREATE POSTMAN IMPLEMENTATION DOCUMENTATION

Create:

```text
docs/POSTMAN_IMPLEMENTATION.md
```

Document:

## Project

```text
QA-Demo Project
```

## Environment

```text
QA-Demo Environment
```

## API Base URL

Actual verified value.

## Collection Structure

Show the actual folder structure.

## Request Naming Convention

Document:

```text
HTTP_METHOD - Action
```

## Variable Strategy

Explain:

* Environment variables
* Collection variables
* Dynamic identifiers

## Authentication Strategy

Document the actual authentication mechanism.

## API Implementation Matrix

Create a table:

| # | Resource | Method | Endpoint | Postman Request | Status      |
| - | -------- | ------ | -------- | --------------- | ----------- |
| 1 | Actual   | GET    | Actual   | Actual request  | Implemented |
| 2 | Actual   | POST   | Actual   | Actual request  | Implemented |

Use actual discovered APIs.

## Unimplemented APIs

List any APIs that could not be implemented and explain why.

## Phase 4 Preparation

Explain what remains for automated test implementation.

---

# 35. UPDATE API INVENTORY

Review:

```text
docs/api-inventory.json
```

If appropriate, add implementation metadata without destroying the discovery information.

For example:

```json
{
  "postmanImplemented": true
}
```

Only add this if it makes the inventory more useful.

Do not alter actual discovery data merely to make the implementation appear complete.

---

# 36. UPDATE API DISCOVERY DOCUMENT

Do not rewrite or destroy:

```text
docs/API_DISCOVERY.md
```

It represents Phase 2 findings.

If necessary, add a small section indicating:

```text
Phase 3 implementation status
```

but preserve the original discovery information.

---

# 37. UPDATE ROOT README

Update:

```text
README.md
```

to:

```text
Current Phase:
Phase 3 - Postman API Request Implementation
```

Preserve the Phase 1 and Phase 2 history.

Reference:

```text
docs/API_DISCOVERY.md
docs/POSTMAN_IMPLEMENTATION.md
```

---

# 38. UPDATE PROMPTS README

Update:

```text
prompts/README.md
```

to include:

```text
prompts/
│
├── phase-1-project-scaffolding.md
├── phase-2-api-discovery.md
├── phase-3-api-request-implementation.md
└── README.md
```

Explain:

```text
Phase 1 → Project scaffolding
Phase 2 → API discovery
Phase 3 → Postman request implementation
```

---

# 39. SAVE THIS PROMPT

The complete Phase 3 prompt must be saved at:

```text
prompts/phase-3-api-request-implementation.md
```

The file must contain the complete instructions being executed.

Do not save only a summary.

---

# 40. SECURITY REQUIREMENTS

Never store or commit:

* Real passwords
* Real API keys
* Access tokens
* Refresh tokens
* Client secrets
* Session cookies
* Sensitive personal information

Use:

```text
{{variableName}}
```

or safe placeholders.

Redact sensitive information in documentation.

---

# 41. VALIDATION — COLLECTION

Validate the resulting Postman collection.

Confirm:

* Collection name = `QA-Demo Project`
* Postman Collection v2.1
* All implemented requests correspond to Phase 2 APIs
* No invented endpoints
* HTTP methods are correct
* URLs are correct
* Variables are correctly referenced
* Path parameters are correct
* Query parameters are correct
* Headers are correct
* Authentication is correct
* Request bodies are correct
* Request descriptions are present
* Folder structure is logical

---

# 42. VALIDATION — ENVIRONMENT

Validate:

```text
QA-Demo Environment
```

Confirm:

* API URL is correct
* Variables are valid
* No secrets are exposed
* No duplicate unnecessary variables exist
* Authentication variables are handled safely

---

# 43. API COVERAGE

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
Total discovered APIs:
Successfully implemented:
Not implemented:
```

Do not claim 100% coverage unless every eligible API has been implemented.

---

# 44. HTTP METHOD COVERAGE

Provide actual counts:

```text
GET:
POST:
PUT:
PATCH:
DELETE:
HEAD:
OPTIONS:
```

---

# 45. UNRESOLVED APIS

If an API from Phase 2 cannot be implemented because information is missing or ambiguous, do NOT guess.

Record:

```text
Endpoint:
Reason:
Missing information:
Recommended next step:
```

in:

```text
docs/POSTMAN_IMPLEMENTATION.md
```

---

# 46. DO NOT INSTALL NEW TOOLS

Do not install:

* Newman
* npm packages
* Additional automation frameworks

Do not modify system configuration.

Phase 3 does not require those tools.

---

# 47. DO NOT IMPLEMENT CI/CD

Do not create or modify:

```text
.github/workflows/
```

Do not implement:

* GitHub Actions
* Newman pipelines
* CI/CD
* Scheduled testing
* Deployment workflows

These belong to later phases.

---

# 48. FINAL PROJECT STRUCTURE

After Phase 3, the expected structure is approximately:

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
│   │
│   ├── environments/
│   │   └── QA-Demo Environment.postman_environment.json
│   │
│   └── data/
│
├── docs/
│   ├── PROJECT_PHASES.md
│   ├── API_DISCOVERY.md
│   ├── api-inventory.json
│   └── POSTMAN_IMPLEMENTATION.md
│
├── tests/
├── scripts/
├── reports/
│
├── .github/
│   └── workflows/
│
├── .gitignore
└── README.md
```

---

# 49. FINAL VALIDATION

Before finishing, verify:

### Phase 1

Phase 1 artifacts remain intact.

### Phase 2

These still exist:

```text
docs/API_DISCOVERY.md
docs/api-inventory.json
```

### Phase 3

This exists:

```text
docs/POSTMAN_IMPLEMENTATION.md
```

### Prompt history

These exist:

```text
prompts/phase-1-project-scaffolding.md
prompts/phase-2-api-discovery.md
prompts/phase-3-api-request-implementation.md
```

### Postman

Confirm:

```text
QA-Demo Project
QA-Demo Environment
```

are valid.

### Security

Confirm no secrets are hardcoded.

---

# 50. FINAL REPORT

After completing the work, provide:

## Phase

```text
Phase 3 - Postman API Request Implementation
```

## Application

```text
https://qademo.com/
```

## API Base URL

Report the verified API base URL.

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
Implemented:
Not implemented:
```

## HTTP Method Coverage

```text
GET:
POST:
PUT:
PATCH:
DELETE:
HEAD:
OPTIONS:
```

## Authentication

Describe the actual authentication implementation.

## Environment Variables

List variable names only.

Do not expose sensitive values.

## Collection Structure

Show the final folder/request hierarchy.

## Documentation

Confirm:

```text
docs/API_DISCOVERY.md
docs/api-inventory.json
docs/POSTMAN_IMPLEMENTATION.md
```

## Prompt History

Confirm:

```text
prompts/phase-1-project-scaffolding.md
prompts/phase-2-api-discovery.md
prompts/phase-3-api-request-implementation.md
```

## Validation

Confirm:

* Collection validated
* Environment validated
* API coverage checked
* No fabricated endpoints
* No secrets exposed

## Phase 4 Preparation

Explain that Phase 4 can now add:

* Status-code assertions
* Response assertions
* Header validation
* JSON validation
* Schema validation
* Business-rule validation
* Negative tests
* Boundary tests

---

# 51. STRICT STOP CONDITION

STOP after Phase 3.

Do NOT proceed to:

* Phase 4
* Automated assertions
* Comprehensive negative testing
* Data chaining
* Newman
* npm automation
* Git
* GitHub
* GitHub Actions
* CI/CD

The next phase will be initiated separately.

The goal of Phase 3 is:

```text
PHASE 2 DISCOVERY
       ↓
VERIFIED API DEFINITIONS
       ↓
POSTMAN REQUESTS
       ↓
PROFESSIONAL COLLECTION
       ↓
READY FOR TEST AUTOMATION
```

Do not go beyond this boundary.
