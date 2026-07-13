# Eze-Cart — Business Rules

> Validation rules, restrictions, approval flows, ownership, and entity lifecycles. Explains the "why" behind each rule.

## 1. Service Location Rules

- The **Service Location** is the top-level partition. Every Category, Product, Offer, Staff assignment, and Order belongs to exactly one Service Location.
- Service Locations are created/removed **only** by Main Admin.
- The Customer App's Service Location dropdown at registration is populated from the Admin-managed list.
- **Why:** Enables neighborhood-scoped catalogs and location-based fulfillment without maps.

**Lifecycle & cascade**
- Adding a location makes it available in the customer registration dropdown.
- Removing a location: **must be confirmed** — cascades to hide its categories/products and affects existing customers and open orders.
  - **Confirmed (Q6): soft-delete.** Removal sets `isActive=false` (hide) and **preserves** historical orders and sales reporting. This applies to locations, categories, and products alike. No hard deletes.

## 2. Category Rules

- Categories (e.g., Fish, Vegetables, Meat) are created per Service Location by Admin, each with an image.
- The same conceptual category may exist independently under multiple locations (each with its own products).
- A category with no active products should still render but show an empty state to the customer.

## 3. Product Rules

- Product fields: **Product Name, Gross Weight, Net Weight, Price**, plus a product **image**.
- Products are managed per Category by Admin (and optionally by Staff with `add_products`, **subject to Admin approval — see §9a**).
- Price is displayed in the customer catalog card.
- ⏳ **PENDING (Q5 — client input):** pricing model — is Price a fixed price for the listed item, or per-kg based on weight? Gross vs Net weight display purpose (informational vs pricing basis) and weight unit (g/kg) are still to be confirmed by the client.

**Soft-delete (Confirmed Q6):** products are never hard-deleted; deactivation sets `isActive=false` and preserves historical order snapshots and reporting.

**Recommended Products (Confirmed — new requirement):**
- Admin can attach a list of **recommended products** to a **Product** (product-level cross-sell) and to a **Category** (category-level cross-sell).
- These recommendations are surfaced to the customer at **cart/checkout (C07)** so they can quick-add them.
- See §13 for the full rule set and `05_DatabaseConcepts.md` for the relationship model.

**Validation**
- Name required, non-empty.
- Gross/Net weight: positive values; Net ≤ Gross (recommended validation).
- Price: positive number.
- Image: required for good UX (card back reveals the image).

## 4. Customer Registration & Auth Rules (Confirmed Q2/Q3/Q4/Q12/Q14)

- Registration fields: **Name, WhatsApp Number, Delivery Address with Nearest Landmark, Service Location** (dropdown).
- **OTP verification is mandatory:** the WhatsApp/mobile number **must be verified via OTP** (OTP sent via **AWS SNS**) before the account becomes active. The OTP Verification screen (C04) is required.
- **Mobile numbers are UNIQUE** (within the customer identity).
- **Cached login / auto-login:** the session is persisted so returning customers are **auto-logged-in** with no repeated friction.
- **Re-entry of an already-verified number skips OTP:** if a user logs out and later re-enters a mobile number that is already verified, they are signed in **without OTP again**. OTP is only required on **first-time** verification.
- After login/registration, the customer lands directly on the **Category page** filtered by their chosen Service Location.
- **Change Service Location after registration (Confirmed Q12):** allowed from Profile. On change, the **cart is re-validated/cleared** because the catalog is location-bound.

**Dual identity (Confirmed Q14):** the same phone number can be both a Customer account (Customer App, OTP, no password) and a Staff account (Staff App, phone + password). The two apps authenticate independently against separate identity records. See `01_UserRoles.md` §8.

**Password reset (Confirmed Q15):** Customers have no password (OTP identity) — no reset flow. Admin self-service forgot-password; Staff passwords are reset by Admin only.

## 5. Cart & Checkout Rules

- Customers add/remove products in the cart before checkout.
- Before placing the order the customer must choose a **Delivery Time** from an Admin-managed dropdown.
- Payment method must be one of exactly two: **Gpay on Delivery** or **Cash on Delivery**. No online payment.
- On successful checkout:
  - An order is created in `Pending` state.
  - An **invoice is auto-generated and downloadable**.
  - An order confirmation screen is shown.
- Empty cart must show a friendly empty state and block checkout.

**Validation**
- Cart must contain ≥ 1 item to checkout.
- Delivery Time selection required.
- Payment method selection required.
- All cart items must belong to the customer's Service Location (catalog is already location-filtered, so this holds by construction).

## 6. Delivery Time Rules

- Delivery Time options are add/remove managed by Admin.
- Presented to the customer as a dropdown at checkout.
- **Confirmed (Q8): global + per-location.** A GLOBAL set of delivery times exists, and each Service Location may define its own. **Resolution rule: per-location overrides global** — if a location has its own delivery times configured, those are shown; otherwise the global defaults apply.

## 7. Order Lifecycle & States

```
Pending ──► Order Confirmed ──► Delivered ──► Payment Confirmed (Completed)
   │
   └──► Cancelled   (only from Pending — see cancellation rule below)
```

| State | Set by | Meaning |
|-------|--------|---------|
| `Pending` | System (on checkout) | Order placed, awaiting staff acknowledgement |
| `Order Confirmed` | Staff (`confirm_order`) | Staff accepted the order |
| `Delivered` | Staff (`confirm_delivery`) | Goods handed to customer |
| `Payment Confirmed` / `Completed` | Staff (`confirm_payment`) | Payment collected; method recorded (Gpay/Cash) |
| `Cancelled` | Customer | Order voided by the customer while still Pending |

**Rules**
- State transitions are forward-only in the happy path; Payment Confirm records `paymentMethodCollected` (Gpay | Cash), which may differ from the customer's stated preference (record actual).
- **Cancellation (Confirmed Q10):** the **customer** can cancel **only while the order is `Pending`**. Once an order is **`Order Confirmed`** (accepted by staff), it can **no longer be cancelled** — the cancel action is hidden/disabled and the server rejects a cancel request on non-Pending orders (409).
- **Payment / Gpay (Confirmed Q13):** "Gpay on Delivery" is a **manual personal transfer** (no in-app UPI ID/QR). When staff performs **Payment Confirm**, the order becomes **Completed**.
- Admin has read visibility across all states per location (delivered vs pending dashboard).

## 8. Offers / Notifications Rules (Confirmed Q1/Q9)

Admin can publish **festival/promotional offers** shown at the **top of the Customer App**. An offer is a promotional banner tied to a **target scope** and can express a discount (e.g., a **% discount for a festival** — "20% off Vegetables for Onam").

**Offer target scope (Confirmed Q1):** every offer targets one of:
1. **All categories** (`targetType = All`).
2. **A specific category** (`targetType = Category`, `targetId = categoryId`).
3. **A specific product** (`targetType = Product`, `targetId = productId`).

Percentage-style festival offers are the **primary model** (e.g., `discountPercent`). Special-day/date framing and simple amount/quantity conditions remain supported as optional attributes, but the core model is **target scope + discount**.

**Rules**
- **Scope (Confirmed Q9): global + per-location.** An offer may be **global** or **per-location**; **per-location overrides global**. Customers see per-location offers when present, otherwise the global ones.
- Offers have a validity window (start/end) and an active flag.
- ⏳ **Sub-decision to confirm later (linked to Q1):** whether the discount is **actually applied to cart totals** or is **display-only** for the trial. Model the targeting and discount value now; if discount application is later confirmed, Cart/Checkout (C07) and the invoice must apply/show the discount. Until then, banners may display the offer without mutating cart math.

## 9. Staff Assignment Rules

- Each staff member is tied to exactly one Service Location.
- Admin sets granular permissions per staff (see `01_UserRoles.md`).
- If `restrict_to_categories` is on, staff sees only orders/products for the allowed categories within their location.
- If `add_products` is on, staff can add **and edit** products for allowed categories from their own page — **subject to Admin approval (§9a)**.

## 9a. Staff Product Approval Workflow (Confirmed Q11)

Products **added or edited by staff** do not go live immediately. They flow through an approval state machine:

```
(staff submits add/edit) ──► PendingApproval ──► Approved  (visible to customers)
                                     │
                                     └────────► Rejected  (not visible; staff notified)
```

- **`PendingApproval`** — created/edited by staff; **not visible** in the customer catalog.
- **`Approved`** — an Admin approved it; becomes live/visible (subject to `isActive`).
- **`Rejected`** — an Admin rejected it; stays hidden. Staff can revise and resubmit.
- Admin approves/rejects from the Admin Products screen (A05), which shows a "Pending approval" queue/filter and the submitting staff.
- Admin-created/edited products are **auto-approved** (Admin is the approver).
- Approval status is tracked per product (see `05_DatabaseConcepts.md` → Product.`approvalStatus`, `submittedBy`).

## 10. Reporting Rules

- **Orders dashboard** is grouped by Service Location and shows delivered vs pending counts/lists.
- **Order sorting (Confirmed Q22):** staff and admin order lists are sorted **pending/oldest first**.
- **Total Sales (Confirmed Q7):** reported per Service Location and counts **only Completed (payment-confirmed) orders**. Pending/Confirmed/Delivered/Cancelled orders are excluded from sales totals.
- **Customer purchase history** is looked up by Name or WhatsApp number and lists that customer's orders.

## 11. Invoice Rules

- Auto-generated at checkout, downloadable by the customer.
- Should include: invoice number, date/time, customer name + delivery address + landmark, service location, itemized products (name, weight, qty, unit price, line total), order total, chosen delivery time, chosen payment method, and business branding.
- ⏳ **PENDING (Q16 — client input):** invoice **format** (PDF assumed), **numbering scheme**, and whether a **tax/GST field** is needed. Still awaiting the client's answer.

## 12. Global Validation & Integrity

- WhatsApp number format validation (country code handling — likely India +91). Number must be **unique** and **OTP-verified** (Q2).
- Prices and weights are non-negative numbers.
- Image uploads restricted to reasonable size/type (Admin & Staff uploads).
- Deleting a category/product that appears in historical orders must not corrupt those orders — historical orders retain a snapshot of purchased items (see `05_DatabaseConcepts.md`). **Soft-delete only (Q6).**
- UI language: **English only** (Q17).

## 13. Recommended Products (Cross-sell / Upsell) — Confirmed (new requirement)

**Purpose:** increase basket size by suggesting related products the customer can quick-add at checkout.

**Configuration (Admin):**
- **Category-level recommendations:** Admin attaches a set of recommended products to a **Category**.
- **Product-level recommendations:** Admin attaches a set of recommended products to a **specific Product**.
- Recommendations are directional references from a source (category or product) to target products. A product may be recommended by many sources.

**Customer surfacing:**
- When a customer selects a product and proceeds to **cart/checkout (C07)**, a **"Recommended for you / You may also like"** section shows the relevant recommended products with a **quick-add** control.
- **Resolution rule:** collect product-level recommendations for the products in the cart, plus category-level recommendations for the categories represented in the cart; de-duplicate; **exclude items already in the cart** and inactive/unapproved products; scope to the customer's Service Location.

**Rules & validation:**
- Recommended targets must be **active, approved, and in the same Service Location** as the customer.
- Self-recommendation (a product recommending itself) is ignored.
- Recommendations are **display/quick-add only** — they do not change pricing; adding one behaves like any normal add-to-cart.
- Soft-deleted/deactivated products automatically drop out of recommendation lists.

**Where documented:** data model (`05_DatabaseConcepts.md` → `ProductRecommendation` / `CategoryRecommendation`), Admin screens (A04 Categories, A05 Products), Customer screen (C07), API (`06_APIConcepts.md`), and flows (`04_DataFlow.md`).
