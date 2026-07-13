# Feature — Location-Scoped Catalog

**Overview:** The catalog (categories → products) is partitioned by Service Location. Customers see only their location's catalog; Admin manages per location.

**Business purpose:** Serve neighborhood-specific inventory and pricing without maps; keep operations location-bound.

**Users:** Main Admin (manage), Customer (browse), Staff (view; add if permitted).

**Entry points:**
- Admin: Service Locations (A03) → Categories (A04) → Products (A05).
- Customer: Category Home (C05) → Product List (C06).

**Dependencies:** Service Location must exist before categories; category before products.

**Data used:** ServiceLocation, Category (image), Product (name, gross/net weight, price, image). See `05_DatabaseConcepts.md`.

**Validation:** Category name + image; product name, positive weights (Net ≤ Gross), positive price, image.

**States:** Loading/empty/error/success on all listing screens; empty category shows empty state to customer.

**Staff product approval (Confirmed Q11):** products added/edited by staff (`add_products`) enter `PendingApproval` and are hidden from customers until an Admin approves (Approved) or rejects (Rejected). Admin add/edit auto-approved. See `02_BusinessRules.md` §9a.

**Soft-delete (Confirmed Q6):** locations/categories/products are deactivated, never hard-deleted; snapshots + reporting preserved.

**Edge cases:**
- Category with no products.
- Removing a category/product referenced by historical orders → snapshots protect history; **soft-delete (Q6)**.
- Location removal cascade (soft-disable).
- Staff-submitted product pending approval not shown to customers.

**Permissions:** Admin full; Staff add/edit if `add_products` within allowed categories (subject to approval).

**Notifications:** staff notified on approve/reject (Q18 channels).

**Errors:** upload failures, validation errors, fetch failures with retry.

**Future considerations:** product search/filter for customers; stock/availability tracking; category ordering. ⏳ **Pricing model (Q5) still pending** — fixed per unit vs per-kg, weight unit.
