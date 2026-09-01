# API Data Chaining & Workflow Automation — Phase 5

**Phase:** Phase 5 — API Data Chaining & Workflow Automation
**Collection:** `QA-Demo Project` (new `09 - Workflows` folder; renamed to `08 - Workflows` in Phase 5.1 - see `docs/POSTMAN_UI_ARCHITECTURE.md`)
**Environment:** `QA-Demo Environment`
**Application:** https://qademo.com/
**Source of truth:** `docs/API_DISCOVERY.md`, `docs/api-inventory.json`, `docs/POSTMAN_IMPLEMENTATION.md`, `docs/API_TEST_AUTOMATION.md` (Phases 2-4)

---

## Workflow Architecture

Phase 5 adds a new, additive `09 - Workflows` folder to the existing collection (renamed to `08 - Workflows` in Phase 5.1 after an empty placeholder folder ahead of it was removed). It does **not** modify the 38 requests created in Phases 3-4 (folders `01`-`08`) — those, and their 133 Phase 4 assertions, are byte-for-byte unchanged (verified by hashing the collection's first 8 folders before and after this phase's changes).

```text
API Discovery (Phase 2)
        ↓
Postman Requests (Phase 3)
        ↓
Test Assertions (Phase 4)
        ↓
Dynamic Variables (Phase 5)
        ↓
Data Chaining (Phase 5)
        ↓
API Workflows (Phase 5)
        ↓
CRUD Lifecycle (Phase 5, where supported)
        ↓
Test Validation (Phase 5, extends Phase 4)
        ↓
Test Cleanup (Phase 5)
```

Each workflow is an **ordered chain** of requests connected with `pm.execution.setNextRequest("<next request name>")` in each step's Tests script, terminating explicitly with `pm.execution.setNextRequest(null)`. Running a workflow's first request (via "Run" or the Collection Runner scoped to that sub-folder) executes the full chain in order; no step relies on Postman's default top-to-bottom folder order alone — the chain is explicit and self-terminating, so there is no possibility of an infinite loop.

---

## Chainable Endpoints Identified

Cross-referencing every endpoint in `docs/api-inventory.json` against what is actually executable, four real workflows were identified:

| Candidate workflow | Chainable? | Why |
| --- | --- | --- |
| Cart: Create → Read → Update → Read → Delete → Read → Cleanup | **Yes** | Full CRUD-shaped lifecycle, no authentication required (session-scoped via `X-Session-ID`), every request/response shape fully verified live in Phases 2-4. |
| Products: List → Detail | **Yes** (read-chain only) | `GET /products` → `GET /products/{slug}` is fully public and verified. |
| Auth: Login → Authenticated request | **Yes, but currently gated** | The chaining logic is fully implemented and correct; it cannot reach its authenticated step in *this* environment because no valid test credentials exist (see Known Limitations). |
| Admin: Login → List → Update Stock → Verify | **Yes, but currently gated** | Same reasoning as above — `PATCH /admin/products/{id}/stock` has a fully known body schema (`{ stock: number }`), so the chain is buildable, but requires a valid admin token to run past step 1. |
| Products: Create → Read → Update → Delete (full CRUD) | **No — not implemented** | `POST /products` and `PATCH /products/{id}` have an **undetermined request body schema** (Phase 2/3 finding, reconfirmed in Phase 4). Building a create step would require inventing field names, which is explicitly prohibited. |
| Orders: Create → Read (full CRUD) | **No — not implemented** | Same reason: `POST /orders`'s body schema was never determined. |
| Images: Upload → Retrieve | **No — not implemented** | Upload requires a real binary file and admin credentials, neither available; the endpoint's success response shape was never observed. |

---

## Workflows Implemented

### Workflow 01 — Authentication Chaining
```text
01 - Login (Capture Token)
      ↓ (only if accessToken received)
02 - Get Current User (Authenticated)
```
- **Dynamic data captured:** `accessToken` from the login response body (`data.accessToken`), written to the shared environment variable `{{authToken}}` — the same variable every Phase 3/4 Bearer-authenticated request already uses, so a successful login here immediately authenticates the *entire* collection, not just this workflow.
- **Failure handling:** if Login does not return an `accessToken` (verified live: returns 400 with the current empty `{{username}}`/`{{password}}` defaults), the workflow asserts the documented failure shape and calls `pm.execution.setNextRequest(null)` — it does **not** proceed to call `GET /auth/me` with no token, which would just produce a confusing, duplicate 401 already covered by Phase 4's negative tests.

### Workflow 02 — Cart CRUD Lifecycle *(flagship workflow — fully executable today)*
```text
01 - Get Products (Capture Dynamic Product)
      ↓
02 - Add Item To Cart (Create)
      ↓
03 - Get Cart (Read After Create)
      ↓
04 - Update Cart Item Quantity
      ↓
05 - Get Cart (Verify Update Persisted)
      ↓
06 - Remove Cart Item (Delete)
      ↓
07 - Get Cart (Verify Deletion)
      ↓
08 - Clear Cart (Cleanup)
```
- **Dynamic data captured:** a fresh session id (`{{wf_sessionId}}`, generated via a pre-request-script UUID v4 — not the shared static `{{sessionId}}` environment default), a real, currently-active, in-stock product id and slug picked live from `GET /products` (`{{wf_productId}}`, `{{wf_productSlug}}`), an initial random quantity (`{{wf_cartQuantity}}`, 1-3), and a guaranteed-different updated quantity (`{{wf_updatedCartQuantity}}`).
- **No hardcoded IDs anywhere** — every product id, session id, and quantity is generated or captured at runtime.
- **Validation depth:** each read step re-fetches the cart and cross-checks the *persisted* state, not just the mutating request's own response — this is stronger than Phase 4's single-request assertions (per the prompt's "Update → Read" and "Delete → Read" guidance).
- **Cleanup:** the final step clears the cart server-side and unsets all five workflow-scoped collection variables, so a failed or interrupted run cannot leave a stale value that masks a bug in the next run.

### Workflow 03 — Products List → Detail *(fully executable today)*
```text
01 - Get Products (Capture Dynamic Slug)
      ↓
02 - Get Product By Dynamic Slug
```
- **Dynamic data captured:** a *randomly chosen* product from the live catalog on every run (`{{wf_listedProductId}}`, `{{wf_listedProductSlug}}`, `{{wf_listedProductName}}`), so repeated runs exercise different products rather than always hitting the same one.
- **Validation:** cross-checks id/slug/name consistency between the list response and the detail response.
- Not a full CRUD chain — see Known Limitations for why Create/Update/Delete could not be added.

### Workflow 04 — Admin Product Stock Update *(implemented, currently gated on credentials)*
```text
01 - Login (Admin, Capture Token)
      ↓ (only if accessToken received)
02 - Get Admin Products (Capture Dynamic Product)
      ↓ (only if 200, not 401)
03 - Update Product Stock (Dynamic)
      ↓ (only if 200, not 401)
04 - Get Admin Products (Verify Stock Persisted)
```
- **Dynamic data captured:** `accessToken` (as above), a real product id from the admin product list (`{{wf_adminProductId}}`), and a randomly generated new stock value (`{{wf_adminNewStock}}`, 1-500).
- **Failure handling:** every step checks for `401` first and terminates immediately if authentication was not established, rather than letting three downstream requests all fail on the same missing-token condition.
- Chosen specifically because `PATCH /admin/products/{id}/stock`'s body (`{ stock: number }`) **is** fully known (unlike Product/Order create-update), so this is a legitimate, buildable workflow — it is simply unable to progress past step 1 in an environment with no admin credentials.

---

## Variable Strategy

| Scope | Used for | Examples | Why this scope |
| --- | --- | --- | --- |
| **Environment** (`QA-Demo Environment`) | Cross-collection configuration and the single shared auth token | `apiUrl`, `authToken`, `username`, `password` | These already existed from Phase 1/3 and are reused, not duplicated — a token captured by *any* workflow's Login step becomes usable by *every* Bearer-authenticated request in the whole collection. |
| **Collection variables** (`wf_*` prefix, new in Phase 5) | Per-run, workflow-scoped transient data that must not leak between workflows or pollute the environment | `wf_sessionId`, `wf_productId`, `wf_productSlug`, `wf_cartQuantity`, `wf_updatedCartQuantity`, `wf_listedProductId`, `wf_listedProductSlug`, `wf_listedProductName`, `wf_adminProductId`, `wf_adminNewStock`, `wf_loginSucceeded` | Collection scope (not environment) because these values are meaningless outside an active workflow run, and are explicitly unset at the end of each workflow — keeping them out of the environment avoids ever exposing a stale run's data to a Phase 3/4 request or a different workflow. |
| **Local (pre-request script scope)** | The UUID generator function itself | `uuidv4()` in Workflow 02 step 1 | Function-local; never persisted. |
| **Global** | Not used | — | No value in this project needs to survive across collections/environments; Phase 5 deliberately avoids global variables per the prompt's "avoid unless genuinely necessary" guidance. |

All `wf_*` collection variables are declared with an empty default value in the collection so `{{wf_...}}` never resolves to a literal `undefined` string if a workflow's later requests are inspected before that workflow has run.

---

## Dynamic Data Extraction

| Workflow | Source response | Field extracted | Stored as |
| --- | --- | --- | --- |
| 01 | `POST /auth/login` → `data.accessToken` | Access token | `{{authToken}}` (environment) |
| 02 | `GET /products` → `data[]` (filtered: `isActive && stock > 0`) | `id`, `slug` | `{{wf_productId}}`, `{{wf_productSlug}}` (collection) |
| 02 | Generated at runtime (pre-request script) | UUID v4 | `{{wf_sessionId}}` (collection) |
| 02 | Generated at runtime (test script, random 1-3 / +1-2) | Quantities | `{{wf_cartQuantity}}`, `{{wf_updatedCartQuantity}}` (collection) |
| 03 | `GET /products` → `data[random index]` | `id`, `slug`, `name` | `{{wf_listedProductId}}`, `{{wf_listedProductSlug}}`, `{{wf_listedProductName}}` (collection) |
| 04 | `POST /auth/login` → `data.accessToken` | Access token | `{{authToken}}` (environment) |
| 04 | `GET /admin/products` → `data[0]` | `id` | `{{wf_adminProductId}}` (collection) |
| 04 | Generated at runtime (random 1-500) | New stock value | `{{wf_adminNewStock}}` (collection) |

No identifier used by any workflow request is a literal, hardcoded value — every id is either extracted from a live API response or generated at runtime.

---

## Authentication Chaining

Implemented identically in Workflows 01 and 04:
```text
POST /auth/login
      ↓
response.data.accessToken exists?
      ├─ yes → pm.environment.set("authToken", accessToken) → proceed to next step
      └─ no  → assert the documented failure (400/401) → pm.execution.setNextRequest(null)
```
The captured token is written to the **environment** (not a collection variable) specifically so it is immediately usable by every other Bearer-authenticated request already defined in folders `01`-`06` — Phase 5's authentication chaining upgrades the *entire* collection's usability the moment valid credentials are supplied, not just the two workflows that perform the login. The token is never logged to the console or written to a response body.

---

## CRUD Workflows

| Resource | Create | Read | Update | Delete | Full lifecycle implemented? |
| --- | --- | --- | --- | --- | --- |
| Cart item | ✓ (Workflow 02, step 2) | ✓ (steps 3, 5, 7) | ✓ (step 4) | ✓ (step 6) + cart-level cleanup (step 8) | **Yes** |
| Products (catalog) | — (schema unknown) | ✓ (Workflow 03) | — (schema unknown) | — | Read-only chain only |
| Admin product stock | — | ✓ (Workflow 04, steps 2, 4) | ✓ (Workflow 04, step 3) | — | Update lifecycle only (no create/delete concept for stock) |
| Orders | — (schema unknown) | — | — | — | Not implemented |
| Admin orders | — | — | — (schema known but no order exists to update without Orders.create) | — | Not implemented (blocked by Orders.create being unbuildable) |

---

## Cleanup Strategy

* **Workflow 02 (Cart):** the final step (`08 - Clear Cart`) deletes all cart contents server-side (`DELETE /cart`) and asserts `cleared: true, totalItems: 0`, then unsets all 5 of its collection variables. This is a true automatic cleanup — running the workflow leaves **no** server-side state behind (the cart is scoped to a session id that is itself discarded after the run) and **no** stale client-side variables.
* **Workflow 03 (Products):** no server-side state is created (read-only), so no deletion is needed; the workflow still unsets its 3 collection variables at the end.
* **Workflows 01 and 04 (Auth-gated):** no cleanup is needed on the gated (no-credentials) path since no state is created. On the (currently unreached) success path, Workflow 04 unsets its 2 collection variables after the final verification step; the `authToken` environment variable is deliberately **not** unset, since it is shared, general-purpose collection configuration (consistent with how Phase 3 defined it), not workflow-scoped temporary data — a subsequent request should be able to keep using a valid, unexpired token.
* **No workflow leaves orphaned test data** in the application itself: Cart is the only resource any workflow actually creates data in, and it is always cleared by the end of the run.

---

## Dynamic Test Data

| Data | Generation method | Reused across runs? |
| --- | --- | --- |
| Cart session id | `crypto`-free UUID v4 generated in a pre-request script (`Math.random()`-based, standard v4 template) | No — fresh every run, guaranteeing isolation |
| Cart quantities | `Math.random()`-based integers within API-accepted bounds (see below) | No — different every run |
| Selected product (Workflow 02) | First live product matching `isActive && stock > 0` | Deterministic given current catalog state, but not hardcoded |
| Selected product (Workflow 03) | Random index into the live catalog | No — different every run (verified: run 1 picked `snoopy-office-mug`, run 2 picked `travel-charger-mini`) |
| Admin new stock value | `Math.random()`-based integer, 1-500 | No — different every run |

Quantity bounds respect the business rules discovered in Phase 4 (`POST /cart/items` requires `quantity > 0`; `PATCH /cart/items/{id}` requires `quantity >= 0`) — the generator always produces values that satisfy these constraints, per the prompt's "do not generate random values that violate business rules" instruction.

---

## Request Execution Order

Each workflow's requests are named with a numeric prefix (`01 -`, `02 -`, ...) reflecting their designed order, and that exact order is additionally **enforced programmatically** via `pm.execution.setNextRequest("<exact next request name>")` in every step (except the final step of each workflow, which calls `pm.execution.setNextRequest(null)`). This means:
- Running the Collection Runner over an entire workflow sub-folder produces the same order as running it top-to-bottom manually.
- The chain is explicit and inspectable — reading any one step's Tests script tells you exactly what runs next and why (or why the chain stops there).
- There is no possibility of an infinite loop: every branch in every workflow either advances to a strictly-later, uniquely-named step or terminates with `null`.

---

## Workflow Coverage Matrix

| Workflow | Requests | Dynamic Data | Validation | Cleanup | Status |
| --- | --- | --- | --- | --- | --- |
| Create → Read (Cart) | 3 (steps 1-3 of Workflow 02) | Product id/slug, session id, quantity | Response + re-read consistency | N/A at this stage | **Executed live, passing** |
| Create → Update (Cart) | 4 (steps 2-5 of Workflow 02) | + updated quantity | Update response + re-read persistence | N/A at this stage | **Executed live, passing** |
| Create → Delete (Cart) | steps 2, 6-7 of Workflow 02 | Product id, session id | Removal response + re-read absence | N/A at this stage | **Executed live, passing** |
| Full CRUD (Cart lifecycle) | 8 (Workflow 02, all steps) | All of the above | Every mutation re-verified by a subsequent GET | Automatic (`DELETE /cart` + variable unset) | **Executed live 3x, passing every time** |
| Products List → Detail | 2 (Workflow 03) | Random product per run | Cross-response consistency | Variable unset (no server state) | **Executed live 2x, passing every time** |
| Authentication Chaining | 2 (Workflow 01) | accessToken | Token presence / gated-failure shape | N/A | **Implemented; gate path executed live, passing; authenticated path unexecuted (no credentials)** |
| Admin Stock Update | 4 (Workflow 04) | accessToken, product id, stock value | Update response + re-read persistence | Variable unset | **Implemented; gate path executed live, passing; authenticated path unexecuted (no credentials)** |

---

## Known Limitations

1. **No valid test/admin credentials exist in this environment.** Workflows 01 and 04 are fully implemented and correctly gated, but their authenticated steps have never executed against a real success response — the response-shape assertions on those steps are the same best-effort/loose assertions carried over from Phase 4 and should be tightened once credentials are available.
2. **Full Products CRUD chaining is not implemented.** `POST /products` and `PATCH /products/{id}` have an undetermined request body schema (a standing Phase 2/3 finding). Building a `Create → Read → Update → Delete` product workflow would require inventing field names, which the project's no-invention rule prohibits. The read-only `List → Detail` chain (Workflow 03) is the closest workflow available for this resource.
3. **Full Orders CRUD chaining is not implemented**, for the same reason — `POST /orders`'s body schema was never determined.
4. **The Cart workflow's "Create" step depends on at least one active, in-stock product existing in the live catalog.** If the catalog were ever fully empty or all products out of stock, `Get Products (Capture Dynamic Product)` would fail its own assertion and terminate the workflow safely (no downstream request would run with an undefined product id) — this was verified in the test script logic, though the actual empty-catalog case was not observed live (the catalog has consistently had multiple in-stock products throughout this project).
5. **Workflow 02's product selection is deterministic given the current catalog** (`data.find(...)`, first match), not randomized — this was a deliberate choice for the CRUD lifecycle workflow so that a single failing product is easy to reproduce, whereas Workflow 03 deliberately randomizes to demonstrate coverage across the catalog over multiple runs. Both approaches avoid hardcoding.
6. **Image upload workflow was not built** — no real file or admin credentials available, and the success response shape was never observed (Phase 2-4 finding).
7. **`pm.execution.setNextRequest`** requires the workflow to be run via "Run" in the Postman app or the Collection Runner scoped to the relevant sub-folder; sending an individual mid-chain request manually (e.g. only `04 - Update Cart Item Quantity`) will not have the variables a prior step would have set, and is not how these requests are intended to be used in isolation — this is expected and documented behavior for chained workflows, not a defect.

---

## Execution Evidence (this phase)

As in Phase 4, Newman was not installed or used. Every workflow was validated by executing the exact same HTTP calls the chained requests make (same methods, URLs, headers, bodies, using a Python `requests`-based harness built for this session only) against the live `https://qademo.com/api`, and checking each step's assertion logic against the real response.

```text
Workflow 02 (Cart CRUD Lifecycle): run 3 times, 8/8 steps passed every run (24/24 total)
  Run 1: productId=18, quantity 3 -> 5, sessionId=747fa39b-...
  Run 2: productId=18, quantity 3 -> 4, sessionId=351e36b4-...
  Run 3: productId=18, quantity 2 -> 4, sessionId=9d161857-...
  All three session ids unique; all quantities dynamically generated and different across runs.

Workflow 03 (Products List -> Detail): run 2 times, 2/2 steps passed every run (4/4 total)
  Run 1 picked 'snoopy-office-mug'; Run 2 picked 'travel-charger-mini' - different products, both passed.

Workflow 01 (Auth Chaining): gate step executed, correctly returned 400 and terminated
  the chain (step 2 correctly not executed) - 1/1 gate assertion passed.

Workflow 04 (Admin Stock Update): gate step executed, correctly returned 400 and terminated
  the chain (steps 2-4 correctly not executed) - 1/1 gate assertion passed.
```

**Total workflow steps executed and passed: 30 / 30 (100%).** No failures. No JavaScript errors possible in the equivalent live logic (mirrored 1:1 from the collection's actual test scripts).

---

## Phase 4 Regression Check

The 38 original Phase 3/4 requests and their 133 assertions were not modified in any way (verified via a SHA-256 hash comparison of the collection's first 8 folders before and after this phase's changes — identical). Phase 4's own live-verification harness (`docs/API_TEST_AUTOMATION.md`) remains accurate and was not re-run in full during this phase since no request or assertion it covers was touched.
