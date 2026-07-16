# Feature — Stock / Inventory Management (Requirements #9, #10)

**Overview:** Full stock and inventory management tracking per-location stock counts, purchase data (cost price, supplier), selling data, and profit/loss calculations.

**Business purpose:** Financial visibility — know exactly what's purchased vs sold and the resulting profit/loss. Operational visibility — know what's in stock and when to reorder. Supports the pre-order feature (out of stock triggers pre-order).

**Users:** Admin (full management + reporting), Staff (update stock counts if permitted).

**Entry points:**
- Admin: Inventory Management (A15), Products (A05), Sales Reports (A11 profit/loss tab).
- Staff: S05 (stock update section).

**Dependencies:** Products (many-to-many with locations), Service Locations, Orders (for selling data).

**Data used:**
- `ProductLocationStock` — current stock count per product per location.
- `PurchaseRecord` — purchase rate, weights, qty, supplier, date per product per location.
- `StockUpdateLog` — audit trail of stock changes.
- OrderItems (from Completed orders) — selling data (qty sold × price).

**Stock tracking:**
- Each product has an independent stock count at each assigned location.
- **Stock is decremented when orders are placed** (on Pending state — Confirmed Q26). Stock is reserved at order time.
- **Stock is restored/incremented back on cancellation** — when customer or staff cancels an order, all item quantities are restored to per-location stock counts.
- Stock = 0 → "Out of Stock" for that product at that location → triggers pre-order flow.
- Staff with `update_stock` can increment/adjust stock counts from the Staff App.
- Admin can adjust stock counts from A15 or A05.

**Purchase data (Requirement #10):**
- Admin records purchases: product, location, purchase rate (cost price per unit), gross/net weight of batch, quantity purchased, supplier (optional), purchase date.
- Purchase records contribute to **Total Expense** in profit/loss calculations.
- Multiple purchase records per product (different batches at different prices).

**Selling data:**
- Derived from Completed orders: selling price × quantity sold per product per location.
- Automatically computed from order data (no manual entry needed).

**Profit/Loss calculation (Confirmed Q27 — SIMPLIFIED):**
- **Total Sale − Total Expense = Profit/Loss**
- Total Sale = revenue from all completed orders (sum of order totals).
- Total Expense = sum of all purchase records (stock purchases) + sum of all miscellaneous purchases (staff expenses).
- This is a simple aggregate calculation — no per-batch costing, no average cost, no FIFO needed.
- Date-range and location filters supported in reporting.

**Validation:**
- Stock count ≥ 0.
- Purchase rate > 0; quantity > 0.
- Product and location must be valid (product assigned to that location).

**States:**
- Stock levels: In Stock (count > threshold) / Low Stock (count ≤ threshold) / Out of Stock (count = 0).
- Purchase record: created (immutable once saved).
- Stock update: logged (old → new + who + when).

**Edge cases:**
- Stock goes negative (shouldn't happen; enforce ≥ 0).
- Order cancelled → stock restored; ensure atomic operation.
- Purchase data entered after items already sold → profit/loss retroactively calculable.
- Product removed from a location → stock data preserved for historical reporting.
- Concurrent stock updates → last-write-wins with audit log.

**Permissions:** Admin (full: view/edit stock, record purchases, view profit/loss). Staff with `update_stock` (update stock counts at assigned locations only; no purchase data access).

**Notifications:** (suggestion) low-stock alerts to Admin.

**Errors:** validation errors; network retry; concurrent update conflicts.

**Future considerations:** reorder alerts; supplier management; batch/lot tracking; barcode scanning for stock updates.
