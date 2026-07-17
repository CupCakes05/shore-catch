# Muciriz — Admin Panel (Full Prompt for Google Stitch)

> One prompt for the entire Admin Panel. 14 screens. Next.js, web (desktop/tablet).

---

Design a complete web admin panel UI/UX for **Muciriz Admin** (Next.js, desktop/tablet). This is the Main Admin console for managing locations, catalog, staff, offers, orders, and sales. Design ALL 14 screens below as one cohesive web application with sidebar navigation.

---

## DESIGN SYSTEM

**Colors (strict 2–3 palette):**
- Background: White (#FFFFFF)
- Text: Black (#000000)
- Primary accent: Deep Sea Teal (#0E7C86) — buttons, active states, links, sidebar active item
- Button text on accent: White
- Table header bg: #F9FAFB (very light gray)
- No gradients, no heavy shadows. Clean and minimal.

**Typography:** One clean sans-serif. Clear hierarchy: page title (24px bold), section header (18px medium), body (14px), caption (12px muted). English only.

**Global Layout (all authenticated screens):**
- **Left sidebar** (fixed, ~240px wide, white or very light bg):
  - Muciriz logo at top
  - Nav links with icons: Dashboard, Service Locations, Categories, Products, Delivery Times, Staff, Offers, Orders, Sales Reports, Customer History
  - Active item: teal text + light teal background
  - Bottom: Settings, Logout
- **Top bar** (horizontal, spans content area):
  - Left: Page title / breadcrumb
  - Right: **Global Service Location dropdown** ("All Locations" or specific), Admin avatar/name dropdown (Profile, Logout)
- **Content area:** tables, cards, forms in modals/drawers

**Components:**
- Tables: with header row (light gray bg), pagination, search bar, column sort, row hover highlight
- Buttons: primary (teal filled), secondary (outline), destructive (red), ghost (text only)
- Modals: centered overlay with backdrop for forms/confirmations
- Drawers: slide-in from right for complex forms (staff, recommendations)
- Chips/badges: status colored (same palette as mobile), category/location labels
- Inputs: outlined with label above; dropdowns with chevron
- Toasts: top-right corner (success green, error red)
- Empty states: centered in content area
- KPI cards: bordered cards with large number + label + subtle icon

**States (every screen):**
- Loading: skeleton shimmer matching layout
- Empty: illustration + message + CTA
- Error: inline retry
- Success: data populated

---

## SCREENS

---

### A01 — Login (no sidebar)
**Purpose:** Admin authentication. Centered card layout (no sidebar/topbar yet).

**UI:**
- Full page, light gray background
- Centered white card (max 400px):
  - Eze-Cart logo → Muciriz logo
  - "Admin Login" heading
  - Email/username field
  - Password field (show/hide)
  - "Login" button (primary, full-width)
  - "Forgot password?" link (teal) below
- Error: alert banner inside card

**States:** Idle, Loading (spinner), Error (alert), Success → redirect to Dashboard.

---

### A02 — Dashboard (Home)
**Purpose:** Overview KPIs + quick navigation.

**UI (content area):**
- **KPI cards row** (responsive grid, 3–6 cards):
  - Total Orders (with "X pending" highlighted)
  - Total Sales ₹ (this month)
  - Active Offers
  - Staff Count
  - Categories
  - Products
  - Each card: icon + large number + label, clickable
- **Recent Orders mini-table** below:
  - 5 rows: Order #, customer, status chip, total, date
  - "View All Orders →" link
- Location selector in top bar scopes KPIs

**States:** Loading (skeleton cards + table), Empty (fresh system: "Welcome! Create your first Service Location." + CTA), Error (retry), Success.

---

### A03 — Service Locations
**Purpose:** Manage top-level locations. Everything cascades from here.

**UI:**
- Page title: "Service Locations"
- "Add Location" button (primary, top-right)
- **Table:** Name, Status (Active/Inactive chip), Categories, Products, Customers, Orders, Created, Actions (Edit/Deactivate/Delete icons)
- Search bar + pagination
- **Add/Edit modal:** Location Name field + Save button
- **Deactivate dialog:** warning about cascade + Deactivate/Cancel buttons

**Interactions:** Add → modal → save → toast. Edit → modal. Deactivate → dialog → confirm. Click row → Categories (A04) for that location.

**States:** Loading, Empty ("No locations yet. Create your first." + Add CTA), Error, Success.

---

### A04 — Categories
**Purpose:** Manage categories per location with image + recommended products.

**UI:**
- Page title: "Categories" + breadcrumb showing location
- Scoped by global location selector
- "Add Category" button (primary)
- **Table/grid:** Image thumbnail, Name, Status, Products count, Recommendations count, Sort order, Actions (Edit/Manage Recs/Deactivate/Delete)
- **Add/Edit modal:** Name + Image upload (drag & drop zone with preview + progress) + Sort order + Save
- **Manage Recommendations drawer** (slides from right):
  - Title: "Recommended Products for [Category]"
  - Searchable product picker (same location, active/approved)
  - Selected products as removable chips/tags
  - "Save Recommendations" button

**Interactions:** Add → modal → upload + save. Manage Recs → drawer → search + add/remove → save. Deactivate → dialog. Click row → Products (A05).

**States:** Loading, Empty ("Select a location" or "No categories"), Error, Success. Image upload progress.

---

### A05 — Products
**Purpose:** Manage products per category. Includes approval workflow for staff-submitted products.

**UI:**
- Page title: "Products" + breadcrumb (Location > Category)
- **Filter bar:** Location dropdown, Category dropdown, Status (All/Active/Inactive/Pending Approval)
- "Add Product" button (primary)
- **Table:** Image (thumb), Name, Gross Weight, Net Weight, Price ₹, Status, Approval Status (Approved green/Pending amber/Rejected red/N/A), Added By (Admin/Staff name), Actions
- **Add/Edit modal:** Product Name, Gross Weight, Net Weight, Price, Image upload (drag & drop + preview), Category selector, Save
- **Manage Recommendations drawer:** same pattern as categories ("Recommended Products for [Product Name]")
- **Approval actions** (for Pending rows): "Approve" (green button) + "Reject" (red button) in row or detail view

**Interactions:** Add → modal → save. Filter "Pending Approval" → shows staff submissions. Approve → confirm → toast "Approved & live". Reject → confirm (+ optional note) → toast. Manage Recs → drawer. Deactivate = soft-hide.

**States:** Loading, Empty ("No products. Add one."), Error, Success. Pending Approval filter shows staff items.

---

### A06 — Delivery Times
**Purpose:** Manage delivery time slot options.

**UI:**
- Page title: "Delivery Times"
- **Scope tabs:** "Global" | "Per Location" (with location dropdown when Per Location)
- "Add Delivery Time" button (primary)
- **Table:** Label (e.g., "Morning 8–10 AM"), Scope (Global/Location chip), Status, Actions
- **Add/Edit modal:** Label + Scope radio (Global / Specific location + dropdown) + Save
- **Warning banner** (yellow, top of table): "⚠️ Customers in [Location] cannot checkout — no delivery times configured." (shown when applicable)

**Interactions:** Add → modal → save. Scope tabs filter. Per-location overrides global.

**States:** Loading, Empty (with warning about checkout blocking), Error, Success.

---

### A07 — Staff Management
**Purpose:** Create staff, assign locations, configure permissions.

**UI:**
- Page title: "Staff Management"
- "Add Staff" button (primary)
- **Table:** Name, Phone, Location (chip), Permissions (mini chips), Status, Actions (Edit/Reset Password/Deactivate)
- **Add/Edit drawer** (right slide):
  - Name
  - Phone number (required)
  - Password (on create) / "Reset Password" button (on edit)
  - Assigned Location dropdown (required)
  - **Permissions checkboxes:**
    - ☑ View Orders
    - ☑ Confirm Order
    - ☑ Confirm Delivery
    - ☑ Confirm Payment
    - ☐ Add/Edit Products
    - ☐ Restrict to Categories → (if checked, reveals multi-select category picker scoped to selected location)
  - Status toggle: Active/Inactive
  - Save button
- **Reset Password dialog:** shows new temporary password with "Copy" button

**Interactions:** Add → drawer → fill + permissions → save. Reset Password → dialog → copy. Deactivate → confirm. "Restrict to Categories" checkbox → reveals category multi-select.

**States:** Loading, Empty ("No staff. Add first."), Error, Success.

---

### A08 — Offers / Notifications
**Purpose:** Create festival/promotional offers with target scope.

**UI:**
- Page title: "Offers & Promotions"
- Scope filter: "All" | "Global" | specific location
- "Create Offer" button (primary)
- **Table:** Title, Target (All/Category/Product chip), Discount %, Scope (Global/Location chip), Validity, Status (Active/Expired), Actions
- **Create/Edit modal (large):**
  - Title (text)
  - Message/description (textarea)
  - **Target scope** radio: "All Categories" | "Specific Category" (+ picker) | "Specific Product" (+ picker)
  - Discount % (number, 0–100)
  - Banner image upload (optional, with preview)
  - Validity: Start date + End date (date pickers)
  - Scope radio: "Global" | "Specific Location" (+ dropdown)
  - "Save" button

**Interactions:** Create → modal → pick target + discount + validity → save → toast. Target selection shows/hides relevant picker. Deactivate hides from customers.

**States:** Loading, Empty ("No offers. Create first."), Error, Success.

---

### A09 — Orders Dashboard
**Purpose:** Monitor all orders by location. Operational oversight.

**UI:**
- Page title: "Orders"
- **Summary cards row:** Pending (amber), Confirmed (blue), Delivered (teal), Completed (green), Cancelled (muted) — count in each
- **Filter bar:** Location, Status dropdown, Date range picker, Search (order # / customer)
- **Table:** Order #, Customer, Location, Delivery Time, Items, Total ₹, Payment (preference → collected), Status (chip), Staff, Date
- Default sort: pending/oldest first
- Pagination (25/50/100)
- Summary cards clickable (quick-filter)
- "Export CSV" button (secondary)

**Interactions:** Filter/search → table updates. Click row → A10. Click summary card → filters table.

**States:** Loading (skeleton), Empty, Error, Success.

---

### A10 — Order Detail (Admin Read-Only)
**Purpose:** Full view of a single order.

**UI:**
- Page title: "Order #..." + breadcrumb
- **Status timeline** (horizontal stepper): Pending → Confirmed → Delivered → Completed (with timestamps, teal reached, gray pending)
- **Two-column layout:**
  - Left: items table (product, weight, qty, price, line total) + subtotal + total
  - Right: customer info (name, number, address, landmark), delivery time, payment preference, payment collected (if done), assigned staff
- "Download Invoice" button
- Read-only (no fulfillment actions — staff handles that)

**States:** Loading, Error, Success.

---

### A11 — Sales Reports
**Purpose:** Revenue reporting by location.

**UI:**
- Page title: "Sales Reports"
- **Filter bar:** Location ("All"/specific), Date range (Today/Week/Month/Custom)
- **Headline:** Large ₹ total (e.g., "₹1,24,500") for scope/period
- **Breakdown table:** Location, Completed Orders, Total Sales ₹, Gpay Collected ₹, Cash Collected ₹
- Footer row: grand totals
- **Bar chart** (optional): daily/weekly sales bars (with text summary below for accessibility)
- "Export CSV" button

**Interactions:** Change filters → data refreshes. Export.

**States:** Loading, Empty ("No completed orders in this period" — only Completed count), Error, Success.

---

### A12 — Customer History Lookup
**Purpose:** Search customers by name/number, view purchase history.

**UI:**
- Page title: "Customer Lookup"
- **Search bar** (prominent, full-width): "Search by name or WhatsApp number..."
- **Results list** (cards/table): Name, WhatsApp, Location, Total Orders, Total Spent ₹
- **Customer detail** (expands below or sub-view):
  - Profile: name, number (partially masked), location, registered date
  - **Order history table:** Order #, Date, Items, Total, Status chip, Payment
  - Click order → A10
  - Pagination

**Interactions:** Type → debounced search → results appear. Click customer → detail + orders. Click order → A10.

**States:** Idle ("Search for a customer"), Loading (searching), Empty ("No match"), Error, Success.

---

### A13 — Profile / Settings
**Purpose:** Admin account management.

**UI:**
- Page title: "Settings"
- Profile section: name, email (display)
- Change Password form: current + new + confirm + Save
- Logout button

**States:** Loading/Success on save. Password validation.

---

### A14 — Error States
**Purpose:** 404 and server error pages.

**UI:**
- Centered in content area (sidebar remains):
  - 404: "Page not found" + illustration + "Go to Dashboard" link
  - 500: "Something went wrong" + "Retry" button

---

## NAVIGATION (SIDEBAR)

```
Sidebar:
  Dashboard (A02)
  Service Locations (A03) → Categories (A04) → Products (A05)
  Delivery Times (A06)
  Staff (A07)
  Offers (A08)
  Orders (A09) → Order Detail (A10)
  Sales Reports (A11)
  Customer History (A12)
  Settings (A13)
```

## WHAT TO SHOW

Design all 14 screens within the sidebar layout. Additionally show:
1. The overall layout clearly: sidebar + top bar (with location selector) + content area
2. Dashboard (A02) populated with KPIs and recent orders
3. Dashboard (A02) empty state (new system)
4. Service Locations table with "Add Location" modal open
5. Categories with "Manage Recommendations" drawer open (showing product picker + chips)
6. Products table filtered to "Pending Approval" with Approve/Reject buttons visible
7. Staff Management with Add Staff drawer open (showing permissions checkboxes + "Restrict to Categories" expanded with multi-select)
8. Offers create modal with "Specific Category" target selected (showing category picker)
9. Orders Dashboard with summary cards + populated table (mixed statuses)
10. Sales Reports with headline ₹ figure + breakdown table + bar chart
11. Customer Lookup with a customer selected showing their order history
12. Delivery Times with the yellow warning banner ("no times configured")
