# Feature — Order Fulfillment Lifecycle

**Overview:** Staff progress orders through Order Confirm → Delivery Confirm → Payment Confirm (recording Gpay/Cash); Admin has visibility. **Updated (Requirement #2):** each state transition records a timestamp and the performing staff member for operational performance tracking.

**Business purpose:** Operational tracking of what's delivered vs pending and how payment was collected. Timestamps enable performance measurement (how quickly staff fulfills orders).

**Users:** Staff (act), Admin (monitor + performance reporting), Customer (see status).

**Entry points:** Staff Order List (S03) → Order Detail (S04); Admin Orders Dashboard (A09) → Order Detail (A10); Customer Order Detail (C10).

**Dependencies:** Order created by customer (or Shop Sale by staff — Requirement #12); staff assigned to the order's location with relevant permissions.

**Data used:** Order.state, paymentMethodCollected, **state transition timestamps** (orderConfirmedAt, deliveredAt, paymentConfirmedAt), **performing staff** (orderConfirmedBy, deliveredBy, paymentConfirmedBy), deliveryDate (for pre-orders).

**State transition timestamps (Requirement #2):**
- `orderConfirmedAt` + `orderConfirmedBy` — when/who performed Order Confirm.
- `deliveredAt` + `deliveredBy` — when/who performed Delivery Confirm.
- `paymentConfirmedAt` + `paymentConfirmedBy` — when/who performed Payment Confirm.
- These enable: average confirmation time, delivery speed, per-staff performance metrics, operational bottleneck identification.

**Validation:** forward-only transitions; permission per action; correct current state; idempotent.

**States:** Pending → Order Confirmed → Delivered → Completed; **Cancelled reachable from Pending (customer) or any pre-Completed state (staff) — Q10/Q26. Stock restored on any cancellation.**

**Pre-order handling:** Pre-orders (deliveryDate in future) follow the same lifecycle. Staff sees them in their order list with the scheduled delivery date prominently displayed. Fulfillment happens on/near the delivery date.

**Edge cases:**
- Out-of-order transition → 409.
- Permission missing → action hidden; server rejects.
- Payment method collected differs from customer preference → record actual. **Payment Confirm → Completed (Q13).**
- **Cancellation (Q10/Q26):** customer can cancel only while Pending (blocked after Order Confirmed — 409). **Staff can cancel in any state before Completed.** On ANY cancellation, stock is restored.
- **Stock impact:** stock is decremented at order placement (Q26); restored on cancellation.
- Concurrent staff acting on same order (idempotency guards).
- Orders placed by staff (placedBy = staff) → same lifecycle, but noted in audit.
- Pre-order delivered early or late relative to scheduled date → no system enforcement; operational decision.

**Permissions:** `confirm_order`, `confirm_delivery`, `confirm_payment` per staff.

**Notifications:** (future) push to customer on each transition; trial reflects on refresh.

**Errors:** network retry; conflict messaging.

**Future considerations:** delivery proof/photo, ETA, admin override, SLA alerts (if transition takes too long).
