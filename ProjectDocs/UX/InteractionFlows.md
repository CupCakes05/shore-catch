# Muciriz — Screen Interaction Flows (Step by Step)

> Detailed tap-by-tap interactions for the most interaction-heavy screens.

## Customer — Registration (C03)
```
Land on form → enter Name → enter WhatsApp Number (phone keyboard)
→ enter Delivery Address + Nearest Landmark → open Service Location dropdown → select
→ tap Register (enabled once valid) → button disables + loader
→ POST /auth/register (server enforces UNIQUE number)
   → OTP sent via WhatsApp Business API (primary) / AWS SNS (fallback) → OTP Verification (C04): enter code → Verify
       → wrong/expired → inline error + Resend (countdown)
       → valid → store cached session → navigate to Category Home → welcome toast
   → (logged-out re-entry of an already-verified number → signed in directly, NO OTP)
```

## Customer — Browse & Add to Cart (C05 → C06 → C07)
```
Category Home: offers banner rotates at top → tap a category card
→ Product List loads (skeleton → cards)
→ tap a product card → flips to reveal image (tap again → flips back)
→ tap quantity add → stepper appears (− 1 +) → cart badge increments (toast: "Added")
→ use category scroll strip to switch categories (slow scroll); back arrow → Category Home
→ tap cart icon → Cart/Checkout
```

## Customer — Checkout (C07)
```
Review items → adjust qty / remove (confirm on remove)
→ "Recommended for you" section loads → tap quick-add on a suggestion → item added, drops from list, cart badge updates
→ select Delivery Date (today or future for pre-order) → date picker
→ open Delivery Time Slot dropdown (per-location if set, else global, for selected date) → select
→ choose Payment: Gpay on Delivery | Cash on Delivery
  → if Gpay selected: show company UPI ID + "Pay via GPay" deep-link button (opens GPay app with amount pre-filled)
→ tap Place Order (enabled when cart≥1 + date + time + payment chosen) → loader
→ POST /orders (with deliveryDate, deliveryTimeId)
   → error → inline/toast + retry (cart preserved)
   → success → cart clears → Order Confirmation → download invoice (GST/non-GST split) → success toast
```

## Staff — Fulfill Order (S04)
```
Open order (Pending) → tap Order Confirm → confirm dialog → loader → status = Order Confirmed
→ deliver goods → tap Delivery Confirm → confirm dialog → status = Delivered
→ collect payment → tap Payment Confirm → choose Gpay/Cash → loader → status = Completed
   → each step: success toast; out-of-order/permission errors show message
```

## Admin — Add Product (A05)
```
Select Location → select Category → tap Add Product
→ fill Name, Gross Weight, Net Weight, Price → upload image (progress)
→ tap Save (validated: Net ≤ Gross, positives) → loader
   → error → inline field errors
   → success → product appears in table → success toast → form resets/closes
```

## Admin — Create Offer (A08)
```
Tap Create Offer → enter title/message → choose Target scope (All categories | a Category | a Product)
→ (if Category/Product) pick the target → enter discountPercent (e.g., 20%)
→ set validity start/end → choose Scope (Global | a location) → (optional) upload banner
→ Save (validated) → offer listed → active offers served to customer app on next fetch
   (per-location overrides global)
```

## Admin — Approve Staff-Submitted Product (A05)
```
Open Products → filter "Pending approval" → open a staff-submitted item
→ review fields/image → tap Approve (goes live if active) or Reject (stays hidden; staff notified)
```

## Staff — Submit Product for Approval (S05)
```
Select Category → fill Name/Gross/Net/Price → upload image
→ tap Submit for approval → "Pending approval" badge → awaits Admin review
```
