# A07 — Staff Management

**App:** Admin Panel (Next.js, web)

## Purpose
Create and manage delivery staff accounts, assign each to a Service Location, and configure granular per-staff accessibility/permissions (view orders, restrict to categories, add products, and confirm actions).

## Users
Main Admin.

## Navigation
- **From:** sidebar.

## Key UI Elements
- **Table** of staff: name, **phone**, assigned location, active status, permission summary.
- "Add Staff" → form/modal:
  - Name, **phone (login identifier — Q14)**, initial password set by Admin.
  - **Assigned Service Location** (dropdown).
  - **Permission toggles:** `view_orders`, `restrict_to_categories` (+ category multi-select when on), `add_products`, `confirm_order`, `confirm_delivery`, `confirm_payment`.
- Row actions: Edit permissions/assignment, Reset password, Activate/Deactivate.

## States
- **Loading:** table skeleton.
- **Empty:** no staff → CTA to add first staff.
- **Error:** Retry.
- **Success:** table rendered.
- Add/Edit: validation (unique identifier), submit states.
- Deactivate: confirmation.

## Actions
- Create staff (`POST /staff`).
- Edit permissions/location (`PATCH /staff/{id}`).
- Reset password (Admin-driven — recommended for trial).
- Activate/Deactivate (`DELETE`/soft-disable).

## Business Rules (Confirmed Q11/Q14/Q15)
- Each staff tied to one Service Location. **Login identifier = phone; auth = phone + password.**
- The **same phone** may also exist as a Customer account — staff and customer are separate identities; uniqueness is enforced **within** staff (not against customers). See `01_UserRoles.md` §8.
- Permissions drive Staff App UI and are enforced server-side.
- When `restrict_to_categories` is on, must select the allowed categories (within that location).
- When `add_products` is on, staff can add/edit products from their app, but those changes **require Admin approval** before going live (managed on A05).
- **Password reset:** Admin sets/reset staff passwords (staff cannot self-reset — Q15).
- Deactivated staff cannot log in and lose access immediately.

## Validation / Permissions
- **Phone unique within staff**; location required. Admin only.

## Responsive / Accessibility
- Permission toggles clearly labeled and grouped; category multi-select accessible; confirmed destructive actions.

## Dependencies
- `/staff`, `/locations`, `/categories`.
