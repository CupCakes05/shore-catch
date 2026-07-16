# Muciriz — Project Overview

> Living Specification — single source of truth. Update this document whenever new information changes product scope, goals, or the ecosystem of apps.

## 1. What Muciriz Is

Muciriz is a **small trial-run ecommerce system** for local/neighborhood grocery-style commerce (fresh goods such as Fish, Vegetables, Meat). It is intentionally lightweight: no online payment gateway, no maps, no complex third-party integrations. The goal is to validate the end-to-end commerce loop — browse → order → deliver → confirm payment → report — across a small set of **Service Locations**.

> Naming note: The original client PDF (Malayalam + English) referred to the project as **"VillageKart."** An intermediate working title was **"Eze-Cart"** — both are now superseded. The confirmed project name is **Muciriz**. Treat "VillageKart" and "Eze-Cart" in any legacy source as synonyms for Muciriz.

## 2. Business Purpose

- Let customers in a specific **Service Location** browse locally available product categories and place orders from their phone.
- Let **delivery staff** fulfill and confirm orders (and record how payment was collected) for their assigned location.
- Let a **Main Admin** centrally control the catalog (locations, categories, products), delivery time slots, staff access, promotional offers, and see sales/orders reporting.

Everything in the system **cascades by Service Location**. A Service Location is the top-level partition of the catalog and the orders.

## 3. The Three Applications (Ecosystem)

| # | Application | Platform | Primary Users | Purpose |
|---|-------------|----------|---------------|---------|
| 1 | **Muciriz Admin** | Next.js (web) | Main Admin (authenticated console) + public visitors (legal pages) | Manage catalog, locations, staff, offers; view orders & sales. **Also hosts a small public, unauthenticated surface** — legal pages (Privacy Policy, Terms, optional About/Contact) at stable public URLs |
| 2 | **Muciriz Staff** | Flutter (Android only, for now) | Delivery Staff | See & fulfill orders for assigned location; confirm delivery & payment |
| 3 | **Muciriz** | Flutter (Android only, for now) | Customers | Register, browse by location, order, checkout, track history |

A shared **Backend + Database** serves all three apps (see `03_SystemArchitecture.md`).

## 4. Target Audience

- **Customers:** local residents of a supported Service Location who want fresh goods delivered.
- **Delivery Staff:** field workers assigned to one or more Service Locations, fulfilling orders.
- **Main Admin:** the business operator managing the whole catalog and operations.

## 5. Problems Being Solved

- No easy way for a small local seller to take structured orders across multiple neighborhoods.
- Manual tracking of what's delivered vs pending, and how payment was collected (Gpay vs Cash).
- No central control of catalog, pricing, delivery slots, and promotions.

## 6. Scope Constraints (Confirmed)

**In scope**
- Customer registration + **OTP-verified** mobile (WhatsApp) number (OTP via **WhatsApp Business API** as primary channel, AWS SNS as fallback); unique numbers.
- Cached login / persisted session + **auto-login** for returning customers; re-entry of an already-verified number skips OTP.
- Location-filtered catalog (categories → products). **Multi-location scoping:** categories, products, and staff can be assigned to MULTIPLE service locations (many-to-many); stock/inventory remains per-location.
- Cart, delivery-time selection (date + time slot for pre-orders), checkout, auto-generated downloadable invoice with GST/non-GST split.
- **Pre-Order**: customers can order for future days (next day or any available day) with time slot selection; also triggered when products are out of stock or today's slots are exhausted. **No limit** on how far ahead a customer can pre-order — all available future dates are shown.
- **Recommended Products** (cross-sell): Admin-configured per category and per product, surfaced at cart/checkout.
- Payment methods limited to **"Gpay on Delivery"** and **"Cash on Delivery"**. Company GPay/UPI ID displayed at checkout with optional deep-link (UPI intent URI); staff still confirms payment on delivery.
- Staff order fulfillment (Order Confirm, Delivery Confirm, Payment Confirm → Completed) with **timestamps per state transition** for operational performance tracking.
- Staff-added/edited products with **Admin approval workflow** (permission-gated).
- **Staff can update product stock** (permission-gated, per assigned locations).
- **Staff can record miscellaneous purchases** (expense tracking for operational procurement).
- **Staff can place "Shop Sales"** (walk-in / counter sales — quick billing against a phone number without requiring customer registration).
- Admin catalog/location/staff/offer management + orders & sales reporting (sales count Completed orders only).
- **Stock/Inventory management**: purchase data (cost price, weights, supplier), selling data, profit/loss reporting.
- **GST support**: per-product GST toggle + percentage; product prices are displayed **inclusive** of GST (the displayed price already includes GST); on the invoice and cart, the GST component is broken out (base amount + GST amount = total).
- **Admin can edit customer details** (name, address, landmark, service location).
- **Customer care contact** configurable from Admin, accessible in customer app.
- **Notifications inbox** delivered in-app, via push, and via SMS (SMS via AWS SNS).
- Standard app formalities: onboarding, auth, profile, settings, notifications, and empty/loading/error/success states.
- **English-only** UI for now.

**Out of scope (explicitly excluded for the trial)**
- Online payment gateway / real payment processing (GPay/UPI ID is displayed for manual transfer; no payment gateway integration).
- Google Maps / geolocation / live delivery tracking.
- Complex integrations (ERP, external logistics).
- Bilingual / Malayalam UI (English only for now).
- Admin-managed CMS for static/legal content (static for now). **Note:** legal content is still not an admin CMS, but it is now served as **static public pages from the Next.js Admin Panel** (see §6b) rather than only bundled inside the mobile apps.
- iOS builds for the Flutter apps (Android only for now).

## 6a. Product Pricing Model (Confirmed)

Products are **packaged** (e.g., 1 kg packet, 500g packet). Each package has a **fixed price** regardless of weight — pricing is NOT per-kg. The price is for the whole packet/unit. Price is editable at any time (can change daily if needed). Gross Weight and Net Weight are **informational/display** fields (they describe the packet but pricing is NOT calculated from them). Weight unit: supports both grams (g) and kilograms (kg) — the product listing shows the package weight as info.

## 6b. Play Store Compliance — Hosted Privacy Policy (Confirmed)

Both Flutter apps (Customer and Staff) will be published to the **Google Play Store**, and both collect **personal data** — customer name, WhatsApp/mobile number, delivery address, and OTP (via WhatsApp Business API / AWS SNS). Google Play therefore **requires a publicly hosted Privacy Policy URL** (a real, publicly reachable web link — not just an in-app page) on the Play listing, and the **Play Data Safety form must declare** the personal data the apps collect (name, mobile number, address, phone-number-based OTP).

**Confirmed hosting approach:** The Next.js Admin Panel (Muciriz Admin) is already a hosted web app, so it will serve **public, unauthenticated legal pages** at stable URLs (e.g. `/privacy`, `/terms`, and optionally `/about`, `/contact`). These public URLs are the **canonical source** of the legal content used for the Play listing and linked from the mobile apps. See §6c, `03_SystemArchitecture.md`, `07_Integrations.md`, and the public-legal-pages entry in the Admin Panel screen inventory.

## 6c. Admin Panel — Public vs Authenticated Surface

The Admin Panel (Next.js, Muciriz Admin) now has **two surfaces**:

| Surface | Access | Contents |
|---------|--------|----------|
| **Authenticated console** | Requires admin login | All management screens (A01–A15): catalog, locations, staff, offers, orders, reports |
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
     │ Muciriz Admin   │     │  Muciriz Staff  │     │    Muciriz      │
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

Most decisions are now **confirmed** and propagated across the spec (see `Suggestions/00_OpenQuestions.md`). Only one remains **PENDING** client input:
1. **Legal copy authoring (Q23):** who authors the Privacy Policy & Terms text; the copy must match the data collected (name, mobile #, address, OTP) and the Play Data Safety declaration. Hosting is confirmed (public static pages on the Next.js Admin Panel).

Recently confirmed highlights: OTP-verified unique mobile number (**WhatsApp Business API** primary, AWS SNS fallback), cached login + auto-login (re-entry of verified number skips OTP), soft-delete, Completed-only sales, global+per-location delivery times & offers (per-location overrides), cancellation only while Pending (customer AND staff can cancel — stock restored on cancel), staff product approval workflow, phone+password staff login (same phone can be both customer & staff), Admin-reset for staff passwords, English-only UI, in-app/push/SMS notifications, accent `#0E7C86`, bottom nav, oldest-first order sorting, **Recommended Products** cross-sell, **Pre-Order** (future date + time slot, no limit on how far ahead), **Multi-location scoping** (categories/products/staff many-to-many), **GPay/UPI ID at checkout**, **Customer care contact**, **Admin edit customer details**, **Staff misc purchases**, **Staff stock update**, **Stock management with profit/loss (simplified: Total Sale − Total Expense)**, **GST support (inclusive pricing, breakdown on invoice)**, **Shop Sales (staff quick billing for walk-ins)**, **Invoice with GST/non-GST split (numbering: M2026#00001)**, **stock decremented on order placement, restored on cancellation**, and **order state transition timestamps**.
