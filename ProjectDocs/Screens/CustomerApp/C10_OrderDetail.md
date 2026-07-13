# C10 — Order Detail

**App:** Customer (Flutter/Android)

## Purpose
Show full detail and current lifecycle status of a single order, with invoice access.

## Users
Authenticated Customer.

## Navigation
- **From:** Order History (C09), Order Confirmation (C08).
- **To:** back to history; invoice download.

## Key UI Elements
- Order header: order number, date/time, status chip.
- **Status timeline:** Pending → Order Confirmed → Delivered → Completed (with reached/pending styling).
- Itemized list: product name, weight, qty, unit price, line total.
- Totals; delivery time; payment method (preference and, once collected, actual method recorded by staff).
- Delivery address + nearest landmark (snapshot).
- **Download Invoice** button.
- **Cancel Order** button — shown **only while the order is `Pending`** (Confirmed Q10). Hidden/disabled once `Order Confirmed` or later.

## States
- **Loading:** skeleton.
- **Error:** fetch failure → Retry.
- **Success:** full detail.
- Cancel action (if present): confirm dialog + loading + success/error.

## Actions
- Download invoice (`/orders/{id}/invoice`).
- Cancel order (only while Pending) → `POST /orders/{id}/cancel`.
- Back.

## Business Rules (Confirmed Q10)
- Read-only status (updated by staff actions).
- **Cancellation: customer can cancel only while `Pending`.** After staff Order Confirm, cancel is unavailable; the server rejects a cancel on non-Pending orders (409).

## Validation / Permissions
- Authenticated; own order only.

## Responsive / Accessibility
- Timeline conveys state via text + icon; invoice/cancel buttons labeled; confirm dialog for destructive cancel.

## Dependencies
- `/orders/{id}`, `/orders/{id}/invoice`, `/orders/{id}/cancel` (Pending only).
