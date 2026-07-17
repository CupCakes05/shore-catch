# A10 — Order Detail | Muciriz Admin Panel

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Next.js web. Fixed left sidebar + top bar.

## Screen
**App:** Admin Panel (Next.js, web)
**Purpose:** Full read-only view of a single order with fulfillment timeline timestamps, GST details, and invoice download. Admin oversight — no direct actions.

## UI Elements
- Sidebar + top bar (standard layout)
- **Breadcrumb:** Orders > Order #M2026#00015
- **Header section:**
  - Order #: "M2026#00015" (large, bold)
  - Status chip (large, colored)
  - Pre-order badge (if applicable): "📅 Pre-order — Delivery: 20 Jan 2026"
  - "Placed By: Customer" or "Placed By: Staff: Rajesh"
- **Two-column layout:**
  - **Left column:**
    - **Customer card:** Name, WhatsApp, Address, Landmark, Service Location
    - **Delivery info:** Date + Time Slot (highlighted if future for pre-order)
    - **Payment:** Preference: "GPay on Delivery" | Collected: "GPay" (once completed)
  - **Right column:**
    - **"Download Invoice"** button (teal outline, PDF icon)
    - **Fulfillment Timeline** (vertical, card):
      - ✅ Order Placed — 15 Jan 2026, 9:30 AM
      - ✅ Order Confirmed — 15 Jan 2026, 9:45 AM — by Rajesh — "Confirmed in 15 min"
      - ✅ Delivered — 15 Jan 2026, 11:30 AM — by Rajesh — "Delivered in 1h 45min"
      - ✅ Payment Confirmed — 15 Jan 2026, 11:35 AM — by Rajesh — GPay — "Completed in 5 min"
      - (If cancelled): ❌ Cancelled — [timestamp] — by [Staff/Customer] — "Stock restored"
      - Time between transitions shown in muted text
- **Items table** (full-width below):
  - Columns: Product, Weight (G/N), Qty, Unit Price, GST Rate, GST Amount, Line Total
  - e.g., "Seer Fish | 1kg/850g | 2 | ₹680 | 5% | ₹64.76 | ₹1,360"
- **Order Totals** (right-aligned):
  - Subtotal (before GST): ₹1,912
  - GST 5%: ₹95.60 (back-calculated)
  - **Grand Total: ₹2,007** (bold)

## Interactions
- Download Invoice → PDF (includes Muciriz branding, invoice #, GST/non-GST split)
- Back (breadcrumb) → A09
- Read-only — no state-change actions for admin (staff performs fulfillment)

## States
- **Loading:** Skeleton layout
- **Error:** "Couldn't load order" + Retry
- **Success:** Full detail view

## Navigation
- **From:** Orders Dashboard (A09), Customer History (A12)
- **To:** Back to A09 or A12

## What to Show
- Completed order: all 4 timeline steps with timestamps and "by Rajesh" attribution, time-between calculations shown. Items table with 3 products (2 with GST, 1 without). Totals with GST breakdown. Pre-order badge visible. Download Invoice button. Two-column professional layout.
