# Eze-Cart — Project Overview

> Living Specification — single source of truth. Update this document whenever new information changes product scope, goals, or the ecosystem of apps.

## 1. What Eze-Cart Is

Eze-Cart is a **small trial-run ecommerce system** for local/neighborhood grocery-style commerce (fresh goods such as Fish, Vegetables, Meat). It is intentionally lightweight: no online payment gateway, no maps, no complex third-party integrations. The goal is to validate the end-to-end commerce loop — browse → order → deliver → confirm payment → report — across a small set of **Service Locations**.

> Naming note: The original client PDF (Malayalam + English) referred to the project as **"VillageKart."** The confirmed project name is now **Eze-Cart**. Treat "VillageKart" in any legacy source as a synonym for Eze-Cart.

## 2. Business Purpose

- Let customers in a specific **Service Location** browse locally available product categories and place orders from their phone.
- Let **delivery staff** fulfill and confirm orders (and record how payment was collected) for their assigned location.
- Let a **Main Admin** centrally control the catalog (locations, categories, products), delivery time slots, staff access, promotional offers, and see sales/orders reporting.

Everything in the system **cascades by Service Location**. A Service Location is the top-level partition of the catalog and the orders.

## 3. The Three Applications (Ecosystem)

| # | Application | Platform | Primary Users | Purpose |
|---|-------------|----------|---------------|---------|
| 1 | **Admin Panel** | Next.js (web) | Main Admin (authenticated console) + public visitors (legal pages) | Manage catalog, locations, staff, offers; view orders & sales. **Also hosts a small public, unauthenticated surface** — legal pages (Privacy Policy, Terms, optional About/Contact) at stable public URLs |
| 2 | **Staff App** | Flutter (Android only, for now) | Delivery Staff | See & fulfill orders for assigned location; confirm delivery & payment |
| 3 | **Customer App** | Flutter (Android only, for now) | Customers | Register, browse by location, order, checkout, track history |

A shared **Backend + Database** serves all three apps (see `03_SystemArchitecture.md`).

## 4. Target Audience

- **Customers:** local residents of a supported Service Location who want fresh goods delivered.
- **Delivery Staff:** field workers assigned to one Service Location, fulfilling orders.
- **Main Admin:** the business operator managing the whole catalog and operations.

## 5. Problems Being Solved

- No easy way for a small local seller to take structured orders across multiple neighborhoods.
- Manual tracking of what's delivered vs pending, and how payment was collected (Gpay vs Cash).
- No central control of catalog, pricing, delivery slots, and promotions.

## 6. Scope Constraints (Confirmed)

**In scope**
- Customer registration + **OTP-verified** mobile (WhatsApp) number (OTP via AWS SNS); unique numbers.
- Cached login / persisted session + **auto-login** for returning customers; re-entry of an already-verified number skips OTP.
- Location-filtered catalog (categories → products).
- Cart, delivery-time selection, checkout, auto-generated downloadable invoice.
- **Recommended Products** (cross-sell): Admin-configured per category and per product, surfaced at cart/checkout.
- Payment methods limited to **"Gpay on Delivery"** and **"Cash on Delivery"** only (Gpay is a manual personal transfer — no in-app UPI ID/QR).
- Staff order fulfillment (Order Confirm, Delivery Confirm, Payment Confirm → Completed).
- Staff-added/edited products with **Admin approval workflow** (permission-gated).
- Admin catalog/location/staff/offer management + orders & sales reporting (sales count Completed orders only).
- **Notifications inbox** delivered in-app, via push, and via SMS (SMS via AWS SNS).
- Standard app formalities: onboarding, auth, profile, settings, notifications, and empty/loading/error/success states.
- **English-only** UI for now.

**Out of scope (explicitly excluded for the trial)**
- Online payment gateway / real payment processing.
- Google Maps / geolocation / live delivery tracking.
- Complex integrations (ERP, external logistics).
- Bilingual / Malayalam UI (English only for now).
- Admin-managed CMS for static/legal content (static for now). **Note:** legal content is still not an admin CMS, but it is now served as **static public pages from the Next.js Admin Panel** (see §6b) rather than only bundled inside the mobile apps.
- iOS builds for the Flutter apps (Android only for now).

## 6a. Play Store Compliance — Hosted Privacy Policy (Confirmed)

Both Flutter apps (Customer and Staff) will be published to the **Google Play Store**, and both collect **personal data** — customer name, WhatsApp/mobile number, delivery address, and OTP (via AWS SNS). Google Play therefore **requires a publicly hosted Privacy Policy URL** (a real, publicly reachable web link — not just an in-app page) on the Play listing, and the **Play Data Safety form must declare** the personal data the apps collect (name, mobile number, address, phone-number-based OTP).

**Confirmed hosting approach:** The Next.js Admin Panel is already a hosted web app, so it will serve **public, unauthenticated legal pages** at stable URLs (e.g. `/privacy`, `/terms`, and optionally `/about`, `/contact`). These public URLs are the **canonical source** of the legal content used for the Play listing and linked from the mobile apps. See §6b, `03_SystemArchitecture.md`, `07_Integrations.md`, and the public-legal-pages entry in the Admin Panel screen inventory.

## 6b. Admin Panel — Public vs Authenticated Surface

The Admin Panel (Next.js) now has **two surfaces**:

| Surface | Access | Contents |
|---------|--------|----------|
| **Authenticated console** | Requires admin login | All management screens (A01–A14): catalog, locations, staff, offers, orders, reports |
| **Public legal pages** | No login (publicly reachable) | Privacy Policy (`/privacy`), Terms & Conditions (`/terms`), optional About (`/about`), Contact (`/contact`) |

The public legal pages are **static/hardcoded** in the Next.js app (consistent with Q19 — not an admin-managed CMS). They provide the **stable public URLs required for the Play Store listing** and are the **single source of truth** for legal content. The mobile apps link to (or render in a webview) these hosted URLs so that legal edits do not require an app update.

## 7. Design & Branding

- **Max 2–3 colors** total.
- Background: **White**. Body text: **Black**. Button text: **White or Black**.
- **Brand accent: deep sea teal `#0E7C86`** (fits the sea-food/fish identity; pairs with white/black).
- Clean, minimal, uncluttered look.
- See `UX/DesignLanguage.md` for the consolidated design system notes.

## 8. Ecosystem Diagram (conceptual)

```
                         ┌───────────────────────────┐
                         │      Backend + Database    │
                         │  (Auth, Catalog, Orders,   │
                         │   Offers, Reporting, Files) │
                         └─────────────┬─────────────┘
              ┌───────────────────────┼───────────────────────┐
              │                       │                       │
     ┌────────▼────────┐     ┌────────▼────────┐     ┌────────▼────────┐
     │  Admin Panel    │     │   Staff App     │     │  Customer App   │
     │   (Next.js)     │     │   (Flutter)     │     │   (Flutter)     │
     │  Main Admin     │     │ Delivery Staff  │     │   Customers     │
     └─────────────────┘     └─────────────────┘     └─────────────────┘
```

## 9. Document Map

| Area | Location |
|------|----------|
| Roles & permissions | `01_UserRoles.md` |
| Business rules | `02_BusinessRules.md` |
| System architecture | `03_SystemArchitecture.md` |
| Data flow | `04_DataFlow.md` |
| Data model / entities | `05_DatabaseConcepts.md` |
| API concepts | `06_APIConcepts.md` |
| Integrations | `07_Integrations.md` |
| Screen inventories | `Screens/CustomerApp/`, `Screens/StaffApp/`, `Screens/AdminPanel/` |
| End-to-end journeys | `UserFlows/` |
| Feature specs | `Features/` |
| UX system | `UX/` |
| Suggestions (not confirmed) | `Suggestions/` |
| Implementation prompts | `Prompts/` (generated on request) |

## 10. Open Decisions (Client to Confirm)

Most decisions are now **confirmed** and propagated across the spec (see `Suggestions/00_OpenQuestions.md`). Three remain **PENDING** client input:
1. **Pricing model (Q5):** fixed price per listed unit vs per-kg; what Gross vs Net weight are used for; weight unit (g/kg).
2. **Invoice format & numbering (Q16):** format (PDF assumed), numbering scheme, and whether a tax/GST field is needed.
3. **Legal copy authoring (Q23):** who authors the Privacy Policy & Terms text; the copy must match the data collected (name, mobile #, address, OTP) and the Play Data Safety declaration. Hosting is confirmed (public static pages on the Next.js Admin Panel).

Recently confirmed highlights: OTP-verified unique mobile number (AWS SNS), cached login + auto-login (re-entry of verified number skips OTP), soft-delete, Completed-only sales, global+per-location delivery times & offers (per-location overrides), cancellation only while Pending, staff product approval workflow, phone+password staff login (same phone can be both customer & staff), Admin-reset for staff passwords, English-only UI, in-app/push/SMS notifications, accent `#0E7C86`, bottom nav, oldest-first order sorting, and the new **Recommended Products** cross-sell capability.
