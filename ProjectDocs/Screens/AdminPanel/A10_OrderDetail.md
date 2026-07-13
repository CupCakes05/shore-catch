# A10 — Order Detail

**App:** Admin Panel (Next.js, web)

## Purpose
Full read view of a single order for Admin oversight, including items, customer, fulfillment timeline, payment, and invoice.

## Users
Main Admin.

## Navigation
- **From:** Orders Dashboard (A09), Customer History (A12).
- **To:** back; download invoice.

## Key UI Elements
- Order header: number, date/time, status chip.
- Customer: name, WhatsApp number, delivery address + landmark, service location.
- Itemized products (snapshot): name, weight, qty, unit price, line total; total.
- Delivery time; payment preference and collected method.
- **Fulfillment timeline:** who confirmed order/delivery/payment and when (staff audit).
- **Download Invoice** button.
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
- Snapshots preserve historical accuracy.

## Validation / Permissions
- Admin only.

## Responsive / Accessibility
- Accessible layout; timeline text+icon; invoice button labeled.

## Dependencies
- `/orders/{id}`, `/orders/{id}/invoice`.
