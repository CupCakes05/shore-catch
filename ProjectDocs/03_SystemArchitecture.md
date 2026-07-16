# Muciriz — System Architecture

> Conceptual architecture across the three apps and shared backend. No implementation code — architecture and responsibilities only.

## 1. High-Level Components

```
┌──────────────────────────────────────────────────────────────────┐
│                         Client Applications                        │
│                                                                    │
│  Admin Panel (Next.js, web)   Staff App (Flutter)   Customer App   │
│  "Muciriz Admin"              "Muciriz Staff"       "Muciriz"      │
└─────────┼──────────────────────────┼──────────────────┼───────────┘
          │            HTTPS / REST (JSON)               │
          └──────────────────────┬───────────────────────┘
                                 │
                    ┌────────────▼────────────┐
                    │        Backend API       │
                    │  Auth · Catalog · Orders │
                    │  Offers · Reporting ·    │
                    │  Staff/Perms · Invoices  │
                    └────────────┬────────────┘
             ┌──────────────────┼──────────────────┐
     ┌───────▼───────┐  ┌────────▼────────┐  ┌──────▼──────┐
     │   Database     │  │  File/Image     │  │  Invoice    │
     │  (relational   │  │  Storage        │  │  Generator  │
     │   recommended) │  │ (product/cat    │  │  (PDF)      │
     │                │  │  images)        │  │             │
     └────────────────┘  └─────────────────┘  └─────────────┘
```

## 2. Component Responsibilities

### Frontend — Admin Panel (Next.js, web) — "Muciriz Admin"
- Responsive web UI for Main Admin.
- CRUD screens for locations, categories, products, delivery times, staff, offers.
- Dashboards for orders and sales; customer history lookup.
- Image upload UI for categories/products.
- **Public (unauthenticated) surface:** also serves static, publicly reachable **legal pages** — Privacy Policy (`/privacy`), Terms (`/terms`), optional About/Contact — at stable public URLs. These require **no admin login** and are distinct from the authenticated console. They provide the **hosted Privacy Policy URL required by the Google Play Store** (the Flutter apps collect personal data) and act as the **canonical source** of legal content that the mobile apps link to / render. Static/hardcoded — not an admin CMS (Q19). See `00_ProjectOverview.md` §6a/§6b and `Screens/AdminPanel/00_ScreenInventory.md`.

### Frontend — Staff App (Flutter, Android) — "Muciriz Staff"
- Auth tied to one or more Service Locations (multi-location assignment).
- Order list + detail; order/delivery/payment confirmation actions.
- Optional product-add screen (permission-gated).
- Profile.

### Frontend — Customer App (Flutter, Android) — "Muciriz"
- Onboarding, registration, auto-login.
- Location-scoped category browse, product cards (flip to image), quantity add.
- Category scroll strip, cart/checkout, delivery-time & payment selection.
- Invoice download, order confirmation, order history, offers banner, profile/settings.

### Backend API
- **Auth & Session:** customer registration + **OTP verification (WhatsApp Business API primary, AWS SNS fallback)** + cached/auto-login sessions; staff login (phone+password); admin login. Issues tokens; enforces role. Resolves customer vs staff identity per app.
- **Catalog service:** service locations, categories, products (all with **multi-location many-to-many** scoping); **staff product approval workflow**; **recommended products** (category-level and product-level); **GST support** per product.
- **Orders service:** create order (with delivery date + time slot; supports pre-orders), lifecycle transitions with **timestamps per state** (incl. **customer cancel while Pending, staff cancel before Completed**), order queries by role/scope. Supports **Shop Sales** (walk-in quick billing without customer account). **Stock decremented on order placement, restored on cancellation (Q26).**
- **Stock/Inventory service:** per-location stock counts, purchase records (cost price, weights, supplier), stock update (by staff/admin), stock decrement on order placement and restoration on cancellation. Profit/loss = Total Sale − Total Expense (simplified, Q27).
- **Offers service:** create/publish offers with **target scope (all/category/product)** and **global or per-location** scope (per-location overrides global); serve active offers to customers.
- **Staff & permissions service:** create staff, assign **multiple locations** + granular permissions (including `update_stock`, `record_misc_purchase`, `place_shop_sale`); Admin resets staff passwords.
- **Misc purchases service:** staff records operational expenses; admin views reports. Included in Total Expense for profit/loss.
- **Notifications service:** fan-out to **in-app inbox, push, and SMS (AWS SNS)**.
- **Reporting service:** orders-by-location (oldest-first), sales-by-location (**Completed only**), **profit/loss** (Total Sale − Total Expense, simplified Q27), **staff performance** (transition timestamps), customer history lookup.
- **Invoice service:** generate downloadable invoice (PDF) with **GST/non-GST split** from order data. Invoice numbering: `M{YEAR}#{SEQ5}` (e.g., M2026#00001), resets annually. Muciriz branding.
- **System config service:** manage admin-configurable settings (GPay UPI ID, customer care contact).

### Database
- Relational model recommended (clear relationships between location → category → product → order). See `05_DatabaseConcepts.md`.

### File/Image Storage
- Stores category and product images; returns URLs consumed by all apps.

### Invoice Generator
- Produces a downloadable invoice document (PDF recommended) at checkout.

## 3. Communication Patterns

- All clients talk to the Backend API over **HTTPS/REST (JSON)**.
- Auth via token (e.g., JWT/session token) attached to requests; backend enforces role and location scope on every request.
- Images referenced by URL from storage.
- Invoices requested on-demand (generate at checkout, allow re-download from order history).

## 4. Authentication & Authorization

- **Customer:** registration creates an account keyed on the WhatsApp number, which is **OTP-verified via WhatsApp Business API (primary) / AWS SNS fallback** and **unique**. Session is **cached/persisted** so returning users are **auto-logged-in**. On logout + re-entry of an already-verified number, sign-in proceeds **without OTP** (first-time verification only). No password.
- **Staff:** credentials created by Admin; login is **phone + password**, tied to one or more Service Locations; permissions enforced server-side. Staff cannot self-reset passwords (Admin resets).
- **Admin:** single Main Admin account (credentialed login) with self-service forgot-password.
- **Shared phone number:** the same phone can be both a Customer and a Staff account. Customer and Staff are **separate identity records**; each app authenticates against its own identity independently (see `01_UserRoles.md` §8).
- Authorization is **role + scope** based: every request is checked for role (admin/staff/customer) and data scope (which Service Location(s), which categories).

> Security note: because there is no payment gateway, the most sensitive surfaces are auth and staff permission enforcement. Customer identity is now hardened with **OTP verification (WhatsApp Business API + AWS SNS fallback) + unique numbers**. Enforce all permission/scope checks **server-side**, never trust the client. Staff-added/edited products pass through an **Admin approval workflow** before going live. Stock updates and misc purchases are permission-gated and audit-logged.

## 5. Notifications (Confirmed Q18)

- Offers/announcements are authored in Admin and surfaced at the top of the Customer App.
- A persistent **in-app Notifications inbox (C14)** is required, in addition to the top banner.
- **Delivery channels:** notifications can be delivered **in-app**, via **push** (push provider, e.g., FCM), **and** via **SMS** (**AWS SNS**).
- The backend fans out a notification to the configured channels; the in-app inbox is always populated, push/SMS are sent per notification/settings.

## 5a. OTP & SMS (Confirmed Q2/Q18, updated Requirement #1)

- **WhatsApp Business API** (via Meta Cloud API or Twilio) is the **primary channel** for sending OTP codes for customer number verification.
- **AWS SNS** is the **fallback** for OTP delivery (SMS) if WhatsApp delivery fails, and is used for **SMS notifications**.
- OTP generation/validation is handled server-side; WhatsApp/SNS are delivery transports.

## 6. Background Jobs

- Minimal for the trial. Potential lightweight jobs: expiring offers past their validity window; optional daily sales rollup. Not strictly required — can be computed on read.

## 7. Environments & Platform Notes

- Flutter apps: **Android only** for the trial (no iOS build), published to the **Google Play Store**.
- Admin Panel: modern desktop browsers; responsive down to tablet. Additionally exposes **public legal-page routes** (`/privacy`, `/terms`, optional `/about`, `/contact`) that must be reachable without authentication.
- **Play Store compliance:** because the apps collect personal data (name, mobile/WhatsApp #, address, OTP via AWS SNS), the Play listing requires a **publicly hosted Privacy Policy URL** — served from the Admin Panel — and a matching **Play Data Safety** declaration. See `00_ProjectOverview.md` §6a.
- Keep the color system to 2–3 colors across all clients (see `UX/DesignLanguage.md`).

## 8. Non-Functional Considerations (trial-appropriate)

- **Performance:** catalog is small and location-scoped; simple pagination on product lists is enough.
- **Reliability:** order state transitions must be atomic and idempotent (avoid double-confirm).
- **Security:** server-side scope enforcement, input validation, image upload restrictions.
- **Scalability:** modest; design entities so a location can grow categories/products without schema change.
