# S04 — Order Detail (Fulfillment) | Muciriz Staff App

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Flutter Android.

## Screen
**App:** Staff (Flutter/Android)
**Purpose:** Core working screen for fulfillment. View order details and perform state transitions (Confirm, Deliver, Payment) with timestamps. Cancel available before Completed.

## UI Elements
- **App bar:** "Order #M2026#00015" + back arrow
- **Order header:**
  - Large status chip (current state, colored)
  - Pre-order badge: "📅 Delivery: 20 Jan 2026" (teal chip, if applicable)
  - "Placed by: Customer" or "Placed by: Staff" (small text indicator)
- **Customer section** (card):
  - Name: "Priya Menon" (bold)
  - WhatsApp: "+91 98765 43210" + tap-to-call icon (📞) + WhatsApp icon (green)
  - Address: "Flat 4B, Sea View Apartments, MG Road"
  - Landmark: "Near Lulu Mall"
- **Delivery info:** Date + Time Slot (highlighted for future dates)
- **Items section** (card):
  - Table: Product | Qty | Price | GST | Total
  - e.g., "Seer Fish (1kg/850g) | 2 | ₹680 | 5% | ₹1,360"
  - Subtotal, GST total, **Order Total** (bold)
- **Payment:** "Preference: GPay on Delivery" (shown); "Collected: GPay" (shown once completed)
- **Fulfillment Timeline** (vertical with connecting line):
  - ✅ Order Placed — 15 Jan, 9:30 AM
  - ✅ Order Confirmed — 15 Jan, 9:45 AM — by Rajesh (staff)
  - ⏳ Delivered — pending
  - ⏳ Payment Confirmed — pending
  - Green dots + solid line for completed; gray dots + dashed for pending
- **Action buttons** (bottom, state-aware):
  - **"Confirm Order"** (teal filled, full-width) — shown when Pending + has permission
  - **"Confirm Delivery"** (teal filled) — shown when Order Confirmed
  - **"Confirm Payment"** (teal filled) — shown when Delivered → opens method selector (GPay/Cash)
  - **"Cancel Order"** (red outline, full-width, below main action) — shown before Completed
- All buttons are permission-gated (hidden if staff lacks permission)

## Interactions
- Tap Confirm Order → dialog "Confirm this order?" → confirm → PATCH state → toast "Order Confirmed", timeline updates, chip changes to Confirmed (blue)
- Tap Confirm Delivery → dialog → PATCH → toast "Delivery Confirmed"
- Tap Confirm Payment → bottom sheet: "Select payment method collected" with GPay/Cash options → confirm → PATCH → Completed (green chip)
- Tap Cancel → dialog "Cancel order? Stock will be restored for all items." → confirm → POST cancel → Cancelled chip, toast
- Tap phone icon → device dialer; tap WhatsApp → wa.me link
- Each action records timestamp + staffId

## States
- **Loading:** Detail fetch skeleton; action in-progress (button loader)
- **Error:** Out-of-order transition (409) → toast "Cannot perform this action"; network → retry
- **Success:** Timeline and chip update; success toast

## Navigation
- **From:** Order List (S03)
- **To:** Back to S03 (updated)

## What to Show
- Order in "Order Confirmed" state: timeline 2 steps complete with timestamps, "Confirm Delivery" button visible (teal), "Cancel Order" button visible (red outline below). Pre-order badge showing future date. Items with GST info. Payment preference GPay.
- Alternate: Payment method selector bottom sheet open with GPay/Cash options
