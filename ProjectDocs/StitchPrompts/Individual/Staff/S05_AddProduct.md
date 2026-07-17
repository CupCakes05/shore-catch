# S05 — Add Product & Stock Update | Muciriz Staff App

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Flutter Android.

## Screen
**App:** Staff (Flutter/Android)
**Purpose:** Permission-gated screen for authorized staff to add/edit products (subject to admin approval) and update stock counts.

## UI Elements

### Tab/Segment: "Add Product" | "Update Stock"

### Add Product Tab (requires `add_products`):
- **Category selector** (dropdown, limited to staff's allowed categories): "Fresh Fish"
- **Product form** (outlined inputs):
  - Product Name (text, required): e.g., "Pomfret / Avoli"
  - Gross Weight (number + unit selector g/kg): "500g"
  - Net Weight (number + unit, must ≤ Gross): "420g"
  - Price (₹, number, required): "₹380"
  - **GST Applicable** (toggle switch, teal when on)
  - **GST Percentage** (number field, shown when toggle on): "5" (%)
- **Image upload** button: camera/gallery picker. Shows image preview thumbnail when uploaded.
- **"Submit for Approval"** button (teal filled, full-width)
- **My Submissions** section below:
  - List of staff's submitted products with badges:
    - "Pending Approval" (amber chip)
    - "Approved" (green chip)
    - "Rejected" (red chip) — with "Revise" button
  - Each shows: product name, category, date submitted

### Update Stock Tab (requires `update_stock`):
- **Location selector** (dropdown, staff's assigned locations): "Ernakulam"
- **Product list** with stock:
  - Each row: product name, category, current stock count (number), "Edit" icon
  - Tap Edit → inline number input appears → "Save" checkmark button
- Confirmation toast: "Stock updated for Pomfret at Ernakulam"
- Change logged with timestamp

## Interactions
- Product form: fill → upload image → Submit for Approval → POST /products → toast "Submitted for approval", form resets
- Stock: select location → see products + counts → tap edit → enter new count → save → PATCH /stock → toast
- Rejected product: tap "Revise" → form pre-fills for editing → re-submit
- Validation: Net ≤ Gross, price > 0, GST% valid (5/12/18/28), name required

## States
- **Loading:** Saving product; uploading image (progress indicator); saving stock
- **Empty:** No submissions yet (product tab); no products at location (stock tab)
- **Error:** Validation errors inline (red text); upload failure toast; network → retry
- **Success:** "Submitted for approval" toast (product); "Stock updated" toast (stock)

## Navigation
- **From:** Order List (S03) FAB menu
- **To:** Back to S03

## What to Show
- Add Product tab active: form filled with "Pomfret / Avoli", 500g/420g, ₹380, GST toggle ON showing 5%, image preview visible (fish photo). "Submit for Approval" button enabled. Below: 2 submissions — one "Pending Approval" (amber), one "Approved" (green).
- Alternate: Stock Update tab with product list showing stock counts, one row in edit mode with number input
