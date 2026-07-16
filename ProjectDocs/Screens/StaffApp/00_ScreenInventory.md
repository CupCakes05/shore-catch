# Muciriz Staff (Flutter, Android) — Screen Inventory

> Delivery Staff app. Login tied to one or more Service Locations; permission-gated actions. Each screen has a dedicated doc.

## Screen List

| # | Screen | File | Purpose |
|---|--------|------|---------|
| S01 | Splash | `S01_Splash.md` | Branding + session check |
| S02 | Login | `S02_Login.md` | Staff authentication — phone + password, location-tied (Q14); Admin-reset only (Q15) |
| S03 | Order List | `S03_OrderList.md` | Orders for assigned location(s)/categories |
| S04 | Order Detail | `S04_OrderDetail.md` | Fulfillment actions per order (with timestamps — Requirement #2) |
| S05 | Add/Edit Product & Stock Update (conditional) | `S05_AddProduct.md` | Staff product add/edit if permitted — requires Admin approval (Q11); stock update if `update_stock` (Requirement #9) |
| S06 | Profile | `S06_Profile.md` | Staff profile info |
| S07 | Settings | `S07_Settings.md` | Preferences, logout, legal |
| S08 | Error / No-Connection | `S08_ErrorStates.md` | Global error/offline/maintenance |
| S09 | Miscellaneous Purchases (conditional) | `S09_MiscPurchases.md` | Record operational expenses if `record_misc_purchase` (Requirement #8) |
| S10 | Shop Sale (conditional) | `S10_PlaceOrderForCustomer.md` | Create Shop Sales (walk-in/counter billing) if `place_shop_sale` (Requirement #12, Q28) |

## Global Navigation Model
- **Post-login home:** Order List (S03).
- Navigation: Orders (home), Profile, Settings. (Product/Stock entry only if `add_products` or `update_stock` granted; Misc Purchases if `record_misc_purchase` granted; Shop Sale if `place_shop_sale` granted.)

## Permission-Driven UI
Staff UI adapts to Admin-configured permissions (`view_orders`, `restrict_to_categories`, `add_products`, `confirm_order`, `confirm_delivery`, `confirm_payment`, `update_stock`, `record_misc_purchase`, `place_shop_sale`). Actions the staff lacks permission for are hidden/disabled; backend also enforces.

## Cross-Screen States
Loading / Empty / Error / Success defined per screen. See `UX/StatesAndFeedback.md`.
