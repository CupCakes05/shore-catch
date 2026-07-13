# End-to-End Order Lifecycle

The central journey spanning all three apps: Customer browses and orders → Staff fulfills → Admin has visibility.

## Happy Path

```
CUSTOMER APP
1. Customer opens app → auto-logged-in → Category Home (C05), scoped to their Service Location.
2. Taps a category → Product List (C06). Flips cards to view images; taps quantity add on products.
3. Opens Cart (C07): reviews items, adds/removes, adjusts quantities.
4. Selects Delivery Time (Admin-managed dropdown).
5. Selects Payment: Gpay on Delivery OR Cash on Delivery.
6. Places order → Order created (state = Pending) → invoice auto-generated.
7. Order Confirmation (C08): downloads invoice; can view order.

BACKEND
8. Order stored with item snapshots, scoped to location; visible to staff of that location.

STAFF APP
9. Staff (assigned to that location) sees the order in Order List (S03) as Pending.
10. Opens Order Detail (S04):
    - Taps "Order Confirm" → state = Order Confirmed.
    - Delivers goods, taps "Delivery Confirm" → state = Delivered.
    - Collects payment, taps "Payment Confirm" → records Gpay/Cash → state = Completed.

CUSTOMER APP
11. Customer sees status update in Order History (C09) / Order Detail (C10).

ADMIN PANEL
12. Admin sees the order progress in Orders Dashboard (A09) (pending → delivered → completed).
13. Completed order contributes to Sales Reports (A11) (per confirmed inclusion rule).
14. Appears in Customer History (A12) for that customer.
```

## Alternate Paths
- **Empty cart:** customer can't checkout; empty-cart state shown (C07).
- **Recommended products at checkout:** customer quick-adds cross-sell items shown at C07 (Confirmed new requirement).
- **No delivery times configured:** checkout blocked; customer sees message (Admin must add global or per-location options in A06 — Q8).
- **Change of quantity/removal:** handled in cart before placing.
- **Customer cancels while Pending (Q10):** allowed until staff Order Confirm; order → `Cancelled`. After Order Confirmed, cancel is blocked.
- **Location change (Q12):** catalog rescopes; cart re-validated/cleared (C12 rule).

## Failure Scenarios
- **Network failure at checkout:** order not created; customer sees error + retry; cart preserved.
- **Price/availability changed server-side:** 409 → customer shown updated totals/items before finalizing.
- **Session expired mid-flow:** routed to registration/login; cart ideally preserved locally.
- **Out-of-order staff transition (e.g., Payment before Delivery):** rejected (409); staff sees message.
- **Staff lacks permission:** action hidden/disabled; server rejects if forced.
- **Order cancellation (Q10):** only the customer, only while `Pending`. Cancel on a non-Pending order is rejected (409).

## State Reference
`Pending → Order Confirmed → Delivered → Completed`, with `Cancelled` reachable **only from Pending** (customer). Sales count **Completed only** (Q7). See `02_BusinessRules.md` §7.
