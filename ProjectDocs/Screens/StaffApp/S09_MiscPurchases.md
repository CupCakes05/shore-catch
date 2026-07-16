# S09 — Miscellaneous Purchases (Requirement #8)

**App:** Staff (Flutter/Android)

> ⚠️ **Permission-gated screen.** Visible only when the staff member has `record_misc_purchase`.

## Purpose
Allow staff to record miscellaneous operational purchases (e.g., plastic bags, packaging materials, ice) for expense tracking. These are separate from product inventory and customer orders.

## Users
Authenticated Staff with `record_misc_purchase`.

## Navigation
- **From:** Menu / bottom nav (only if permitted).
- **To:** back to Order List.

## Key UI Elements
- **"Record Purchase" form:**
  - Item Name (text, required) — e.g., "Plastic bags"
  - Quantity (number, required) — e.g., 100
  - Cost / Amount (₹, required) — e.g., 1000
  - Receipt/Image upload (optional, camera/gallery)
  - Purchase Date (date picker, defaults to today)
  - Notes (optional text)
  - "Save" button.
- **Purchase history list** (staff's own records):
  - Each entry: date, item name, quantity, cost, receipt indicator (📎 if attached).
  - Scrollable list with date grouping.
  - Pull-to-refresh.

## States
- **Loading:** saving entry; loading history.
- **Empty:** no purchases recorded yet → "Record your first purchase" CTA.
- **Error:** validation errors inline; upload failure; network → retry.
- **Success:** "Purchase recorded" toast; entry appears in history.

## Actions
- Fill form; upload receipt image (optional); save (`POST /misc-purchases`).
- View past purchases (`GET /misc-purchases?staff=me`).
- (Optional) Tap a past entry to view details / receipt image.

## Business Rules
- Misc purchases are **operational expenses** — they do NOT appear in order/sales reports but are tracked separately for Admin in reporting (A11 Misc Purchases tab).
- Staff can only view their own misc purchases.
- Admin sees all misc purchases across all staff in reporting.
- Purchase is attributed to the staff member and their active location (if multiple locations assigned, select which one or default to primary).

## Validation / Permissions
- Requires `record_misc_purchase` permission.
- Item name required; quantity > 0; cost > 0.
- Image upload: size/type limits.

## Responsive / Accessibility
- Labeled fields; image upload accessible; clear list with dates; amounts formatted with ₹.

## Dependencies
- `/misc-purchases`, `/uploads`.
