# A05 — Products

**App:** Admin Panel (Next.js, web)

## Purpose
Manage products **per Category**: add/remove with fields Product Name, Gross Weight, Net Weight, Price, GST settings, and product image. Assign products to multiple service locations. Manage per-location stock. These populate the customer Product List flip cards.

## Users
Main Admin (and Staff with `add_products` via the Staff App, separately).

## Navigation
- **From:** Categories (A04 drill-down) or sidebar with category scope.
- **To:** back to Categories; Inventory Management (A15).

## Key UI Elements
- **Category selector** (scopes products).
- **Table** of products: image thumbnail, name, gross weight, net weight, price, **GST (Yes/No + %)**, **assigned locations** (chips), active status, **approval status** (`Approved` / `Pending approval` / `Rejected`), added-by / submitted-by (Admin/Staff), **stock per location** (summary).
- **Approval filter/queue:** a "Pending approval" filter listing staff-submitted adds/edits awaiting review, with **Approve** / **Reject** actions (Q11).
- "Add Product" → form/modal:
  - Product Name, Gross Weight, Net Weight, Price.
  - **GST applicable toggle** (yes/no). If yes: **GST percentage** field (e.g., 5, 12, 18, 28).
  - **Service Locations multi-select** (assign to one or more locations — Requirement #4).
  - **Image upload**.
  - (Admin adds are auto-approved.)
- **Manage Recommended Products** (per product): a picker to attach recommended products (product-level cross-sell), surfaced at cart/checkout (C07).
- **Stock/Inventory quick view:** per-location stock counts for the product; link to full inventory management.
- Row actions: Edit, Approve/Reject (for pending), Manage Recommended Products, Manage Stock, Activate/Deactivate, Remove.
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
- Edit fields (including GST settings and location assignments) (`PATCH`).
- Activate/Deactivate; Remove (`DELETE`/soft-disable) with confirm.
- Approve/Reject staff submissions.
- Manage recommended products.
- View/update stock per location.

## Business Rules
- Product belongs to one category but can be assigned to **multiple** locations (many-to-many — Requirement #4).
- **Stock is per-location** — each location has its own stock count even for a shared product.
- Validation: name required; weights positive; **Net ≤ Gross** (recommended); price positive; GST percent valid if applicable; at least one location; image recommended.
- **Pricing model (Confirmed Q5):** products are **packaged** (e.g., 1 kg packet, 500g packet). Each package has a **fixed price** regardless of weight. The price is for the whole packet/unit. Price is editable at any time. Gross/Net Weight are informational fields. Weight unit: g or kg.
- **GST (Requirement #11):** toggle + percentage. Products with GST show the rate in the table. GST is reflected in cart totals and invoices.
- **Approval (Q11):** Admin add/edit → auto-`Approved`; **staff add/edit → `PendingApproval`** and hidden from customers until Admin approves. Customers see only `Approved` + active products.
- **Soft-delete (Q6):** removal deactivates; historical orders unaffected (snapshots).
- **Recommended products** must be active/approved and in the same location to surface.

## Validation / Permissions
- Field validation as above. Admin (full); Staff limited via Staff App.

## Responsive / Accessibility
- Accessible table; image alt; numeric inputs with proper types; multi-select accessible; confirmed destructive actions.

## Dependencies
- `/products`, `/products/{id}/approval`, `/products/{id}/recommendations`, `/products/{id}/locations`, `/stock`, `/uploads`, `/categories`, `/locations`.
