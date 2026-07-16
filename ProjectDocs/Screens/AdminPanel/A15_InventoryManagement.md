# A15 — Inventory Management (Requirements #9, #10)

**App:** Admin Panel (Next.js, web)

## Purpose
Full stock/inventory management: view stock levels per product per location, record purchase data (cost price, supplier), and track profit/loss. Supports the business's financial oversight.

## Users
Main Admin.

## Navigation
- **From:** sidebar; Products (A05) stock link.
- **To:** Product detail; Sales Reports (A11) profit/loss tab.

## Key UI Elements

### Stock Levels Tab
- **Location selector** (filter by location or "All").
- **Table** of products with per-location stock: product name, category, location, current stock count, last updated (by whom, when), status indicator (In Stock / Low Stock / Out of Stock).
- **Inline stock edit:** click to update stock count directly.
- **Bulk stock update** (optional): upload or form to update multiple products at once.
- Filters: category, stock status (in stock / low / out), search by product name.
- Low stock threshold (configurable or default, e.g., < 5).

### Purchase Records Tab (Requirement #10)
- **"Record Purchase" button** → form/modal:
  - Product (searchable dropdown).
  - Location (dropdown).
  - Purchase Rate / Cost Price (₹ per unit).
  - Gross Weight (of purchase batch).
  - Net Weight (of purchase batch).
  - Quantity Purchased.
  - Supplier Name (optional).
  - Purchase Date.
- **Purchase history table:** product, location, purchase rate, qty, supplier, date, recorded by.
- Filters: product, location, date range.
- Pagination.

### Profit/Loss Summary
- Quick overview cards: Total Sale (Completed orders), Total Expense (purchases + misc), Net Profit/Loss for selected period.
- Link to detailed profit/loss in Sales Reports (A11).

## States
- **Loading:** table skeletons.
- **Empty:** no stock data / no purchase records → CTA to add.
- **Error:** Retry.
- **Success:** tables rendered.
- Form: validation + save states.

## Actions
- View/filter stock levels.
- Update stock count (`PATCH /stock/{productId}/{locationId}`).
- Record purchase entry (`POST /purchases`).
- View purchase history.
- Navigate to profit/loss report.

## Business Rules
- **Stock is per-location** — same product has independent stock at each assigned location.
- Stock count of 0 = "Out of Stock" → triggers pre-order flow for customers at that location.
- Purchase records are the source of cost data for profit/loss calculations.
- **Profit/Loss (Simplified Q27) = Total Sale − Total Expense.** Total Expense includes all purchase records + all misc purchases. No per-product per-batch costing needed.
- **Stock is decremented on order placement (Q26)**, not on completion. Restored on cancellation.
- Stock updates are audit-logged (who, when, old→new).
- Purchase records are immutable once created (corrections via new entries).

## Validation / Permissions
- Admin only.
- Stock count ≥ 0; purchase rate > 0; quantity > 0; product and location required.

## Responsive / Accessibility
- Accessible tables; inline edit with keyboard support; clear status indicators (color + text); forms with labeled fields.

## Dependencies
- `/stock`, `/purchases`, `/products`, `/locations`, `/reports/profit-loss`.
