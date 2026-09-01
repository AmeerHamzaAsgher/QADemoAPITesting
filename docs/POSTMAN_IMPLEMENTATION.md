# Postman Implementation — Phase 3

**Phase:** Phase 3 — Postman API Request Implementation
**Application:** https://qademo.com/
**Source of truth:** `docs/API_DISCOVERY.md` and `docs/api-inventory.json` (Phase 2)

> **Phase 4 correction (added 2026-08-31):** the Zod validation error shape referenced throughout this document as a top-level, unwrapped body was found to actually be wrapped in the standard error envelope (`{ success: false, error: { issues, name: "ZodError" } }`) during Phase 4 live re-verification. The response example on `POST - Login` in the collection has been corrected. See `docs/API_TEST_AUTOMATION.md`, "Phase 4 Corrections".
>
> **Phase 5 update (added 2026-08-31):** the requests documented below remain exactly as Phase 3 left them (unmodified, verified by hash) and are still independent, standalone requests suitable for ad-hoc testing. Phase 5 added a **separate, additive** `09 - Workflows` folder (renamed to `08 - Workflows` in Phase 5.1) that chains a subset of these same underlying endpoints into ordered, stateful workflows with dynamic data extraction (Create → Read → Update → Delete style lifecycles). This document describes the independent-request layer; see `docs/API_DATA_CHAINING.md` for the data-driven workflow layer built on top of it.

---

## Project

```text
QA-Demo Project
```
`postman/collections/QA-Demo Project.postman_collection.json` — Postman Collection v2.1.

## Environment

```text
QA-Demo Environment
```
`postman/environments/QA-Demo Environment.postman_environment.json`

## API Base URL

```text
https://qademo.com/api
```
Verified live in Phase 2 (same-origin, `/api` prefix). Referenced in every request as `{{apiUrl}}`.

---

## Collection Structure

```text
QA-Demo Project
│
├── 01 - Authentication          (4 requests)
│   ├── POST - Login
│   ├── POST - Logout
│   ├── POST - Refresh Token
│   └── GET  - Get Current User
│
├── 02 - Products                (5 requests)
│   ├── GET    - Get All Products
│   ├── GET    - Get Product By Slug
│   ├── POST   - Create Product (Admin)          [body undetermined]
│   ├── PATCH  - Update Product (Admin)           [body undetermined]
│   └── DELETE - Delete Product (Admin)
│
├── 03 - Cart                    (5 requests)
│   ├── GET    - Get Cart
│   ├── POST   - Add Item To Cart
│   ├── PATCH  - Update Cart Item Quantity
│   ├── DELETE - Remove Cart Item
│   └── DELETE - Clear Cart
│
├── 04 - Orders                  (3 requests)
│   ├── GET  - Get All Orders
│   ├── GET  - Get Order By ID
│   └── POST - Create Order                       [body undetermined]
│
├── 05 - Admin                   (5 requests)
│   ├── GET   - Get Admin Products
│   ├── PATCH - Update Product Stock
│   ├── GET   - Get Admin Orders
│   ├── PATCH - Update Order Status
│   └── GET   - Get Admin Stats
│
├── 06 - Images                  (2 requests)
│   ├── POST - Upload Image
│   └── GET  - Get Image
│
├── 07 - Negative Tests          (0 requests — reserved for Phase 4)
│
└── 08 - Cleanup                 (0 requests — reserved for future phases)
```

**Total: 24 requests**, replacing the Phase 1 generic placeholder folders (`01 - Authentication`, `02 - API Requests`, `03 - Negative Tests`, `04 - Cleanup`) with resource-based folders matching the Postman Implementation Plan proposed in `docs/API_DISCOVERY.md`.

---

## Request Naming Convention

```text
HTTP_METHOD - Action
```

Examples: `GET - Get All Products`, `POST - Add Item To Cart`, `PATCH - Update Order Status`, `DELETE - Clear Cart`. Names are unique within their folder and describe the action, not the raw endpoint path.

---

## Variable Strategy

All variables live in the `QA-Demo Environment` (no collection-level variables were added — every value discovered is environment-appropriate: URLs, tokens, and identifiers used against this one target application).

### Environment-specific configuration (Phase 1/2, unchanged)
| Variable | Value | Notes |
| --- | --- | --- |
| `baseUrl` | `https://qademo.com/` | Frontend application URL |
| `apiUrl` | `https://qademo.com/api` | Verified API base URL (Phase 2) |

### Authentication state (Phase 1, unchanged — still empty)
| Variable | Type | Notes |
| --- | --- | --- |
| `authToken` | secret | Populate manually after a successful `POST - Login`, or via Phase 5 automated chaining |
| `refreshToken` | secret | Reserved; refresh flow is cookie-based per Phase 2 findings, not header-based, so this is not currently referenced by any request |

### New — credentials for the login request (Phase 3)
| Variable | Type | Notes |
| --- | --- | --- |
| `username` | default, empty | No real credentials were available during discovery; fill in with safe/test credentials before running Login |
| `password` | secret, empty | Same as above |

### New — dynamic identifiers (Phase 3)
| Variable | Default | Used by | Notes |
| --- | --- | --- | --- |
| `sessionId` | a placeholder UUID | All Cart requests (`X-Session-ID` header) | Mirrors the frontend's client-generated, `localStorage`-persisted session id (Phase 2 finding). Not a secret — it only scopes an anonymous cart. |
| `productId` | `4` | Cart item requests, Products/Admin PATCH/DELETE requests | `4` is a real, verified product id (Bluetooth Speaker) confirmed live in Phase 2 |
| `productSlug` | `bluetooth-speaker` | `GET - Get Product By Slug` | Verified live in Phase 2; product detail lookup uses the slug, not the numeric id |
| `orderId` | empty | Order/Admin order requests | No order could be created during discovery (requires authentication); left empty until a real order id is available |
| `imageFolder` | `products` | `GET - Get Image` | Verified live folder segment |
| `imageFilename` | `1783414430049-e232a0f7.jpeg` | `GET - Get Image` | A real, verified filename referenced by a live product's `imageUrl` |

No variable was created without a discovered, real use in at least one request. No duplicate environment/collection variables exist.

---

## Authentication Strategy

Two independent mechanisms were implemented, exactly as discovered in Phase 2 — no assumption of a single, uniform scheme:

1. **Bearer token** (`Authorization: Bearer {{authToken}}`), applied via Postman's request-level `auth: bearer` on every endpoint Phase 2 confirmed requires authentication (Auth `logout`/`me`, all of Orders, all of Admin, Products create/update/delete, Images upload). `POST - Login` and `POST - Refresh Token` are marked `noauth` since they precede/bypass this token.
2. **X-Session-ID header** (`{{sessionId}}`), applied as a manual header on all five Cart requests — verified live to be required independently of login state (guest carts work without a Bearer token; omitting the header returns `400 VALIDATION_ERROR`).

No token-acquisition or auto-refresh scripting was added — per Phase 3 scope, `authToken` must currently be populated manually after running `POST - Login` with valid credentials. Automated chaining is explicitly deferred to Phase 5.

---

## API Implementation Matrix

| # | Resource | Method | Endpoint | Postman Request | Status |
| - | --- | --- | --- | --- | --- |
| 1 | Auth | POST | `/auth/login` | 01 - Authentication / POST - Login | Implemented |
| 2 | Auth | POST | `/auth/logout` | 01 - Authentication / POST - Logout | Implemented |
| 3 | Auth | POST | `/auth/refresh` | 01 - Authentication / POST - Refresh Token | Implemented |
| 4 | Auth | GET | `/auth/me` | 01 - Authentication / GET - Get Current User | Implemented |
| 5 | Products | GET | `/products` | 02 - Products / GET - Get All Products | Implemented |
| 6 | Products | GET | `/products/{slug}` | 02 - Products / GET - Get Product By Slug | Implemented |
| 7 | Products | POST | `/products` | 02 - Products / POST - Create Product (Admin) | Partially implemented (body undetermined) |
| 8 | Products | PATCH | `/products/{id}` | 02 - Products / PATCH - Update Product (Admin) | Partially implemented (body + id-type undetermined) |
| 9 | Products | DELETE | `/products/{id}` | 02 - Products / DELETE - Delete Product (Admin) | Implemented (id-type unconfirmed — see note below) |
| 10 | Cart | GET | `/cart` | 03 - Cart / GET - Get Cart | Implemented |
| 11 | Cart | POST | `/cart/items` | 03 - Cart / POST - Add Item To Cart | Implemented |
| 12 | Cart | PATCH | `/cart/items/{productId}` | 03 - Cart / PATCH - Update Cart Item Quantity | Implemented |
| 13 | Cart | DELETE | `/cart/items/{productId}` | 03 - Cart / DELETE - Remove Cart Item | Implemented |
| 14 | Cart | DELETE | `/cart` | 03 - Cart / DELETE - Clear Cart | Implemented |
| 15 | Orders | GET | `/orders` | 04 - Orders / GET - Get All Orders | Implemented |
| 16 | Orders | GET | `/orders/{id}` | 04 - Orders / GET - Get Order By ID | Implemented |
| 17 | Orders | POST | `/orders` | 04 - Orders / POST - Create Order | Partially implemented (body undetermined) |
| 18 | Admin | GET | `/admin/products` | 05 - Admin / GET - Get Admin Products | Implemented |
| 19 | Admin | PATCH | `/admin/products/{id}/stock` | 05 - Admin / PATCH - Update Product Stock | Implemented |
| 20 | Admin | GET | `/admin/orders` | 05 - Admin / GET - Get Admin Orders | Implemented |
| 21 | Admin | PATCH | `/admin/orders/{id}/status` | 05 - Admin / PATCH - Update Order Status | Implemented (valid `status` values undetermined) |
| 22 | Admin | GET | `/admin/stats` | 05 - Admin / GET - Get Admin Stats | Implemented |
| 23 | Images | POST | `/images` | 06 - Images / POST - Upload Image | Implemented |
| 24 | Images | GET | `/images/{folder}/{filename}` | 06 - Images / GET - Get Image | Implemented |

**24 / 24 discovered endpoints have a corresponding Postman request.** "Partially implemented" (3 of the 24) means the method, URL, and authentication are confirmed and configured, but the request body could not be populated because Phase 2 could not recover its schema — per the Phase 3 no-guessing rule, no fields were invented for these bodies; each has a placeholder comment explaining why and pointing back to this document.

---

## Unresolved / Partially Implemented APIs

### `POST /products` — Create Product (Admin)
```text
Endpoint: POST /products
Reason: Method, URL, and auth requirement (401 without token) confirmed live. Request body could
        not be confirmed - the frontend's createProduct(e) forwards its argument verbatim, so field
        names are not visible in the minified bundle, and the endpoint was not exercised live to
        avoid creating uncontrolled catalog data without a confirmed schema.
Missing information: Exact JSON field names/types for a new product (e.g. name, price, stock,
        description, slug, isActive - names only inferred by analogy to the GET /products response
        shape, not confirmed as the accepted input shape).
Recommended next step: With valid admin credentials, submit a minimal/experimental payload in a
        disposable/test catalog context, observe the response and any 400 validation errors (this API
        uses Zod, which reports missing/invalid fields explicitly), and update the request body
        accordingly.
```

### `PATCH /products/{id}` — Update Product (Admin)
```text
Endpoint: PATCH /products/{id}
Reason: Same as above for the body. Additionally, whether {id} expects the numeric id or the slug was
        not confirmed (GET /products/{slug} is confirmed to require the slug; this endpoint's
        identifier convention was not exercised live).
Missing information: Request body schema; identifier type for the path parameter.
Recommended next step: Same approach as Create Product, plus a targeted live test of both id and slug
        in the path to confirm which is accepted before relying on this request.
```

### `POST /orders` — Create Order
```text
Endpoint: POST /orders
Reason: Method, URL, and auth requirement (401 without token) confirmed live. Body could not be
        confirmed - createOrder(e) forwards its argument verbatim; presumed (not confirmed) to derive
        from the current cart's contents at checkout.
Missing information: Exact JSON field names/types for order creation (e.g. shipping details, payment
        placeholder fields, whether cart contents are read server-side from the session or must be
        included explicitly in the body).
Recommended next step: With valid credentials and a populated cart (via the already-implemented Cart
        requests), submit an experimental payload and observe the response/validation errors to
        determine the real schema.
```

No endpoint from `docs/api-inventory.json` was skipped entirely — every one of the 24 discovered endpoints has a request in the collection, even where its body is unresolved.

---

## Validation

* **Collection name:** `QA-Demo Project` — unchanged (matches Phase 1/2).
* **Schema:** Postman Collection v2.1 (`https://schema.getpostman.com/json/collection/v2.1.0/collection.json`); the file parses as valid JSON.
* **Environment name:** `QA-Demo Environment` — unchanged.
* **API coverage:** 24 discovered / 24 implemented / 0 not implemented (3 marked partially implemented — body undetermined, documented above).
* **No invented endpoints:** every request's method + URL traces directly to an entry in `docs/api-inventory.json`; no additional, speculative endpoints were added.
* **No secrets exposed:** `authToken`, `refreshToken`, and `password` remain empty, `secret`-typed environment variables; `username` is empty; `sessionId`/`productId`/`productSlug`/`imageFolder`/`imageFilename` hold only non-sensitive, publicly-observable values from live discovery requests.
* **No test assertions added:** no `pm.test(...)` scripts exist anywhere in the collection (grep-verified). A small number of sanitized response *examples* (not scripts) were attached to already-verified requests to document observed behavior — these are static reference data, not executable assertions.
* **Pre-request scripts:** none were added — the `X-Session-ID` value is supplied via a static environment variable, which was sufficient; no dynamic generation was required for Phase 3.

---

## Phase 4 Preparation

Phase 4 (API Test Automation) can now add, per request:

* Status-code assertions (the expected/observed codes are already documented in each request's description and, where verified, in its response examples).
* Response-shape assertions distinguishing the two error envelopes discovered in Phase 2 (`{ success, error: { code, message } }` vs. the raw `{ issues, name: "ZodError" }` shape).
* Header validation (e.g. `Content-Type: application/json`).
* JSON/schema validation against the documented response shapes in `docs/API_DISCOVERY.md`.
* Business-rule validation (e.g. cart `totalAmount` = sum of item price × quantity).
* Negative tests under `07 - Negative Tests` (missing/invalid `X-Session-ID`, invalid login credentials, missing auth on protected endpoints, unknown product slug/id) — the specific scenarios were already exercised live in Phase 2 and are captured as response examples.
* Resolution of the three partially implemented endpoints (`POST /products`, `PATCH /products/{id}`, `POST /orders`) once valid admin/user credentials are available to determine their real request/response schemas.

Phase 5 will subsequently add dynamic data chaining (e.g. auto-populating `authToken` from the Login response, `productId`/`orderId` from create responses) — none of that was implemented in Phase 3, per scope.
