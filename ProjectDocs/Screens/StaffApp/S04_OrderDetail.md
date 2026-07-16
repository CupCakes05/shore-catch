# S04 — Order Detail (Fulfillment)

**App:** Staff (Flutter/Android)

## Purpose
The core staff working screen. Shows a single order's full details and provides the fulfillment actions: **Order Confirm, Delivery Confirm, Payment Confirm** (recording Gpay or Cash). Each action records a **timestamp** for performance tracking.

## Users
Authenticated Staff (with relevant confirm permissions).

## Navigation
- **From:** Order List (S03).
- **To:** back to Order List (updated).

## Key UI Elements
- Order header: order number, date/time, current status chip, **pre-order badge** (if applicable with delivery date), **"Placed by: Staff/Customer"** indicator.
- Customer info: name, WhatsApp number (tap-to-call/WhatsApp via device — not an API), delivery address + nearest landmark.
- **Delivery date + time slot** (prominently shown; future date highlighted for pre-orders).
- Itemized products: name, weight, qty, unit price, **GST info (if applicable)**, line total; subtotal, GST total, order total.
- Payment preference (Gpay on Delivery / Cash on Delivery).
- **Fulfillment timeline with timestamps (Requirement #2):**
  - Shows completed transitions with date/time and staff name.
  - Pending transitions shown as next action.
- **Action buttons (state-aware, permission-gated):**
  - **Order Confirm** (enabled when Pending; needs `confirm_order`). Records timestamp.
  - **Delivery Confirm** (enabled when Order Confirmed; needs `confirm_delivery`). Records timestamp.
  - **Payment Confirm** (enabled when Delivered; needs `confirm_payment`) — opens a small selector to record **actual method collected: Gpay or Cash**. Records timestamp.
  - **Cancel Order** (enabled in any state before Completed; needs `confirm_order` or similar permission). Opens a confirm dialog. On confirm, order → Cancelled; stock restored.
- Status timeline showing progress with timestamps.

## States
- **Loading:** detail fetch; action-in-progress (button loader).
- **Error:**
  - Out-of-order transition attempt (409) → message.
  - Permission missing → action hidden/disabled.
  - Network/server → Retry.
- **Success:** status chip + timeline update; success toast; timestamp recorded.

## Actions
- Tap Order Confirm → `PATCH /orders/{id}/state {OrderConfirmed}` → records `orderConfirmedAt` + `orderConfirmedBy`.
- Tap Delivery Confirm → `PATCH /orders/{id}/state {Delivered}` → records `deliveredAt` + `deliveredBy`.
- Tap Payment Confirm → choose Gpay/Cash → `PATCH /orders/{id}/payment {collectedMethod}` → Completed; records `paymentConfirmedAt` + `paymentConfirmedBy`.
- Tap Cancel Order → confirm dialog → `POST /orders/{id}/staff-cancel` → order → Cancelled; stock restored; records `cancelledBy` + `cancelledAt`.
- Contact customer (device dialer/WhatsApp).

## Business Rules
- Forward-only transitions; each guarded by permission + correct current state (server-enforced, idempotent).
- **Each transition records a timestamp and the staffId** (Requirement #2) for operational performance tracking.
- `paymentMethodCollected` records the real method, which may differ from customer preference. **Payment Confirm → Completed (Q13).**
- **Staff cancellation (Q26):** Staff can cancel orders **in any state before Completed** using the Cancel button. On cancel, stock is **restored/incremented back** for all items. The cancellation records `cancelledBy = staffId` and `cancelledAt` timestamp.
- **Customer cancellation (Q10):** the customer can cancel **only while Pending**. Once the staff performs **Order Confirm**, the customer can no longer cancel. A Pending order may appear as `Cancelled` if the customer cancelled before the staff confirmed.
- **Gpay on Delivery (Q13):** manual personal transfer; company UPI ID shown at checkout, not on this screen.
- Actions only for orders within the staff's scope (assigned locations).
- **Pre-orders** are shown with their scheduled delivery date; staff fulfills on that date.

## Validation / Permissions
- Each action requires its specific permission and correct order state.

## Responsive / Accessibility
- Primary action prominent based on current state; confirm dialogs for state changes; timeline conveyed via text+icon with timestamps; contact links labeled.

## Dependencies
- `/orders/{id}`, `/orders/{id}/state`, `/orders/{id}/payment`, `/orders/{id}/staff-cancel`.
