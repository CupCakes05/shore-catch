# S10 — Shop Sale (Walk-in Billing) | Muciriz Staff App

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Flutter Android.

## Screen
**App:** Staff (Flutter/Android)
**Purpose:** Permission-gated quick billing for walk-in/counter customers. No customer account needed. Sale immediately completed. Stock decremented. Invoice generated.

## UI Elements — Multi-step flow:

### Step 1: Customer Info (Optional)
- Heading: "New Shop Sale"
- Phone Number field (optional, phone keyboard): label "Phone (optional)"
- Customer Name field (optional): label "Name (optional)"
- Helper text (muted): "Phone and name are optional — just for the invoice"
- "Continue" button (teal filled, full-width)
- "Skip" text link (below button) → goes directly to Step 2

### Step 2: Product Selection
- Heading: "Select Products"
- **Category chips/tabs** (horizontal scroll): "Fish" (active), "Vegetables", "Meat"
- **Product list** for selected category:
  - Each row: product name, price (₹), stock indicator
  - "Add" button (teal) → becomes stepper (− count +)
  - Out of stock items: muted with "Out of Stock" label
  - Low stock items: amber "Low: 3 left" indicator
- **Running cart summary bar** (sticky bottom):
  - "3 items • ₹1,650" + "Review →" button (teal filled)

### Step 3: Payment & Review
- Heading: "Review Sale"
- Order summary card:
  - Items: name, qty, price, GST indicator, line total
  - Subtotal (before GST)
  - GST breakdown by rate
  - **Total** (bold, large)
- Phone/Name (if provided): shown at top
- **Payment method** selector: "GPay" (radio) | "Cash" (radio) — required
- **"Complete Sale"** button (teal filled, full-width, large)

### Step 4: Confirmation
- Success checkmark (teal circle)
- "Sale Completed!" heading
- Sale number: "M2026#00042" (Muciriz invoice numbering)
- Total: "₹1,650"
- **"Download Invoice"** button (teal outline)
- **"New Sale"** button (teal filled, full-width) → resets to Step 1
- **"Back to Orders"** text link → S03

## Interactions
- Step 1: optionally enter phone/name → Continue (or Skip) → Step 2
- Step 2: browse categories, add products → Review → Step 3
- Step 3: select payment (GPay/Cash) → Complete Sale → POST /orders/shop-sale
- Step 4: Download Invoice (PDF, Muciriz branding, GST split); New Sale (reset); Back to Orders
- Sale is immediately Completed (no Pending → fulfillment cycle)
- Stock decremented for all items at staff's location

## States
- **Loading:** Placing sale (Step 3 → 4 transition, button loader)
- **Empty:** No products available (Step 2) — shouldn't happen normally
- **Error:** Sale creation failure → retry; network error toast
- **Success:** Confirmation screen with invoice download

## Navigation
- **From:** Order List (S03) FAB menu (requires `place_shop_sale`)
- **To:** Back to S03; or restart (New Sale)

## What to Show
- Step 2: "Select Products" with Fish category active, 3 products visible (one with stepper showing "2", one with "Add" button, one "Out of Stock" muted). Running summary bar at bottom "2 items • ₹1,360" with "Review →" button.
- Step 4: Confirmation with "Sale Completed!", sale number, Download Invoice and New Sale buttons.
