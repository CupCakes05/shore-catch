# A12 — Customer History Lookup

**App:** Admin Panel (Next.js, web)

## Purpose
Look up a customer's purchase history by Name or WhatsApp number, for support and operational insight. **Admin can also edit customer details** (Requirement #7).

## Users
Main Admin.

## Navigation
- **From:** sidebar.
- **To:** Order Detail (A10).

## Key UI Elements
- Search bar: query by **Name** or **WhatsApp number**.
- Results list of matching customers: name, WhatsApp number, service location, total orders, total spend.
- On selecting a customer:
  - **Customer profile summary** (name, WhatsApp number, address, landmark, service location).
  - **"Edit Customer" button** → opens edit form/modal (Requirement #7).
  - **Order history table** (order number, date, total, status, payment, delivery date, pre-order flag).
- Pagination.

### Edit Customer Modal (Requirement #7)
- Editable fields: **Name**, **Delivery Address**, **Nearest Landmark**, **Service Location** (dropdown).
- Non-editable: WhatsApp number (identity key — displayed but disabled).
- Save / Cancel buttons.
- Confirmation before save.

## States
- **Idle:** prompt to search.
- **Loading:** searching; saving edits.
- **Empty:** no matches → "No customer found."
- **Error:** Retry; save error.
- **Success:** results + drill-down history; edit saved confirmation.

## Actions
- Search by name/number.
- Select customer → view profile + order history.
- **Edit customer details** → `PATCH /customers/{id}` (Requirement #7).
- Tap an order → Order Detail (A10).

## Business Rules
- Search matches on name (partial) and WhatsApp number (exact/partial — confirm).
- **Admin can edit:** name, delivery address, nearest landmark, service location. WhatsApp number is NOT editable (identity key).
- On service location change by Admin, the customer's cart is re-validated/cleared on next use (same rule as customer-initiated change — Q12).
- Changes are audit-logged (who changed what, when).

## Validation / Permissions
- Admin only. Handle PII per Privacy Policy. Name required on edit.

## Responsive / Accessibility
- Accessible search + results table + edit form; clear empty state; confirmed edits.

## Dependencies
- `/reports/customers`, `/customers/{id}` (PATCH), `/orders`.
