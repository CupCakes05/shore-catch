# A08 — Offers / Notifications

**App:** Admin Panel (Next.js, web)

## Purpose
Create and publish festival/promotional offers/announcements that appear at the **top of the Customer App**. Each offer has a **target scope** (all categories, a specific category, or a specific product) and typically expresses a **% discount** (e.g., "20% off Vegetables for Onam") — Confirmed Q1.

## Users
Main Admin.

## Navigation
- **From:** sidebar.

## Key UI Elements
- **Scope selector:** **Global** vs a specific **Service Location** (per-location overrides global — Q9).
- **Table** of offers: title, target (All/Category/Product), discount, scope (Global/location), validity window, active status.
- "Create Offer" → form/modal:
  - Title + message (banner text, e.g., "20% off Vegetables for Onam").
  - **Target scope (Q1):** **All categories** | **A specific category** (pick category) | **A specific product** (pick product).
  - **Discount:** `discountPercent` (primary festival model, e.g., 20%).
  - Optional **occasion/special-date** label and banner image upload.
  - Validity: start & end date/time.
  - **Scope:** Global or a specific location.
  - `Applies discount to cart?` flag — ⏳ pending sub-decision (linked to Q1); default off = display-only for now.
- Row actions: Edit, Activate/Deactivate, Remove.

## States
- **Loading:** table skeleton.
- **Empty:** no offers → CTA to create.
- **Error:** Retry.
- **Success:** table rendered.
- Create/Edit: validation (condition matches type, valid date range), submit states.

## Actions
- Create (`POST /offers`); Edit (`PATCH`); Activate/Deactivate; Remove (`DELETE`).

## Business Rules (Confirmed Q1/Q9)
- Every offer has a **target scope**: all categories, a specific category, or a specific product.
- Offers are **global or per-location**; **per-location overrides global**.
- Only active offers within validity window are served to the customer app.
- ⏳ **Sub-decision (linked to Q1):** whether `discountPercent` is applied to cart totals or is display-only. If applied later, Cart/Checkout (C07) and invoice must reflect it.

## Validation / Permissions
- Title required; target selected (and targetId when Category/Product); `discountPercent` valid (0–100); end after start. Admin only.

## Responsive / Accessibility
- Type-dependent form fields clearly labeled; date pickers accessible; image alt.

## Dependencies
- `/offers`, `/uploads`, `/locations`.
