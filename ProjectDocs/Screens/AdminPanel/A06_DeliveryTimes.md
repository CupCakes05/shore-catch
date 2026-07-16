# A06 — Delivery Times

**App:** Admin Panel (Next.js, web)

## Purpose
Manage the Delivery Time options customers choose from at checkout (add/remove).

## Users
Main Admin.

## Navigation
- **From:** sidebar.

## Key UI Elements
- **Scope selector:** **Global** vs a specific **Service Location** (Q8). Global times apply everywhere unless a location defines its own.
- List/table of delivery time options: label (e.g., "Morning 8–10 AM"), scope (Global / location), active status.
- "Add Delivery Time" → form/modal: label + scope (Global or a location).
- Row actions: Edit, Activate/Deactivate, Remove.

## States
- **Loading:** skeleton.
- **Empty:** no delivery times → CTA (note: customers can't checkout without at least one — highlight this).
- **Error:** Retry.
- **Success:** list rendered.
- Add/Edit/Remove: validation + confirm states.

## Actions
- Add (`POST /delivery-times`); Edit; Remove (`DELETE`) with confirm.

## Business Rules (Confirmed Q8)
- **Global + per-location:** a global set exists; each location may define its own. **Per-location overrides global** — if a location has its own delivery times, customers there see those; otherwise the global defaults apply.
- At least one applicable (per-location or global) active delivery time must exist for customers to check out — surface a warning if none.
- **Pre-order support (Requirement #3):** delivery times define available time slots. At checkout, customers select a **date + time slot**. When today's slots are exhausted, the system presents future available dates. The delivery time options apply to all dates (same slots available daily unless extended in future).

## Validation / Permissions
- Label required. Admin only.

## Responsive / Accessibility
- Accessible list; confirmed removal.

## Dependencies
- `/delivery-times`, `/locations` (if scoped).
