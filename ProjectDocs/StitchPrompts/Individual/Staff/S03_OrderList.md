# S03 — Order List | Muciriz Staff App

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Flutter Android.

## Screen
**App:** Staff (Flutter/Android)
**Purpose:** Staff home screen showing orders for assigned service locations. Core operational view for fulfillment.

## UI Elements
- **App bar:** "Orders" title (left), assigned location label as subtitle (e.g., "📍 Ernakulam"), profile icon (right → S06)
- **Status filter tabs** (horizontal scroll below app bar):
  - All | Pending | Confirmed | Delivered | Completed | Cancelled
  - Active tab: teal underline + bold text
- **Order cards** (vertical list, white cards with border):
  - Order number: "M2026#00015" (bold)
  - Customer name: "Priya Menon"
  - Delivery: "15 Jan 2026 • Morning 8-10 AM"
  - Items + Total: "3 items • ₹2,007"
  - Payment preference: GPay icon or Cash icon (small)
  - **Status chip** (right side): Pending (amber), Confirmed (blue), etc.
  - **Pre-order badge** (if delivery date is future): small teal outline chip "Pre-order"
  - **"Placed by: Staff"** indicator (small text, if applicable)
- **Pull-to-refresh**
- **FAB (Floating Action Button)** (bottom-right, teal):
  - Expands to show permission-gated options:
    - "➕ Add Product" (if `add_products`)
    - "📦 Update Stock" (if `update_stock`)
    - "🧾 Misc Purchase" (if `record_misc_purchase`)
    - "🏪 Shop Sale" (if `place_shop_sale`)
  - Hidden entirely if user has none of these permissions

## Interactions
- Tap order card → S04 (Order Detail)
- Tap status tab → filters list by that status
- Pull-to-refresh → reloads orders
- Tap FAB → expands/collapses options
- Tap FAB option → navigates to S05/S09/S10
- Tap profile icon → S06
- Orders sorted: pending/oldest first

## States
- **Loading:** 3-4 skeleton order cards (shimmer)
- **Empty:** Illustration (inbox) + "No orders right now" for the current filter
- **Error:** "Couldn't load orders" + Retry button
- **Success:** Order cards list with filter tabs

## Navigation
- **From:** Splash (S01) auto-session, Login (S02)
- **To:** S04 (Order Detail), S05 (Add Product), S06 (Profile), S09 (Misc), S10 (Shop Sale)

## What to Show
- Success state: 4 order cards — one Pending (amber chip), one Confirmed (blue) with pre-order badge "Pre-order", one Delivered (teal), one showing "Placed by: Staff". Filter tabs showing "All" active. FAB expanded showing 3 options (Add Product, Misc Purchase, Shop Sale). Location "Ernakulam" in subtitle.
