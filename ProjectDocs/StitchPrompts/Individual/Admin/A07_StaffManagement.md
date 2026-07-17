# A07 — Staff Management | Muciriz Admin Panel

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Next.js web. Fixed left sidebar + top bar.

## Screen
**App:** Admin Panel (Next.js, web)
**Purpose:** Create/manage staff accounts with multi-location assignment and granular permission toggles including new capabilities (stock update, misc purchases, shop sales).

## UI Elements
- Sidebar + top bar (standard layout)
- Page title: "Staff Management"
- **"Add Staff"** button (teal filled, top-right)
- Search bar
- **Staff table:**
  - Columns: Name, Phone, Assigned Locations (chips), Active (status indicator), Permissions (summary chips), Actions
  - Permissions summary: small chips like "Orders", "Confirm", "Products", "Stock", "Misc", "Sales"
  - Actions: Edit (pencil), Reset Password (key icon), Activate/Deactivate (toggle)
  - Example: "Rajesh Kumar | +91 90123... | Ernakulam, Fort Kochi | Active ✓ | Orders, Confirm, Products, Stock"
- Pagination

**Add/Edit Staff Drawer (right slide-in):**
- Name (required)
- Phone (required, unique within staff — login identifier)
- Initial Password / Reset Password field
- **Assigned Service Locations** multi-select (checkbox dropdown, at least one required): ☑ Ernakulam ☑ Fort Kochi ☐ Mattancherry
- **Permissions section** (toggle group, clearly labeled):
  - ☑ View Orders (default: on)
  - ☐ Restrict to Categories → when on, shows category multi-select
  - ☐ Add Products (default: off)
  - ☑ Confirm Order (default: on)
  - ☑ Confirm Delivery (default: on)
  - ☑ Confirm Payment (default: on)
  - ☐ Update Stock (default: off)
  - ☐ Record Misc Purchases (default: off)
  - ☐ Place Shop Sales (default: off)
- Save / Cancel

**Reset Password Modal:**
- "Reset password for Rajesh Kumar"
- New Password field + Confirm Password
- Save / Cancel

## Interactions
- Add Staff → drawer → fill name, phone, password, locations, permissions → save → staff in table
- Edit → pre-filled drawer (password hidden)
- Reset Password → modal → new password → save (admin-driven, no staff self-reset)
- Deactivate → confirm "Staff will lose access immediately" → soft-disable
- Permissions drive what staff sees in their app

## States
- **Loading:** Table skeleton
- **Empty:** "No staff members yet. Add your first staff member." + CTA
- **Error:** Retry
- **Success:** Table with staff rows

## Navigation
- **From:** Sidebar
- **To:** N/A (self-contained)

## What to Show
- Table with 3 staff: "Rajesh Kumar" (Ernakulam + Fort Kochi, Active, permissions: Orders/Confirm/Products/Stock), "Arun Nair" (Ernakulam, Active, permissions: Orders/Confirm/Misc/Sales), "Deepa S" (Fort Kochi, Inactive/grayed). Add Staff drawer open on right showing permission toggles with some on (teal) and some off, location multi-select with 2 checked.
