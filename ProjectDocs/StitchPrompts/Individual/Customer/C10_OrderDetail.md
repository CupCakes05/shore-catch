# C10 — Order Detail | Muciriz Customer App

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Flutter Android.

## Screen
**App:** Customer (Flutter/Android)
**Purpose:** Full order details with status timeline, items, GST info, invoice download, and cancel option (while Pending only).

## UI Elements
- App bar: "Order #M2026#00015" + back arrow
- **Status chip** (large, top-center): colored based on current status
- **Pre-order badge** (if applicable): "📅 Delivery: 20 Jan 2026" teal chip
- **Status timeline** (vertical, left-aligned with line):
  - ✅ Order Placed — "15 Jan 2026, 9:30 AM" (always complete)
  - ✅ Order Confirmed — "15 Jan 2026, 9:45 AM" (if reached, with timestamp)
  - ⏳ Delivered — pending (gray dot, dashed line)
  - ⏳ Completed — pending (gray dot, dashed line)
  - Green filled dots for completed steps, gray hollow for pending
- **Items section:**
  - Each: Product name, weight, qty, unit price, line total
  - GST indicator per item if applicable
- **Order Summary:**
  - Subtotal (before GST)
  - GST breakdown by rate
  - Total (bold)
- **Delivery info:** Date + Time Slot + Address + Landmark
- **Payment:** Method preference + actual collected (if completed)
- **"Download Invoice"** button (teal outline, bottom)
- **"Cancel Order"** button (red outline text, shown ONLY when status = Pending):
  - Hidden after Order Confirmed
  - On tap → confirm dialog: "Cancel this order? This cannot be undone."

## Interactions
- Download Invoice → opens/saves PDF
- Cancel Order (Pending only) → confirm dialog → POST /orders/{id}/cancel → status updates to Cancelled, success toast
- Back arrow → C09 (Order History)

## States
- **Loading:** Skeleton layout
- **Error:** "Couldn't load order" + Retry
- **Success:** Full detail with timeline
- **Cancel flow:** Confirm dialog → loading → success (status chip changes to Cancelled) / error

## Navigation
- **From:** Order History (C09), Order Confirmation (C08)
- **To:** Back to C09; invoice download

## What to Show
- Order in "Order Confirmed" state: timeline showing 2 steps complete (Placed ✅, Confirmed ✅) with timestamps, 2 pending (Delivered ⏳, Completed ⏳). Cancel button HIDDEN (past Pending). Pre-order badge visible. 3 items with GST info. Download Invoice button at bottom.
- Alternate: Order in "Pending" state showing Cancel button visible (red outline)
