# C07 — Cart / Checkout

**App:** Customer (Flutter/Android)

## Purpose
Review selected products, adjust quantities, add/remove items, choose delivery time and payment method, then place the order. Central conversion screen.

## Users
Authenticated Customer.

## Navigation
- **From:** Category Home (C05) / Product List (C06) via cart icon.
- **To:**
  - Order Confirmation (C08) on successful checkout.
  - Product List / Category Home to continue shopping.

## Key UI Elements
- **Cart item list:** each row shows product name, weight (gross/net), unit price, quantity stepper (add more / reduce), line total, and a remove control.
- "Add more products" affordance (back to browsing).
- **"Recommended for you / You may also like" section (Confirmed new requirement):** horizontal list of recommended products resolved for the cart (product-level + category-level), each with a **quick-add** button. Excludes items already in the cart. Source: `GET /cart/recommendations`.
- **Order summary:** subtotal, total. (Offer discount application to totals is a pending sub-decision linked to Q1; if confirmed, show a discount line.)
- **Delivery Time dropdown** — options from `/delivery-times` (per-location if set, else global — Q8). Required.
- **Payment method selector** — exactly two options: **Gpay on Delivery**, **Cash on Delivery**. Required. **Gpay is a manual personal transfer — NO UPI ID/QR is shown in-app (Q13).**
- **Place Order** button (disabled until valid).

## States
- **Empty cart:** dedicated empty state ("Your cart is empty") + CTA to browse; checkout blocked; recommendations section hidden.
- **No recommendations:** the "Recommended for you" section is simply hidden (has its own loading state while fetching).
- **Loading:** while placing order (button loader); delivery-time dropdown has its own loading/empty state.
- **Error:**
  - Item no longer available / price changed on server (409) → inform and update cart.
  - Missing delivery time or payment method → inline prompts.
  - Network/server error → toast/banner with retry.
- **Success:** navigate to Order Confirmation (C08).

## Actions
- Increase/decrease quantity; remove item.
- **Quick-add a recommended product** to the cart (cross-sell).
- Add more products (navigate back).
- Select delivery time.
- Select payment method.
- Place Order → `POST /orders`.

## Business Rules
- Cart must have ≥ 1 item to checkout.
- Delivery time + payment method required.
- Server recomputes totals authoritatively; on mismatch, show updated totals before finalizing.
- On success: order created (Pending), invoice generated, cart cleared.

## Validation / Permissions
- Authenticated customer; quantities positive; required selections present.

## Responsive / Accessibility
- Clear remove/adjust controls with labels; dropdown and radio/segmented payment selector accessible; total updates announced; sticky Place Order button for easy reach.

## Dependencies
- Cart state, `/delivery-times?location={id}`, `/cart/recommendations`, `/orders`.
