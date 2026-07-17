# Muciriz — Admin Panel (Full UI/UX Prompt for Google Stitch)

## DESIGN SYSTEM

**Project:** Muciriz — local fresh-goods ecommerce (fish, vegetables, meat)
**Platform:** Next.js web application (responsive to tablet)
**Colors:** Background White (#FFFFFF), Text Black (#000000), Accent Deep Sea Teal (#0E7C86), Button text on accent: White. No gradients, no heavy shadows. Clean and minimal.
**Typography:** One clean sans-serif family. English only. Clear hierarchy.
**Components:** Data tables with pagination, modals/drawers for forms, KPI cards, status chips (Pending=amber, Confirmed=blue, Delivered=teal, Completed=green, Cancelled=muted red), outlined inputs, toasts, confirm dialogs, multi-select dropdowns, tabs.
**States:** Every screen needs Loading (skeleton shimmer), Empty (illustration + message + CTA), Error (retry), Success.
**Layout:** Fixed left sidebar navigation + top bar with global location selector. Content area with tables, cards, and forms.

---

## NAVIGATION MAP

```
Login (A01) → Dashboard (A02)
Sidebar:
├── Dashboard (A02)
├── Service Locations (A03)
├── Categories (A04)
├── Products (A05)
├── Delivery Times (A06)
├── Staff Management (A07)
├── Offers (A08)
├── Orders (A09) → Order Detail (A10)
├── Sales Reports (A11)
├── Customer History (A12)
├── Inventory (A15)
└── Profile/Settings (A13)
Top bar: Global location selector, profile menu
Any → A14 (Error States)
```

---

## GLOBAL LAYOUT

**Left Sidebar (fixed, 240px):**
- Muciriz logo/wordmark at top (teal on white)
- Nav items with icons: Dashboard, Locations, Categories, Products, Delivery Times, Staff, Offers, Orders, Reports, Customers, Inventory, Settings
- Active item: teal background highlight + white text
- Collapsed state on smaller screens (icons only)

**Top Bar (fixed):**
- Left: breadcrumb / page title
- Center/Right: **Global Service Location selector** dropdown ("All Locations" or specific)
- Right: profile avatar/name + dropdown (Profile, Settings, Logout)

---

## SCREENS

### A01 — Login
**Purpose:** Admin authentication (single main admin account).
**UI Elements:**
- Centered card on white/light gray background
- Muciriz logo above form
- Email/username field (outlined)
- Password field (outlined, show/hide toggle)
- "Login" button (teal filled, full-width)
- "Forgot Password?" link (teal text) → self-service reset flow
- Error message area (below button)

**Interactions:**
- Enter credentials → Login → POST /auth/login/admin
- Forgot Password → email-based reset flow
- Success → A02 (Dashboard)
- Error → "Invalid credentials" / "Account locked" / network error

**States:**
- Idle: empty form
- Loading: button loader
- Error: inline message
- Success: redirect to Dashboard

---

### A02 — Dashboard (Home)
**Purpose:** Landing overview with KPIs, respects global location selector.
**UI Elements:**
- Page title: "Dashboard"
- **KPI cards row** (4-6 cards):
  - Total Orders (with pending count highlight)
  - Total Sales (₹, period)
  - Active Offers
  - Staff Count
  - Categories / Products count
  - Pre-orders pending
- **Quick action shortcuts** (icon + label buttons):
  - Manage Locations, Categories, Products, Staff, Orders, Reports
- (Optional) Recent orders mini-table (5 rows)

**Interactions:**
- Change global location → KPIs update
- Click KPI card → navigate to relevant section
- Click shortcuts → navigate

**States:**
- Loading: KPI card skeletons
- Empty: fresh system → "Welcome! Create your first Service Location to get started" + CTA
- Error: Retry
- Success: populated KPIs

---

### A03 — Service Locations
**Purpose:** Manage top-level service locations (foundation for all data).
**UI Elements:**
- Page title: "Service Locations"
- "Add Location" button (teal, top-right)
- **Table:** Name, Active (toggle), Categories count, Products count, Customers count, Orders count, Created date, Actions
- Row actions: Edit, Activate/Deactivate, Remove
- Search bar above table
- Pagination

**Add/Edit Modal:**
- Location Name field (required)
- Save / Cancel buttons
- Remove: confirmation dialog ("This will deactivate the location and hide its categories/products. Historical data is preserved.")

**Interactions:**
- Add → modal → save → location appears in table
- Edit → modal pre-filled → save
- Deactivate → confirm → soft-delete (isActive=false)

**States:**
- Loading: table skeleton
- Empty: "No locations yet. Create your first Service Location." + CTA
- Error: Retry
- Success: table populated

---

### A04 — Categories
**Purpose:** Manage categories with multi-location assignment and recommendations.
**UI Elements:**
- Page title: "Categories"
- **Location filter dropdown** (top, scopes view)
- "Add Category" button (teal)
- **Table/Grid:** Image thumbnail, Name, Assigned Locations (chips), Active, Product Count, Sort Order, Actions
- Row actions: Edit, Manage Recommendations, Activate/Deactivate, Remove

**Add/Edit Modal:**
- Category Name (required)
- Image upload (drag-drop / file picker, preview)
- **Service Locations multi-select** (checkbox dropdown, at least one required)
- Sort order (number)
- Save / Cancel

**Recommendations Manager (sub-modal/drawer):**
- Product picker: search + add recommended products
- Current recommendations list with remove option

**Interactions:**
- Add → modal → upload image + select locations → save
- Edit → pre-filled modal
- Drag to reorder (optional)
- Manage Recommendations → drawer/modal with product picker

**States:**
- Loading: grid skeleton
- Empty: "No categories for this location" + CTA
- Error: Retry
- Success: grid/table populated

---

### A05 — Products
**Purpose:** Manage products with GST, multi-location, stock, and approval workflow.
**UI Elements:**
- Page title: "Products"
- **Category selector** (dropdown, scopes products)
- **Approval filter** tabs: All | Approved | Pending Approval | Rejected
- "Add Product" button (teal)
- **Table:** Image, Name, Gross Wt, Net Wt, Price (₹), GST (Yes/No + %), Locations (chips), Stock summary, Status (Active/Inactive), Approval (badge), Added By, Actions
- Row actions: Edit, Approve/Reject (for pending), Manage Recommendations, Manage Stock, Activate/Deactivate, Remove

**Add/Edit Modal/Drawer:**
- Product Name (required)
- Gross Weight + unit (g/kg)
- Net Weight + unit (must ≤ Gross)
- Price (₹, required) — fixed per packet
- **GST Applicable** toggle (Yes/No)
- **GST Percentage** (shown when Yes: dropdown 5/12/18/28 or custom number)
- **Service Locations multi-select** (at least one)
- Image upload with preview
- (Admin adds are auto-approved)

**Approval actions (for Pending items):**
- "Approve" button (green) → product goes live
- "Reject" button (red outline) → product hidden, staff notified

**Stock quick-view:**
- Per-location stock counts in table cell or expandable row

**Interactions:**
- Add → drawer → fill fields → save → product in table (Approved)
- Pending items → Approve/Reject
- Manage Recommendations → product picker drawer
- Manage Stock → link to A15 or inline editor

**States:**
- Loading: table skeleton
- Empty: "No products in this category" or "Select a category first"
- Error: Retry
- Success: table with products, approval badges on pending items

---

### A06 — Delivery Times
**Purpose:** Manage delivery time slot options (global + per-location, supports pre-order dates).
**UI Elements:**
- Page title: "Delivery Times"
- **Scope toggle/tab:** Global | Per Location (with location selector)
- "Add Delivery Time" button (teal)
- **Table:** Label (e.g., "Morning 8–10 AM"), Scope (Global/Location name), Active, Actions
- Row actions: Edit, Activate/Deactivate, Remove
- Warning banner if no active times: "Customers cannot checkout without delivery times!"

**Add/Edit Modal:**
- Label (required, e.g., "Morning 8–10 AM")
- Scope: Global (radio) or Specific Location (radio + location dropdown)
- Save / Cancel

**Interactions:**
- Add → modal → save
- Edit → pre-filled modal
- Remove → confirm dialog
- Per-location overrides global (explained in helper text)

**States:**
- Loading: table skeleton
- Empty: "No delivery times configured" + warning + CTA
- Error: Retry
- Success: table populated

---

### A07 — Staff Management
**Purpose:** Create/manage staff with multi-location assignment and granular permissions.
**UI Elements:**
- Page title: "Staff Management"
- "Add Staff" button (teal)
- **Table:** Name, Phone, Assigned Locations (chips), Active, Permission summary, Actions
- Row actions: Edit, Reset Password, Activate/Deactivate

**Add/Edit Drawer:**
- Name (required)
- Phone (required, unique within staff, login identifier)
- Initial Password (set by admin)
- **Assigned Service Locations** multi-select (at least one)
- **Permission toggles** (grouped, clearly labeled):
  - ☑ View Orders (default: on)
  - ☐ Restrict to Categories (+ category multi-select when on)
  - ☐ Add Products (default: off)
  - ☑ Confirm Order (default: on)
  - ☑ Confirm Delivery (default: on)
  - ☑ Confirm Payment (default: on)
  - ☐ Update Stock (default: off)
  - ☐ Record Misc Purchases (default: off)
  - ☐ Place Shop Sales (default: off)
- Save / Cancel

**Reset Password Modal:**
- New password field + confirm
- Save → admin-driven reset

**Interactions:**
- Add → drawer → fill details + permissions → save
- Edit → pre-filled drawer
- Reset Password → modal
- Deactivate → confirm ("Staff will lose access immediately")

**States:**
- Loading: table skeleton
- Empty: "No staff members yet" + CTA
- Error: Retry
- Success: table populated

---

### A08 — Offers
**Purpose:** Create/manage promotional offers with target scope and discount.
**UI Elements:**
- Page title: "Offers & Promotions"
- **Scope filter:** Global | Per Location
- "Create Offer" button (teal)
- **Table:** Title, Target (All/Category/Product), Discount %, Scope, Validity, Active, Actions
- Row actions: Edit, Activate/Deactivate, Remove

**Create/Edit Modal:**
- Title + Message (e.g., "20% off Vegetables for Onam")
- **Target scope:** All categories (radio) | Specific Category (+ picker) | Specific Product (+ picker)
- Discount Percent (number, 0-100)
- Banner Image upload (optional)
- Validity: Start date + End date (date pickers)
- Scope: Global (radio) | Specific Location (+ dropdown)
- Save / Cancel

**Interactions:**
- Create → modal → fill → save → offer in table
- Edit → pre-filled modal
- Remove → confirm

**States:**
- Loading: table skeleton
- Empty: "No offers created yet" + CTA
- Error: Retry
- Success: table with offers

---

### A09 — Orders Dashboard
**Purpose:** Monitor all orders with pre-order filter and staff cancel visibility.
**UI Elements:**
- Page title: "Orders"
- **Location filter** (uses global selector)
- **Summary cards row:** Pending (count), Confirmed, Delivered, Completed, Cancelled, Pre-orders
- **Filters row:** Status dropdown, Date range picker, Pre-order only toggle, Search (order # / customer name)
- **Orders table:** Order #, Customer, Location, Delivery Date, Time Slot, Total (₹), Payment (pref/collected), Status chip, Staff handling, Placed by (Customer/Staff), Pre-order badge, Date placed
- **Default sort:** Pending/oldest first
- Pagination
- (Optional) Export CSV button

**Interactions:**
- Filter/search → table updates
- Click order row → A10 (Order Detail)
- Change location → scopes table
- Pre-order toggle → shows only pre-orders

**States:**
- Loading: table skeleton + card skeletons
- Empty: "No orders for this scope"
- Error: Retry
- Success: summary cards + table

---

### A10 — Order Detail
**Purpose:** Full read view of a single order with timestamps, GST details, and invoice.
**UI Elements:**
- Page title: "Order #M2026#00015" + back breadcrumb
- **Status chip** (large) + **Pre-order badge** + **"Placed by: Staff/Customer"**
- Two-column layout:
  - Left: Customer info (name, WhatsApp, address, landmark, location), Delivery (date + time slot)
  - Right: Payment (preference + collected), Invoice download
- **Items table:** Product, Weight, Qty, Unit Price, GST Rate, GST Amount, Line Total
- **Totals:** Subtotal, GST breakdown by rate, **Grand Total**
- **Fulfillment Timeline:**
  - Order Placed: [date/time]
  - Order Confirmed: [date/time] by [Staff Name] — "Confirmed in 15 min"
  - Delivered: [date/time] by [Staff Name] — "Delivered in 2h"
  - Payment Confirmed: [date/time] by [Staff Name] — GPay/Cash
  - (If cancelled): Cancelled: [date/time] by [Staff Name/Customer] — reason/stock restored note
- "Download Invoice" button (teal outline)

**Interactions:**
- Download Invoice → PDF
- Back → A09
- Timestamps clickable for staff details (optional)

**States:**
- Loading: skeleton
- Error: Retry
- Success: full detail

---

### A11 — Sales Reports (4 Tabs)
**Purpose:** Revenue analysis, profit/loss, staff performance, misc expense tracking.
**UI Elements:**
- Page title: "Reports"
- **Location selector** + **Date range filter** (today/week/month/custom)
- **4 Tabs:** Sales | Profit/Loss | Staff Performance | Misc Purchases

**Sales Tab:**
- Total Sales figure (₹, large)
- Breakdown table: Location, Orders Count, Total Sales, GPay collected, Cash collected
- GST summary: total GST collected, breakdown by rate
- (Optional) Simple bar chart of daily sales

**Profit/Loss Tab:**
- KPI cards: Total Sale (₹), Total Expense (₹), Net Profit/Loss (₹, green if profit, red if loss)
- Total Expense = Purchase Records + Misc Purchases
- Note if no expense data: "No expense data recorded yet. Record purchases in Inventory to see profit/loss."
- Filterable by location + date range

**Staff Performance Tab:**
- Table: Staff Name, Orders Handled, Avg Time to Confirm, Avg Time to Deliver, Avg Time to Complete
- Location filter
- Performance indicators (optional color coding)

**Misc Purchases Tab:**
- Table: Date, Staff Name, Item, Qty, Cost (₹), Location
- Total operational expenses for period
- Location filter, date filter

**Interactions:**
- Switch tabs
- Change filters → data reloads
- (Optional) Export CSV per tab

**States:**
- Loading: skeletons per tab
- Empty: "No data for this period" per tab
- Error: Retry
- Success: populated tables/charts

---

### A12 — Customer History
**Purpose:** Look up customers, view purchase history, edit customer details.
**UI Elements:**
- Page title: "Customer Lookup"
- **Search bar:** "Search by name or WhatsApp number" (prominent)
- **Results list:** Name, WhatsApp, Location, Total Orders, Total Spend
- **Selected customer panel:**
  - Profile summary: Name, WhatsApp, Address, Landmark, Location
  - "Edit Customer" button (teal outline)
  - Order history table: Order #, Date, Total, Status, Payment, Delivery Date, Pre-order flag
  - Pagination

**Edit Customer Modal:**
- Editable: Name, Delivery Address, Nearest Landmark, Service Location (dropdown)
- Non-editable: WhatsApp Number (shown but disabled/greyed)
- Save / Cancel
- Confirm before save: "Update customer details?"

**Interactions:**
- Search → results list
- Click customer → expanded panel with profile + orders
- Edit Customer → modal → save → PATCH /customers/{id} → toast
- Click order → A10

**States:**
- Idle: "Search for a customer by name or WhatsApp number"
- Loading: searching / saving
- Empty: "No customer found"
- Error: Retry / save error
- Success: results + detail

---

### A13 — Profile / Settings
**Purpose:** Admin profile, password, system configuration (UPI, customer care).
**UI Elements:**
- Page title: "Settings"
- **Two sections (tabs or accordion):**

**Profile Section:**
- Name, Email/Username (display)
- Change Password form: Current password, New password, Confirm password
- "Update Password" button
- "Logout" button (red outline)

**System Configuration Section:**
- Section header: "System Configuration"
- **GPay/UPI ID** field (e.g., "merchant@upi") — displayed to customers at checkout
- **Merchant Name** field — for UPI intent display
- **Customer Care Phone** field — shown in customer app help
- **Customer Care WhatsApp** field — for wa.me deep-link in customer app
- "Save Configuration" button (teal)
- Helper text: "Leave empty to hide from customer app"

**Interactions:**
- Update password → validate → save → toast
- Update config → save → PUT /config → toast
- Logout → confirm → clear session → A01

**States:**
- Loading/Error/Success on save
- Password validation inline
- Config save confirmation

---

### A14 — Error States (Global Web)
**Purpose:** Global error handling for the admin panel.
**UI Elements:**
- **404 Not Found:** illustration, "Page not found", "Back to Dashboard" link
- **Server Error (5xx):** illustration, "Something went wrong", "Retry" button
- **Session Expired (401):** message, redirect to Login
- **Forbidden (403):** "You don't have access", "Back" button
- **Fresh System:** guidance to create first Service Location
- **Network Offline:** top banner "You're offline — check your connection", Retry

**Interactions:**
- Retry, navigate home, re-login as appropriate

---

### A15 — Inventory Management
**Purpose:** Full stock management, purchase records, and profit/loss overview.
**UI Elements:**
- Page title: "Inventory"
- **3 Tabs:** Stock Levels | Purchase Records | Profit/Loss Summary

**Stock Levels Tab:**
- Location selector
- **Table:** Product, Category, Location, Current Stock, Status (In Stock/Low/Out of Stock), Last Updated (by whom, when)
- Status indicators: green chip (In Stock), amber (Low Stock <5), red (Out of Stock = 0)
- Inline edit: click stock count → number input → save
- Filters: category, stock status, product search
- (Optional) Bulk update button

**Purchase Records Tab:**
- "Record Purchase" button (teal)
- **Table:** Product, Location, Purchase Rate (₹/unit), Qty, Gross Wt, Net Wt, Supplier, Date, Recorded By
- Filters: product, location, date range
- Pagination

**Record Purchase Modal:**
- Product (searchable dropdown, required)
- Location (dropdown, required)
- Purchase Rate / Cost Price (₹, required)
- Quantity (number, required)
- Gross Weight (optional)
- Net Weight (optional)
- Supplier Name (optional)
- Purchase Date (date picker)
- Save / Cancel

**Profit/Loss Summary:**
- KPI cards: Total Sale, Total Expense (purchases + misc), Net Profit/Loss
- Link: "View detailed reports →" (A11)

**Interactions:**
- Inline stock edit → save → audit logged
- Record purchase → modal → save → appears in table
- Switch tabs
- Filter/search

**States:**
- Loading: table skeletons
- Empty: "No stock data" / "No purchases recorded" + CTA
- Error: Retry
- Success: populated tables

---

## WHAT TO SHOW

Generate the following key screens with full fidelity:
1. **A02 Dashboard** — populated with KPI cards (5 cards with realistic numbers), global location selector showing "All Locations", quick action buttons, sidebar visible with all nav items
2. **A05 Products** — table with 5 products, showing GST column, approval status badges (one Pending), location chips, category selector at top, "Add Product" button
3. **A09 Orders Dashboard** — summary cards row, table with 6 orders (mix of statuses), pre-order badges on 2, status chips colored, filters visible, "Placed by: Staff" on one
4. **A11 Sales Reports** — Profit/Loss tab active showing KPI cards (Total Sale ₹2,45,000 / Total Expense ₹1,80,000 / Net Profit ₹65,000 in green), date range filter
5. **A07 Staff Management** — table with 3 staff members, permission summary chips, multi-location chips, "Add Staff" button
6. **A15 Inventory** — Stock Levels tab with table showing products with stock status indicators, one Out of Stock (red), one Low Stock (amber), inline edit visible on one row
7. **A12 Customer History** — search results showing 2 customers, one selected with order history table visible, "Edit Customer" button
8. **A01 Login** — centered login form with Muciriz branding

For all screens: white background, teal accent (#0E7C86), clean table layouts, proper spacing, realistic sample data (Indian fish/vegetable/meat context, ₹ pricing, Indian names). Sidebar always visible. Professional admin panel aesthetic.
