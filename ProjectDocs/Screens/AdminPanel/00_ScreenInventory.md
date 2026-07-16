# Muciriz Admin (Next.js, web) — Screen Inventory

> Main Admin web console. Manages everything, cascading by Service Location. Each screen has a dedicated doc.

## Screen List

| # | Screen | File | Purpose |
|---|--------|------|---------|
| A01 | Login | `A01_Login.md` | Admin authentication |
| A02 | Dashboard (Home) | `A02_Dashboard.md` | Overview KPIs + navigation |
| A03 | Service Locations | `A03_ServiceLocations.md` | Manage locations (add/remove) |
| A04 | Categories | `A04_Categories.md` | Manage categories (multi-location assignment); manage category recommended products |
| A05 | Products | `A05_Products.md` | Manage products (multi-location, GST, stock); approve staff submissions (Q11); manage recommended products |
| A06 | Delivery Times | `A06_DeliveryTimes.md` | Manage delivery time options (supports pre-order date+slot) |
| A07 | Staff Management | `A07_StaffManagement.md` | Create staff, assign multiple locations + permissions (incl. update_stock, record_misc_purchase, place_shop_sale) |
| A08 | Offers / Notifications | `A08_Offers.md` | Create/publish offers to customer app |
| A09 | Orders Dashboard | `A09_OrdersDashboard.md` | Orders by location; delivered vs pending; pre-orders |
| A10 | Order Detail | `A10_OrderDetail.md` | Single order full view with state transition timestamps, GST details |
| A11 | Sales Reports | `A11_SalesReports.md` | Sales, profit/loss, staff performance, misc purchases reporting |
| A12 | Customer History Lookup | `A12_CustomerHistory.md` | Lookup by name / WhatsApp number; edit customer details |
| A13 | Admin Profile / Settings | `A13_ProfileSettings.md` | Admin account, password, system config (GPay UPI ID, customer care contact) |
| A14 | Error / Empty / 404 | `A14_ErrorStates.md` | Web error & empty states |
| A15 | Inventory Management | `A15_InventoryManagement.md` | Stock levels, purchase records, profit/loss (Requirements #9, #10) |

## Public (Unauthenticated) Pages

The Admin Panel also serves a **small public-facing surface** — legal/informational pages that require **no admin login** and are **publicly reachable**. These provide the **stable public URLs required for the Google Play Store listing** (the Flutter apps collect personal data, so Play requires a hosted Privacy Policy URL) and are the **canonical single source of truth** for legal content that the mobile apps link to.

| # | Page | Public Route | Purpose |
|---|------|--------------|---------|
| P01 | Privacy Policy | `/privacy` | How customer/staff data (name, mobile/WhatsApp #, address, OTP) is collected & used — **the URL supplied to the Play Console** |
| P02 | Terms & Conditions | `/terms` | Usage/order/delivery/payment terms |
| P03 | About (optional) | `/about` | About Muciriz |
| P04 | Contact (optional) | `/contact` | Business contact info |

**Notes**
- These pages are **static/hardcoded** in the Next.js app — consistent with Q19 (not an admin-managed CMS). They are served as public static pages rather than being bundled only inside the mobile apps.
- Content lives here as the **canonical source**; the Customer App (C15) and Staff App (S07) link to / render these URLs so legal edits don't require an app update.
- Distinct from the authenticated console (A01–A14): no session/token required to view them.
- The **Play Data Safety form** must be kept consistent with the Privacy Policy content published here.

## Global Layout Model
- Persistent left sidebar navigation + top bar.
- **Global Service Location selector** in the top bar (many screens are scoped by the currently selected location).
- Content area with tables, forms, modals/drawers.

## Cross-Screen States
Loading, Empty, Error, Success defined per screen. Tables include pagination, search, sort where relevant. See `UX/StatesAndFeedback.md`.

## Security Note
Single Main Admin (trial). All actions require admin auth; server enforces. See `Suggestions/03_TechnicalSuggestions.md` for hardening (audit log, 2FA) recommendations.
