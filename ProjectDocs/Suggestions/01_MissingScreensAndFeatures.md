# Suggestions — Missing Screens & Features

> Suggestions only, not confirmed requirements. Explains why each is useful for a professional trial.

## Standard Screens Already Included (for reference)
These are covered in the screen inventories: Splash, Onboarding, Registration, Profile, Settings, Notifications, Static/Legal (Privacy, Terms, About, Help/FAQ, Contact), Error/Empty/Offline/Maintenance, Order Confirmation.

## Suggested Additional Screens / States

| Suggestion | App | Why |
|-----------|-----|-----|
| ✅ **Password reset** | Admin (self-service), Staff (Admin-reset) | Confirmed Q15 — Admin has forgot-password; Admin resets staff passwords |
| **Change password** | Staff, Admin | Basic account security hygiene |
| **Session timeout screen** | Admin (web) | Web sessions expire; graceful re-login prevents confusion |
| ✅ **Order cancellation (customer, while Pending)** | Customer | Confirmed Q10 — customer can cancel only while Pending |
| **Search products** | Customer | As catalog grows, browsing-only becomes tedious |
| **Reorder / order-again** | Customer | Grocery buying is repetitive; boosts convenience |
| **Delivery proof / note** | Staff | Confirm delivery with a note/photo for disputes |
| **Empty/first-run guidance (Admin)** | Admin | Guides Admin through required setup order (location→category→product→delivery time) |
| **Bulk import products** | Admin | Speeds catalog setup if inventory is large |

> Items marked ✅ have since been **confirmed** and moved into the core spec — kept here for traceability.

## Suggested Feature Enhancements

| Suggestion | Why |
|-----------|-----|
| Product availability/stock flag | Prevent ordering out-of-stock items |
| Functional offer discounts | ⏳ Sub-decision (linked to Q1): whether offer % actually reduces cart totals |
| ✅ Push notifications | Confirmed Q18 — push + SMS (AWS SNS) + in-app inbox |
| ✅ Recommended products (cross-sell) | Confirmed new requirement — see `Features/08_RecommendedProducts.md` |
| Invoice via SMS/email | Convenient delivery of receipt |
| Basic ratings/feedback on orders | Trial learning signal |
| Delivery fee / minimum order value | Common commerce rules |

## Suggested Additional States / Edge Handling
- ✅ Re-entry of an already-verified number skips OTP (confirmed Q3).
- Cart conflict resolution UI (price/availability changed).
- Graceful handling when a location/category is deactivated while a customer is browsing it.
