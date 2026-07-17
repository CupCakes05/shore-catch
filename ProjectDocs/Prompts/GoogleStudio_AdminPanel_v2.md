# Muciriz Admin Panel — Google AI Studio STRICT Rebuild Prompt (v2)

> ⚠️ CRITICAL: This prompt defines EXACT screens. Do NOT invent, add, rename, or reimagine ANY functionality. Build ONLY what is specified below. No cold storage modules. No courier dispatch. No AI copilots. No temperature sensors. No loyalty tiers. ONLY the 15 screens listed here.

---

## 🚫 DO NOT BUILD (Explicitly Forbidden)

The following features are NOT part of this project. Do NOT generate them under any circumstances:

- ❌ Cold storage / refrigerator monitoring / temperature sensors
- ❌ Courier dispatch center / driver assignments / packing slips
- ❌ AI chat assistant / Gemini copilot / AI logistics
- ❌ Loyalty tiers / badges (Bronze, Silver, Gold, Platinum)
- ❌ Waste tracking / freshness rotation / spoilage logs
- ❌ Istanbul drivers / Turkish locale — this is Kerala, India
- ❌ Real-time sensor readings / thermal safety
- ❌ SVG category charts / custom interactive charts
- ❌ Any screen or module not explicitly listed in the SCREENS section below

If you feel the urge to add something "cool" or "premium" — DON'T. Follow the spec exactly.

---

## WHAT THIS PROJECT ACTUALLY IS

**Muciriz** is a simple local fresh-goods ecommerce admin panel for a small business in Kerala, India that sells fish, vegetables, and meat via WhatsApp-based ordering.

**It is NOT:** A logistics platform, a cold chain management system, a courier app, or an AI-powered anything.

**Business model:** Admin manages catalog (products by location), staff fulfills orders, customers order via a mobile app. That's it.

**Tech stack:** Next.js (App Router) + TypeScript  
**Target:** Desktop-first, responsive to 768px tablet  
**Locale:** Kerala, India. Currency: ₹. Language: English only.  
**Locations:** Aluva, Perumbavoor, Kalady, Angamaly (small towns, not cities)

---

## DESIGN SYSTEM (Use Exactly — No Additions)

### Colors (ONLY these — no other colors allowed)
| Token | Hex | Usage |
|-------|-----|-------|
| `--color-bg` | `#FFFFFF` | Page backgrounds, card backgrounds |
| `--color-text` | `#000000` | Primary text, headings |
| `--color-text-secondary` | `#6B7280` | Secondary/helper text |
| `--color-accent` | `#0E7C86` | Buttons, links, active nav, highlights |
| `--color-accent-hover` | `#0A5F67` | Button hover state |
| `--color-accent-light` | `#E6F4F5` | Active nav background, subtle highlights |
| `--color-border` | `#E5E7EB` | Input borders, table dividers, card borders |
| `--color-error` | `#DC2626` | Error messages, destructive actions |
| `--color-success` | `#16A34A` | Success toasts, profit indicators |
| `--color-warning` | `#F59E0B` | Warning banners, pending states |

### Status Chips
| Status | Background | Text Color |
|--------|-----------|------------|
| Pending | `#FEF3C7` | `#92400E` |
| Confirmed | `#DBEAFE` | `#1E40AF` |
| Delivered | `#E6F4F5` | `#0E7C86` |
| Completed | `#DCFCE7` | `#166534` |
| Cancelled | `#FEE2E2` | `#991B1B` |
| Active | `#DCFCE7` | `#166534` |
| Inactive | `#F3F4F6` | `#6B7280` |

### Typography
- Primary font: `Inter` (system sans-serif fallback)
- Headings: bold — 24px (h1), 20px (h2), 16px (h3)
- Body: 14px regular
- Helper text: 12px, secondary color
- NO monospace fonts in the UI. NO JetBrains Mono.

### Spacing
- 4px grid, card padding 24px, table cells 12px 16px, section gaps 24px
- Border radius: 8px (cards, modals), 6px (inputs, buttons, chips)
- Card shadow: `0 1px 3px rgba(0,0,0,0.1)` — nothing heavier

---

## REUSABLE COMPONENTS (Build Once, Reuse Everywhere)

Create these in `/components/ui/` and use the SAME component on every screen. NO one-off variants.

| # | Component | Purpose |
|---|-----------|---------|
| 1 | `<AppShell>` | Fixed sidebar (240px) + top bar (64px) + scrollable content |
| 2 | `<Sidebar>` | Logo "Muciriz" + nav items with Lucide icons + active state (teal bg + left border) |
| 3 | `<TopBar>` | Page title + Location Selector dropdown + Admin avatar dropdown |
| 4 | `<DataTable>` | Reusable table: header, rows, hover, pagination, search, empty/loading states |
| 5 | `<StatusChip>` | Colored pill based on status map above |
| 6 | `<KPICard>` | Icon + number + label card |
| 7 | `<Modal>` | Centered overlay, max 560px, title + close + footer actions |
| 8 | `<Drawer>` | Right-slide panel, 480px, full height, sticky footer |
| 9 | `<FormInput>` | Outlined input, focus teal border, error state, label above |
| 10 | `<Button>` | Primary (teal), Outline, Destructive (red), Disabled, Loading states |
| 11 | `<Toast>` | Bottom-right auto-dismiss notifications |
| 12 | `<ConfirmDialog>` | Warning modal with Cancel + Confirm buttons |
| 13 | `<MultiSelect>` | Dropdown with checkboxes + selected chips |
| 14 | `<Tabs>` | Horizontal tabs with teal underline indicator |

### Sidebar Navigation Items (EXACT — no additions or removals):
1. Dashboard (LayoutDashboard icon)
2. Service Locations (MapPin)
3. Categories (Grid3x3)
4. Products (Package)
5. Delivery Times (Clock)
6. Staff (Users)
7. Offers (Tag)
8. Orders (ShoppingBag)
9. Reports (BarChart3)
10. Customers (UserSearch)
11. Inventory (Warehouse)
12. ── separator ──
13. Settings (Settings) — bottom
14. Logout (LogOut) — bottom

---

## ROUTING (Exact Paths — No Others)

```
/login                → Login screen (no AppShell)
/dashboard            → Dashboard
/locations            → Service Locations
/categories           → Categories
/products             → Products
/delivery-times       → Delivery Times
/staff                → Staff Management
/offers               → Offers
/orders               → Orders Dashboard
/orders/[id]          → Order Detail
/reports              → Sales Reports
/customers            → Customer History
/inventory            → Inventory Management
/settings             → Profile & Settings
```

NO other routes. NO /cold-chain. NO /dispatch. NO /copilot. NO /freshness.

---

## SCREENS (Build EXACTLY These 15 — Nothing More, Nothing Less)


### A01 — Login (`/login`)
- Full-page centered card, NO sidebar, NO topbar
- "Muciriz" logo text (teal), "Admin Login" heading
- Email input + Password input (show/hide eye icon) + "Login" button (full-width primary)
- "Forgot Password?" link below
- Error state: red text below button
- Loading state: button shows spinner
- That's ALL. No social login. No registration. No OTP.

### A02 — Dashboard (`/dashboard`)
- 6 KPI cards in 3×2 grid: Total Orders, Total Sales (₹), Active Offers, Staff Count, Categories, Products
- KPIs filter when global location selector changes
- Quick action buttons row (Add Product, New Order, View Reports)
- Recent orders mini-table (5 most recent, basic columns)
- NO charts. NO SVG visualizations. NO real-time alerts. NO waste rates.

### A03 — Service Locations (`/locations`)
- DataTable columns: Name, Active (toggle), Categories count, Products count, Orders count, Created date, Actions
- "Add Location" button → Modal with single name field
- Row actions: Edit (opens modal), Deactivate (confirm dialog)
- Standard search + pagination
- That's ALL. No maps. No geolocation. No zones.

### A04 — Categories (`/categories`)
- Scoped by global location selector
- DataTable: Thumbnail (48px), Name, Locations (chips), Active, Products count, Sort order, Actions
- "Add Category" → Modal: name, image upload (drag+drop, preview), locations multi-select, sort order number
- Row actions: Edit, Manage Recommendations (→ Drawer with product picker), Deactivate

### A05 — Products (`/products`)
- Filter bar: Category dropdown + Status tabs (All | Approved | Pending | Rejected)
- DataTable: Image, Name, Gross Wt, Net Wt, Price (₹), GST chip, Locations, Stock, Status, Approval badge, Added By, Actions
- "Add Product" → Drawer: name, gross weight, net weight, price, GST toggle + %, locations multi-select, image upload
- Pending products show Approve/Reject action buttons
- NO temperature sensors. NO thermal safety. NO lot registration.

### A06 — Delivery Times (`/delivery-times`)
- Tabs: Global | Per Location
- DataTable: Label (e.g. "Morning 7-9am"), Scope chip (Global/Location name), Active, Actions
- "Add Delivery Time" → Modal: label text + scope radio (Global or Specific Location with dropdown)
- Warning banner shown if any location has zero active delivery times

### A07 — Staff Management (`/staff`)
- DataTable: Name, Phone, Locations (chips), Permissions (chips), Active, Actions
- "Add Staff" → Drawer: name, phone number, password, locations multi-select, permission toggles:
  - View Orders, Confirm Order, Confirm Delivery, Confirm Payment
  - Add Products, Restrict Categories (shows picker), Update Stock, Record Misc Purchases, Place Shop Sales
- "Reset Password" row action → Modal with new password field

### A08 — Offers (`/offers`)
- Filter by scope + DataTable: Title, Target (chip: All/Category/Product), Discount %, Scope, Validity dates, Active, Actions
- "Create Offer" → Modal: title, message, target radio (All/Category/Product + picker), discount %, banner image upload, date range, scope (Global/Location)

### A09 — Orders Dashboard (`/orders`)
- Summary row: 6 status count cards (Pending, Confirmed, Delivered, Completed, Cancelled, Pre-orders) — each colored per status
- Filter bar: Status dropdown, Date range picker, Pre-order toggle, Search
- DataTable: Order # (format M2026#XXXXX), Customer name, Location, Delivery Date, Time Slot, Total (₹), Payment method, Status chip, Staff name, Placed By (Staff/Customer), Pre-order badge, Created date
- Default sort: Pending first, oldest first
- Clicking a row navigates to `/orders/[id]`
- NO courier dispatch. NO driver assignment. NO packing slips.

### A10 — Order Detail (`/orders/[id]`)
- Breadcrumb: Orders > Order #M2026#00015
- Header: large status chip + pre-order badge + "Placed by: Staff/Customer" label
- Two-column layout:
  - Left: Customer info (name, phone, address), Delivery details (date, time slot, location)
  - Right: Payment info (method, status), Invoice download button
- Items table: Product, Weight, Qty, Unit Price, GST Rate, GST Amount, Line Total
- Totals section: Subtotal, GST breakdown by rate, Grand Total
- Fulfillment timeline: vertical stepper showing state transitions with timestamps and staff names
- "Download Invoice" outline button

### A11 — Sales Reports (`/reports`)
- Date range filter + location selector
- 4 Tabs:
  - Sales: headline ₹ figure + table (location, orders count, total sales, GPay amount, cash amount)
  - Profit/Loss: 3 KPI cards (Total Sale ₹, Total Expense ₹, Net Profit/Loss — green or red)
  - Staff Performance: table (name, orders handled, avg confirm time, avg deliver time)
  - Misc Purchases: table (date, staff, item description, qty, cost ₹, location)

### A12 — Customer History (`/customers`)
- Large search bar: "Search by name or WhatsApp number"
- Results: cards showing name, WhatsApp number, location, total orders, total spent (₹)
- Selected customer: profile summary card + "Edit Customer" button + order history table below
- Edit Customer → Modal: name, address, landmark, location dropdown (WhatsApp number shown but not editable)
- NO loyalty tiers. NO badges. NO prep preferences.

### A13 — Profile & Settings (`/settings`)
- Two sections via tabs or cards:
  - Profile: admin info display + change password form (current, new, confirm) + Logout button
  - System Config: form fields for GPay/UPI ID, Merchant Name, Customer Care Phone, Customer Care WhatsApp + Save button

### A14 — Error States (Globally Applied)
- 404 page: simple illustration + "Page not found" + "Back to Dashboard" button
- 500 page: illustration + "Something went wrong" + "Retry" button
- Session expired: auto-redirect to /login + toast message
- Offline: top banner "You're offline — check your connection" + retry button

### A15 — Inventory Management (`/inventory`)
- 3 Tabs:
  - Stock Levels: table (product, category, location, current stock kg, status chip green/amber/red based on level), inline quick edit (± buttons)
  - Purchase Records: "Record Purchase" button → Modal (product, location, cost price ₹, quantity, weight, supplier name, date)
  - Profit/Loss Summary: 3 KPI cards (Total Sales, Total Purchases, Net) + "View Full Reports" link
- NO temperature monitoring. NO cold chain. NO freshness rotation. NO waste logs.

---

## GLOBAL RULES (Enforce Strictly)

1. **ONLY these 15 screens exist.** If a screen isn't listed above, it doesn't exist. Don't create it.
2. **Component reuse is mandatory.** Every table = `<DataTable>`. Every popup = `<Modal>` or `<Drawer>`. Every input = `<FormInput>`. No custom one-off components.
3. **Color discipline.** ONLY the hex values from the design system table. No dark mode. No gradients. No new grays or blues.
4. **No invented features.** If functionality isn't described in a screen spec above, don't add it. No "nice to have" extras.
5. **State handling per screen:** Loading (skeleton shimmer), Empty (centered message + CTA), Error (message + retry), Success (populated data). Default to success with sample data.
6. **Location scoping:** The TopBar global location selector filters data on: Dashboard, Categories, Products, Orders, Reports, Inventory. "All Locations" = aggregate view.
7. **Navigation:** Every sidebar item navigates correctly. Active item is highlighted. Page title in TopBar matches current page.
8. **Indian context:** ₹ currency, Kerala location names, Indian customer/staff names, order format M{YEAR}#{5-digit sequence}.

---

## SAMPLE DATA (Use This — Not Turkish/Generic Names)

- **Locations:** Aluva, Perumbavoor, Kalady, Angamaly
- **Categories:** Fresh Fish, Vegetables, Meat & Poultry, Prawns & Crabs
- **Products:** Karimeen (Pearl Spot) 500g ₹320, Seer Fish 1kg ₹850, Chicken Breast 500g ₹180, Prawns Large 500g ₹420, Lady's Finger 250g ₹35, Tomato 500g ₹25
- **Staff:** Rahul K, Priya S, Mohammed A, Deepak R
- **Customers:** Arun Kumar (+91 98765 43210), Sneha R (+91 87654 32109), Thomas P (+91 76543 21098)
- **Orders:** M2026#00001 through M2026#00025
- **Currency:** Always ₹ (Indian Rupees). Never $ or €.
- **Delivery times:** "Morning 7-9am", "Morning 9-11am", "Evening 4-6pm"

---

## FINAL CHECKLIST (Verify Before Presenting)

- [ ] Exactly 15 screens + login. No extra screens invented.
- [ ] Sidebar has exactly 11 nav items + Settings + Logout. Nothing else.
- [ ] All colors match the hex table. No new colors introduced.
- [ ] Every table uses the same `<DataTable>` component.
- [ ] Every form uses `<FormInput>`, `<Button>`, `<Modal>`/`<Drawer>`.
- [ ] Sample data uses Indian names, ₹ currency, Kerala locations.
- [ ] No cold storage, no courier dispatch, no AI chat, no loyalty tiers, no temperature sensors.
- [ ] Navigation works between all screens via sidebar.
- [ ] Inter font only. No monospace.
- [ ] No dark mode. White backgrounds only.

---

## REMEMBER

You are building a SIMPLE admin panel for a small fish/meat/vegetable delivery business in Kerala. It's a CRUD app with tables, forms, and basic reports. Nothing more. Keep it clean, consistent, and exactly as specified.
