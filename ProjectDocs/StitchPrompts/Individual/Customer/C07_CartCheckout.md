# C07 — Cart / Checkout | Muciriz Customer App

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Flutter Android.

## Screen
**App:** Customer (Flutter/Android)
**Purpose:** Review cart items, select delivery date + time slot, choose payment method, view GST breakdown, see GPay UPI ID, and place order. Supports pre-orders (future dates).

## UI Elements

### Cart Items Section
- App bar: "Cart" title + item count (e.g., "Cart (3 items)")
- Each cart item row:
  - Product name (bold, e.g., "Seer Fish / Neymeen")
  - Weight info: "1 kg / 850g net"
  - Unit price: "₹680"
  - GST indicator (if applicable): small gray text "incl. 5% GST"
  - Quantity stepper: (−) [2] (+) in teal
  - Line total (right): "₹1,360"
  - Remove button (X icon, muted red, right corner) — triggers confirm dialog
- **Out of stock alert banner** (amber background, if applicable):
  - ⚠️ "Some items are out of stock today. Select a future delivery date to pre-order."

### Recommendations Section
- Section header: "You may also like" (muted, with horizontal rule)
- Horizontal scrollable cards (small):
  - Product image thumbnail, name, price, "Add" button (teal pill, small)
- Hidden if no recommendations available

### Order Summary Card
- White card with subtle border:
  - Subtotal (before GST): "₹1,890"
  - GST breakdown:
    - "GST 5%: ₹45"
    - "GST 18%: ₹72"
  - **Total** (bold, large): "₹2,007"

### Delivery Section
- **Delivery Date:** Calendar-style date picker (today highlighted in teal; future dates selectable; no limit). Shows "Pre-order" label when future date selected.
- **Time Slot:** Dropdown (e.g., "Morning 8-10 AM", "Afternoon 12-2 PM"). Has own loading/empty states.

### Payment Section
- **Payment Method:** Two radio options / segmented control:
  - "GPay on Delivery" (with GPay icon)
  - "Cash on Delivery" (with cash icon)
- **GPay UPI Display** (shown ONLY when GPay selected):
  - Card with UPI icon: "UPI ID: muciriz@okaxis"
  - "Pay via GPay" button (teal, with GPay icon) — deep-link to GPay app
  - Note (muted): "Staff will confirm payment on delivery"

### Place Order Button
- Sticky bottom: "Place Order" (teal filled, full-width). Shows "Place Pre-order" if future date.
- Disabled until: ≥1 item + date selected + time slot selected + payment selected

## Interactions
- Adjust quantities (stepper) → totals recalculate
- Remove item → confirm dialog "Remove Seer Fish from cart?" → remove → totals update
- Quick-add recommendation → item moves to cart, disappears from recommendations
- Select date → time slots reload for that date
- Select GPay → UPI section appears with company UPI ID
- "Pay via GPay" → UPI intent (upi://pay?pa={id}&pn=Muciriz&am={total}&cu=INR). If no app → toast "No UPI app found"
- Place Order → button loader → POST /orders → success → C08
- Server price mismatch (409) → "Prices updated" dialog, cart refreshes

## States
- **Empty cart:** Illustration "Your cart is empty" + "Browse Products" CTA button. No checkout section.
- **Loading:** Placing order (button loader); time slots dropdown shimmer
- **Error:** Price changed (409) → info + refresh; out of stock → suggest future date; network → toast + retry
- **Pre-order mode:** Future date selected → "Pre-order" badge on Place Order button, date highlighted in teal chip
- **Success:** Navigate to C08 (Order Confirmation)

## Navigation
- **From:** Category Home (C05), Product List (C06) via cart icon
- **To:** Order Confirmation (C08) on success; back to browse

## What to Show
- Main state: 3 items in cart (Seer Fish ₹680 x2, King Prawns ₹450 x1, Pomfret ₹520 x1), GST breakdown visible, delivery date showing today (Jan 15) selected in calendar, time slot "Morning 8-10 AM" selected, GPay selected with UPI ID "muciriz@okaxis" visible and "Pay via GPay" button, Place Order button enabled. Recommendations section with 2 suggested products.
- Alternate: Empty cart state with illustration
