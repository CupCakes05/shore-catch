# A11 — Sales Reports | Muciriz Admin Panel

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Next.js web. Fixed left sidebar + top bar.

## Screen
**App:** Admin Panel (Next.js, web)
**Purpose:** Revenue analysis with 4 tabs: Sales overview, Profit/Loss, Staff Performance, and Miscellaneous Purchases tracking.

## UI Elements
- Sidebar + top bar (standard layout)
- Page title: "Reports"
- **Location selector** dropdown (top-left): "All Locations" / specific
- **Date range filter** (top-right): Today | This Week | This Month | Custom (date pickers)
- **4 Tabs:** Sales | Profit/Loss | Staff Performance | Misc Purchases

### Sales Tab
- **Total Sales** large figure: "₹2,45,000" (teal, large)
- **Breakdown table:** Location | Orders | Total Sales (₹) | GPay Collected | Cash Collected
  - e.g., "Ernakulam | 98 | ₹1,65,000 | ₹85,000 | ₹80,000"
- **GST Summary:** "Total GST Collected: ₹12,380" with rate breakdown (5%: ₹8,200, 18%: ₹4,180)
- (Optional) Simple bar chart: daily sales over the selected period

### Profit/Loss Tab
- **3 KPI cards:**
  - Total Sale: "₹2,45,000" (teal card)
  - Total Expense: "₹1,80,000" (gray card) — "Purchases: ₹1,65,000 + Misc: ₹15,000"
  - **Net Profit: "₹65,000"** (green card with ↑ arrow) or **Net Loss** (red card with ↓)
- Formula note (muted): "Profit = Total Sale − Total Expense (Purchases + Misc Purchases)"
- Filterable by location + date range
- If no expense data: "No expense data recorded yet. Record purchases in Inventory to see profit/loss." with link to A15

### Staff Performance Tab
- **Table:** Staff Name | Orders Handled | Avg Confirm Time | Avg Delivery Time | Avg Payment Time
  - e.g., "Rajesh Kumar | 45 | 12 min | 1h 30min | 8 min"
  - "Arun Nair | 38 | 18 min | 2h 15min | 5 min"
- Color indicators: green (fast), amber (average), red (slow) — with thresholds
- Location filter

### Misc Purchases Tab
- **Total Expenses** figure: "₹15,000"
- **Table:** Date | Staff | Item | Qty | Cost (₹) | Location
  - e.g., "15 Jan | Rajesh | Plastic bags | 100 | ₹500 | Ernakulam"
- Location filter, date filter
- These are operational expenses (separate from product sales)

## Interactions
- Switch tabs → content changes
- Change location/date range → all tabs refetch
- (Optional) Export CSV per tab
- Click staff name → expandable detail (optional)

## States
- **Loading:** Tab content skeletons
- **Empty:** "No data for this period" per tab
- **Error:** Retry per tab
- **Success:** Populated tables/charts/KPIs

## Navigation
- **From:** Sidebar, Dashboard
- **To:** Order Detail (A10) from drill-down links

## What to Show
- Profit/Loss tab active: 3 KPI cards (Total Sale ₹2,45,000 teal, Total Expense ₹1,80,000 gray, Net Profit ₹65,000 green). Date range showing "This Month". Formula note below. Location "All Locations" selected.
- Alternate: Staff Performance tab with 3 staff rows, performance metrics with color coding
