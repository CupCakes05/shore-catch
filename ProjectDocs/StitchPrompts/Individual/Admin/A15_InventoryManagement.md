# A15 — Inventory Management | Muciriz Admin Panel

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Next.js web. Fixed left sidebar + top bar.

## Screen
**App:** Admin Panel (Next.js, web)
**Purpose:** Full stock/inventory management with per-location stock levels, purchase records (cost data), and profit/loss overview.

## UI Elements
- Sidebar + top bar (standard layout)
- Page title: "Inventory"
- **3 Tabs:** Stock Levels | Purchase Records | Profit/Loss Summary

### Stock Levels Tab
- **Location selector** dropdown: "All" / specific location
- **Filters:** Category dropdown, Stock status (In Stock / Low / Out of Stock), Product search
- **Stock table:**
  - Columns: Product, Category, Location, Current Stock, Status, Last Updated (by whom, when)
  - Status indicators:
    - "In Stock" (green chip, e.g., stock=15)
    - "Low Stock" (amber chip, stock <5, e.g., stock=3)
    - "Out of Stock" (red chip, stock=0)
  - **Inline edit:** Click stock count → becomes editable number input → ✓ save / ✗ cancel
  - Last Updated: "Rajesh, 15 Jan 9:30 AM"
- (Optional) "Bulk Update" button

### Purchase Records Tab
- **"Record Purchase"** button (teal filled, top-right)
- **Purchases table:**
  - Columns: Product, Location, Rate (₹/unit), Qty, Gross Wt, Net Wt, Supplier, Date, Recorded By
  - e.g., "Seer Fish | Ernakulam | ₹500/unit | 30 | 35kg | 30kg | Harbour Fresh | 14 Jan | Admin"
- Filters: product, location, date range
- Pagination

**Record Purchase Modal:**
- Product (searchable dropdown, required)
- Location (dropdown, required)
- Purchase Rate / Cost Price (₹ per unit, required)
- Quantity (number, required)
- Gross Weight (optional, number + kg/g)
- Net Weight (optional)
- Supplier Name (optional text)
- Purchase Date (date picker, defaults today)
- Save / Cancel

### Profit/Loss Summary Tab
- **3 KPI cards:**
  - Total Sale: "₹2,45,000" (teal)
  - Total Expense: "₹1,80,000" (Purchases ₹1,65,000 + Misc ₹15,000) (gray)
  - Net Profit/Loss: "₹65,000" (green if profit, red if loss)
- "View Detailed Reports →" link → A11
- Filterable by location + date

## Interactions
- Stock Levels: inline edit count → save → PATCH /stock → audit logged (old→new, by whom, when)
- Record Purchase → modal → save → appears in table, contributes to profit/loss
- Switch tabs
- Filter/search
- Stock=0 triggers pre-order flow for customers at that location

## States
- **Loading:** Table skeletons per tab
- **Empty:** "No stock data" / "No purchases recorded" + CTA
- **Error:** Retry
- **Success:** Populated tables

## Navigation
- **From:** Sidebar, Products (A05) stock link
- **To:** Sales Reports (A11) via profit/loss link

## What to Show
- Stock Levels tab active: table with 5 products — "Seer Fish, Ernakulam, 15, In Stock (green)", "Pomfret, Ernakulam, 3, Low Stock (amber)", "King Prawns, Ernakulam, 0, Out of Stock (red)", "Tuna, Fort Kochi, 12, In Stock", "Sardine, Fort Kochi, 8, In Stock". One row showing inline edit mode (number input focused). Location filter "All" selected.
- Alternate: Record Purchase modal open with form fields
