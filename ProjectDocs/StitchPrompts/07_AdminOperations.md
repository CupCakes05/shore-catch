# Batch 7 — Admin Operations (A06, A07, A08, A09, A10, A11, A12)

> Delivery times, staff management, offers, orders, sales reports, and customer history.

---

Continue the **Admin Panel** (Next.js web). Same sidebar layout established in Batch 6. These screens handle operations, fulfillment oversight, and reporting.

---

### Screen 1: Delivery Times (A06)
**Purpose:** Manage delivery time slot options customers choose at checkout.

**UI Elements:**
- Page title: "Delivery Times"
- **Scope tabs/filter:** "Global" | "Per Location" (with location dropdown when Per Location is selected)
- **"Add Delivery Time"** button (primary)
- **Table:**
  - Columns: Label (e.g., "Morning 8–10 AM"), Scope (Global / Location name chip), Status (Active/Inactive), Actions
  - Row actions: Edit, Deactivate, Delete
- **Add/Edit modal:**
  - Label (text, required)
  - Scope: Radio — "Global (all locations)" or "Specific location" + location dropdown
  - "Save" button
- **Warning banner** (yellow): shown when no active delivery times exist for any location — "⚠️ Customers in [Location] cannot checkout — no delivery times configured."

**Interactions:**
- Toggle between Global and Per Location view
- Add → modal → fill → Save → appears in table → toast
- Deactivate/Delete → confirmation
- Per-location overrides global: if a location has its own times, those are shown to customers; otherwise global applies

**States:**
- Loading: skeleton
- Empty: "No delivery times configured. Add at least one so customers can checkout." + CTA
- Success: table

---

### Screen 2: Staff Management (A07)
**Purpose:** Create staff accounts, assign locations, and configure granular permissions.

**UI Elements:**
- Page title: "Staff Management"
- **"Add Staff"** button (primary)
- **Table:**
  - Columns: Name, Phone, Assigned Location, Permissions (chips/tags), Status (Active/Inactive), Actions
  - Row actions: Edit, Reset Password, Deactivate
- **Add/Edit drawer (side panel):**
  - Name (text)
  - Phone number (required, login identifier)
  - Password (required on create; "Reset Password" button on edit)
  - Assigned Service Location (dropdown, required)
  - **Permissions checkboxes:**
    - ☑ View Orders
    - ☑ Confirm Order
    - ☑ Confirm Delivery
    - ☑ Confirm Payment
    - ☐ Add/Edit Products (grants access to product submission)
    - ☐ Restrict to Categories (if checked, shows a multi-select for allowed categories)
  - Status: Active/Inactive toggle
  - "Save" button
- **Reset Password section (in edit):**
  - "Reset Password" button → generates new temporary password shown once in a dialog (copy-to-clipboard)
  - Or a "Set New Password" field

**Interactions:**
- Add → drawer opens → fill → Save → staff appears in table → toast
- Edit → drawer with current values → modify → Save
- Reset Password → confirmation → new password displayed in modal → "Copy" button → toast "Password reset"
- Deactivate → confirmation → status changes (staff can no longer login)
- "Restrict to Categories" checkbox → reveals multi-select category picker (filtered to assigned location)

**States:**
- Loading: skeleton
- Empty: "No staff members yet. Add your first delivery staff."
- Success: table
- Drawer validation: phone required, unique; password strength indicator on create

---

### Screen 3: Offers / Notifications (A08)
**Purpose:** Create and manage promotional offers that appear in the customer app.

**UI Elements:**
- Page title: "Offers & Promotions"
- **Scope filter:** "All" | "Global" | specific location
- **"Create Offer"** button (primary)
- **Table:**
  - Columns: Title, Target (All/Category name/Product name as chip), Discount %, Scope (Global/Location chip), Validity (start–end), Status (Active/Expired/Draft), Actions
  - Row actions: Edit, Deactivate, Delete
- **Create/Edit modal (large, or drawer):**
  - Title (text, required — e.g., "Onam Special")
  - Message/description (textarea)
  - **Target scope:** Radio — "All Categories" | "Specific Category" (picker) | "Specific Product" (picker)
    - When Category/Product selected: searchable dropdown to pick the target
  - Discount % (number, 0–100)
  - Banner image (optional upload with preview)
  - Validity: Start date + End date (date pickers)
  - Scope: Radio — "Global" | "Specific Location" (dropdown)
  - Active toggle
  - "Save" button

**Interactions:**
- Create → large modal/drawer → fill target + discount + validity → Save → appears in table → toast
- Conditional fields: target scope changes which picker appears
- Deactivate → immediately hidden from customers
- Expired offers stay in table (filterable) for reference

**States:**
- Loading: skeleton
- Empty: "No offers yet. Create your first promotion." + CTA
- Success: table with offers

---

### Screen 4: Orders Dashboard (A09)
**Purpose:** Monitor all orders by location. Operational oversight — what's pending vs delivered.

**UI Elements:**
- Page title: "Orders"
- **Summary cards row:** Pending (amber), Confirmed (blue), Delivered (teal), Completed (green), Cancelled (muted) — each showing count
- **Filter bar:** Location selector, Status dropdown, Date range picker, Search (order # or customer name)
- **Orders table:**
  - Columns: Order #, Customer, Location, Delivery Time, Items, Total (₹), Payment (preference → collected), Status (chip), Staff, Date
  - Default sort: **pending/oldest first**
  - Row click → Order Detail (A10)
  - Pagination (25/50/100 per page)
- Optional: "Export CSV" button (secondary)

**Interactions:**
- Filter/search → table updates live
- Click row → Order Detail (A10)
- Summary cards also act as quick filters (click "Pending" card → filters table to Pending)

**States:**
- Loading: skeleton summary + table
- Empty: "No orders for this scope."
- Error: retry
- Success: summary cards + table

---

### Screen 5: Order Detail (A10)
**Purpose:** Full read-only view of a single order (admin oversight, not fulfillment).

**UI Elements:**
- Page title: "Order #EZC-2024-0042" + breadcrumb (Orders > Order #)
- **Status timeline** (horizontal stepper): Pending → Confirmed → Delivered → Completed (with timestamps)
- **Two-column layout:**
  - Left: Order items table (product, weight, qty, price, line total), subtotal, total
  - Right: Customer info (name, WhatsApp, address, landmark), delivery time, payment preference, payment collected (if completed), assigned staff
- **Invoice section:** "Download Invoice" link/button
- No action buttons (admin is read-only for fulfillment in this version)

**States:**
- Loading: skeleton
- Error: retry
- Success: full detail

---

### Screen 6: Sales Reports (A11)
**Purpose:** Total sales by service location. Revenue visibility.

**UI Elements:**
- Page title: "Sales Reports"
- **Filter bar:** Location selector ("All" or specific), Date range (Today / This Week / This Month / Custom)
- **Total sales headline:** Large ₹ figure (e.g., "₹1,24,500") for the selected scope/period
- **Breakdown table:**
  - Columns: Location, Orders (completed), Total Sales (₹), Gpay Collected (₹), Cash Collected (₹)
  - Row per location; or single row if location-specific
  - Footer: grand totals
- **Simple bar chart** (optional): sales over time (daily/weekly bars)
  - Below the chart: text summary as accessible alternative
- "Export CSV" button (secondary)

**Interactions:**
- Change location/date range → data refreshes
- Chart updates with filters
- Export downloads CSV

**States:**
- Loading: skeleton
- Empty: "No completed orders in this period." (Only completed orders count — Q7)
- Error: retry
- Success: headline + table + chart

---

### Screen 7: Customer History Lookup (A12)
**Purpose:** Look up any customer by name or WhatsApp number and see their purchase history.

**UI Elements:**
- Page title: "Customer Lookup"
- **Search bar** (prominent, full-width): placeholder "Search by name or WhatsApp number..."
- **Search results** (appears after typing):
  - Customer cards/rows: Name, WhatsApp number, Service Location, Total Orders, Total Spent (₹)
  - Click a customer → expands/navigates to their detail
- **Customer detail view** (below or as a sub-page):
  - Profile summary: name, number (masked partially for privacy), location, registered date
  - **Order history table:** Order #, Date, Items, Total, Status (chip), Payment
  - Pagination
  - Click an order → Order Detail (A10)

**Interactions:**
- Type in search → results appear (debounced)
- Click customer → shows their profile + orders
- Click an order → full order detail

**States:**
- Idle: empty prompt "Search for a customer to view their history"
- Loading: searching spinner
- Empty: "No customer found matching '[query]'"
- Error: retry
- Success: results + detail

---

## Show:
1. Delivery Times table with the warning banner (no times for a location)
2. Staff Management with the Add Staff drawer open, showing permissions checkboxes
3. Offers create modal with "Specific Category" selected and the category picker visible
4. Orders Dashboard with summary cards + populated table (mixed statuses)
5. Sales Reports with the headline figure + breakdown table + simple bar chart
6. Customer Lookup with a customer selected, showing their order history table
7. Order Detail (A10) with the horizontal status timeline showing "Delivered" as current step
