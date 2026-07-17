# A05 — Products | Muciriz Admin Panel

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Next.js web. Fixed left sidebar + top bar.

## Screen
**App:** Admin Panel (Next.js, web)
**Purpose:** Manage products with GST settings, multi-location assignment, stock overview, and approval workflow for staff submissions.

## UI Elements
- Sidebar + top bar (standard layout)
- Page title: "Products"
- **Category selector** dropdown (top-left): required to scope products
- **Approval filter tabs:** All | Approved | Pending Approval | Rejected
- **"Add Product"** button (teal filled, top-right)
- Search bar
- **Products table:**
  - Columns: Image, Name, Gross Wt, Net Wt, Price (₹), GST, Locations, Stock, Status, Approval, Added By, Actions
  - GST column: "5%" or "—" (dash if not applicable)
  - Locations: chips (e.g., "Ernakulam" "Fort Kochi")
  - Stock: summary (e.g., "15 / 8" for two locations, or "Out of Stock" in red for 0)
  - Approval badge: "Approved" (green), "Pending" (amber), "Rejected" (red)
  - Added By: "Admin" or "Staff: Rajesh"
  - Actions: Edit, Approve/Reject (for pending), Recommendations, Stock, Activate, Remove

**Add/Edit Drawer (right slide-in):**
- Product Name (required)
- Gross Weight + unit (g/kg selector)
- Net Weight + unit (must ≤ Gross)
- Price ₹ (required) — "Fixed price per packet"
- **GST Applicable** toggle (switch)
- **GST Percentage** field (shown when on: number, e.g., 5, 12, 18, 28)
- **Service Locations multi-select** (at least one)
- Image upload (drag-drop or file picker, preview)
- Note: "Admin additions are auto-approved"
- Save / Cancel buttons

**Approve/Reject actions (for pending items):**
- "Approve" button (green) on row or in detail view
- "Reject" button (red outline) — with optional rejection reason

## Interactions
- Select category → table loads products for that category
- Add → drawer → fill fields (incl. GST toggle) → save → product appears (auto-approved for admin)
- Pending tab → shows staff-submitted items → Approve/Reject
- Manage Recommendations → product picker drawer
- Manage Stock → navigate to A15 or inline editor
- Edit/Remove with confirmation

## States
- **Loading:** Table skeleton
- **Empty:** "No products in this category" or "Select a category" prompt
- **Error:** Retry
- **Success:** Table with products, approval badges

## Navigation
- **From:** Categories (A04) drill-down, sidebar
- **To:** Inventory (A15) via stock link

## What to Show
- Table with 5 products in "Fresh Fish" category: Seer Fish (₹680, GST 5%, Ernakulam+Fort Kochi, Stock: 15/8, Approved, Admin), Pomfret (₹380, GST 5%, Ernakulam, Stock: 20, Approved, Admin), King Prawns (₹450, GST 5%, Ernakulam, Stock: 0 in red "Out of Stock", Approved), Tuna (₹320, —, Fort Kochi, Stock: 12, **Pending Approval amber badge**, Staff: Rajesh), Sardine (₹120, —, All, Stock: 50, Approved). Approval filter showing "All" active. Category selector showing "Fresh Fish".
