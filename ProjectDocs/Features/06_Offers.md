# Feature — Offers & Notifications

**Overview:** Admin publishes **festival/promotional offers** (target scope: all categories / a category / a product; typically a % discount) shown at the top of the Customer App. A separate in-app **Notifications inbox** (in-app + push + SMS) is also provided.

**Business purpose:** Drive engagement and larger orders; announce promotions.

**Users:** Admin (create), Customer (view).

**Entry points:** Admin Offers (A08); Customer banner on Category Home (C05), Offers (C11), Notifications inbox (C14).

**Dependencies:** Service Location (offers are global or per-location; per-location overrides global — Q9).

**Data used:**
- Offer (targetType All/Category/Product, targetId, discountPercent, occasion, validity, global/per-location scope, active, optional banner image, appliesDiscount flag).
- Notification (customer/location, title/message, type, channels in-app/push/SMS, read state) — Q18.

**Validation:** title; target selected (+ targetId for Category/Product); discountPercent 0–100; valid date range.

**States:** active within validity served; empty (no offers) hides banner; loading/error on fetch.

**Edge cases:**
- Expired/deactivated offers removed from customer view.
- Multiple active offers → carousel.
- Per-location offer present → overrides global for that location.
- ⏳ **Display-only vs discount (sub-decision linked to Q1):** if discount application confirmed, Cart + invoice must reflect it.

**Permissions:** Admin-only creation.

**Notifications (Confirmed Q18):** in-app inbox (always) + push + SMS (AWS SNS); order-status and offer messages.

**Errors:** fetch retry.

**Future considerations:** functional discount engine, stacking rules, per-customer targeting, scheduled/recurring offers.
