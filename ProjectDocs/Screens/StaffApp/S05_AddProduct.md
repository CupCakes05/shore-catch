# S05 — Add Product (Conditional)

**App:** Staff (Flutter/Android)

> ⚠️ **Permission-gated screen.** Visible only when the staff member has `add_products` (assigned by Admin). Lets certain staff add products for a category from their own page.

## Purpose
Allow authorized staff to add and edit products for a category within their assigned location (and allowed categories), reducing Admin workload for on-the-ground catalog updates. **All staff adds/edits require Admin approval before going live (Q11).**

## Users
Authenticated Staff with `add_products`.

## Navigation
- **From:** Order List (S03) entry point / menu (only if permitted).
- **To:** back to list with confirmation.

## Key UI Elements
- Category selector (limited to staff's allowed categories within their location).
- Product form: Product Name, Gross Weight, Net Weight, Price.
- Product image upload (camera/gallery).
- Save/Submit button (label reflects that it goes for approval, e.g., "Submit for approval").
- List of products the staff added/edited with an **approval-status badge**: `Pending approval` / `Approved` / `Rejected`. Rejected items can be revised and resubmitted.

## States
- **Loading:** while saving/uploading image.
- **Empty:** no products added yet (if list shown).
- **Error:** validation errors inline; upload failure; network error → retry.
- **Success:** "Submitted for approval" toast; item shows `Pending approval`; form resets.

## Actions
- Select category; fill fields; upload image; submit (`POST /products` + `/uploads`).
- Edit an existing product (`PATCH /products/{id}`) — re-enters `PendingApproval`.

## Business Rules (Confirmed Q11)
- Staff can add **and edit** within their location + allowed categories.
- **All staff adds/edits enter `PendingApproval` and are NOT visible to customers until an Admin approves** (Approved) or rejects (Rejected). See `02_BusinessRules.md` §9a.
- Field validation matches product rules (name required, weights positive, Net ≤ Gross, price positive, image).

## Validation / Permissions
- Requires `add_products`; scope enforced server-side.

## Responsive / Accessibility
- Labeled fields; image upload accessible; clear success/error feedback.

## Dependencies
- `/uploads`, `/products`, `/categories` (allowed subset).
