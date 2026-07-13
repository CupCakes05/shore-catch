# Feature — Reporting

**Overview:** Admin reporting: orders by location (delivered vs pending), total sales by location, and customer purchase-history lookup.

**Business purpose:** Operational and financial visibility for the operator.

**Users:** Admin only.

**Entry points:** Dashboard (A02), Orders Dashboard (A09), Sales Reports (A11), Customer History (A12).

**Dependencies:** Orders and their states; payment collected data.

**Data used:** Orders, OrderItems, Order.state, paymentMethodCollected, Customer identity (name/WhatsApp).

**Validation/rules:**
- Orders dashboard groups by location and status; **sorted pending/oldest first (Q22)**.
- **Sales counts only `Completed` orders (Confirmed Q7).**
- Payment breakdown by collected method (Gpay/Cash).
- Customer lookup by name (partial) / WhatsApp (exact or partial — confirm).

**States:** loading/empty/error/success; date-range and location filters.

**Edge cases:**
- No data in range → empty state.
- Cancelled orders excluded from sales.
- Large result sets → pagination.

**Permissions:** Admin only; handle PII per privacy policy.

**Notifications:** none.

**Errors:** fetch retry.

**Future considerations:** CSV/PDF export, charts/trends, top-products report, per-staff performance, date-comparison.
