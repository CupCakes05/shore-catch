# A02 — Dashboard | Muciriz Admin Panel

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Next.js web. Fixed left sidebar + top bar.

## Screen
**App:** Admin Panel (Next.js, web)
**Purpose:** Landing overview after login. KPI metrics, quick navigation. Respects global location selector.

## UI Elements
- **Left sidebar** (fixed, 240px, white bg, teal active item):
  - Muciriz logo (top)
  - Nav items: Dashboard (active, teal highlight), Locations, Categories, Products, Delivery Times, Staff, Offers, Orders, Reports, Customers, Inventory, Settings
- **Top bar** (white bg, border-bottom):
  - Page title: "Dashboard" (left)
  - Global location selector dropdown (center-right): "All Locations" / "Ernakulam" / "Fort Kochi"
  - Profile avatar + name (right) with dropdown (Profile, Logout)
- **KPI Cards row** (6 cards, 3-column grid):
  - Total Orders: "156" (with "+12 pending" in amber)
  - Total Sales: "₹2,45,000" (period)
  - Active Offers: "3"
  - Staff Members: "5"
  - Categories: "6" / Products: "48"
  - Pre-orders Pending: "8"
- **Quick action buttons** (row below KPIs, icon + label):
  - Manage Locations, Categories, Products, Staff, Orders, Reports
- (Optional) Recent Orders mini-table (5 rows: order #, customer, status chip, total)

## Interactions
- Change global location → KPIs re-fetch for that location
- Click KPI card → navigate to relevant section (e.g., Total Orders → A09)
- Click quick action → navigate to section
- Sidebar nav → navigate between sections
- Profile dropdown → A13 (settings) or Logout

## States
- **Loading:** KPI card skeletons (6 gray rectangles shimmer)
- **Empty (fresh system):** "Welcome to Muciriz! Create your first Service Location to get started." + "Create Location" CTA button
- **Error:** "Couldn't load metrics" + Retry button
- **Success:** Populated KPIs + quick actions

## Navigation
- **From:** Login (A01)
- **To:** Any section via sidebar/KPIs/shortcuts

## What to Show
- Success state: sidebar visible with all nav items (Dashboard active), top bar with "All Locations" selected, 6 KPI cards with realistic numbers, quick action buttons row, recent orders mini-table with 5 orders (mix of statuses). Professional admin panel layout.
