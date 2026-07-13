# A12 — Customer History Lookup

**App:** Admin Panel (Next.js, web)

## Purpose
Look up a customer's purchase history by Name or WhatsApp number, for support and operational insight.

## Users
Main Admin.

## Navigation
- **From:** sidebar.
- **To:** Order Detail (A10).

## Key UI Elements
- Search bar: query by **Name** or **WhatsApp number**.
- Results list of matching customers: name, WhatsApp number, service location, total orders, total spend.
- On selecting a customer: their profile summary + **order history table** (order number, date, total, status, payment).
- Pagination.

## States
- **Idle:** prompt to search.
- **Loading:** searching.
- **Empty:** no matches → "No customer found."
- **Error:** Retry.
- **Success:** results + drill-down history.

## Actions
- Search by name/number.
- Select customer → view order history.
- Tap an order → Order Detail (A10).

## Business Rules
- Search matches on name (partial) and WhatsApp number (exact/partial — confirm).
- Read-only view of customer data (privacy: avoid unnecessary PII exposure; access limited to Admin).

## Validation / Permissions
- Admin only. Handle PII per Privacy Policy.

## Responsive / Accessibility
- Accessible search + results table; clear empty state.

## Dependencies
- `/reports/customers`, `/orders`.
