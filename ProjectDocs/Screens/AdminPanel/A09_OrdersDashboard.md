# A09 — Orders Dashboard

**App:** Admin Panel (Next.js, web)

## Purpose
Monitor orders by Service Location, seeing what's delivered vs pending, for operational oversight.

## Users
Main Admin.

## Navigation
- **From:** sidebar / Dashboard KPI.
- **To:** Order Detail (A10).

## Key UI Elements
- **Service Location selector** (or "All").
- Summary counts: Pending, Order Confirmed, Delivered, Completed, Cancelled. **Pre-order count** (orders with future delivery dates).
- **Orders table:** order number, customer name, location, **delivery date**, delivery time, total, payment (preference / collected), status chip, staff handling, date placed, **pre-order badge** (if applicable), **placed by** (Customer/Staff).
- Filters: status, date range, location, **pre-order filter** (show pre-orders only); search by order number / customer.
- Sort + pagination. **Default sort: pending/oldest first (Confirmed Q22).**

## States
- **Loading:** table skeleton.
- **Empty:** no orders for scope → empty state.
- **Error:** Retry.
- **Success:** table + summary counts.

## Actions
- Filter/search/sort.
- Tap order → Order Detail (A10).
- Export (optional — CSV) [suggestion].

## Business Rules
- Read visibility across all order states per location (delivered vs pending focus). **Sorted pending/oldest first (Q22).**
- `Cancelled` orders shown (customer-cancelled while Pending — Q10) but excluded from sales.
- **Pre-orders** shown with delivery date badge; filterable.
- **Staff-placed orders** indicated (placedBy = staff) for audit.
- Admin oversight is read-only for fulfillment; staff perform confirmations (unless a manual override is later requested).

## Validation / Permissions
- Admin only.

## Responsive / Accessibility
- Accessible table; status via text+color; filters labeled.

## Dependencies
- `/reports/orders`, `/orders`.
