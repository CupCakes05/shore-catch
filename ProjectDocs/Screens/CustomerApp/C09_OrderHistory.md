# C09 — Order History

**App:** Customer (Flutter/Android)

## Purpose
List the customer's active and past orders with their current status, providing access to detail and invoice.

## Users
Authenticated Customer.

## Navigation
- **From:** Bottom nav "Orders", Order Confirmation (C08).
- **To:** Order Detail (C10).

## Key UI Elements
- List of order cards: order number, date, item count, total, **status chip** (Pending / Order Confirmed / Delivered / Completed / Cancelled), payment method.
- Optional filter/segmented control: Active vs Completed.
- Pull-to-refresh.

## States
- **Loading:** skeleton list.
- **Empty:** no orders → "You haven't placed any orders yet" + CTA to browse.
- **Error:** fetch failure → Retry.
- **Success:** list of orders.

## Actions
- Tap order → Order Detail (C10).
- Filter (optional).
- Refresh.

## Business Rules
- Shows only the authenticated customer's orders.
- Status reflects staff-driven lifecycle updates.

## Validation / Permissions
- Authenticated; own orders only.

## Responsive / Accessibility
- Status chips use text + color (not color alone) for accessibility; list rows labeled; adequate tap targets.

## Dependencies
- `/orders?customer=me`.
