# C07 — Cart / Checkout

**App:** Customer (Flutter/Android)

## Purpose
Review selected products, adjust quantities, add/remove items, choose delivery date + time slot and payment method, view GPay/UPI ID, then place the order. Central conversion screen. Supports pre-orders (future dates).

## Users
Authenticated Customer.

## Navigation
- **From:** Category Home (C05) / Product List (C06) via cart icon.
- **To:**
  - Order Confirmation (C08) on successful checkout.
  - Product List / Category Home to continue shopping.

## Key UI Elements
- **Cart item list:** each row shows product name, weight (gross/net), unit price, GST indicator (if applicable, show "incl. GST X%"), quantity stepper (add more / reduce), line total, and a remove control.
- **Out of stock indicator:** if any item is out of stock for today, show an alert banner: "Some items are out of stock for today. You can pre-order for a future date."
- "Add more products" affordance (back to browsing).
- **"Recommended for you / You may also like" section (Confirmed):** horizontal list of recommended products resolved for the cart (product-level + category-level), each with a **quick-add** button. Excludes items already in the cart. Source: `GET /cart/recommendations`.
- **Order summary:**
  - Total (sum of unit prices × quantities — prices are already GST-inclusive).
  - GST breakdown (back-calculated, grouped by rate, e.g., "GST 5%: ₹X", "GST 18%: ₹Y") — informational.
  - Subtotal (before GST, back-calculated: total minus GST).
  - (Offer discount application to totals is a pending sub-decision linked to Q1; if confirmed, show a discount line.)
- **Delivery Date picker** — defaults to today (if slots available). Shows available future dates for pre-orders. If today's slots are exhausted or items are out of stock, defaults to next available date.
- **Delivery Time Slot dropdown** — options for the selected date, from `/delivery-times` (per-location if set, else global — Q8). Required.
- **Payment method selector** — exactly two options: **Gpay on Delivery**, **Cash on Delivery**. Required.
- **GPay/UPI ID display (Requirement #5):** When "Gpay on Delivery" is selected, show the company's UPI ID (from `SystemConfig.gpay_upi_id`). Include a **"Pay via GPay" button** that deep-links to GPay app with amount pre-filled (UPI intent URI: `upi://pay?pa={upiId}&pn={name}&am={total}&cu=INR`). Note: "Staff will confirm payment on delivery."
- **Place Order** button (disabled until valid).

## States
- **Empty cart:** dedicated empty state ("Your cart is empty") + CTA to browse; checkout blocked; recommendations section hidden.
- **No recommendations:** the "Recommended for you" section is simply hidden (has its own loading state while fetching).
- **Pre-order mode:** when delivery date is future, show a "Pre-order" badge/label on the Place Order button and confirmation.
- **Loading:** while placing order (button loader); delivery-time dropdown has its own loading/empty state.
- **Error:**
  - Item no longer available / price changed on server (409) → inform and update cart.
  - Item out of stock for selected date → suggest alternate date or pre-order.
  - Missing delivery date/time or payment method → inline prompts.
  - GPay intent fails (no app installed) → toast "No UPI app found. Pay manually on delivery."
  - Network/server error → toast/banner with retry.
- **Success:** navigate to Order Confirmation (C08).

## Actions
- Increase/decrease quantity; remove item.
- **Quick-add a recommended product** to the cart (cross-sell).
- Add more products (navigate back).
- Select delivery date (today or future).
- Select delivery time slot (for selected date).
- Select payment method.
- Tap "Pay via GPay" (UPI deep-link — optional convenience, not required for checkout).
- Place Order → `POST /orders` (includes deliveryDate + deliveryTimeId).

## Business Rules
- Cart must have ≥ 1 item to checkout.
- Delivery date + time slot + payment method required.
- Server recomputes totals authoritatively (including GST); on mismatch, show updated totals before finalizing.
- On success: order created (Pending), invoice generated (with GST split), cart cleared.
- If deliveryDate is future → order marked as pre-order (`isPreOrder = true`).
- GPay deep-link is informational/convenience only; does NOT confirm payment (staff does that).
- GST calculation (back-calc from inclusive): for each GST-applicable item, `basePrice = unitPrice / (1 + gstPercent/100)`, `gstAmount = (unitPrice - basePrice) × qty`.

## Validation / Permissions
- Authenticated customer; quantities positive; required selections present.

## Responsive / Accessibility
- Clear remove/adjust controls with labels; date picker and dropdown accessible; radio/segmented payment selector accessible; total updates announced; sticky Place Order button for easy reach; GPay button clearly labeled as optional.

## Dependencies
- Cart state, `/delivery-times?location={id}&date={date}`, `/cart/recommendations`, `/orders`, `/config` (for GPay UPI ID).
