# Muciriz Admin Panel — Google AI Studio (Firebase Studio) Prompt

> Paste this entire prompt into Google AI Studio / Firebase Studio / Project IDX when importing the Stitch-exported admin panel project. This instructs Studio to regenerate all screens with consistent components, unified colors, proper navigation, and standardized styling.

---

## CONTEXT

I have a **Next.js web application** (Muciriz Admin Panel) exported from Google Stitch. The export has some inconsistencies — certain components look different across screens, colors vary slightly, and navigation isn't fully wired. I need you to **rebuild and standardize all screens** using a single, consistent component library and design system while preserving the existing layout structure and functionality.

**Project:** Muciriz — local fresh-goods ecommerce (fish, vegetables, meat) admin console  
**Platform:** Next.js (App Router preferred) with TypeScript  
**Target:** Desktop-first, responsive down to tablet (min 768px)

---

## DESIGN SYSTEM (Enforce Globally)

### Colors (STRICT — do not deviate)
| Token | Hex | Usage |
|-------|-----|-------|
| `--color-bg` | `#FFFFFF` | Page backgrounds, card backgrounds |
| `--color-text` | `#000000` | Primary text, headings |
| `--color-text-secondary` | `#6B7280` | Secondary/helper text |
| `--color-accent` | `#0E7C86` | Buttons, links, active nav, highlights |
| `--color-accent-hover` | `#0A5F67` | Button hover state |
| `--color-accent-light` | `#E6F4F5` | Active nav item background, subtle highlights |
| `--color-border` | `#E5E7EB` | Input borders, table dividers, card borders |
| `--color-error` | `#DC2626` | Error messages, destructive actions |
| `--color-success` | `#16A34A` | Success toasts, profit indicators |
| `--color-warning` | `#F59E0B` | Warning banners, pending states |

### Status Chips (Reusable Component)
| Status | Background | Text |
|--------|-----------|------|
| Pending | `#FEF3C7` | `#92400E` (amber) |
| Confirmed | `#DBEAFE` | `#1E40AF` (blue) |
| Delivered | `#E6F4F5` | `#0E7C86` (teal) |
| Completed | `#DCFCE7` | `#166534` (green) |
| Cancelled | `#FEE2E2` | `#991B1B` (muted red) |
| Active | `#DCFCE7` | `#166534` |
| Inactive | `#F3F4F6` | `#6B7280` |

### Typography
- Font family: `Inter` (or system sans-serif fallback)
- Headings: bold, sizes 24px (h1), 20px (h2), 16px (h3)
- Body: 14px regular
- Small/helper: 12px, `--color-text-secondary`
- All text: English only

### Spacing & Layout
- Base unit: 4px grid
- Card padding: 24px
- Table cell padding: 12px 16px
- Section gaps: 24px
- Border radius: 8px (cards, modals), 6px (inputs, buttons, chips)

### Shadows
- Cards: `0 1px 3px rgba(0,0,0,0.1)`
- Modals/Drawers: `0 4px 20px rgba(0,0,0,0.15)`
- No heavy drop shadows anywhere

---

## REUSABLE COMPONENTS (Create Once, Use Everywhere)

Standardize these components and reuse them across ALL screens. Do NOT recreate them with different styles per screen:

### 1. `<AppShell>` — Global Layout
- Fixed left sidebar (240px, white bg, right border)
- Fixed top bar (64px height, white bg, bottom border)
- Scrollable content area (padding: 24px)

### 2. `<Sidebar>`
- Logo: "Muciriz" wordmark at top (teal color, 20px bold)
- Nav items with Lucide icons (20px):
  - Dashboard (LayoutDashboard)
  - Service Locations (MapPin)
  - Categories (Grid3x3)
  - Products (Package)
  - Delivery Times (Clock)
  - Staff (Users)
  - Offers (Tag)
  - Orders (ShoppingBag)
  - Reports (BarChart3)
  - Customers (UserSearch)
  - Inventory (Warehouse)
- Bottom: Settings (Settings), Logout (LogOut)
- Active state: `--color-accent-light` background, `--color-accent` text, left 3px teal border
- Inactive: `--color-text-secondary`, no background
- On tablet (<1024px): collapse to icons only (56px wide)

### 3. `<TopBar>`
- Left: Page title (h1, 24px bold)
- Center-right: Global Location Selector (`<Select>` with "All Locations" default)
- Far-right: Admin avatar (circle, 32px) + name + chevron dropdown (Profile, Settings, Logout)

### 4. `<DataTable>` 
- Header: bold 12px uppercase, `--color-text-secondary`, bottom border
- Rows: 14px, hover `#F9FAFB` background
- Pagination: "Showing 1–10 of 45" + prev/next buttons
- Search bar above: outlined input with search icon
- Empty state: centered illustration + message + CTA button
- Loading state: 5 shimmer rows (skeleton animation)

### 5. `<StatusChip>`
- Rounded pill (6px radius, 12px text, 4px 12px padding)
- Color based on status map above

### 6. `<KPICard>`
- White card, border, 8px radius
- Icon (teal circle bg, 40px), number (24px bold), label (12px secondary)
- Clickable (cursor pointer, hover slight lift)

### 7. `<Modal>` and `<Drawer>`
- Modal: centered, max-width 560px, backdrop blur, 8px radius
- Drawer: slides from right, 480px width, full height
- Both: header with title + close (X) button, scrollable body, sticky footer with action buttons

### 8. `<FormInput>`
- Outlined style: 1px `--color-border`, 6px radius, 14px text
- Focus: 2px `--color-accent` border (no shadow)
- Error: red border + red helper text below
- Label above (14px medium)

### 9. `<Button>`
- Primary: `--color-accent` bg, white text, 6px radius, 14px medium, 40px height
- Secondary/Outline: white bg, `--color-accent` border + text
- Destructive: `--color-error` bg, white text
- Disabled: opacity 0.5, no pointer
- Loading: spinner icon replaces text

### 10. `<Toast>`
- Bottom-right, auto-dismiss 4s
- Success: green left border
- Error: red left border
- Info: teal left border

### 11. `<ConfirmDialog>`
- Modal with warning icon, message, two buttons (Cancel outline + Confirm primary/destructive)

### 12. `<MultiSelect>`
- Dropdown with checkboxes, selected items as chips below
- Search within dropdown

### 13. `<Tabs>`
- Horizontal tabs with underline active indicator (teal, 2px)
- 14px medium text, `--color-text-secondary` for inactive

---

## NAVIGATION & ROUTING

Implement proper Next.js routing with these paths:

```
/login                → A01 Login (no sidebar/topbar)
/dashboard            → A02 Dashboard
/locations            → A03 Service Locations
/categories           → A04 Categories
/products             → A05 Products
/delivery-times       → A06 Delivery Times
/staff                → A07 Staff Management
/offers               → A08 Offers
/orders               → A09 Orders Dashboard
/orders/[id]          → A10 Order Detail
/reports              → A11 Sales Reports
/customers            → A12 Customer History
/inventory            → A15 Inventory Management
/settings             → A13 Profile & Settings
/privacy              → P01 Privacy Policy (public, no auth)
/terms                → P02 Terms & Conditions (public, no auth)
```

**Navigation behavior:**
- Sidebar nav items use `<Link>` with active detection based on current path
- Clicking sidebar item navigates immediately (no full-page reload)
- All authenticated routes wrapped in auth check → redirect to `/login` if no session
- `/login` redirects to `/dashboard` if already authenticated
- Back navigation: browser back + breadcrumbs on detail pages
- `/privacy` and `/terms` are public routes (no AppShell, simple centered layout)

---

## SCREENS TO GENERATE

### A01 — Login (`/login`)
- Full-page centered card (no sidebar)
- Muciriz logo (teal), "Admin Login" heading
- Email field + Password field (show/hide toggle) + "Login" button (full-width, primary)
- "Forgot Password?" link (teal text)
- Error: inline red text below button
- Loading: button shows spinner

### A02 — Dashboard (`/dashboard`)
- 6 KPI cards in responsive grid (3×2 on desktop, 2×3 on tablet)
  - Total Orders, Total Sales (₹), Active Offers, Staff Count, Categories, Products
- KPIs respond to global location selector
- Quick action buttons row below KPIs
- Optional: recent orders mini-table (5 rows)

### A03 — Service Locations (`/locations`)
- DataTable: Name, Active (toggle), Categories count, Products, Orders, Created, Actions
- "Add Location" button → Modal (name field only)
- Row actions: Edit (modal), Deactivate (confirm dialog)
- Search + pagination

### A04 — Categories (`/categories`)
- Location scoped (uses global selector)
- DataTable: Image (48px thumbnail), Name, Locations (chips), Active, Products count, Sort order, Actions
- "Add Category" button → Modal: name, image upload (drag-drop + preview), locations multi-select, sort order
- "Manage Recommendations" action → Drawer: searchable product picker with chips
- Row actions: Edit, Recommendations, Deactivate

### A05 — Products (`/products`)
- Filter bar: Category dropdown + Approval filter tabs (All | Approved | Pending | Rejected)
- DataTable: Image, Name, Gross Wt, Net Wt, Price (₹), GST (toggle chip), Locations, Stock summary, Status, Approval badge, Added By, Actions
- "Add Product" → Drawer: name, weights, price, GST toggle + %, locations multi-select, image upload
- Pending items show Approve (green) / Reject (red) action buttons
- "Manage Recommendations" → product picker drawer

### A06 — Delivery Times (`/delivery-times`)
- Tabs: Global | Per Location (with location dropdown)
- DataTable: Label, Scope (chip), Active, Actions
- "Add Delivery Time" → Modal: label + scope radio (Global / Specific location + dropdown)
- Warning banner if no active times for any location

### A07 — Staff Management (`/staff`)
- DataTable: Name, Phone, Locations (chips), Permissions (chips), Active, Actions
- "Add Staff" → Drawer: name, phone, password, locations multi-select, permission toggles:
  - View Orders ✓, Confirm Order ✓, Confirm Delivery ✓, Confirm Payment ✓
  - Add Products ☐, Restrict Categories ☐ (shows category picker), Update Stock ☐, Record Misc Purchases ☐, Place Shop Sales ☐
- "Reset Password" action → Modal with new password field

### A08 — Offers (`/offers`)
- Scope filter + DataTable: Title, Target (chip), Discount %, Scope, Validity dates, Active, Actions
- "Create Offer" → Modal: title, message, target radio (All/Category/Product + picker), discount %, banner image, validity date range, scope (Global/Location)

### A09 — Orders Dashboard (`/orders`)
- Summary cards row: Pending, Confirmed, Delivered, Completed, Cancelled, Pre-orders (colored per status)
- Filter bar: Status, Date range, Pre-order toggle, Search
- DataTable: Order #, Customer, Location, Delivery Date, Time Slot, Total (₹), Payment, Status chip, Staff, Placed By, Pre-order badge, Date
- Default sort: pending/oldest first
- Row click → `/orders/[id]`

### A10 — Order Detail (`/orders/[id]`)
- Breadcrumb: Orders > Order #M2026#00015
- Large status chip + Pre-order badge + "Placed by: Staff/Customer"
- Two-column: Left (customer info, delivery details) | Right (payment info, invoice download)
- Items table: Product, Weight, Qty, Unit Price, GST Rate, GST Amount, Line Total
- Totals: Subtotal, GST breakdown by rate, Grand Total
- Fulfillment timeline (vertical stepper with timestamps and staff names)
- "Download Invoice" button (outline)

### A11 — Sales Reports (`/reports`)
- Date range filter + location selector
- 4 Tabs: Sales | Profit/Loss | Staff Performance | Misc Purchases
- Sales: headline figure + breakdown table (location, orders, sales, GPay, cash)
- Profit/Loss: 3 KPI cards (Total Sale, Total Expense, Net Profit/Loss — green/red)
- Staff Performance: table (name, orders, avg confirm time, avg deliver time)
- Misc Purchases: table (date, staff, item, qty, cost, location)

### A12 — Customer History (`/customers`)
- Prominent search bar: "Search by name or WhatsApp number"
- Results: cards with name, WhatsApp, location, total orders, total spent
- Selected customer: profile summary + "Edit Customer" button (outline) + order history table
- Edit Customer → Modal: name, address, landmark, location dropdown (WhatsApp non-editable)

### A13 — Profile & Settings (`/settings`)
- Two sections (tabs or cards):
  - Profile: display info + change password form + logout button
  - System Config: GPay/UPI ID, Merchant Name, Customer Care Phone, Customer Care WhatsApp + Save button

### A14 — Error States (Global)
- 404: illustration + "Page not found" + "Back to Dashboard" link
- 500: illustration + "Something went wrong" + "Retry" button
- 401/Session Expired: auto-redirect to login with toast
- Network offline: top banner "You're offline" + retry

### A15 — Inventory (`/inventory`)
- 3 Tabs: Stock Levels | Purchase Records | Profit/Loss Summary
- Stock Levels: table with product, category, location, current stock, status indicator (green/amber/red chips), inline edit
- Purchase Records: "Record Purchase" button → Modal (product, location, cost price, qty, weights, supplier, date)
- Profit/Loss: 3 KPI cards + link to Reports

---

## GLOBAL BEHAVIOR RULES

1. **Component Reuse:** Every table uses `<DataTable>`, every modal uses `<Modal>`, every form input uses `<FormInput>`. No ad-hoc one-off styled inputs or tables.
2. **Color Consistency:** Use ONLY the color tokens defined above. No random grays, blues, or accent variations.
3. **State Handling:** Every data-fetching screen must handle: Loading (skeleton/shimmer), Empty (illustration + message + CTA), Error (message + retry button), Success (populated data).
4. **Responsive:** Content reflows at 1024px (sidebar collapses). Tables become scrollable horizontally on tablet. KPI grids reflow to 2 columns.
5. **Feedback:** Every create/edit/delete action shows a toast. Destructive actions require a confirm dialog.
6. **Navigation:** Active sidebar item always highlighted. Page title in TopBar matches current route. Breadcrumbs on detail/sub-pages.
7. **Location Scoping:** The global location selector in TopBar affects Dashboard, Categories, Products, Orders, Reports, Inventory. "All Locations" shows aggregate; specific location scopes data.

---

## SAMPLE DATA

Use realistic sample data with Indian context:
- **Locations:** Aluva, Perumbavoor, Kalady, Angamaly
- **Categories:** Fresh Fish, Vegetables, Meat & Poultry, Prawns & Crabs
- **Products:** Karimeen (Pearl Spot) 500g ₹320, Seer Fish 1kg ₹850, Chicken Breast 500g ₹180, Prawns Large 500g ₹420
- **Staff:** Rahul K, Priya S, Mohammed A
- **Customers:** Arun Kumar, Sneha R, Thomas P
- **Order numbers:** M2026#00001 through M2026#00025
- **Currency:** Always ₹ (Indian Rupees)

---

## WHAT TO GENERATE

Generate the **complete Next.js project** with:
1. All 15 admin screens (A01–A15) + public pages (/privacy, /terms)
2. Shared component library in `/components/ui/` (all reusable components listed above)
3. Proper routing with navigation between all screens
4. Global layout (AppShell with Sidebar + TopBar) applied to all authenticated routes
5. Mock data for all tables and KPIs (realistic, as specified above)
6. All four states per screen (loading, empty, error, success) — default to success with populated data
7. Responsive behavior (sidebar collapse, table scroll, grid reflow)
8. Consistent styling using the exact color tokens and component specs above

**Priority:** Component consistency and navigation completeness over pixel-perfection. Every screen should look like it belongs to the same application.

---

## IMPORTANT NOTES

- The Stitch export may have generated different button styles, input styles, or table layouts across screens. **Override all of those** with the standardized components defined above.
- If Stitch used different shades of teal or gray, **replace them** with the exact hex values in this prompt.
- Do NOT add any colors, fonts, or component variants not specified here.
- Navigation must be fully wired — clicking any sidebar item or any link/button that references another screen should navigate correctly.
- Use `next/link` for navigation, `next/image` for optimized images.
- TypeScript strict mode. Clean code with proper types.
