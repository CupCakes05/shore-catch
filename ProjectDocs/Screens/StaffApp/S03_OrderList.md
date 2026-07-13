# S03 — Order List

**App:** Staff (Flutter/Android)

## Purpose
The staff home. Shows orders for the staff member's assigned Service Location (and, if `restrict_to_categories` is set, only orders containing allowed categories), so staff can pick up and progress fulfillment.

## Users
Authenticated Staff (`view_orders`).

## Navigation
- **From:** Splash auto-session (S01), Login (S02).
- **To:** Order Detail (S04); Profile (S06); Settings (S07); Add Product (S05) if permitted.

## Key UI Elements
- **Order cards:** order number, customer name, delivery time, item count, total, payment preference (Gpay/Cash), **status chip**.
- **Filter/segmented control:** by status (Pending / Confirmed / Delivered / Completed) — helps staff focus on pending vs delivered.
- Assigned location label in app bar.
- Pull-to-refresh.
- (If `add_products`) entry point to Add Product (S05).

## States
- **Loading:** skeleton order cards.
- **Empty:** "No orders right now" for the assigned scope.
- **Error:** fetch failure → Retry.
- **Success:** order list.

## Actions
- Tap order → Order Detail (S04).
- Filter by status.
- Refresh.
- Navigate to profile/settings/add-product.

## Business Rules
- Orders scoped to assigned location; further filtered by allowed categories when restricted.
- **Sorting (Confirmed Q22): pending/oldest first.**

## Validation / Permissions
- Requires `view_orders`. Scope enforced server-side.

## Responsive / Accessibility
- Status chips text+color; clear tap targets; labels for filters.

## Dependencies
- `/orders?location={assigned}&categories={allowed}`.
