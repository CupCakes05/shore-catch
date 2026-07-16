# Feature — Staff Miscellaneous Purchases (Requirement #8)

**Overview:** Staff can record miscellaneous operational purchases (packaging materials, ice, bags, etc.) for expense tracking, separate from product sales and customer orders.

**Business purpose:** Track operational expenses that staff incur in the field. Provides visibility into non-product costs, supports profit/loss analysis, and ensures accountability for business expenses.

**Users:** Staff with `record_misc_purchase` permission (record), Admin (view in reporting).

**Entry points:**
- Staff: S09 (Misc Purchases screen).
- Admin: Sales Reports (A11) — Misc Purchases tab.

**Dependencies:** Staff entity (who recorded), Service Location (context), File storage (receipt images).

**Data used:**
- `MiscPurchase`: staffId, locationId, itemName, quantity, cost, receiptImageUrl, notes, purchaseDate, createdAt.

**Staff flow:**
```
Staff opens S09 → fills form (item name, qty, cost, optional receipt image, optional notes)
  → POST /misc-purchases
  → Entry recorded → appears in staff's purchase history
  → Admin sees it in A11 reporting
```

**What it tracks:**
- Item name (e.g., "Plastic bags", "Ice", "Packaging tape")
- Quantity (e.g., 100 items, 5 bags)
- Total cost (₹, e.g., ₹1000)
- Optional receipt/image (photo of receipt/bill)
- Purchase date
- Staff who recorded + location context

**Reporting (Admin):**
- A11 has a "Misc Purchases" tab showing all staff entries.
- Filters: date range, location, staff member.
- Totals: sum of all misc purchase costs for the period.
- These are **operational expenses** — separate from product sales revenue.
- Can be factored into profit/loss analysis as overhead costs.

**Validation:**
- Item name required (non-empty).
- Quantity > 0.
- Cost > 0 (₹).
- Receipt image: optional; size/type limits if uploaded.
- Purchase date: required (defaults to today).

**States:**
- Staff: form → submitting → success/error; history list.
- Admin: table with filters; empty state if no records.

**Edge cases:**
- Staff records purchase for wrong location → location field helps; Admin can filter/review.
- Large receipt image → size limit + compression.
- Staff without permission → screen hidden; endpoint rejects.
- Retroactive entry (purchase date in the past) → allowed.

**Permissions:** Staff requires `record_misc_purchase`. Admin views all.

**Notifications:** none.

**Errors:** upload failure (receipt image) → retry; network errors → retry.

**Future considerations:** approval workflow for high-value purchases; budget limits per staff; expense categories; integration with accounting; monthly expense summaries; receipt OCR.
