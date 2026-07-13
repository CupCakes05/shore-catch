# A11 — Sales Reports

**App:** Admin Panel (Next.js, web)

## Purpose
Report Total Sales by Service Location, giving the operator visibility into revenue performance across neighborhoods.

## Users
Main Admin.

## Navigation
- **From:** sidebar / Dashboard KPI.

## Key UI Elements
- **Service Location selector** (or "All" with per-location breakdown).
- Date-range filter (e.g., today / week / month / custom).
- **Total sales figure** (₹) for the scope.
- Breakdown table: per location — orders count, total sales, split by payment method collected (Gpay vs Cash).
- (Optional) Simple bar/line chart of sales over time [with text alternative].
- (Optional) Export CSV [suggestion].

## States
- **Loading:** skeletons.
- **Empty:** no sales in range → empty state.
- **Error:** Retry.
- **Success:** figures + table.

## Actions
- Change location scope + date range.
- (Optional) Export.

## Business Rules (Confirmed Q7)
- **Sales count only `Completed` (payment-confirmed) orders.** Pending/Confirmed/Delivered/Cancelled are excluded.
- Payment method breakdown uses `paymentMethodCollected` recorded by staff (Gpay/Cash).

## Validation / Permissions
- Admin only.

## Responsive / Accessibility
- Charts have text/table alternatives; figures clearly labeled; accessible date pickers.

## Dependencies
- `/reports/sales`.
