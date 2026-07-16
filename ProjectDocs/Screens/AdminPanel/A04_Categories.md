# A04 — Categories

**App:** Admin Panel (Next.js, web)

## Purpose
Manage categories (e.g., Fish, Vegetables, Meat) and assign them to **multiple Service Locations** (many-to-many), including image upload. These render as the customer's Category Home cards.

## Users
Main Admin.

## Navigation
- **From:** sidebar (with location scope), or drill-down from Service Locations (A03).
- **To:** Products (A05) filtered to a selected category.

## Key UI Elements
- **Service Location filter** (scopes the categories shown to those assigned to a location; or "All" to see all categories).
- **Grid/table** of categories: image thumbnail, name, **assigned locations** (chips/tags), active status, product count, sort order.
- "Add Category" → form/modal: name + **image upload** + **Service Locations multi-select** (assign to one or more locations).
- Row actions: Edit (name/image/sort/**locations**), **Manage Recommended Products**, Activate/Deactivate, Remove.
- **Recommended Products manager (Confirmed):** a picker to attach a set of recommended products to this category (category-level cross-sell). These are surfaced to customers at cart/checkout (C07) when the cart contains products from this category.

## States
- **Loading:** skeleton.
- **Empty:** no categories (for this location filter) → CTA to add one.
- **Error:** Retry.
- **Success:** grid rendered.
- Add/Edit: image upload progress; validation; submit states.
- Remove: confirmation (affects products + customer visibility).

## Actions
- Add category with image + multi-location assignment (`/uploads` + `POST /categories`).
- Edit; reorder (sort); change location assignments; Activate/Deactivate; Remove (`DELETE`/soft-disable) with confirm.
- Drill into products.

## Business Rules
- Category can be assigned to **multiple** locations (many-to-many — Requirement #4). At least one location required.
- Image recommended (customer cards show image).
- Removing is a **soft-delete** (Q6) — historical orders/reporting preserved; cascades to hide its products from customer-visible catalog.
- **Recommended products** attached here must be active/approved and in the same location as the customer to surface (see `02_BusinessRules.md` §13).

## Validation / Permissions
- Name required; at least one location selected; image type/size limits. Admin only.

## Responsive / Accessibility
- Image alt text; accessible grid; multi-select accessible with keyboard; confirmed destructive actions.

## Dependencies
- `/categories`, `/uploads`, `/locations`, `/categories/{id}/recommendations`.
