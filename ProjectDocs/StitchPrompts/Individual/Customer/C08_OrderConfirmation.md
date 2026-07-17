# C08 — Order Confirmation | Muciriz Customer App

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Flutter Android.

## Screen
**App:** Customer (Flutter/Android)
**Purpose:** Confirm order placed successfully, show summary with GST breakdown, and provide invoice download. Shows pre-order badge if applicable.

## UI Elements
- Centered success layout:
  - Large teal checkmark in circle (success illustration)
  - "Order Placed Successfully!" (bold heading, black)
  - Order number: "M2026#00015" (teal, bold)
- **Pre-order badge** (if delivery date is future): teal chip "📅 Pre-order confirmed for 20 Jan 2026"
- **Order Summary card:**
  - Items list: name, qty, line total per item
  - Delivery: date + time slot
  - Payment: method selected
  - Address: delivery address + landmark
  - Subtotal, GST breakdown (by rate), **Total**
- **"Download Invoice"** button (teal outline, PDF icon left) — opens/saves PDF
- **"View Order"** button (teal filled, full-width) → C10
- **"Continue Shopping"** text button (teal text, below) → C05

## Interactions
- Download Invoice → downloads PDF with Muciriz branding, invoice # M2026#00015, GST/non-GST split
- View Order → Order Detail (C10)
- Continue Shopping → Category Home (C05)

## States
- **Loading:** Brief (fetching invoice URL if not already returned)
- **Error:** Invoice download failure → "Couldn't download. Retry." (order is still confirmed)
- **Success:** Default confirmed state with all elements

## Navigation
- **From:** Cart/Checkout (C07) on success
- **To:** Order Detail (C10), Category Home (C05)

## What to Show
- Success state: checkmark, order number "M2026#00015", pre-order badge showing "Pre-order confirmed for 20 Jan 2026", order summary with 3 items, GST breakdown, total ₹2,007, Download Invoice button, View Order button
- Clean, celebratory but minimal design
