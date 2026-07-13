# S04 — Order Detail (Fulfillment)

**App:** Staff (Flutter/Android)

## Purpose
The core staff working screen. Shows a single order's full details and provides the fulfillment actions: **Order Confirm, Delivery Confirm, Payment Confirm** (recording Gpay or Cash).

## Users
Authenticated Staff (with relevant confirm permissions).

## Navigation
- **From:** Order List (S03).
- **To:** back to Order List (updated).

## Key UI Elements
- Order header: order number, date/time, current status chip.
- Customer info: name, WhatsApp number (tap-to-call/WhatsApp via device — not an API), delivery address + nearest landmark.
- Delivery time.
- Itemized products: name, weight, qty, unit price, line total; order total.
- Payment preference (Gpay on Delivery / Cash on Delivery).
- **Action buttons (state-aware, permission-gated):**
  - **Order Confirm** (enabled when Pending; needs `confirm_order`).
  - **Delivery Confirm** (enabled when Order Confirmed; needs `confirm_delivery`).
  - **Payment Confirm** (enabled when Delivered; needs `confirm_payment`) — opens a small selector to record **actual method collected: Gpay or Cash**.
- Status timeline showing progress.

## States
- **Loading:** detail fetch; action-in-progress (button loader).
- **Error:**
  - Out-of-order transition attempt (409) → message.
  - Permission missing → action hidden/disabled.
  - Network/server → Retry.
- **Success:** status chip + timeline update; success toast.

## Actions
- Tap Order Confirm → `PATCH /orders/{id}/state {OrderConfirmed}`.
- Tap Delivery Confirm → `PATCH /orders/{id}/state {Delivered}`.
- Tap Payment Confirm → choose Gpay/Cash → `PATCH /orders/{id}/payment {collectedMethod}` → Completed.
- Contact customer (device dialer/WhatsApp).

## Business Rules
- Forward-only transitions; each guarded by permission + correct current state (server-enforced, idempotent).
- `paymentMethodCollected` records the real method, which may differ from customer preference. **Payment Confirm → Completed (Q13).**
- **Cancellation (Q10):** only the **customer** can cancel, and **only while Pending**. Once the staff performs **Order Confirm**, the order can no longer be cancelled. A Pending order may therefore appear as `Cancelled` if the customer cancelled before the staff confirmed.
- **Gpay on Delivery (Q13):** manual personal transfer; no UPI ID/QR in-app.
- Actions only for orders within the staff's scope.

## Validation / Permissions
- Each action requires its specific permission and correct order state.

## Responsive / Accessibility
- Primary action prominent based on current state; confirm dialogs for state changes; timeline conveyed via text+icon; contact links labeled.

## Dependencies
- `/orders/{id}`, `/orders/{id}/state`, `/orders/{id}/payment`.
