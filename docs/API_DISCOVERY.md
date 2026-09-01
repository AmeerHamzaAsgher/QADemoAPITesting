# API Discovery & Analysis

> **Phase 3 implementation status (added 2026-08-31, does not alter the Phase 2 findings below):**
> All 24 endpoints documented here have a corresponding Postman request in the `QA-Demo Project` collection (21 fully implemented, 3 with method/URL/auth implemented but an undetermined request body). See `docs/POSTMAN_IMPLEMENTATION.md` for the full implementation matrix and unresolved-body details.
>
> **Phase 4 correction (added 2026-08-31):** every mention below of the Zod schema-validation error shape as a **top-level, unwrapped** body (`{ issues: [...], name: "ZodError" }` with no `success` key) is **incorrect as of Phase 4 re-verification**. Live re-testing during Phase 4 shows this shape is actually **wrapped inside the standard error envelope**: `{ "success": false, "error": { "issues": [...], "name": "ZodError" } }`. This may reflect a backend change made after Phase 2 discovery (this is a live, actively-updated demo application), or a Phase 2 transcription error — the cause was not determined. The Postman collection's test scripts and response examples have been corrected to match the current, live-verified shape. Treat every unwrapped-shape example in this document as historical/superseded; do not use it to build new assertions. See `docs/API_TEST_AUTOMATION.md`, "Phase 4 Corrections".

**Phase:** Phase 2 — API Discovery & Analysis
**Application:** https://qademo.com/
**Discovery date:** 2026-08-31
**Discovery method:** Static analysis of the deployed frontend JavaScript bundle (`/assets/index-CXdXU_RJ.js`, a Vite/React SPA build) combined with live, read-only HTTP requests against the discovered API using `curl`. No browser DevTools network capture was available in this environment, so the frontend bundle's API client class was reverse-engineered from the minified source and every call was independently verified live.

---

## Application

```text
https://qademo.com/
```

A React single-page application ("QA Demo - Automated E-Commerce Testing Platform") served from Cloudflare. The page shell (`index.html`) is nearly empty (`<div id="root">`) and loads three JS bundles:

```text
/assets/index-CXdXU_RJ.js   (application code, incl. API client)
/assets/vendor-B0zTxNnR.js  (React/vendor libraries)
/assets/query-CSEvxQVS.js   (React Query)
```

Frontend routes identified (client-side router): `/`, `/login`, `/catalog`, `/products/:slug`, `/cart`, `/orders`, `/orders/:id`, `/admin`, `/admin/products`, `/admin/orders`, `/admin/stats`.

---

## API Base URL

```text
https://qademo.com/api
```

The frontend API client constant is `Bi = "/api"` and every request is issued as `fetch(`${Bi}${endpoint}`, ...)`. Because no protocol/host is prepended, the API is served **same-origin**, on the same domain/port as the web app, under the `/api` path prefix. This was confirmed live — e.g. `GET https://qademo.com/api/products` returns `200 OK` with a JSON payload.

No separate API host, subdomain (e.g. `api.qademo.com`), or API version segment (e.g. `/api/v1`) was found anywhere in the bundles.

---

## Authentication

**Mechanism:** Bearer JWT-style access token, combined with a same-origin credentialed cookie (for refresh) and a client-generated session identifier header (for guest/cart identity).

* **Access token:** Obtained from `POST /api/auth/login`, held only in memory in the frontend store (`this.accessToken` on the API client singleton — not persisted to `localStorage`), and sent on subsequent requests as:
  ```text
  Authorization: Bearer <accessToken>
  ```
* **Refresh:** `POST /api/auth/refresh` is called with **no request body**. Every request (including this one) is made with `fetch(..., { credentials: "include" })`, so the refresh mechanism relies on a same-origin cookie set by the server on login (not inspectable via static analysis; no `Set-Cookie` was observed on a failed login attempt, since login only sets cookies on success and valid credentials were not available — see Discovery Limitations).
* **Session ID:** A `X-Session-ID` header carrying a client-generated `crypto.randomUUID()` value (persisted in `localStorage` under key `session_id`) is sent on **every** API request. It is required (not optional) — omitting it on endpoints like `GET /cart` returns `400 VALIDATION_ERROR: Missing X-Session-ID header`. This is how the guest/anonymous cart is scoped per browser, independent of login state.
* **Content-Type:** `application/json` is sent by default on all JSON requests; `multipart/form-data` (via `FormData`, browser-set boundary) for image upload.

### Login endpoint

```text
Method:   POST
Endpoint: /api/auth/login
Request:  { "username": "<string>", "password": "<string>" }
Response (success, inferred from code, not exercised live — no valid credentials available):
          { "success": true, "data": { "accessToken": "<string>", "user": { ... } } }
Response (invalid credentials, verified live):
          HTTP 401
          { "success": false, "error": { "code": "INVALID_CREDENTIALS", "message": "Invalid username or password" } }
Response (missing fields, verified live):
          HTTP 400
          { "issues": [ { "code": "invalid_type", "expected": "string", "received": "undefined", "path": ["username"], "message": "Required" }, ... ], "name": "ZodError" }
Token/cookie: accessToken returned in the JSON body and stored in memory by the client; refresh token behavior inferred to be an httpOnly cookie (not directly observable).
```

### Token location

| Data | Location |
| --- | --- |
| Access token | JSON response body (`data.accessToken`), held in-memory by the SPA (not localStorage/sessionStorage) |
| Refresh token | Presumed cookie, set via `Set-Cookie` on successful login (`credentials: "include"` used on every call); could not be confirmed without valid credentials |
| Session ID (cart identity) | `localStorage["session_id"]`, generated client-side, **not** issued by the server |

### Token usage

Confirmed via source: `Authorization: Bearer <accessToken>` header on all authenticated calls. Confirmed live: omitting it on `GET /auth/me`, `GET /orders`, `GET /admin/*`, `POST /orders`, `POST /products` returns:
```text
HTTP 401
{ "success": false, "error": { "code": "UNAUTHORIZED", "message": "Missing authentication" } }
```

---

## Authentication Flow

```text
Login (POST /auth/login)
  ↓
accessToken returned in JSON body (+ presumed refresh cookie set)
  ↓
accessToken held in memory by the SPA
  ↓
Authorization: Bearer <accessToken> attached to subsequent requests
  ↓
On 401 / token expiry (inferred, not exercised): POST /auth/refresh (cookie-based, no body) → new accessToken
```

The `X-Session-ID` header operates independently of this flow and is present on every request (authenticated or not), tying the guest cart to the browser regardless of login state.

---

## API Endpoint Inventory

All endpoints are relative to the API base URL `https://qademo.com/api`. "Verified live" = exercised with `curl` during this discovery session. "From source only" = present in the frontend API client but not exercised live (would require valid credentials or would mutate protected/admin data).

| # | Resource | Method | Endpoint | Purpose | Auth | Verified | Expected Status |
|---|---|---|---|---|---|---|---|
| 1 | Auth | POST | `/auth/login` | Log in with username/password | No | Live (error paths) | 401 invalid creds, 400 validation, 200 on success (inferred) |
| 2 | Auth | POST | `/auth/logout` | Invalidate current session/token | Yes (Bearer) | From source only | 200 (inferred) |
| 3 | Auth | POST | `/auth/refresh` | Exchange refresh cookie for new access token | Cookie (refresh) | From source only | 200 (inferred) |
| 4 | Auth | GET | `/auth/me` | Get current authenticated user | Yes (Bearer) | Live | 401 (no auth, verified), 200 (inferred) |
| 5 | Products | GET | `/products` | List all products | No | Live | 200 |
| 6 | Products | GET | `/products/{slug}` | Get a single product by slug | No | Live | 200, 404 |
| 7 | Products | POST | `/products` | Create a product (admin) | Yes (Bearer, admin) | Live (auth check only) | 401 (no auth, verified), 201/200 (inferred) |
| 8 | Products | PATCH | `/products/{id\|slug}` | Update a product (admin) | Yes (Bearer, admin) | From source only | 200 (inferred) |
| 9 | Products | DELETE | `/products/{id\|slug}` | Delete a product (admin) | Yes (Bearer, admin) | From source only | 200/204 (inferred) |
| 10 | Cart | GET | `/cart` | Get current session's cart | X-Session-ID (required) | Live | 200, 400 (missing header) |
| 11 | Cart | POST | `/cart/items` | Add item to cart | X-Session-ID (required) | Live | 200, 404 (bad productId) |
| 12 | Cart | PATCH | `/cart/items/{productId}` | Update item quantity | X-Session-ID (required) | Live | 200 |
| 13 | Cart | DELETE | `/cart/items/{productId}` | Remove item from cart | X-Session-ID (required) | Live | 200 |
| 14 | Cart | DELETE | `/cart` | Clear entire cart | X-Session-ID (required) | Live | 200 |
| 15 | Orders | GET | `/orders` | List current user's orders | Yes (Bearer) | Live (auth check only) | 401 (no auth, verified), 200 (inferred) |
| 16 | Orders | GET | `/orders/{id}` | Get a single order | Yes (Bearer) | From source only | 200/404 (inferred) |
| 17 | Orders | POST | `/orders` | Create an order (checkout) | Yes (Bearer) | Live (auth check only) | 401 (no auth, verified), 201/200 (inferred) |
| 18 | Admin | GET | `/admin/products` | List products (admin view) | Yes (Bearer, admin) | Live (auth check only) | 401 (no auth, verified), 200 (inferred) |
| 19 | Admin | PATCH | `/admin/products/{id}/stock` | Update product stock | Yes (Bearer, admin) | From source only | 200 (inferred) |
| 20 | Admin | GET | `/admin/orders` | List all orders (admin) | Yes (Bearer, admin) | From source only | 200 (inferred) |
| 21 | Admin | PATCH | `/admin/orders/{id}/status` | Update order status | Yes (Bearer, admin) | From source only | 200 (inferred) |
| 22 | Admin | GET | `/admin/stats` | Get dashboard statistics | Yes (Bearer, admin) | Live (auth check only) | 401 (no auth, verified), 200 (inferred) |
| 23 | Images | POST | `/images` | Upload an image (multipart: `file`, `folder`) | Yes (Bearer, admin, presumed) | From source only | 200 (inferred) |
| 24 | Images | GET | `/images/{folder}/{filename}` | Serve an uploaded image (static asset) | No | Live | 200 |

**Total discovered: 24 endpoints across 5 resources (Auth, Products, Cart, Orders, Admin) plus a static image-serving path.**

---

## HTTP Methods

```text
GET     → discovered (products, cart, orders, admin, auth/me, images)
POST    → discovered (login, logout, refresh, cart/items, orders, products, images)
PUT     → not discovered
PATCH   → discovered (cart/items/{id}, products/{id}, admin/products/{id}/stock, admin/orders/{id}/status)
DELETE  → discovered (cart/items/{id}, cart, products/{id})
HEAD    → not discovered (not exercised; standard for static assets but not confirmed)
OPTIONS → attempted live against /api/products; returned 404 Not Found — no explicit OPTIONS/CORS-preflight handler was observed (consistent with same-origin, non-preflighted requests)
```

---

## Request Headers

Observed/documented per-request headers, from source and live verification:

| Header | Where used | Notes |
| --- | --- | --- |
| `Content-Type: application/json` | All JSON requests | Default, always set by the client |
| `Content-Type: multipart/form-data` | `POST /images` | Set automatically by the browser `FormData` object (not manually specified) |
| `Authorization: Bearer <accessToken>` | Any request while logged in | Omitted entirely when `accessToken` is null (not sent as empty) |
| `X-Session-ID: <uuid>` | Every request | Required on cart endpoints (400 if missing); presumably accepted-but-unused on others |
| `Accept` | Not explicitly set by the client | Not verified as required |

Response headers of note: `Content-Type: application/json`, standard Cloudflare/security headers (`X-Content-Type-Options`, `X-Frame-Options`, `Referrer-Policy`, HSTS, etc.). No custom rate-limit headers were observed.

---

## Path Parameters

| Parameter | Used in | Type | Purpose |
| --- | --- | --- | --- |
| `slug` | `GET /products/{slug}` | string | Identifies a product by its URL slug (e.g. `bluetooth-speaker`). **Verified live**: numeric product `id` values (e.g. `4`, `18`) return `404 NOT_FOUND` — the path segment must be the slug, not the numeric id, even though the frontend variable is generically named. |
| `id` / `productId` | `PATCH/DELETE /products/{id}`, `PATCH/DELETE /cart/items/{productId}` | integer | Identifies a product for update/delete/cart operations. Cart operations were verified live using the numeric `id` (e.g. `4`), confirming cart endpoints use the numeric id while the standalone product-detail endpoint uses the slug. |
| `id` | `GET /orders/{id}` | integer (presumed) | Identifies an order. Not verified live (requires authentication). |
| `id` | `PATCH /admin/products/{id}/stock` | integer | Identifies a product for stock update. Not verified live (requires admin auth). |
| `id` | `PATCH /admin/orders/{id}/status` | integer | Identifies an order for status update. Not verified live (requires admin auth). |
| `folder`, `filename` | `GET /images/{folder}/{filename}` | string | Static image path, e.g. `/images/products/1783414430049-e232a0f7.jpeg`. Verified live. |

---

## Query Parameters

No query parameters (pagination, search, sort, filter) were found in the frontend API client source for any endpoint. `GET /products` returns the full product list in a single response with no `?page=`/`?limit=` support observed in the client code.

```text
None discovered.
```

If pagination/filtering exist server-side but are unused by this frontend, they were not detectable via this discovery method.

---

## Request Bodies

### `POST /auth/login`
```json
{
  "username": "string (required)",
  "password": "string (required)"
}
```
Required fields confirmed via live Zod validation error (`400`, `ZodError`, both fields flagged as required when omitted).

### `POST /cart/items`
```json
{
  "productId": "number (required)",
  "quantity": "number (optional, default 1 client-side)"
}
```
Verified live: valid `productId` → `200`; nonexistent `productId` → `404 NOT_FOUND ("Product not found")`.

### `PATCH /cart/items/{productId}`
```json
{
  "quantity": "number (required)"
}
```
Verified live.

### `POST /orders`
```json
{ /* order payload — shape not determined; endpoint requires authentication and was not exercised beyond the auth check */ }
```
Not determined — see Discovery Limitations.

### `POST /products` (admin, create)
Body shape not determined from source (`createProduct(e)` passes an opaque object straight through). Auth-gated; not exercised beyond confirming `401` without a token.

### `PATCH /products/{id}` (admin, update)
Body shape not determined; passes an opaque object through, same as create.

### `PATCH /admin/products/{id}/stock`
```json
{
  "stock": "number (required)"
}
```
Determined from source only (`updateProductStock(e,n)` builds `{ stock: n }`).

### `PATCH /admin/orders/{id}/status`
```json
{
  "status": "string (required)"
}
```
Determined from source only (`updateOrderStatus(e,n)` builds `{ status: n }`); the set of valid status values was not determined.

### `POST /images`
`multipart/form-data` with fields:
```text
file:   binary (required)
folder: string (optional, default "products")
```
Determined from source only.

---

## Response Structures

The API uses a consistent success/error envelope for almost all endpoints, **except Zod validation failures**, which return a differently-shaped body. This inconsistency is worth calling out for Phase 4 negative testing.

### Success envelope
```json
{
  "success": true,
  "data": { "...endpoint-specific..." }
}
```

### Business-logic error envelope
```json
{
  "success": false,
  "error": {
    "code": "STRING_CODE",
    "message": "Human-readable message"
  }
}
```
Observed codes: `NOT_FOUND`, `UNAUTHORIZED`, `INVALID_CREDENTIALS`, `VALIDATION_ERROR`.

### Schema-validation error envelope (does NOT follow the above shape)
```json
{
  "issues": [
    {
      "code": "invalid_type",
      "expected": "string",
      "received": "undefined",
      "path": ["username"],
      "message": "Required"
    }
  ],
  "name": "ZodError"
}
```
Observed live on `POST /auth/login` with an empty body. No top-level `success` key is present here — a client must handle two distinct error shapes.

### `GET /products` — verified response
```json
{
  "success": true,
  "data": [
    {
      "id": 4,
      "name": "Bluetooth Speaker",
      "slug": "bluetooth-speaker",
      "description": "Portable Bluetooth speaker with 360-degree sound and 12-hour battery life.",
      "price": 159.99,
      "stock": 1915,
      "imageKey": "products/1783414430049-e232a0f7.jpeg",
      "imageUrl": "/api/images/products/1783414430049-e232a0f7.jpeg",
      "isActive": true,
      "createdAt": "2025-12-29 09:11:50",
      "updatedAt": "2026-08-31 11:39:18"
    }
  ]
}
```
Note: `imageKey`/`imageUrl` may be `null` for products with no uploaded image.

### `GET /products/{slug}` — verified response
Same object shape as a single item above; `404` with the standard error envelope for an unknown slug.

### `GET /cart` — verified response
```json
{
  "success": true,
  "data": {
    "items": [
      {
        "productId": 4,
        "quantity": 5,
        "product": { "...full product object as above..." }
      }
    ],
    "totalItems": 5,
    "totalAmount": 799.95
  }
}
```

### `POST /cart/items` — verified response
```json
{ "success": true, "data": { "productId": 4, "quantity": 1, "totalItems": 1 } }
```

### `PATCH /cart/items/{productId}` — verified response
```json
{ "success": true, "data": { "productId": 4, "quantity": 5, "totalItems": 5 } }
```

### `DELETE /cart/items/{productId}` — verified response
```json
{ "success": true, "data": { "productId": 4, "removed": true, "totalItems": 0 } }
```

### `DELETE /cart` — verified response
```json
{ "success": true, "data": { "cleared": true, "totalItems": 0 } }
```

All other response bodies (`auth/me`, `orders`, `admin/*`, `login` success case) were **not observed live** — no valid credentials were available in this environment — and are therefore not documented here. They should be captured during Phase 3 once test/admin credentials are provisioned.

---

## Status Code Analysis

### Confirmed live
```text
200 OK                → successful GET/POST/PATCH/DELETE on products, cart, images
400 Bad Request        → Zod validation error (missing login fields); missing X-Session-ID header
401 Unauthorized       → missing Authorization header on protected routes; invalid login credentials
404 Not Found          → unknown product slug/id; unknown cart productId; unmapped route (OPTIONS)
```

### Expected but not confirmed (no valid credentials / would mutate protected data)
```text
200/201 → successful login, order creation, product create/update, admin actions
204     → not observed anywhere; deletes observed so far return 200 with a body, not 204
403     → role-based access (non-admin user hitting /admin/*) not distinguishable from 401 without a valid non-admin token
409     → not observed
422     → not observed (this API appears to use 400 + ZodError for validation instead)
5xx     → not observed; no server errors were triggered during discovery
```

---

## Validation Discovery

* `username` and `password` are both required strings on login (Zod `invalid_type`/`Required` errors when absent). No length/format rules were observable without triggering more requests than appropriate for a discovery pass.
* `productId` on cart operations must reference an existing, presumably active product — invalid IDs return `404 NOT_FOUND`, not a `400` validation error, i.e. existence is checked rather than just type.
* The `X-Session-ID` header is validated for presence (`400 VALIDATION_ERROR`) on `/cart` at minimum; not re-tested against every endpoint to avoid excessive probing.
* Deeper validation rules (min/max length, numeric bounds on `price`/`stock`/`quantity`, enum values for order `status`) were **not** determined — probing them meaningfully requires an authenticated session or admin token that was not available. Recommended for Phase 3/4 once credentials exist.

---

## Error Handling

Two distinct error shapes exist (see Response Structures above):
1. Business-logic errors: `{ success: false, error: { code, message } }`, with HTTP status matching the failure (401, 404, 400 for the missing-header case).
2. Schema-validation errors: `{ issues: [...], name: "ZodError" }`, HTTP 400, with **no** `success` key.

Any Postman/Newman test suite (Phase 4) must branch on response shape, not assume a single consistent error envelope.

---

## Resource Relationships

```text
Authentication (accessToken)
      ↓
User (GET /auth/me)
      ↓
Orders (GET/POST /orders — scoped to the authenticated user)

X-Session-ID (client-generated, independent of auth)
      ↓
Cart (GET/POST/PATCH/DELETE /cart, /cart/items/{productId} — scoped to the session, works while logged out)

Products (public, no auth)
      ↓
Cart items reference Product by numeric id
      ↓
Orders (inferred to be created from cart contents at checkout — POST /orders; exact payload not confirmed)

Admin (accessToken with elevated/admin privilege, not distinguished from regular auth in this discovery)
      ↓
Admin Products (GET /admin/products, PATCH stock)
Admin Orders (GET /admin/orders, PATCH status)
Admin Stats (GET /admin/stats)
Image upload (POST /images) — referenced by Product.imageKey/imageUrl
```

Notable: the cart is **not** tied to authentication — it is scoped purely by the client-generated `X-Session-ID`, meaning a guest can build a cart before logging in. Whether/how a guest cart is merged into a user's account on login was not determined.

---

## CRUD Matrix

| Resource | Create | Read | Update | Partial Update | Delete |
| --- | --- | --- | --- | --- | --- |
| Products | POST `/products` (admin) | GET `/products`, GET `/products/{slug}` | — | PATCH `/products/{id}` (admin) | DELETE `/products/{id}` (admin) |
| Cart | POST `/cart/items` | GET `/cart` | — | PATCH `/cart/items/{productId}` | DELETE `/cart/items/{productId}`, DELETE `/cart` (clear all) |
| Orders | POST `/orders` | GET `/orders`, GET `/orders/{id}` | — | — | — |
| Admin: Products | — | GET `/admin/products` | — | PATCH `/admin/products/{id}/stock` | — |
| Admin: Orders | — | GET `/admin/orders` | — | PATCH `/admin/orders/{id}/status` | — |
| Admin: Stats | — | GET `/admin/stats` | — | — | — |
| Images | POST `/images` (upload) | GET `/images/{folder}/{filename}` | — | — | — |
| Auth / User | POST `/auth/login` | GET `/auth/me` | — | — | POST `/auth/logout` (session end, not a data delete) |

`✓/—` legend not needed here since the actual method+endpoint is shown directly; a blank cell means no such operation was discovered for that resource.

---

## API Dependency Matrix

| API | Depends On | Required Data |
| --- | --- | --- |
| `GET /auth/me` | Login | Access token |
| `POST /auth/logout` | Login | Access token |
| `POST /auth/refresh` | Login | Refresh cookie (set on login) |
| `GET /products/{slug}` | Product catalog existing | Product slug (from `GET /products`) |
| `POST /cart/items` | Product existing | `X-Session-ID`, numeric `productId` (from `GET /products`) |
| `PATCH /cart/items/{productId}` | Item already in cart | `X-Session-ID`, `productId` |
| `DELETE /cart/items/{productId}` | Item already in cart | `X-Session-ID`, `productId` |
| `POST /orders` | Login, (presumed) non-empty cart | Access token, order payload (undetermined) |
| `GET /orders/{id}` | Order created | Access token, order `id` |
| `PATCH /admin/products/{id}/stock` | Login (admin), product existing | Access token, product `id` |
| `PATCH /admin/orders/{id}/status` | Login (admin), order existing | Access token, order `id` |
| `POST /images` | Login (admin, presumed) | Access token, multipart file |
| Product `imageUrl` field | `POST /images` having been called previously | — |

---

## Postman Implementation Plan

Recommended Phase 3 collection structure (not implemented in this phase):

```text
QA-Demo Project
│
├── 01 - Authentication
│   ├── POST Login
│   ├── POST Logout
│   ├── POST Refresh Token
│   └── GET Current User (/auth/me)
│
├── 02 - Products
│   ├── GET List Products
│   ├── GET Product by Slug
│   ├── POST Create Product (admin)
│   ├── PATCH Update Product (admin)
│   └── DELETE Delete Product (admin)
│
├── 03 - Cart
│   ├── GET Cart
│   ├── POST Add Item
│   ├── PATCH Update Item Quantity
│   ├── DELETE Remove Item
│   └── DELETE Clear Cart
│
├── 04 - Orders
│   ├── GET List Orders
│   ├── GET Order by Id
│   └── POST Create Order
│
├── 05 - Admin
│   ├── GET Admin Products
│   ├── PATCH Update Product Stock
│   ├── GET Admin Orders
│   ├── PATCH Update Order Status
│   └── GET Admin Stats
│
├── 06 - Images
│   ├── POST Upload Image
│   └── GET Serve Image
│
├── 07 - Negative Tests
│   (missing/invalid X-Session-ID, invalid credentials, missing auth, unknown ids/slugs, malformed bodies)
│
└── 08 - Cleanup
    (delete created test products/orders where possible)
```

This expands and replaces the Phase 1 placeholder folders (`01 - Authentication`, `02 - API Requests`, `03 - Negative Tests`, `04 - Cleanup`) once Phase 3 begins. **No requests have been created yet** — the Phase 1 collection scaffold is untouched.

---

## Discovery Limitations

The following could not be verified in this discovery pass and should be resolved at the start of Phase 3, ideally with valid test credentials:

1. **No valid login credentials were available.** The full login success response (`data.user` shape, exact accessToken format/expiry), the `/auth/refresh` cookie mechanics, `/auth/me`, `/orders`, `/admin/*` success responses, and `/products`/`orders` create/update bodies and responses could not be observed live. All such items above are marked "from source only" or "inferred."
2. **Request/response bodies for `POST /orders`, `POST /products`, and `PATCH /products/{id}`** are passed through as opaque objects in the minified client code — their exact field-level schema was not recoverable from static analysis and was not probed live to avoid submitting malformed/junk data against endpoints whose write behavior and side effects (e.g. creating a real order, or a real product visible to other users) were unknown.
3. **Admin vs. regular-user role distinction** could not be confirmed — whether `/admin/*` returns `401` or `403` for a logged-in non-admin user is undetermined (only the "no token" `401` case was tested).
4. **Refresh-token cookie details** (name, `httpOnly`/`Secure`/`SameSite` attributes, expiry) were not observable since no successful login occurred.
5. **Pagination/filtering/sorting on `GET /products`** were not found in the frontend client and were not assumed to exist; if the backend supports undocumented query parameters, this discovery would not have found them.
6. **Rate limiting, `HEAD` support, and CORS preflight behavior** were only spot-checked (`OPTIONS /api/products` → 404) and not exhaustively tested.
7. **This discovery used static bundle analysis + direct `curl` requests, not a live browser network capture.** If the SPA makes additional runtime-conditional calls not present in the reachable code paths of the bundle (e.g. behind admin-only UI), they may not have been captured.

Phase 3 should begin by obtaining valid standard-user and admin-user test credentials (safely, per project security requirements) and re-verifying every item marked "from source only" or "inferred" above before building the full Postman request suite.
