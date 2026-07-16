# Staff Fulfillment Flow

## Happy Path
```
Splash (S01) → session check
→ valid → Order List (S03)
→ invalid → Login (S02): staff enters credentials (Admin-created, location(s)-tied)
Order List (S03): orders for assigned location(s) (filtered by allowed categories if restricted)
  - Pre-orders shown with delivery date badge
→ Tap order → Order Detail (S04)
   1. Order Confirm  (perm confirm_order)  → Order Confirmed; records timestamp + staffId
   2. Delivery Confirm (perm confirm_delivery) → Delivered; records timestamp + staffId
   3. Payment Confirm (perm confirm_payment) → choose Gpay/Cash → Completed; records timestamp + staffId
Each action updates status + records who/when; customer + admin see the change.
```

## Permission Variations
- `restrict_to_categories`: staff only sees orders/products for allowed categories.
- `add_products`: staff can add/edit products (S05) for allowed categories — **all changes require Admin approval before going live (Q11)**.
- `update_stock`: staff can update stock counts for products at assigned locations (S05).
- `record_misc_purchase`: staff can record operational expense entries (S09).
- `place_shop_sale`: staff can create Shop Sales — walk-in/counter billing (S10).
- Missing a confirm permission: that action is hidden/disabled.

## Multi-Location (Requirement #4)
- Staff can be assigned to multiple locations.
- Order List shows orders from ALL assigned locations (with location indicator).
- Staff can act on orders from any of their assigned locations.
- Stock updates scoped to assigned locations only.

## Pre-Order Handling (Requirement #3)
- Pre-orders (delivery date in future) appear in the order list with a delivery date badge.
- Staff fulfills on/near the scheduled delivery date.
- Same confirmation flow applies (Order Confirm → Delivery Confirm → Payment Confirm).

## Place Order for Customer → Shop Sale (Requirement #12, Q28)
```
Staff opens S10 (Shop Sale) → enters phone (optional) + name (optional)
→ Select products from assigned location catalog
→ Select payment method (Gpay/Cash — actual collection)
→ Complete Sale → order created (saleType=shopSale, state=Completed immediately)
→ Stock decremented; invoice generated
→ Success screen with invoice download
```
Note: No customer account created. No delivery date/time. Immediately completed.

## Cancellation Interaction (Q10, Q26)
- A `Pending` order the staff hasn't confirmed yet may be **cancelled by the customer**; it then disappears from the actionable queue (shows as `Cancelled`). **Stock is restored.**
- Once the staff performs **Order Confirm**, the customer can no longer cancel.
- **Staff can cancel orders in any state before Completed** using the Cancel button on S04. Stock is restored on staff cancel too.

## Login (Q14/Q15)
- Staff log in with **phone + password**. The same phone may also be a customer account (Customer App) — identities are independent.
- Staff cannot self-reset passwords; Admin resets them (Q15).

## Failure Scenarios
- **Wrong credentials / deactivated account:** login error.
- **Permission revoked mid-session:** 403 → refresh permissions or re-login (S08).
- **Out-of-order transition:** 409 → message.
- **Network failure during a confirm:** action not applied; retry; transitions idempotent to avoid double-apply.
- **Session expired:** routed to Login.
