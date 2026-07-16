# Feature — Location-Scoped Catalog (Multi-Location)

**Overview:** The catalog (categories → products) is partitioned by Service Location. Customers see only their location's catalog; Admin manages per location. **Updated (Requirement #4):** categories, products, and staff can now be assigned to MULTIPLE locations (many-to-many). Stock/inventory remains per-location.

**Business purpose:** Serve neighborhood-specific inventory and pricing without maps; keep operations location-bound. Multi-location assignment reduces duplication when the same category/product serves multiple areas.

**Users:** Main Admin (manage), Customer (browse), Staff (view; add if permitted; update stock if permitted).

**Entry points:**
- Admin: Service Locations (A03) → Categories (A04) → Products (A05) → Inventory (A15).
- Customer: Category Home (C05) → Product List (C06).
- Staff: S05 (add products / update stock).

**Dependencies:** Service Location must exist before categories; category before products. Multi-location assignment via junction tables.

**Data used:** ServiceLocation, Category (image, many-to-many with locations), Product (name, gross/net weight, price, GST, image, many-to-many with locations), ProductLocationStock (per-location inventory). See `05_DatabaseConcepts.md`.

**Multi-location scoping (Requirement #4):**
- Categories assigned to multiple locations via multi-select in Admin (A04).
- Products assigned to multiple locations via multi-select in Admin (A05).
- Staff assigned to multiple locations via multi-select in Admin (A07).
- Stock/inventory tracked per-location via `ProductLocationStock`.
- Customer sees categories/products assigned to their location, with stock data for their location.

**Stock/Availability (Requirements #9, #10):**
- Each product has a per-location stock count.
- When stock = 0, product shows "Out of Stock" to customer and defaults to pre-order flow.
- Staff with `update_stock` can update counts from S05.
- Admin manages full purchase data (cost price, supplier) in A15.

**Validation:** Category name + image + at least one location; product name, positive weights (Net ≤ Gross), positive price, GST valid if applicable, at least one location, image.

**States:** Loading/empty/error/success on all listing screens; empty category shows empty state to customer.

**Staff product approval (Confirmed Q11):** products added/edited by staff (`add_products`) enter `PendingApproval` and are hidden from customers until an Admin approves (Approved) or rejects (Rejected). Admin add/edit auto-approved. See `02_BusinessRules.md` §9a.

**Soft-delete (Confirmed Q6):** locations/categories/products are deactivated, never hard-deleted; snapshots + reporting preserved.

**Edge cases:**
- Category with no products.
- Removing a category/product referenced by historical orders → snapshots protect history; **soft-delete (Q6)**.
- Location removal cascade (soft-disable).
- Staff-submitted product pending approval not shown to customers.
- Product assigned to multiple locations; stock differs per location.
- Product out of stock at one location but in stock at another — each customer sees their location's stock.

**Permissions:** Admin full; Staff add/edit if `add_products` within allowed categories (subject to approval); Staff update stock if `update_stock`.

**Notifications:** staff notified on approve/reject (Q18 channels).

**Errors:** upload failures, validation errors, fetch failures with retry.

**Future considerations:** product search/filter for customers; category ordering; bulk product/location assignment.
