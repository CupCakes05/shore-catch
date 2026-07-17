# A04 — Categories | Muciriz Admin Panel

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Next.js web. Fixed left sidebar + top bar.

## Screen
**App:** Admin Panel (Next.js, web)
**Purpose:** Manage product categories with image upload, multi-location assignment, and recommended products configuration.

## UI Elements
- Sidebar + top bar (standard layout)
- Page title: "Categories"
- **Location filter** dropdown (top-left): "All Locations" / specific location — scopes view
- **"Add Category"** button (teal filled, top-right)
- **Categories table/grid:**
  - Columns: Image (thumbnail), Name, Assigned Locations (chips), Active, Products Count, Sort Order, Actions
  - Location chips: e.g., "Ernakulam" "Fort Kochi" (teal outline chips)
  - Row actions: Edit, Manage Recommendations, Activate/Deactivate, Remove

**Add/Edit Modal:**
- Category Name (text, required)
- **Image upload** (drag-drop zone or file picker, preview)
- **Service Locations multi-select** (checkbox dropdown, at least one required): ☑ Ernakulam ☑ Fort Kochi ☐ Mattancherry
- Sort Order (number)
- Save / Cancel

**Recommendations Drawer:**
- Title: "Recommended Products for [Category]"
- Search/filter product picker
- Current recommendations list (removable)
- Save

## Interactions
- Add → modal → upload image + select locations → save → category appears
- Edit → pre-filled modal
- Manage Recommendations → drawer with product picker
- Deactivate → confirm (hides from customers + its products)
- Filter by location → scopes table

## States
- **Loading:** Table skeleton
- **Empty:** "No categories for this location" + "Add Category" CTA
- **Error:** Retry
- **Success:** Table with categories

## Navigation
- **From:** Sidebar, drill-down from Locations (A03)
- **To:** Products (A05) filtered by category

## What to Show
- Table with 5 categories: "Fresh Fish" (Ernakulam, Fort Kochi), "Vegetables" (All 3 locations), "Meat & Chicken" (Ernakulam), "Seafood Special" (Fort Kochi), "Ready to Cook" (Ernakulam, Fort Kochi). Image thumbnails visible. Location chips on each row. Add Category button.
- Alternate: Add Category modal with multi-select locations dropdown open
