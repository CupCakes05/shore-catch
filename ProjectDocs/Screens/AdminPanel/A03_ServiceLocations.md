# A03 — Service Locations

**App:** Admin Panel (Next.js, web)

## Purpose
Manage the top-level Service Locations. This is the foundation — categories, products, staff, offers, customers, and orders all cascade from here. Populates the customer registration dropdown.

## Users
Main Admin.

## Navigation
- **From:** sidebar.
- **To:** Categories (A04) filtered to a selected location.

## Key UI Elements
- **Table/list** of locations: name, active status, counts (categories/products/customers/orders), created date.
- "Add Location" button → form/modal (name).
- Row actions: Edit (rename), Activate/Deactivate (soft-disable recommended), Remove.
- Search/filter; pagination (if many).

## States
- **Loading:** table skeleton.
- **Empty:** no locations → prominent "Create your first Service Location" CTA.
- **Error:** Retry.
- **Success:** list rendered.
- Add/Edit form: validation + submitting states.
- Remove: confirmation dialog warning about cascade impact.

## Actions
- Add location (`POST /locations`).
- Edit/rename (`PATCH`).
- Deactivate/Remove (`DELETE` / soft-disable) — **confirmation required** (explains cascade to categories/products/customers/orders).
- Drill into a location's categories.

## Business Rules
- Removing a location affects its entire subtree and existing customers/orders. **Confirmed (Q6): soft-delete** (`isActive=false`) — preserves history & reporting; no hard delete.
- Newly added locations immediately appear in customer registration dropdown.

## Validation / Permissions
- Name required, unique (recommended). Admin only.

## Responsive / Accessibility
- Accessible table (headers, row actions labeled); destructive actions confirmed; works down to tablet.

## Dependencies
- `/locations`.
