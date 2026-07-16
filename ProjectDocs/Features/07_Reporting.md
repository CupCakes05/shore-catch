# Feature — Reporting

**Overview:** Admin reporting: orders by location, total sales, **profit/loss analysis**, **staff performance metrics**, **miscellaneous purchase expenses**, and customer purchase-history lookup.

**Business purpose:** Operational, financial, and performance visibility for the operator. Profit/loss supports business decision-making; staff performance helps optimize fulfillment.

**Users:** Admin only.

**Entry points:** Dashboard (A02), Orders Dashboard (A09), Sales Reports (A11), Customer History (A12), Inventory Management (A15).

**Dependencies:** Orders and their states; payment collected data; stock purchase records (for profit/loss); order state transition timestamps (for staff performance); misc purchase records.

**Data used:** Orders, OrderItems (with GST snapshots), Order.state, paymentMethodCollected, state transition timestamps (orderConfirmedAt/By, deliveredAt/By, paymentConfirmedAt/By), PurchaseRecords (cost data), MiscPurchases, Customer identity (name/WhatsApp).

**Report types:**

### Sales
- Orders dashboard groups by location and status; **sorted pending/oldest first (Q22)**.
- **Sales counts only `Completed` orders (Confirmed Q7).**
- Payment breakdown by collected method (Gpay/Cash).
- GST collected summary.

### Profit/Loss (Requirement #10, Confirmed Q27 — SIMPLIFIED)
- **Total Sale − Total Expense = Profit/Loss** (simple aggregate formula).
- Total Sale = revenue from all completed orders (sum of order totals).
- Total Expense = sum of all purchase records (stock purchases) + sum of all miscellaneous purchases (staff expenses).
- No per-batch costing, average cost, or FIFO needed.
- Date-range and location filters.

### Staff Performance (Requirement #2)
- Average time from order placement → Order Confirm (per staff).
- Average time from Order Confirm → Delivery Confirm (per staff).
- Average time from Delivery Confirm → Payment Confirm (per staff).
- Total orders handled per staff.
- Derived from state transition timestamps.

### Miscellaneous Purchases (Requirement #8)
- All staff-recorded misc purchases (date, staff, item, qty, cost).
- Total operational expenses for period.
- Separate from product sales/revenue.
- Included in **Total Expense** for profit/loss calculation (Q27).

### Customer History
- Lookup by name (partial) / WhatsApp (exact or partial).
- Customer's order history with details.

**States:** loading/empty/error/success; date-range and location filters.

**Edge cases:**
- No data in range → empty state.
- Cancelled orders excluded from sales (stock restored on cancel).
- Large result sets → pagination.
- No purchase data → profit/loss shows Total Sale only with "No expense data recorded" note.

**Permissions:** Admin only; handle PII per privacy policy.

**Notifications:** none.

**Errors:** fetch retry.

**Future considerations:** CSV/PDF export, charts/trends, top-products report, per-staff daily/weekly goals, date-comparison, alerts for slow fulfillment, automated profit/loss reports.
