# Eze-Cart — System Architecture

> Conceptual architecture across the three apps and shared backend. No implementation code — architecture and responsibilities only.

## 1. High-Level Components

```
┌──────────────────────────────────────────────────────────────────┐
│                         Client Applications                        │
│                                                                    │
│  Admin Panel (Next.js, web)   Staff App (Flutter)   Customer App   │
│         │                          │                  (Flutter)    │
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

### Frontend — Admin Panel (Next.js, web)
- Responsive web UI for Main Admin.
- CRUD screens for locations, categories, products, delivery times, staff, offers.
- Dashboards for orders and sales; customer history lookup.
- Image upload UI for categories/products.
- **Public (unauthenticated) surface:** also serves static, publicly reachable **legal pages** — Privacy Policy (`/privacy`), Terms (`/terms`), optional About/Contact — at stable public URLs. These require **no admin login** and are distinct from the authenticated console. They provide the **hosted Privacy Policy URL required by the Google Play Store** (the Flutter apps collect personal data) and act as the **canonical source** of legal content that the mobile apps link to / render. Static/hardcoded — not an admin CMS (Q19). See `00_ProjectOverview.md` §6a/§6b and `Screens/AdminPanel/00_ScreenInventory.md`.

### Frontend — Staff App (Flutter, Android)
- Auth tied to a Service Location.
- Order list + detail; order/delivery/payment confirmation actions.
- Optional product-add screen (permission-gated).
- Profile.

### Frontend — Customer App (Flutter, Android)
- Onboarding, registration, auto-login.
- Location-scoped category browse, product cards (flip to image), quantity add.
- Category scroll strip, cart/checkout, delivery-time & payment selection.
- Invoice download, order confirmation, order history, offers banner, profile/settings.

### Backend API
- **Auth & Session:** customer registration + **OTP verification (AWS SNS)** + cached/auto-login sessions; staff login (phone+password); admin login. Issues tokens; enforces role. Resolves customer vs staff identity per app.
- **Catalog service:** service locations, categories, products (all scoped by location); **staff product approval workflow**; **recommended products** (category-level and product-level).
- **Orders service:** create order, lifecycle transitions (incl. **customer cancel while Pending**), order queries by role/scope.
- **Offers service:** create/publish offers with **target scope (all/category/product)** and **global or per-location** scope (per-location overrides global); serve active offers to customers.
- **Staff & permissions service:** create staff, assign location + granular permissions; Admin resets staff passwords.
- **Notifications service:** fan-out to **in-app inbox, push, and SMS (AWS SNS)**.
- **Reporting service:** orders-by-location (oldest-first), sales-by-location (**Completed only**), customer history lookup.
- **Invoice service:** generate downloadable invoice (PDF) from order data.

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

- **Customer:** registration creates an account keyed on the WhatsApp number, which is **OTP-verified via AWS SNS** and **unique**. Session is **cached/persisted** so returning users are **auto-logged-in**. On logout + re-entry of an already-verified number, sign-in proceeds **without OTP** (first-time verification only). No password.
- **Staff:** credentials created by Admin; login is **phone + password**, tied to a Service Location; permissions enforced server-side. Staff cannot self-reset passwords (Admin resets).
- **Admin:** single Main Admin account (credentialed login) with self-service forgot-password.
- **Shared phone number:** the same phone can be both a Customer and a Staff account. Customer and Staff are **separate identity records**; each app authenticates against its own identity independently (see `01_UserRoles.md` §8).
- Authorization is **role + scope** based: every request is checked for role (admin/staff/customer) and data scope (which Service Location, which categories).

> Security note: because there is no payment gateway, the most sensitive surfaces are auth and staff permission enforcement. Customer identity is now hardened with **OTP verification (AWS SNS) + unique numbers**. Enforce all permission/scope checks **server-side**, never trust the client. Staff-added/edited products pass through an **Admin approval workflow** before going live.

## 5. Notifications (Confirmed Q18)

- Offers/announcements are authored in Admin and surfaced at the top of the Customer App.
- A persistent **in-app Notifications inbox (C14)** is required, in addition to the top banner.
- **Delivery channels:** notifications can be delivered **in-app**, via **push** (push provider, e.g., FCM), **and** via **SMS** (**AWS SNS**).
- The backend fans out a notification to the configured channels; the in-app inbox is always populated, push/SMS are sent per notification/settings.

## 5a. OTP & SMS (Confirmed Q2/Q18)

- **AWS SNS** is used to send **OTP codes** for customer number verification and to send **SMS notifications**.
- OTP generation/validation is handled server-side; SNS is the delivery transport.

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
