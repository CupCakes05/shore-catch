# Feature — Order Fulfillment Lifecycle

**Overview:** Staff progress orders through Order Confirm → Delivery Confirm → Payment Confirm (recording Gpay/Cash); Admin has visibility.

**Business purpose:** Operational tracking of what's delivered vs pending and how payment was collected.

**Users:** Staff (act), Admin (monitor), Customer (see status).

**Entry points:** Staff Order List (S03) → Order Detail (S04); Admin Orders Dashboard (A09) → Order Detail (A10); Customer Order Detail (C10).

**Dependencies:** Order created by customer; staff assigned to the order's location with relevant permissions.

**Data used:** Order.state, paymentMethodCollected, audit fields (confirmedBy/deliveredBy/paymentBy).

**Validation:** forward-only transitions; permission per action; correct current state; idempotent.

**States:** Pending → Order Confirmed → Delivered → Completed; **Cancelled reachable only from Pending (customer) — Q10**.

**Edge cases:**
- Out-of-order transition → 409.
- Permission missing → action hidden; server rejects.
- Payment method collected differs from customer preference → record actual. **Payment Confirm → Completed (Q13).**
- **Cancellation (Q10):** customer-only, only while Pending; blocked after Order Confirmed (409).
- Concurrent staff acting on same order (idempotency guards).

**Permissions:** `confirm_order`, `confirm_delivery`, `confirm_payment` per staff.

**Notifications:** (future) push to customer on each transition; trial reflects on refresh.

**Errors:** network retry; conflict messaging.

**Future considerations:** delivery proof/photo, ETA, admin override, extending the cancel window beyond Pending.
