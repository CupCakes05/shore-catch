# Staff Fulfillment Flow

## Happy Path
```
Splash (S01) → session check
→ valid → Order List (S03)
→ invalid → Login (S02): staff enters credentials (Admin-created, location-tied)
Order List (S03): orders for assigned location (filtered by allowed categories if restricted)
→ Tap order → Order Detail (S04)
   1. Order Confirm  (perm confirm_order)  → Order Confirmed
   2. Delivery Confirm (perm confirm_delivery) → Delivered
   3. Payment Confirm (perm confirm_payment) → choose Gpay/Cash → Completed
Each action updates status; customer + admin see the change.
```

## Permission Variations
- `restrict_to_categories`: staff only sees orders/products for allowed categories.
- `add_products`: staff can add/edit products (S05) for allowed categories — **all changes require Admin approval before going live (Q11)**.
- Missing a confirm permission: that action is hidden/disabled.

## Cancellation Interaction (Q10)
- A `Pending` order the staff hasn't confirmed yet may be **cancelled by the customer**; it then disappears from the actionable queue (shows as `Cancelled`).
- Once the staff performs **Order Confirm**, the customer can no longer cancel.

## Login (Q14/Q15)
- Staff log in with **phone + password**. The same phone may also be a customer account (Customer App) — identities are independent.
- Staff cannot self-reset passwords; Admin resets them (Q15).

## Failure Scenarios
- **Wrong credentials / deactivated account:** login error.
- **Permission revoked mid-session:** 403 → refresh permissions or re-login (S08).
- **Out-of-order transition:** 409 → message.
- **Network failure during a confirm:** action not applied; retry; transitions idempotent to avoid double-apply.
- **Session expired:** routed to Login.
