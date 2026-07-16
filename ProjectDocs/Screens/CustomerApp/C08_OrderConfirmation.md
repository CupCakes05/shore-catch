# C08 — Order Confirmation

**App:** Customer (Flutter/Android)

## Purpose
Confirm the order was placed successfully, show a summary, and let the customer **download the auto-generated invoice** (with GST/non-GST split).

## Users
Authenticated Customer.

## Navigation
- **From:** Cart/Checkout (C07) on success.
- **To:**
  - Order Detail (C10) / Order History (C09) via "View Order".
  - Category Home (C05) via "Continue Shopping".

## Key UI Elements
- Success illustration/checkmark + confirmation message with order number.
- **Pre-order badge** (if delivery date is future): "Pre-order confirmed for [date]".
- Order summary: items (name, qty, GST indicator, line total), subtotal, GST breakdown, total, delivery date + time slot, payment method, delivery address + landmark.
- **Download Invoice** button (opens/saves PDF with GST/non-GST split via `/orders/{id}/invoice`).
- "View Order" and "Continue Shopping" buttons.

## States
- **Loading:** brief while fetching final order/invoice URL (usually returned from checkout).
- **Error:** invoice not ready / download failure → retry download; order itself still confirmed.
- **Success:** default confirmed state.

## Actions
- Download invoice.
- View order → C10.
- Continue shopping → C05.

## Business Rules
- Order is in `Pending` state at this point (awaiting staff Order Confirm).
- Invoice reflects the order snapshot including GST details.
- Pre-orders show the scheduled delivery date prominently.

## Validation / Permissions
- Authenticated; can only see own order.

## Responsive / Accessibility
- Clear confirmation semantics; download button labeled; screen-reader announces success; pre-order status clearly communicated.

## Dependencies
- `/orders/{id}`, `/orders/{id}/invoice`.
