# Feature — Cart & Checkout

**Overview:** Customers build a cart, choose a delivery time and payment method, and place an order.

**Business purpose:** Core conversion — turns browsing into orders.

**Users:** Customer.

**Entry points:** Product List quantity add (C06); Cart/Checkout (C07).

**Cross-sell (Confirmed new requirement):** C07 shows a "Recommended for you" section (product-level + category-level recommendations) with quick-add. See `08_RecommendedProducts.md`.

**Payment (Confirmed Q13):** Gpay on Delivery is a manual personal transfer — no UPI ID/QR shown in-app.

**Dependencies:** Location-scoped catalog; Delivery Times (Admin A06, global+per-location — Q8); at least one product.

**Data used:** Cart items (product refs + qty), DeliveryTime, paymentMethod, Customer delivery snapshot; creates Order + OrderItems.

**Validation:** cart ≥ 1 item; delivery time required; payment method (Gpay on Delivery | Cash on Delivery) required; server recomputes totals.

**States:** empty cart; loading during place-order; error (network / 409 price/availability); success → confirmation.

**Edge cases:**
- Empty cart blocks checkout.
- No delivery times configured → checkout blocked + message.
- Item removed/price changed server-side → 409 handling.
- Session expiry mid-checkout.
- Location change invalidates cart (clear/re-validate — Q12).

**Permissions:** Customer only; own cart.

**Notifications:** order confirmation feedback; (future) push on status.

**Errors:** validation prompts, retry on network, updated-totals prompt on conflict.

**Future considerations:** functional offer discounts, saved carts, reorder, scheduled recurring orders, delivery fee.
