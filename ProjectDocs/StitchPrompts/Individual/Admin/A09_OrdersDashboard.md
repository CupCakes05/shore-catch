# A09 — Orders Dashboard | Muciriz Admin Panel

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Next.js web. Fixed left sidebar + top bar.

## Screen
**App:** Admin Panel (Next.js, web)
**Purpose:** Monitor all orders with status overview, pre-order filtering, shop sale visibility, and staff cancel tracking.

## UI Elements
- Sidebar + top bar (standard layout, global location selector)
- Page title: "Orders"
- **Summary cards row** (5 colored cards):
  - Pending: amber (count, e.g., "12")
  - Confirmed: blue ("8")
  - Delivered: teal ("5")
  - Completed: green ("131")
  - Cancelled: muted red ("4")
  - Pre-orders: teal outline ("8 pre-orders pending")
- **Filters row:**
  - Status dropdown: "All" / Pending / Confirmed / Delivered / Completed / Cancelled
  - Date range picker: Today / This Week / This Month / Custom
  - Pre-order toggle: "Pre-orders only" (checkbox)
  - Search: "Search by order # or customer name"
- **Orders table:**
  - Columns: Order #, Customer, Location, Delivery Date, Time Slot, Total (₹), Payment (pref/collected), Status (chip), Staff, Placed By, Actions
  - Status chips (colored): Pending (amber), Confirmed (blue), Delivered (teal), Completed (green), Cancelled (muted red)
  - Pre-order badge: "📅" icon or "Pre-order" small badge on rows with future delivery dates
  - "Placed By" column: "Customer" or "Staff: Rajesh" or "Shop Sale"
  - Cancelled rows show: "Cancelled by: Staff/Customer"
  - Actions: View (eye icon → A10)
- **Default sort:** Pending first, then oldest
- Pagination: "Showing 1-20 of 160"

## Interactions
- Filter by status/date/pre-order → table updates
- Search → filters by order # or customer name
- Click order row or view icon → A10 (Order Detail)
- Change global location → scopes all data
- Sort by clicking column headers

## States
- **Loading:** Summary card skeletons + table skeleton
- **Empty:** "No orders for this scope" for the current filter
- **Error:** Retry
- **Success:** Summary cards + populated table

## Navigation
- **From:** Sidebar, Dashboard KPI click
- **To:** Order Detail (A10)

## What to Show
- Summary cards populated (12 Pending, 8 Confirmed, 5 Delivered, 131 Completed, 4 Cancelled). Table showing 6 orders: one Pending (pre-order badge, delivery date next week), one Confirmed, one Delivered, one Completed (GPay collected), one Cancelled ("by Customer"), one "Shop Sale" (Placed by: Staff, immediately Completed). Filters visible. Professional table layout.
