# C11 — Offers

**App:** Customer (Flutter/Android)

## Purpose
Show the full list of active offers/announcements for the customer's Service Location (the top banner on Home links here). Communicates festival/promotional offers, each with a **target scope** (all categories, a specific category, or a specific product) and typically a **% discount** (e.g., "20% off Vegetables for Onam") — Confirmed Q1.

## Users
Authenticated Customer.

## Navigation
- **From:** Category Home (C05) offers banner; Notifications (C14).
- **To:** Category Home / Product List to shop toward an offer.

## Key UI Elements
- List/cards of offers: title, message, optional banner image, **discount (e.g., "20% off")**, **target** (all / a category / a product), validity window (e.g., "Valid till DD MMM").
- CTA to start shopping (deep-links to the targeted category/product when applicable).

## States
- **Loading:** skeleton cards.
- **Empty:** "No offers right now" friendly state.
- **Error:** Retry.
- **Success:** offer list.

## Actions
- Tap offer → optionally navigate to relevant category/products (or just informational).
- Refresh.

## Business Rules (Confirmed Q1/Q9)
- Offers are festival/promotional, tied to a **target scope**: all categories, a specific category, or a specific product; typically a **% discount** (e.g., "20% off Vegetables for Onam").
- Scope is **global or per-location** (per-location overrides global); active + within validity window.
- Whether the discount is applied to cart totals or is display-only is a **pending sub-decision** (linked to Q1). If applied, reflect in Cart (C07) and invoice.

## Validation / Permissions
- Authenticated customer.

## Responsive / Accessibility
- Offer text readable; images have alt text; validity communicated in text.

## Dependencies
- `/offers?location={id}&active=true`.
