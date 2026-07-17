# S09 — Miscellaneous Purchases | Muciriz Staff App

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Flutter Android.

## Screen
**App:** Staff (Flutter/Android)
**Purpose:** Permission-gated screen for staff to record operational expenses (plastic bags, ice, packaging). Separate from product inventory and customer orders.

## UI Elements
- App bar: "Misc Purchases" + back arrow
- **Record Purchase form** (top section, card):
  - Item Name (text input, required): e.g., "Plastic bags"
  - Quantity (number input, required): e.g., "100"
  - Cost / Amount (₹, number input, required): e.g., "₹500"
  - Receipt/Image upload (optional): camera/gallery button with preview if uploaded (📎 icon)
  - Purchase Date (date picker, defaults to today)
  - Notes (optional, multiline text): e.g., "For packing fish orders"
  - **"Save"** button (teal filled, full-width)
- **Purchase History** (below form, scrollable list):
  - Section header: "Recent Purchases"
  - Date-grouped entries:
    - Date header: "Today — 15 Jan 2026"
    - Rows: Item name, qty, cost (₹), receipt indicator (📎 if attached)
    - e.g., "Plastic bags • 100 pcs • ₹500 📎"
    - e.g., "Ice blocks • 20 kg • ₹300"
  - Pull-to-refresh

## Interactions
- Fill form → optional receipt upload → Save → POST /misc-purchases → toast "Purchase recorded", form resets, entry appears at top of history
- Tap past entry → view details (item, qty, cost, notes, receipt image)
- Pull-to-refresh history
- Date picker defaults today, can select past dates

## States
- **Loading:** Saving entry; loading history
- **Empty history:** "Record your first purchase" CTA below empty form
- **Error:** Validation inline (red text: "Item name required", "Amount must be > 0"); upload failure; network → retry toast
- **Success:** "Purchase recorded" toast; new entry appears in history

## Navigation
- **From:** Order List (S03) FAB menu (requires `record_misc_purchase`)
- **To:** Back to S03

## What to Show
- Form filled with "Plastic bags", qty 100, ₹500, today's date. History below showing 3 entries grouped by date: today (2 entries) and yesterday (1 entry). One entry showing 📎 receipt attached indicator. Clean form + list layout.
