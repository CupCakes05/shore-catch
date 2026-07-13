# A04 — Categories

**App:** Admin Panel (Next.js, web)

## Purpose
Manage categories (e.g., Fish, Vegetables, Meat) **per Service Location**, including image upload. These render as the customer's Category Home cards.

## Users
Main Admin.

## Navigation
- **From:** sidebar (with location scope), or drill-down from Service Locations (A03).
- **To:** Products (A05) filtered to a selected category.

## Key UI Elements
- **Service Location selector** (scopes the categories shown).
- **Grid/table** of categories for the location: image thumbnail, name, active status, product count, sort order.
- "Add Category" → form/modal: name + **image upload**.
- Row actions: Edit (name/image/sort), **Manage Recommended Products**, Activate/Deactivate, Remove.
- **Recommended Products manager (Confirmed new requirement):** a picker to attach a set of recommended products to this category (category-level cross-sell). These are surfaced to customers at cart/checkout (C07) when the cart contains products from this category.

## States
- **Loading:** skeleton.
- **Empty:** no categories for this location → CTA to add one; also "select a location first" if none selected.
- **Error:** Retry.
- **Success:** grid rendered.
- Add/Edit: image upload progress; validation; submit states.
- Remove: confirmation (affects products + customer visibility).

## Actions
- Add category with image (`/uploads` + `POST /categories`).
- Edit; reorder (sort); Activate/Deactivate; Remove (`DELETE`/soft-disable) with confirm.
- Drill into products.

## Business Rules
- Category belongs to exactly one location.
- Image recommended (customer cards show image).
- Removing is a **soft-delete** (Q6) — historical orders/reporting preserved; cascades to hide its products and customer-visible catalog.
- **Recommended products** attached here must be active/approved and in the same location to surface (see `02_BusinessRules.md` §13).

## Validation / Permissions
- Name required; image type/size limits. Admin only.

## Responsive / Accessibility
- Image alt text; accessible grid; confirmed destructive actions.

## Dependencies
- `/categories`, `/uploads`, `/locations`, `/categories/{id}/recommendations`.
