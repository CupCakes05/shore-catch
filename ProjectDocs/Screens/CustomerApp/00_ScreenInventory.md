# Muciriz (Customer App, Flutter, Android) — Screen Inventory

> Complete list of screens. Each has a dedicated doc in this folder. Purpose, key UI, states, and actions per screen.

## Screen List

| # | Screen | File | Purpose |
|---|--------|------|---------|
| C01 | Splash | `C01_Splash.md` | Branding + auto-login check |
| C02 | Onboarding | `C02_Onboarding.md` | 2–3 intro slides (first launch) |
| C03 | Registration | `C03_Registration.md` | New customer sign-up |
| C04 | OTP Verification (required) | `C04_OTPVerification.md` | Verify WhatsApp number via OTP (WhatsApp Business API primary, AWS SNS fallback) — Confirmed Q2, Req #1 |
| C05 | Category (Home) | `C05_CategoryHome.md` | Location-filtered category cards + offers banner |
| C06 | Product List | `C06_ProductList.md` | Products for a category; flip cards; qty add; scroll strip; stock/pre-order indicators |
| C07 | Cart / Checkout | `C07_CartCheckout.md` | Review items, recommended products, GST breakdown, delivery date+slot, GPay UPI display, payment, place order; pre-order support |
| C08 | Order Confirmation | `C08_OrderConfirmation.md` | Success + invoice download (with GST/non-GST split); pre-order badge |
| C09 | Order History | `C09_OrderHistory.md` | Past & active orders list |
| C10 | Order Detail | `C10_OrderDetail.md` | Single order status + invoice |
| C11 | Offers | `C11_Offers.md` | Full offers list (from banner) |
| C12 | Profile | `C12_Profile.md` | View/edit profile & delivery info |
| C13 | Settings | `C13_Settings.md` | App preferences, customer care contact, logout, legal links |
| C14 | Notifications (required) | `C14_Notifications.md` | In-app announcements/offers/order-status inbox (also push + SMS) — Confirmed Q18 |
| C15 | Static/Legal | `C15_StaticPages.md` | Privacy, Terms, About, Help/FAQ, Contact |
| C16 | Error / No-Connection | `C16_ErrorStates.md` | Global error, offline, maintenance |

## Global Navigation Model

- **Post-login home:** Category (Home) page (C05), filtered by the customer's Service Location.
- **Primary navigation (Confirmed Q21):** bottom navigation with 4 tabs — Home (Categories), Cart, Orders, Profile.
- **Offers banner** appears at the top of the Category home (and optionally persistent).
- Cart badge shows item count.

## Cross-Screen States (apply everywhere)
Every data-driven screen must define Loading (skeleton/spinner), Empty, Error (with retry), and Success/content states. See `UX/StatesAndFeedback.md`.
