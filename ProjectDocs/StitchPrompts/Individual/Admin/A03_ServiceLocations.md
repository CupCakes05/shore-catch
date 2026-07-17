# A03 — Service Locations | Muciriz Admin Panel

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Next.js web. Fixed left sidebar + top bar.

## Screen
**App:** Admin Panel (Next.js, web)
**Purpose:** Manage top-level service locations — the foundation for all other data (categories, products, staff cascade from here).

## UI Elements
- Sidebar + top bar (standard layout)
- Page title: "Service Locations"
- **"Add Location"** button (teal filled, top-right)
- Search bar: "Search locations..."
- **Locations table:**
  - Columns: Name, Status (Active toggle/chip), Categories, Products, Customers, Orders, Created
  - e.g., "Ernakulam | Active ✓ | 6 | 48 | 230 | 156 | 10 Jan 2026"
  - Row actions (icon buttons): Edit (pencil), Activate/Deactivate (toggle), Remove (trash)
- Pagination (if many)

**Add/Edit Modal:**
- Modal title: "Add Service Location" / "Edit Location"
- Location Name field (outlined, required)
- Save button (teal) + Cancel button (outline)

**Remove Confirmation Dialog:**
- "Deactivate Ernakulam?"
- Warning text: "This will hide all categories, products, and staff assignments for this location. Historical orders and reports will be preserved."
- "Deactivate" button (red) + "Cancel"

## Interactions
- Add Location → modal → save → row appears in table
- Edit → pre-filled modal → save
- Deactivate → confirm dialog (with cascade warning) → soft-delete (isActive=false)
- Search → filters table
- Click location name → drill into Categories (A04) filtered to that location

## States
- **Loading:** Table skeleton (5 rows shimmer)
- **Empty:** "No service locations yet. Create your first Service Location to get started!" + prominent CTA
- **Error:** "Couldn't load locations" + Retry
- **Success:** Table populated

## Navigation
- **From:** Sidebar
- **To:** Categories (A04) filtered by location

## What to Show
- Table with 3 locations: "Ernakulam" (active, 6 cat, 48 prod, 230 customers), "Fort Kochi" (active, 4 cat, 32 prod), "Mattancherry" (inactive, grayed). Add Location button top-right. Search bar above table.
- Alternate: Remove confirmation dialog showing cascade warning
