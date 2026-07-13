# A05 — Products

**App:** Admin Panel (Next.js, web)

## Purpose
Manage products **per Category**: add/remove with fields Product Name, Gross Weight, Net Weight, Price, and product image. These populate the customer Product List flip cards.

## Users
Main Admin (and Staff with `add_products` via the Staff App, separately).

## Navigation
- **From:** Categories (A05 drill-down) or sidebar with location+category scope.
- **To:** back to Categories.

## Key UI Elements
- **Location + Category selector** (scopes products).
- **Table** of products: image thumbnail, name, gross weight, net weight, price, active status, **approval status** (`Approved` / `Pending approval` / `Rejected`), added-by / submitted-by (Admin/Staff).
- **Approval filter/queue:** a "Pending approval" filter listing staff-submitted adds/edits awaiting review, with **Approve** / **Reject** actions (Q11).
- "Add Product" → form/modal: Product Name, Gross Weight, Net Weight, Price, **image upload** (Admin adds are auto-approved).
- **Manage Recommended Products** (per product): a picker to attach recommended products (product-level cross-sell), surfaced at cart/checkout (C07).
- Row actions: Edit, Approve/Reject (for pending), Manage Recommended Products, Activate/Deactivate, Remove.
- Search + pagination.

## States
- **Loading:** table skeleton.
- **Empty:** category has no products → CTA to add; "select category first" prompt if none.
- **Error:** Retry.
- **Success:** table rendered.
- Add/Edit: image upload progress; field validation; submit states.
- **Pending approval:** staff-submitted rows badged `Pending approval`; Approve/Reject actions available.
- Remove: confirmation (soft-delete; historical orders keep snapshots, unaffected).

## Actions
- Add product with image (`/uploads` + `POST /products`).
- Edit fields (`PATCH`).
- Activate/Deactivate; Remove (`DELETE`/soft-disable) with confirm.

## Business Rules
- Product belongs to exactly one category (thus one location).
- Validation: name required; weights positive; **Net ≤ Gross** (recommended); price positive; image recommended.
- ⏳ **Pricing model (Q5) PENDING:** fixed per unit vs per-kg, and weight unit (g/kg) — still awaiting client input.
- **Approval (Q11):** Admin add/edit → auto-`Approved`; **staff add/edit → `PendingApproval`** and hidden from customers until Admin approves. Customers see only `Approved` + active products.
- **Soft-delete (Q6):** removal deactivates; historical orders unaffected (snapshots).
- **Recommended products** must be active/approved and in the same location to surface.

## Validation / Permissions
- Field validation as above. Admin (full); Staff limited via Staff App.

## Responsive / Accessibility
- Accessible table; image alt; numeric inputs with proper types; confirmed destructive actions.

## Dependencies
- `/products`, `/products/{id}/approval`, `/products/{id}/recommendations`, `/uploads`, `/categories`, `/locations`.
