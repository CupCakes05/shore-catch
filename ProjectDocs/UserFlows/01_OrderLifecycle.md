# End-to-End Order Lifecycle

The central journey spanning all three apps: Customer browses and orders → Staff fulfills → Admin has visibility.

## Happy Path

```
CUSTOMER APP
1. Customer opens app → auto-logged-in → Category Home (C05), scoped to their Service Location.
2. Taps a category → Product List (C06). Flips cards to view images; taps quantity add on products.
   - Out-of-stock products show "Out of Stock — Pre-order" badge; can still add (pre-order).
3. Opens Cart (C07): reviews items, adds/removes, adjusts quantities.
   - Sees GST breakdown (subtotal, GST by rate, total).
   - Sees recommended products section; can quick-add.
4. Selects Delivery Date (today or future — pre-order) + Time Slot.
5. Selects Payment: Gpay on Delivery OR Cash on Delivery.
   - If Gpay selected: sees company UPI ID + "Pay via GPay" deep-link button.
6. Places order → Order created (state = Pending, deliveryDate, isPreOrder flag) → invoice auto-generated (with GST/non-GST split).
7. Order Confirmation (C08): downloads invoice; can view order.

BACKEND
8. Order stored with item snapshots (incl. GST), scoped to location; visible to staff of that location.
   - State transition timestamps prepared (null until staff acts).

STAFF APP
9. Staff (assigned to that location) sees the order in Order List (S03) as Pending.
   - Pre-orders shown with delivery date badge.
10. Opens Order Detail (S04):
    - Taps "Order Confirm" → state = Order Confirmed; records orderConfirmedAt + orderConfirmedBy.
    - Delivers goods, taps "Delivery Confirm" → state = Delivered; records deliveredAt + deliveredBy.
    - Collects payment, taps "Payment Confirm" → records Gpay/Cash → state = Completed; records paymentConfirmedAt + paymentConfirmedBy.

CUSTOMER APP
11. Customer sees status update in Order History (C09) / Order Detail (C10).

ADMIN PANEL
12. Admin sees the order progress in Orders Dashboard (A09) (pending → delivered → completed) with timestamps.
13. Completed order contributes to Sales Reports (A11) (per confirmed inclusion rule) and Profit/Loss analysis.
14. Appears in Customer History (A12) for that customer.
15. Staff performance (transition times) tracked in A11 Staff Performance tab.
```

## Alternate Paths
- **Empty cart:** customer can't checkout; empty-cart state shown (C07).
- **Recommended products at checkout:** customer quick-adds cross-sell items shown at C07.
- **Pre-order (Requirement #3):** customer orders for a future date (triggered by out-of-stock, exhausted slots, or proactive choice). Order follows same lifecycle, fulfilled on scheduled date.
- **Staff places Shop Sale (Requirement #12, Q28):** staff creates a quick counter sale in S10. No customer account needed. Immediately completed. Invoice generated.
- **No delivery times configured:** checkout blocked; customer sees message (Admin must add global or per-location options in A06 — Q8).
- **Change of quantity/removal:** handled in cart before placing.
- **Customer cancels while Pending (Q10):** allowed until staff Order Confirm; order → `Cancelled`; **stock restored**. After Order Confirmed, customer cancel is blocked.
- **Staff cancels (Q26):** staff can cancel in any state before Completed; order → `Cancelled`; **stock restored**.
- **Location change (Q12):** catalog rescopes; cart re-validated/cleared (C12 rule).
- **GPay deep-link (Requirement #5):** customer taps "Pay via GPay" → opens GPay app with amount pre-filled. If no UPI app → toast. Payment still confirmed by staff on delivery.

## Failure Scenarios
- **Network failure at checkout:** order not created; customer sees error + retry; cart preserved.
- **Price/availability/GST changed server-side:** 409 → customer shown updated totals/items before finalizing.
- **Session expired mid-flow:** routed to registration/login; cart ideally preserved locally.
- **Out-of-order staff transition (e.g., Payment before Delivery):** rejected (409); staff sees message.
- **Staff lacks permission:** action hidden/disabled; server rejects if forced.
- **Order cancellation (Q10):** only the customer, only while `Pending`. Cancel on a non-Pending order (by customer) is rejected (409). **Staff can cancel before Completed.**
- **GPay intent fails:** toast "No UPI app found"; checkout still proceeds normally.
- **All today's slots gone:** auto-suggest pre-order for next available date.

## State Reference
`Pending → Order Confirmed → Delivered → Completed`, with `Cancelled` reachable from Pending (customer) or any pre-Completed state (staff). **Stock decremented on placement, restored on cancel (Q26).** Sales count **Completed only** (Q7). Each transition records timestamp + staff (Requirement #2). See `02_BusinessRules.md` §7.
