# Staff App (Flutter, Android) — Screen Inventory

> Delivery Staff app. Login tied to a Service Location; permission-gated actions. Each screen has a dedicated doc.

## Screen List

| # | Screen | File | Purpose |
|---|--------|------|---------|
| S01 | Splash | `S01_Splash.md` | Branding + session check |
| S02 | Login | `S02_Login.md` | Staff authentication — phone + password, location-tied (Q14); Admin-reset only (Q15) |
| S03 | Order List | `S03_OrderList.md` | Orders for assigned location/categories |
| S04 | Order Detail | `S04_OrderDetail.md` | Fulfillment actions per order |
| S05 | Add/Edit Product (conditional) | `S05_AddProduct.md` | Staff product add/edit if permitted — requires Admin approval (Q11) |
| S06 | Profile | `S06_Profile.md` | Staff profile info |
| S07 | Settings | `S07_Settings.md` | Preferences, logout, legal |
| S08 | Error / No-Connection | `S08_ErrorStates.md` | Global error/offline/maintenance |

## Global Navigation Model
- **Post-login home:** Order List (S03).
- Simple navigation: Orders (home), Profile, Settings. (Product-add entry only if `add_products` granted.)

## Permission-Driven UI
Staff UI adapts to Admin-configured permissions (`view_orders`, `restrict_to_categories`, `add_products`, `confirm_order`, `confirm_delivery`, `confirm_payment`). Actions the staff lacks permission for are hidden/disabled; backend also enforces.

## Cross-Screen States
Loading / Empty / Error / Success defined per screen. See `UX/StatesAndFeedback.md`.
