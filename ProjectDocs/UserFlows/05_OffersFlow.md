# Offers Publish → Customer Sees Flow

```
ADMIN PANEL
1. Admin creates an Offer (A08): target scope (All categories / a category / a product),
   discountPercent (e.g., 20%), optional occasion, validity window,
   scope (Global or a specific location), active = true.

BACKEND
2. Offer stored with target scope + validity; global or per-location (per-location overrides global).

CUSTOMER APP
3. On app open / pull-to-refresh, Customer App fetches active offers for its location.
4. Offer appears as a banner at the TOP of Category Home (C05); full list in Offers (C11);
   also surfaced in Notifications (C14) inbox.
5. Customer taps banner → Offers (C11) → deep-links to the targeted category/product.
```

## Offer Model (Confirmed Q1/Q9)
| Attribute | Values |
|-----------|--------|
| Target scope | **All categories** \| **A specific category** \| **A specific product** |
| Discount | `discountPercent` (primary festival model, e.g., "20% off Vegetables for Onam") |
| Scope | Global or per-location (**per-location overrides global**) |
| Validity | start/end window + active flag |

## Pending Sub-Decision (linked to Q1)
**Discount application vs display-only.** Whether `discountPercent` actually reduces cart totals or is a display-only banner for the trial is still to be confirmed. If applied, Cart/Checkout (C07) must apply and show the benefit, and the invoice must reflect it.

## Failure / Edge
- Offer expires (past `endsAt`) → no longer served; banner disappears.
- No active offers → banner hidden; Offers screen shows empty state.
- Deactivated offer → immediately removed from customer view on next fetch.
