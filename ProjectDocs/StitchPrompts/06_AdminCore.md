# Batch 6 — Admin Panel Core (A01, A02, A03, A04, A05)

> Admin web console: login, dashboard, and catalog management (locations, categories, products).

---

Design the **Admin Panel** (Next.js, web — desktop/tablet). This is a professional web console with a persistent sidebar layout. Same color system: white background, black text, #0E7C86 accent.

**Layout structure (applies to all admin screens):**
- **Left sidebar** (fixed, ~240px):
  - Eze-Cart logo at top
  - Navigation links with icons: Dashboard, Service Locations, Categories, Products, Delivery Times, Staff, Offers, Orders, Sales Reports, Customer History
  - Active item: teal background/text; others: dark gray text
  - Bottom of sidebar: Settings, Logout
- **Top bar** (horizontal):
  - Left: Page title breadcrumb
  - Center/right: **Global Service Location selector** (dropdown: "All Locations" or specific one — many screens filter by this)
  - Far right: Admin avatar/name + dropdown (Profile, Logout)
- **Content area:** main working space with tables, cards, forms

---

### Screen 1: Login (A01)
**Purpose:** Admin authentication. Clean, centered form (no sidebar yet).

**UI Elements:**
- Centered card on white/light-gray background:
  - Eze-Cart logo at top
  - "Admin Login" heading
  - Email/username field (outlined)
  - Password field (outlined, show/hide)
  - "Login" button (primary, full-width of card)
  - "Forgot password?" link below (teal) → triggers password reset email
- Error messages below form fields or as alert banner

**States:**
- Idle: blank form
- Loading: button spinner
- Error: "Invalid credentials" alert; network error alert
- Success: redirect to Dashboard

---

### Screen 2: Dashboard / Home (A02)
**Purpose:** Overview KPIs and quick navigation after login.

**UI Elements (in content area):**
- **KPI cards row** (4-6 cards):
  - Total Orders (with pending count highlighted)
  - Total Sales (₹) (this period)
  - Active Offers
  - Staff Count
  - Categories Count
  - Products Count
- Each KPI card: icon + number + label, clickable (navigates to relevant section)
- **Recent Orders mini-table** below KPIs:
  - 5 most recent orders: order #, customer, status chip, total
  - "View All Orders →" link
- Location selector in top bar scopes the KPIs

**States:**
- Loading: skeleton KPI cards + table
- Empty: "Welcome! Start by creating your first Service Location." + CTA button
- Error: retry
- Success: populated KPIs + recent orders

---

### Screen 3: Service Locations (A03)
**Purpose:** Manage the top-level service locations. Everything cascades from here.

**UI Elements (in content area):**
- Page title: "Service Locations"
- **"Add Location"** button (top-right, primary)
- **Data table:**
  - Columns: Name, Status (Active/Inactive chip), Categories, Products, Customers, Orders, Created
  - Row actions (icon buttons or dropdown): Edit, Deactivate, Delete
  - Pagination + search bar above table
- **Add/Edit modal:**
  - Field: Location Name (text, required)
  - "Save" button (primary)
- **Delete confirmation dialog:**
  - Warning: "Deactivating this location will hide it from customers. All categories, products, and orders remain for reporting."
  - "Deactivate" (amber) / "Cancel"

**Interactions:**
- Add → modal opens → fill name → Save → location appears in table → success toast
- Edit → modal with current name → Save → updated
- Deactivate → confirmation dialog → confirms → status chip changes to "Inactive"
- Click a location row → navigates to Categories (A04) filtered to that location

**States:**
- Loading: table skeleton
- Empty: illustration + "No service locations yet. Create your first one to get started." + Add button
- Error: retry
- Success: table with data

---

### Screen 4: Categories (A04)
**Purpose:** Manage categories per service location, with images and recommended products.

**UI Elements (in content area):**
- Page title: "Categories" + breadcrumb showing selected location
- **Location selector** filters what's shown (uses global selector from top bar)
- **"Add Category"** button (primary)
- **Data grid/table:**
  - Columns: Image (thumbnail), Name, Status (Active/Inactive), Products Count, Recommendations, Sort Order, Actions
  - Row actions: Edit, Manage Recommendations, Reorder, Deactivate, Delete
- **Add/Edit modal/drawer:**
  - Name (text, required)
  - Image upload (drag & drop zone with preview; progress bar during upload)
  - Sort order (number)
  - "Save" button
- **Manage Recommendations panel** (side drawer or modal):
  - "Recommended Products for [Category Name]"
  - Searchable product picker (products from same location, active/approved only)
  - Selected products shown as chips/tags (removable)
  - "Save Recommendations" button
- Delete/Deactivate confirmation

**Interactions:**
- Select location → categories load for that location
- Add → modal → fill name + upload image → Save → appears in table
- Manage Recommendations → drawer opens → search and add/remove products → Save
- Deactivate → dialog → cascade warning
- Click a category → navigates to Products (A05) filtered to that category

**States:**
- Loading: skeleton
- Empty (no location selected): "Select a service location first"
- Empty (no categories): "No categories for [Location]. Add your first one."
- Error: retry
- Success: table/grid with data
- Image upload: progress bar + preview

---

### Screen 5: Products (A05)
**Purpose:** Manage products per category, with images, pricing, and approval workflow for staff-submitted products.

**UI Elements (in content area):**
- Page title: "Products" + breadcrumb (Location > Category)
- **Filter bar:** Location dropdown, Category dropdown, Status filter (All / Active / Inactive / Pending Approval)
- **"Add Product"** button (primary)
- **Data table:**
  - Columns: Image (thumb), Name, Gross Weight, Net Weight, Price (₹), Status, Approval Status, Added By, Actions
  - Approval Status column: "Approved" (green chip), "Pending Approval" (amber chip), "Rejected" (red chip), "N/A" (admin-added)
  - Row actions: Edit, Manage Recommendations, Approve (if pending), Reject (if pending), Deactivate, Delete
- **Add/Edit modal/drawer:**
  - Product Name (required)
  - Gross Weight (number)
  - Net Weight (number, ≤ Gross)
  - Price ₹ (number, > 0)
  - Image upload (drag & drop, preview, progress)
  - Category selector (if adding from products page directly)
  - "Save" button
- **Manage Recommendations panel** (same pattern as categories):
  - "Recommended Products for [Product Name]"
  - Searchable picker (same location, active, approved; exclude self)
  - Chips/tags of selected products
  - "Save Recommendations"
- **Approval actions (for staff-submitted products):**
  - When viewing a "Pending Approval" product: prominent "Approve" (green) and "Reject" (red) buttons
  - Approve → product goes Active/visible to customers
  - Reject → stays hidden; staff sees "Rejected" on their end

**Interactions:**
- Filter by location/category/status → table updates
- Add → modal → fill fields + upload → Save → success toast
- Approve pending product → confirm → approved → toast "Product approved and live"
- Reject → confirm + optional rejection note → toast "Product rejected"
- Manage Recommendations → drawer → search + add/remove → Save
- Deactivate → soft-disable (product hidden from customers, preserved in orders)
- Validation: Net ≤ Gross, price > 0, name required

**States:**
- Loading: table skeleton
- Empty: "No products for this category. Add your first one."
- Error: retry
- Success: table with data
- Filter "Pending Approval": shows only staff-submitted items awaiting review

---

## Show:
1. The overall admin layout (sidebar + top bar + content area) with the Dashboard populated
2. Service Locations table with a few rows and the "Add Location" modal open
3. Categories table showing image thumbnails, with the "Manage Recommendations" drawer open
4. Products table filtered to "Pending Approval" showing the Approve/Reject action buttons
5. Products Add/Edit modal with image upload in progress
6. Dashboard in empty state (new system, no locations yet)
