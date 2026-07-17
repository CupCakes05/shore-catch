# C09 — Order History | Muciriz Customer App

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Flutter Android.

## Screen
**App:** Customer (Flutter/Android)
**Purpose:** List the customer's active and past orders with status chips for quick overview.

## UI Elements
- App bar: "My Orders" title
- Optional segmented control (top): "Active" | "Past" (toggle filter)
- **Order cards** (vertical list, white cards with subtle border):
  - Order number (bold, e.g., "M2026#00015")
  - Date placed (muted, e.g., "15 Jan 2026, 9:30 AM")
  - Item count + Total: "3 items • ₹2,007"
  - **Status chip** (colored pill):
    - Pending = amber bg + dark text
    - Order Confirmed = blue bg + white text
    - Delivered = teal bg + white text
    - Completed = green bg + white text
    - Cancelled = muted red bg + white text
  - Payment method icon (GPay/Cash)
  - Pre-order badge (small teal outline chip "Pre-order" if applicable)
- Pull-to-refresh indicator
- Bottom nav: Orders tab active (teal)

## Interactions
- Tap order card → Order Detail (C10)
- Toggle Active/Past → filters list
- Pull-to-refresh → reloads orders
- Bottom nav → navigate

## States
- **Loading:** 3-4 skeleton order cards (shimmer)
- **Empty:** Illustration (empty clipboard) + "You haven't placed any orders yet" + "Start Shopping" CTA → C05
- **Error:** "Couldn't load orders" + Retry button
- **Success:** Order cards list

## Navigation
- **From:** Bottom nav "Orders", Order Confirmation (C08)
- **To:** Order Detail (C10)

## What to Show
- Success state: 4 order cards — one Pending (amber), one Order Confirmed (blue), one Delivered with pre-order badge, one Completed (green). Realistic order numbers, dates, totals. Bottom nav with Orders tab active.
- Also: Empty state variant
