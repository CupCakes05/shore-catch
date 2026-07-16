# A07 — Staff Management

**App:** Admin Panel (Next.js, web)

## Purpose
Create and manage delivery staff accounts, assign each to **one or more** Service Locations (many-to-many), and configure granular per-staff accessibility/permissions.

## Users
Main Admin.

## Navigation
- **From:** sidebar.

## Key UI Elements
- **Table** of staff: name, **phone**, assigned locations (chips/tags), active status, permission summary.
- "Add Staff" → form/modal:
  - Name, **phone (login identifier — Q14)**, initial password set by Admin.
  - **Assigned Service Locations** (**multi-select** — Requirement #4). At least one required.
  - **Permission toggles:**
    - `view_orders` — see orders for assigned locations (default: On)
    - `restrict_to_categories` — limit to specific categories (+ category multi-select when on)
    - `add_products` — add/edit products, subject to Admin approval (default: Off)
    - `confirm_order` — perform Order Confirm (default: On)
    - `confirm_delivery` — perform Delivery Confirm (default: On)
    - `confirm_payment` — perform Payment Confirm (default: On)
    - `update_stock` — update stock/inventory counts for assigned locations (default: Off) — Requirement #9
    - `record_misc_purchase` — record miscellaneous operational purchases (default: Off) — Requirement #8
    - `place_shop_sale` — create Shop Sales for walk-in/counter billing (default: Off) — Requirement #12
- Row actions: Edit permissions/locations, Reset password, Activate/Deactivate.

## States
- **Loading:** table skeleton.
- **Empty:** no staff → CTA to add first staff.
- **Error:** Retry.
- **Success:** table rendered.
- Add/Edit: validation (unique identifier), submit states.
- Deactivate: confirmation.

## Actions
- Create staff (`POST /staff`).
- Edit permissions/locations (`PATCH /staff/{id}`).
- Reset password (Admin-driven — Q15).
- Activate/Deactivate (`DELETE`/soft-disable).

## Business Rules (Confirmed Q11/Q14/Q15, updated Requirement #4)
- Each staff can be assigned to **one or more** Service Locations (many-to-many via multi-select). **Login identifier = phone; auth = phone + password.**
- The **same phone** may also exist as a Customer account — staff and customer are separate identities; uniqueness is enforced **within** staff (not against customers). See `01_UserRoles.md` §8.
- Permissions drive Staff App UI and are enforced server-side.
- When `restrict_to_categories` is on, must select the allowed categories (within the assigned locations).
- When `add_products` is on, staff can add/edit products from their app, but those changes **require Admin approval** before going live (managed on A05).
- When `update_stock` is on, staff can update stock counts for products at their assigned locations.
- When `record_misc_purchase` is on, staff can record misc operational purchases.
- When `place_shop_sale` is on, staff can create Shop Sales (walk-in/counter quick billing).
- **Password reset:** Admin sets/resets staff passwords (staff cannot self-reset — Q15).
- Deactivated staff cannot log in and lose access immediately.

## Validation / Permissions
- **Phone unique within staff**; at least one location required. Admin only.

## Responsive / Accessibility
- Permission toggles clearly labeled and grouped; location and category multi-selects accessible; confirmed destructive actions.

## Dependencies
- `/staff`, `/locations`, `/categories`.
