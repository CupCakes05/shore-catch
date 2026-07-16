# A10 — Order Detail

**App:** Admin Panel (Next.js, web)

## Purpose
Full read view of a single order for Admin oversight, including items (with GST details), customer, fulfillment timeline with **timestamps**, payment, and invoice.

## Users
Main Admin.

## Navigation
- **From:** Orders Dashboard (A09), Customer History (A12).
- **To:** back; download invoice.

## Key UI Elements
- Order header: number, date/time, status chip, **pre-order badge** (if applicable), **"Placed by: Staff / Customer"** indicator.
- Customer: name, WhatsApp number, delivery address + landmark, service location.
- **Delivery date + time slot** (prominently shown; future date highlighted for pre-orders).
- Itemized products (snapshot): name, weight, qty, unit price, **GST (rate + amount if applicable)**, line total; subtotal, **GST total**, order total.
- Payment preference and collected method.
- **Fulfillment timeline with timestamps (Requirement #2):**
  - Order Placed: date/time
  - Order Confirmed: date/time + staff name
  - Delivered: date/time + staff name
  - Payment Confirmed: date/time + staff name + method (Gpay/Cash)
  - Time between transitions shown (e.g., "Confirmed in 15 min", "Delivered in 2h")
- **Download Invoice** button (PDF with GST/non-GST split).
- (Optional) Admin override actions — only if explicitly requested (not default).

## States
- **Loading:** skeleton.
- **Error:** Retry.
- **Success:** full detail.

## Actions
- Download invoice (`/orders/{id}/invoice`).
- Back.
- (Optional) Override — if enabled.

## Business Rules
- Primarily read-only oversight.
- Snapshots preserve historical accuracy (including GST snapshot at time of purchase).
- State transition timestamps enable staff performance tracking.

## Validation / Permissions
- Admin only.

## Responsive / Accessibility
- Accessible layout; timeline text+icon; invoice button labeled; timestamps clearly formatted.

## Dependencies
- `/orders/{id}`, `/orders/{id}/invoice`.
