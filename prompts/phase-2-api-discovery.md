# PHASE 2 — API DISCOVERY & ANALYSIS

## ROLE

Act as a **Senior QA Automation Architect, API Test Architect, API Analyst, and Postman Expert**.

You are continuing an existing professional API testing project for:

**Application Under Test:**

```text
https://qademo.com/
```

**Project Root:**

```text
D:\API Testing\Newman API Testing\QADemoAPITesting
```

The project was initialized during **Phase 1**.

Your responsibility in this phase is to perform a **complete API discovery and analysis** of the QA Demo application and prepare the project for Phase 3, where the actual Postman API requests will be implemented.

---

# 1. PHASE 1 CONTEXT

Before performing any work, read and understand the Phase 1 prompt:

```text
prompts/phase-1-project-scaffolding.md
```

Also inspect the current project structure and existing files.

Phase 1 established the initial project foundation, including:

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

Phase 1 created the initial Postman collection:

```text
QA-Demo Project
```

and environment:

```text
QA-Demo Environment
```

Phase 1 intentionally did NOT implement:

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

Phase 2 now focuses specifically on **API discovery and analysis**.

---

# 2. PHASE 2 OBJECTIVE

The objective of Phase 2 is to discover the actual APIs used by:

```text
https://qademo.com/
```

and create a reliable API inventory.

The result of this phase should answer:

> **What APIs does the QA Demo application actually expose, how do they work, what data do they require, how are they authenticated, and how should they later be represented in Postman?**

Do not guess.

Do not fabricate.

Do not assume standard REST endpoints.

Discover the actual API behavior.

---

# 3. IMPORTANT PHASE BOUNDARY

This phase is primarily **API discovery and documentation**.

DO NOT turn Phase 2 into full API automation.

Do NOT implement the complete Postman test suite yet.

Do NOT create comprehensive test scripts yet.

Do NOT implement Newman yet.

Do NOT implement CI/CD yet.

The expected flow is:

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
Data Chaining
       ↓
Phase 6
Newman
       ↓
Phase 7
Git/GitHub
       ↓
Phase 8
GitHub Actions / CI/CD
```

---

# 4. APPLICATION EXPLORATION

Thoroughly explore:

```text
https://qademo.com/
```

Analyze the application from an API-testing perspective.

Explore all accessible functionality.

Identify:

* Main pages
* Navigation
* Forms
* Authentication
* Login
* Logout
* Registration
* User functionality
* Product functionality
* Search
* Filtering
* Sorting
* Pagination
* CRUD operations
* Forms that submit data
* Data retrieval
* Data modification
* Data deletion
* Relationships between resources
* Error handling
* Validation behavior
* Authorization behavior
* Any other functionality that communicates with backend APIs

---

# 5. DISCOVER THE ACTUAL API LAYER

Investigate how the frontend communicates with the backend.

Look for:

* REST APIs
* HTTP endpoints
* API base URLs
* API versions
* AJAX requests
* Fetch requests
* XMLHttpRequest
* frontend JavaScript API calls
* network requests
* backend services
* API documentation
* OpenAPI/Swagger documentation if available
* GraphQL endpoints if applicable
* authentication endpoints
* authorization endpoints

Inspect available frontend/network information where technically possible.

Do not assume that the API is hosted at the same URL as the frontend.

For example, do NOT assume:

```text
https://qademo.com/api
```

or:

```text
https://qademo.com/api/v1
```

unless this is actually discovered.

---

# 6. API BASE URL

Determine the actual API base URL.

Document:

```text
Application URL:
https://qademo.com/

API Base URL:
<actual discovered value>
```

If the API base URL cannot be reliably determined, explicitly report:

```text
API Base URL:
Unable to determine reliably
```

and explain why.

Never invent a value.

---

# 7. API ENDPOINT INVENTORY

Create a complete inventory of discovered API endpoints.

For every endpoint document:

* Resource
* HTTP method
* Full endpoint
* Relative endpoint
* Purpose
* Authentication requirement
* Content-Type
* Accept header
* Required headers
* Path parameters
* Query parameters
* Request body
* Required fields
* Optional fields
* Expected status codes
* Response structure
* Dependencies
* Data created/modified/deleted
* Notes

Use a table similar to:

| # | Resource        | Method | Endpoint        | Purpose        | Auth   | Expected Status |
| - | --------------- | ------ | --------------- | -------------- | ------ | --------------- |
| 1 | Actual resource | GET    | Actual endpoint | Actual purpose | Yes/No | Actual status   |
| 2 | Actual resource | POST   | Actual endpoint | Actual purpose | Yes/No | Actual status   |
| 3 | Actual resource | PUT    | Actual endpoint | Actual purpose | Yes/No | Actual status   |
| 4 | Actual resource | DELETE | Actual endpoint | Actual purpose | Yes/No | Actual status   |

Only include APIs that are actually discovered.

---

# 8. HTTP METHOD ANALYSIS

Identify every HTTP method actually used.

Evaluate:

```text
GET
POST
PUT
PATCH
DELETE
HEAD
OPTIONS
```

Do not create or document methods simply because they are common REST methods.

Document only methods actually supported or observed.

For example:

```text
GET    → discovered
POST   → discovered
PUT    → not discovered
PATCH  → not discovered
DELETE → discovered
```

---

# 9. AUTHENTICATION DISCOVERY

Determine exactly how authentication works.

Investigate whether the application uses:

* Bearer tokens
* JWT
* API keys
* Basic authentication
* Session cookies
* OAuth
* CSRF tokens
* Cookies
* Custom authentication
* Other mechanisms

Determine:

### Login endpoint

If available:

```text
Method:
Endpoint:
Request:
Response:
Token/cookie:
```

### Token location

Determine whether authentication data is stored in:

* Response JSON
* Headers
* Cookies
* Local storage
* Session storage
* Other locations

### Token usage

Determine whether subsequent requests require something like:

```text
Authorization: Bearer <token>
```

or another mechanism.

Do not assume the format.

Document the actual behavior.

---

# 10. AUTHENTICATION FLOW

Document the authentication sequence.

For example, if actually applicable:

```text
Login
  ↓
Authentication response
  ↓
Extract token
  ↓
Store token
  ↓
Authenticated API request
```

If refresh tokens exist:

```text
Login
  ↓
Access Token
  ↓
Refresh Token
  ↓
Refresh Access Token
```

Document the actual flow.

---

# 11. REQUEST HEADERS

For each discovered API, identify important headers.

Examples may include:

```text
Content-Type
Accept
Authorization
Cookie
X-CSRF-Token
X-API-Key
```

Do not automatically add these headers to Postman yet.

First document what the actual application uses.

---

# 12. PATH PARAMETERS

Identify all path parameters.

Example:

```text
GET /users/{id}
```

Document:

```text
Parameter:
id

Type:
integer/string

Purpose:
Identifies the user
```

Use actual discovered parameter names.

---

# 13. QUERY PARAMETERS

Identify all query parameters.

For example, if actually supported:

```text
?page=1
?limit=10
?search=test
?sort=name
```

Document:

* Parameter
* Type
* Required/optional
* Default
* Valid values
* Purpose

Do not invent query parameters.

---

# 14. REQUEST BODY ANALYSIS

For POST, PUT, PATCH, and other body-bearing requests, inspect the actual payload.

Document:

* Content-Type
* Fields
* Field types
* Required fields
* Optional fields
* Default values
* Validation rules
* Nested objects
* Arrays
* Nullable fields

Example:

```json
{
  "field": "actual value"
}
```

Use actual discovered structures.

Do not create fictional schemas.

---

# 15. RESPONSE ANALYSIS

For each important endpoint analyze actual responses.

Document:

* Status code
* Response headers
* Content-Type
* Response body
* JSON/object structure
* Arrays
* Nested objects
* IDs
* Required fields
* Error structure

Where practical, provide representative response structures without exposing sensitive information.

---

# 16. STATUS CODE ANALYSIS

Identify actual status codes returned by the application.

Document expected behavior for:

### Success

Examples:

```text
200 OK
201 Created
202 Accepted
204 No Content
```

### Client errors

Examples:

```text
400 Bad Request
401 Unauthorized
403 Forbidden
404 Not Found
409 Conflict
422 Unprocessable Entity
```

### Server errors

Examples:

```text
500 Internal Server Error
502 Bad Gateway
503 Service Unavailable
```

Do not assume these codes.

Record actual observed/expected behavior.

---

# 17. VALIDATION DISCOVERY

Identify API validation rules.

Investigate:

* Required fields
* Minimum length
* Maximum length
* Email validation
* Numeric validation
* Enum values
* Date validation
* Duplicate values
* Null handling
* Empty strings
* Invalid formats
* Invalid IDs
* Missing parameters

Document the actual behavior.

This information will be used in Phase 4 for negative testing.

---

# 18. ERROR RESPONSE ANALYSIS

For invalid requests, where safe and appropriate, determine:

* Error status code
* Error message
* Error structure
* Validation errors
* Error codes
* Field-level errors

Example structure:

```json
{
  "error": "Actual error information"
}
```

Do not invent error formats.

---

# 19. RESOURCE RELATIONSHIPS

Identify dependencies between APIs.

For example, if actually applicable:

```text
Authentication
      ↓
User
      ↓
Order
      ↓
Order Item
```

Document relationships such as:

```text
Create User
   ↓
User ID
   ↓
Get User
   ↓
Update User
   ↓
Delete User
```

These relationships will be important for Phase 5 data chaining.

---

# 20. CRUD MATRIX

Create a CRUD matrix based on actual discovered APIs.

Example:

| Resource        | Create | Read | Update | Partial Update | Delete |
| --------------- | ------ | ---- | ------ | -------------- | ------ |
| Actual Resource | POST   | GET  | PUT    | PATCH          | DELETE |

Use:

* ✓ = discovered/supported
* — = not discovered
* ? = unable to determine

Do not assume unsupported operations.

---

# 21. API DEPENDENCY MATRIX

Create a dependency analysis.

Example:

| API             | Depends On      | Required Data |
| --------------- | --------------- | ------------- |
| Get Resource    | Authentication  | Token         |
| Create Resource | Authentication  | Request body  |
| Update Resource | Create Resource | Resource ID   |
| Delete Resource | Create Resource | Resource ID   |

Use actual dependencies.

---

# 22. POSTMAN IMPACT ANALYSIS

Based on the discovered APIs, determine how the Postman project should eventually be organized.

Do NOT yet build the complete request suite.

Instead, document the proposed future structure.

For example:

```text
QA-Demo Project
│
├── 01 - Authentication
│
├── 02 - <Actual Resource>
│
├── 03 - <Actual Resource>
│
├── 04 - <Actual Resource>
│
├── 05 - Negative Tests
│
└── 06 - Cleanup
```

Replace placeholders with actual resources only after discovery.

---

# 23. UPDATE THE POSTMAN ENVIRONMENT

Phase 1 created:

```text
QA-Demo Environment
```

with foundational variables.

After discovering the actual API base URL, update the environment ONLY if the value is reliably discovered.

For example:

```text
baseUrl
apiUrl
authToken
refreshToken
```

If:

```text
apiUrl
```

can now be determined, configure it.

Do not create unnecessary variables.

Do not store real secrets.

Do not store temporary access tokens permanently.

---

# 24. DO NOT IMPLEMENT API REQUESTS YET

This is a strict requirement.

Do not populate the collection with actual API requests during Phase 2.

The collection remains the Phase 1 scaffold.

The endpoint inventory created during Phase 2 will be used in:

**Phase 3 — API Request Implementation.**

---

# 25. CREATE API DISCOVERY DOCUMENTATION

Create:

```text
docs/API_DISCOVERY.md
```

This should be the primary Phase 2 API discovery document.

It should include:

## Application

```text
https://qademo.com/
```

## API Base URL

Actual discovered value.

## Authentication

Actual mechanism.

## API Inventory

Complete endpoint table.

## HTTP Methods

Actual methods.

## Request Headers

Actual headers.

## Path Parameters

Actual parameters.

## Query Parameters

Actual parameters.

## Request Bodies

Actual structures.

## Response Structures

Actual structures.

## Status Codes

Actual behavior.

## Validation Rules

Actual findings.

## Error Handling

Actual findings.

## Resource Relationships

Actual dependencies.

## CRUD Matrix

Actual CRUD capabilities.

## API Dependency Matrix

Actual dependencies.

## Postman Implementation Plan

Recommended Phase 3 structure.

## Discovery Limitations

Clearly document anything that could not be verified.

---

# 26. CREATE API INVENTORY FILE

In addition to the documentation, create a machine-readable API inventory if practical.

Preferred location:

```text
docs/api-inventory.json
```

The structure should be clean and easy to consume later.

For example:

```json
{
  "application": "https://qademo.com/",
  "apiBaseUrl": "",
  "authentication": {
    "type": "",
    "loginEndpoint": ""
  },
  "endpoints": []
}
```

Populate it with actual discovered information.

Do not fabricate values.

If a value cannot be determined, use an appropriate empty/null representation and explain it in the documentation.

---

# 27. UPDATE PROJECT README

Update the root:

```text
README.md
```

to reflect that Phase 2 has been completed.

Include:

```text
Current Phase:
Phase 2 - API Discovery & Analysis
```

Add a reference to:

```text
docs/API_DISCOVERY.md
```

Do not remove important Phase 1 information.

---

# 28. CREATE PHASE 2 PROMPT RECORD

The exact prompt used for Phase 2 must be saved in:

```text
prompts/phase-2-api-discovery.md
```

This file must contain the complete Phase 2 instructions.

Do not save only a summary.

The project should maintain a history of prompts used for each phase.

---

# 29. UPDATE PROMPTS README

Update:

```text
prompts/README.md
```

to include Phase 2:

```text
prompts/
│
├── phase-1-project-scaffolding.md
├── phase-2-api-discovery.md
└── README.md
```

Explain briefly that:

* Phase 1 established the project structure.
* Phase 2 discovers and documents the actual API.
* Phase 3 will implement the discovered APIs in Postman.

---

# 30. SECURITY REQUIREMENTS

Never store or commit:

* Real passwords
* API keys
* Access tokens
* Refresh tokens
* Session cookies
* Private credentials
* Sensitive personal data

If credentials are required for discovery, use safe/test credentials where authorized.

Mask sensitive values in documentation.

Example:

```text
Authorization: Bearer <REDACTED>
```

rather than storing a real token.

---

# 31. DO NOT MODIFY UNRELATED FILES

Preserve existing Phase 1 work.

Before modifying anything:

1. Inspect the repository.
2. Identify existing files.
3. Read relevant Phase 1 artifacts.
4. Make only Phase 2 changes.

Do not delete or replace the Phase 1 collection/environment unless there is a clear reason.

---

# 32. DISCOVERY QUALITY STANDARD

The discovery must be comprehensive enough that a QA engineer can use:

```text
docs/API_DISCOVERY.md
```

to implement Phase 3 without having to rediscover the API from scratch.

The document should answer:

> What endpoint should I call?

> Which HTTP method should I use?

> What authentication is required?

> What headers are required?

> What parameters are required?

> What request body is required?

> What response should I expect?

> What status code should I validate?

> What data does the endpoint depend on?

> What data does the endpoint produce?

---

# 33. FINAL VALIDATION

Before finishing Phase 2, validate:

### Project

Confirm the Phase 1 structure still exists.

### Prompt

Confirm:

```text
prompts/phase-1-project-scaffolding.md
prompts/phase-2-api-discovery.md
```

exist.

### Documentation

Confirm:

```text
docs/API_DISCOVERY.md
```

exists and is complete.

### API Inventory

Confirm:

```text
docs/api-inventory.json
```

exists if machine-readable inventory was created.

### Postman Environment

Confirm that the environment remains valid.

If the API base URL was reliably discovered, confirm that `apiUrl` has been updated appropriately.

### Postman Collection

Confirm that:

```text
QA-Demo Project
```

still exists and remains a Phase 1 scaffold.

Do NOT populate it with the full API request suite yet.

---

# 34. FINAL REPORT

After completing Phase 2, provide a professional summary.

Use the following format:

## Phase

```text
Phase 2 - API Discovery & Analysis
```

## Application

```text
https://qademo.com/
```

## API Base URL

Report the actual discovered value or clearly state that it could not be determined.

## Authentication

Report the actual discovered authentication mechanism.

## APIs Discovered

Provide the total number of discovered endpoints.

## HTTP Methods

Summarize the methods discovered:

```text
GET:
POST:
PUT:
PATCH:
DELETE:
Other:
```

## Resources Discovered

List the actual API resources.

## Documentation Created

```text
docs/API_DISCOVERY.md
docs/api-inventory.json
```

as applicable.

## Postman Environment

Explain any environment changes made.

## Postman Collection

Confirm that:

```text
QA-Demo Project
```

remains the Phase 1 scaffold and that the complete request implementation has NOT yet been performed.

## Phase 3 Preparation

Summarize what Phase 3 can now implement based on the discovery.

## Discovery Limitations

Clearly list anything that could not be verified.

---

# 35. STRICT STOP CONDITION

STOP after Phase 2.

Do NOT proceed automatically to:

* Phase 3
* Postman request implementation
* Test automation
* Newman
* npm
* GitHub
* GitHub Actions
* CI/CD

The next task will be initiated separately using:

```text
prompts/phase-3-api-request-implementation.md
```

The goal of Phase 2 is:

```text
DISCOVER → ANALYZE → DOCUMENT → PREPARE
```

not:

```text
DISCOVER → BUILD EVERYTHING
```

---

# EXPECTED PHASE 2 RESULT

At the end of this phase, the project should conceptually be:

```text
QADemoAPITesting/
│
├── prompts/
│   ├── phase-1-project-scaffolding.md
│   ├── phase-2-api-discovery.md
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
│   └── api-inventory.json
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

The key Phase 2 deliverable is the **accurate API discovery documentation and inventory**.

Phase 3 will use this information to create the actual Postman API requests.
