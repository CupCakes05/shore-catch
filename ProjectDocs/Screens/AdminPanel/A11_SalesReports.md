# A11 — Sales Reports

**App:** Admin Panel (Next.js, web)

## Purpose
Report Total Sales by Service Location, giving the operator visibility into revenue performance across neighborhoods. Now includes **profit/loss analysis**, **staff performance metrics**, and **miscellaneous purchase expenses**.

## Users
Main Admin.

## Navigation
- **From:** sidebar / Dashboard KPI.
- **To:** Order Detail (A10) from drill-down.

## Key UI Elements
- **Service Location selector** (or "All" with per-location breakdown).
- Date-range filter (e.g., today / week / month / custom).

### Sales Tab
- **Total sales figure** (₹) for the scope.
- Breakdown table: per location — orders count, total sales, split by payment method collected (Gpay vs Cash).
- **GST summary:** total GST collected (breakdown by rate if useful).
- (Optional) Simple bar/line chart of sales over time [with text alternative].

### Profit/Loss Tab (Requirement #10, Simplified Q27)
- **Total Sale:** sum of all Completed order totals.
- **Total Expense:** sum of all PurchaseRecords + sum of all MiscPurchases.
- **Profit/Loss:** Total Sale − Total Expense (simple aggregate).
- Filterable by location and date-range.
- No per-product per-batch costing needed.

### Staff Performance Tab (Requirement #2)
- Average time from order placement to Order Confirm (per staff).
- Average time from Order Confirm to Delivery Confirm (per staff).
- Average time from Delivery Confirm to Payment Confirm (per staff).
- Orders handled count per staff.
- Location filter.

### Miscellaneous Purchases Tab (Requirement #8)
- List of staff-recorded misc purchases (date, staff name, item, qty, cost).
- Total operational expenses for the period.
- Location filter.
- These are **expenses** — shown separately from product sales.

### Common Elements
- (Optional) Export CSV [suggestion].

## States
- **Loading:** skeletons.
- **Empty:** no data in range → empty state per tab.
- **Error:** Retry.
- **Success:** figures + tables.

## Actions
- Change location scope + date range.
- Switch tabs (Sales / Profit-Loss / Staff Performance / Misc Purchases).
- (Optional) Export.

## Business Rules (Confirmed Q7, Requirements #2, #8, #10)
- **Sales count only `Completed` (payment-confirmed) orders.** Pending/Confirmed/Delivered/Cancelled are excluded.
- Payment method breakdown uses `paymentMethodCollected` recorded by staff (Gpay/Cash).
- **Profit/Loss** requires purchase records to be entered (A15); without purchase data, only Total Sale shown with "No expense data" note.
- **Staff performance** derived from order state transition timestamps.
- **Misc purchases** are operational expenses, included in Total Expense for profit/loss.

## Validation / Permissions
- Admin only.

## Responsive / Accessibility
- Charts have text/table alternatives; figures clearly labeled; accessible date pickers; tabs navigable.

## Dependencies
- `/reports/sales`, `/reports/profit-loss`, `/reports/staff-performance`, `/reports/misc-purchases`.
