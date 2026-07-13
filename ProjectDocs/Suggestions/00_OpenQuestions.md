# Open Questions / Decisions to Confirm

> Tracks client decisions. Items are either **✅ CONFIRMED** (decided and propagated into the living spec) or **⏳ PENDING** (still to ask the client).
>
> As of this revision, the client answered most questions and added a new **Recommended Products** requirement. Q5, Q16, and Q23 remain pending.

## Status Summary

| # | Topic | Status |
|---|-------|--------|
| 1 | Offers model (target scope) | ✅ Confirmed |
| 2 | Customer identity / OTP verification | ✅ Confirmed |
| 3 | Already-registered / login caching | ✅ Confirmed |
| 4 | Auto-login | ✅ Confirmed |
| 5 | Pricing model (per-unit vs per-kg, weights) | ⏳ **PENDING client input** |
| 6 | Soft-delete vs hard-delete | ✅ Confirmed |
| 7 | Sales inclusion rule | ✅ Confirmed |
| 8 | Delivery times: global vs per-location | ✅ Confirmed |
| 9 | Offers scope: global vs per-location | ✅ Confirmed |
| 10 | Order cancellation | ✅ Confirmed |
| 11 | Staff-added products approval | ✅ Confirmed |
| 12 | Change Service Location after registration | ✅ Confirmed |
| 13 | Gpay on Delivery details | ✅ Confirmed |
| 14 | Staff login identifier | ✅ Confirmed |
| 15 | Password reset flows | ✅ Confirmed |
| 16 | Invoice format & numbering / GST | ⏳ **PENDING client input** |
| 17 | Language (bilingual?) | ✅ Confirmed |
| 18 | Notifications inbox & delivery channels | ✅ Confirmed |
| 19 | Static/legal content ownership | ✅ Confirmed (hosting revised: public pages on Next.js) |
| 20 | Brand accent color | ✅ Confirmed |
| 21 | Customer primary navigation | ✅ Confirmed |
| 22 | Order sorting | ✅ Confirmed |
| 23 | Legal copy authoring + Play Data Safety | ⏳ **PENDING client input** |
| NEW | Recommended Products (cross-sell) | ✅ Confirmed (new requirement) |

---

## ✅ Confirmed Decisions

### 1. Offers model — target scope
Offers are **festival/promotional banners** tied to a **target scope**: **all categories**, a **specific category**, or a **specific product** (e.g., "20% off Vegetables for Onam"). Percentage-style festival offers are the primary model.
- Model an offer with: `targetType` (All | Category | Product), `targetId` (nullable when All), and a discount expression (e.g., `discountPercent`).
- **Still to confirm later (minor):** whether the discount is actually applied to cart totals or is display-only for the trial. Model the targeting now; treat discount application as a follow-up sub-decision (see note in `02_BusinessRules.md` §8).

### 2. Customer identity / verification
- WhatsApp (mobile) number **MUST be verified via OTP**.
- OTP is sent via **AWS SNS**.
- Mobile numbers are **UNIQUE**.
- **OTP Verification screen (C04) is now REQUIRED** (no longer conditional).

### 3. Already-registered / login caching
- Cache login info (**persist session**) so users don't re-enter their mobile number every time.
- If a user logs out and later re-enters a mobile number that is **already verified**, sign them in by default **WITHOUT OTP again**.
- First-time verification requires OTP; subsequent logins on re-entry of an already-verified number **skip OTP**.

### 4. Auto-login
Confirmed — **auto-login is required** via a persisted session/token.

### 6. Delete strategy
**Soft-delete** for locations/categories/products (preserve history & reporting). `isActive=false` rather than hard delete.

### 7. Sales inclusion rule
Sales reporting counts **only Completed (payment-confirmed) orders**.

### 8. Delivery times — global + per-location
Both a **GLOBAL** option and **per-location** options exist. **Resolution rule: per-location overrides global default.** If a location has its own delivery times, use those; otherwise fall back to global.

### 9. Offers scope — global + per-location
Offers can be **global or per-location**. **Per-location overrides global.**

### 10. Order cancellation
- Customers can cancel while the order is **Pending**.
- Once **Order Confirmed** (accepted), it can **NO LONGER be cancelled**.
- Reflected in the order state machine and business rules.

### 11. Staff-added products — approval workflow
- Some staff can add/edit products (permission-gated).
- Staff-added/edited products require **Admin approval** before going live.
- New approval states: `PendingApproval → Approved | Rejected`. Reflected in Staff Add Product (S05), Admin Products (A05), and permissions.

### 12. Change Service Location after registration
**Yes** — via profile. On change, **re-validate / clear the cart** (catalog is location-bound).

### 13. Gpay on Delivery
Purely **manual personal transfer** — no UPI ID/QR displayed in-app. When staff approves/confirms the payment (**Payment Confirm**), the order becomes **Completed**.

### 14. Staff login identifier
- **Phone + password.**
- A staff member can **also** use the Customer App as a normal customer with the **SAME phone number**.
- The same phone number can be **both** a customer account and a staff account; the two apps authenticate **independently** against separate identity tables. Identity/role resolution is documented in `01_UserRoles.md` §8 and `02_BusinessRules.md` §4.

### 15. Password reset
- **Admin:** self-service "forgot password".
- **Staff:** NO self-reset — the **Admin resets** staff passwords.
- **Customer:** no password (OTP-based identity), so no password reset applies.

### 17. Language
**English only** for now. No Malayalam/bilingual UI required.

### 18. Notifications
- An **in-app Notifications inbox is needed** (C14 confirmed, kept separate from Offers).
- Notifications can be delivered **in-app, via push, AND via SMS**.
- SMS via **AWS SNS**; push via a push provider (e.g., FCM). Reflected in integrations and the Notifications screen.

### 19. Static/legal content
**Static** for now (not an admin-managed CMS).

**Revised delivery (Confirmed):** legal content is still **static/hardcoded (not a CMS)**, but it is now **served as public, unauthenticated pages from the Next.js Admin Panel** (`/privacy`, `/terms`, optional `/about`, `/contact`) rather than only bundled inside the mobile apps. These hosted pages are the **canonical single source of truth** and provide the **publicly hosted Privacy Policy URL required by the Google Play Store** (the Flutter apps collect personal data: name, mobile/WhatsApp #, address, OTP via AWS SNS). The mobile apps **link to / render** these URLs (C15, C13, C03, S07) so legal edits don't require an app update. See `00_ProjectOverview.md` §6a/§6b, `03_SystemArchitecture.md`, `07_Integrations.md` §6, and `Screens/AdminPanel/00_ScreenInventory.md`.

### 20. Brand accent color
**Chosen accent: deep sea teal `#0E7C86`.** Rationale: the business sells mainly sea-related food (fish) plus vegetables/meat; a sea/teal blue fits the identity and pairs cleanly with the white background / black text branding. Used for primary buttons, active states, links, and status highlights. Everything else stays black/white.

### 21. Customer primary navigation
Confirmed — **bottom navigation**: Home (Categories), Cart, Orders, Profile.

### 22. Order sorting
Confirmed — **pending/oldest first** in staff and admin order lists.

### NEW. Recommended Products (cross-sell / upsell)
- Admin configures recommended products at **two levels**: (a) per **Category**, and (b) per **specific Product**.
- At **cart/checkout**, the relevant recommended products are shown so the customer can quick-add them.
- Propagated into data model, Admin Categories/Products screens, Customer Cart/Checkout (C07), flows, and API concepts.

---

## ⏳ Pending — To Ask Client Later

> These remain open. Do not treat as decided. The rest of the spec marks the affected areas as "pending Q5 / Q16".

### 5. Pricing model — **PENDING**
Is Price a **fixed price per listed unit** or **per-kg**? What are **Gross vs Net weight** used for (informational vs pricing basis)? What **weight unit** (g/kg)? Still awaiting client answer. Product docs and data model leave the pricing basis unresolved and flagged.

### 16. Invoice format & numbering — **PENDING**
Invoice **format** (PDF assumed), **numbering scheme**, and whether a **tax/GST field** is needed. Still awaiting client answer. Invoice feature/rules keep these flagged.

### 23. Legal copy authoring (Privacy Policy & Terms text) — **PENDING**
The **hosting mechanism is confirmed** (public static pages on the Next.js Admin Panel — see Q19 revision), but the **actual legal copy still needs to be authored/provided**. Open items:
- **Ownership:** who writes the Privacy Policy & Terms & Conditions text (client / legal counsel / drafted for review)?
- The Privacy Policy must accurately describe the data collected: **name, WhatsApp/mobile number, delivery address, and OTP (via AWS SNS)**.
- **Play Data Safety consistency:** the Google Play **Data Safety declaration** must match the published Privacy Policy (same data categories, purposes, and handling). These two must be kept in sync at submission and whenever data collection changes.

Until the copy is provided, the `/privacy` and `/terms` pages remain placeholders and the app cannot be submitted to the Play Store.
