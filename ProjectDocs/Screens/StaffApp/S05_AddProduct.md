# S05 — Add/Edit Product & Stock Update (Conditional)

**App:** Staff (Flutter/Android)

> ⚠️ **Permission-gated screen.** Product add/edit visible only when the staff member has `add_products`. Stock update visible only when staff has `update_stock`. Lets certain staff manage products and stock for categories within their assigned locations.

## Purpose
Allow authorized staff to add and edit products for a category within their assigned locations (and allowed categories), reducing Admin workload for on-the-ground catalog updates. **All staff adds/edits require Admin approval before going live (Q11).** Also allows staff with `update_stock` to update inventory counts.

## Users
Authenticated Staff with `add_products` and/or `update_stock`.

## Navigation
- **From:** Order List (S03) entry point / menu (only if permitted).
- **To:** back to list with confirmation.

## Key UI Elements

### Product Add/Edit Section (requires `add_products`)
- Category selector (limited to staff's allowed categories within their assigned locations).
- Product form: Product Name, Gross Weight, Net Weight, Price, **GST applicable (toggle)**, **GST % (if applicable)**.
- Product image upload (camera/gallery).
- Save/Submit button (label reflects that it goes for approval, e.g., "Submit for approval").
- List of products the staff added/edited with an **approval-status badge**: `Pending approval` / `Approved` / `Rejected`. Rejected items can be revised and resubmitted.

### Stock Update Section (requires `update_stock` — Requirement #9)
- **Location selector** (limited to staff's assigned locations).
- **Product list** with current stock count per product at the selected location.
- **Inline stock update:** tap a product → enter new stock count → save.
- Confirmation after stock update ("Stock updated for [product] at [location]").
- Change is logged (old count → new count + staffId + timestamp).

## States
- **Loading:** while saving/uploading image; while updating stock.
- **Empty:** no products added yet (if list shown); no products at location (stock section).
- **Error:** validation errors inline; upload failure; network error → retry.
- **Success:** "Submitted for approval" toast (product add); "Stock updated" toast (stock update); form resets.

## Actions
- Select category; fill fields; upload image; submit (`POST /products` + `/uploads`).
- Edit an existing product (`PATCH /products/{id}`) — re-enters `PendingApproval`.
- **Update stock count** for a product at a location → `PATCH /stock/{productId}/{locationId}`.

## Business Rules (Confirmed Q11, Requirement #9)
- Staff can add **and edit** products within their assigned locations + allowed categories.
- **All staff adds/edits enter `PendingApproval` and are NOT visible to customers until an Admin approves** (Approved) or rejects (Rejected). See `02_BusinessRules.md` §9a.
- Field validation matches product rules (name required, weights positive, Net ≤ Gross, price positive, GST % valid if applicable, image).
- **Stock updates** are logged with audit trail. Stock of 0 = out of stock (triggers pre-order for customers).
- Staff can only update stock for their assigned locations.

## Validation / Permissions
- `add_products` for product management; `update_stock` for inventory updates; scope enforced server-side.

## Responsive / Accessibility
- Labeled fields; image upload accessible; stock update inline with clear feedback; clear success/error feedback.

## Dependencies
- `/uploads`, `/products`, `/categories` (allowed subset), `/stock`.
