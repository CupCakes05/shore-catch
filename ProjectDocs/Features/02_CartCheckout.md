# Feature — Cart & Checkout

**Overview:** Customers build a cart, choose a delivery date + time slot and payment method, view GPay/UPI ID, and place an order. Supports pre-orders for future dates and GST breakdown.

**Business purpose:** Core conversion — turns browsing into orders. Pre-order support ensures customers can always place orders even when stock is low or today's slots are gone.

**Users:** Customer. Staff (via S10 — Shop Sale).

**Entry points:** Product List quantity add (C06); Cart/Checkout (C07); Staff S10 (Shop Sale).

**Cross-sell (Confirmed):** C07 shows a "Recommended for you" section (product-level + category-level recommendations) with quick-add. See `08_RecommendedProducts.md`.

**GPay/UPI display (Requirement #5):** Company's UPI ID shown at checkout when "Gpay on Delivery" is selected. "Pay via GPay" button deep-links using UPI intent URI. Staff still confirms payment on delivery.

**Pre-order (Requirement #3):** Delivery selection is now date + time slot. If today's slots are exhausted or product is out of stock, customer is prompted to select a future date. Customers can also proactively pre-order.

**GST (Requirement #11):** Cart shows GST breakdown (subtotal, GST by rate, total with GST). Each line item shows GST indicator if applicable.

**Dependencies:** Location-scoped catalog; Delivery Times (Admin A06, global+per-location — Q8); at least one product; SystemConfig for GPay UPI ID.

**Data used:** Cart items (product refs + qty + GST info), DeliveryTime, deliveryDate, paymentMethod, Customer delivery snapshot; creates Order + OrderItems with GST snapshots.

**Validation:** cart ≥ 1 item; delivery date required; delivery time slot required; payment method (Gpay on Delivery | Cash on Delivery) required; server recomputes totals (including GST).

**States:** empty cart; loading during place-order; error (network / 409 price/availability / out of stock); success → confirmation; pre-order mode (future date selected).

**Edge cases:**
- Empty cart blocks checkout.
- No delivery times configured → checkout blocked + message.
- All time slots for today exhausted → auto-suggest future date (pre-order).
- Product out of stock → show indicator on product card, suggest pre-order.
- Item removed/price changed/GST rate changed server-side → 409 handling.
- Session expiry mid-checkout.
- Location change invalidates cart (clear/re-validate — Q12).
- GPay intent fails (no app installed) → fallback toast.
- GPay UPI ID not configured → GPay section hidden.

**Permissions:** Customer only; own cart. Staff with `place_shop_sale` via S10 (Shop Sale).

**Notifications:** order confirmation feedback; (future) push on status.

**Errors:** validation prompts, retry on network, updated-totals prompt on conflict, UPI intent failure toast.

**Future considerations:** functional offer discounts, saved carts, reorder, scheduled recurring orders, delivery fee, partial pre-order (mix of in-stock today + pre-order items).
