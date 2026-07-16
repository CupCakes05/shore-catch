# Muciriz — Business Rules

> Validation rules, restrictions, approval flows, ownership, and entity lifecycles. Explains the "why" behind each rule.

## 1. Service Location Rules

- The **Service Location** is the top-level partition. Every Order belongs to exactly one Service Location. Categories, Products, and Staff can be assigned to **MULTIPLE** Service Locations (many-to-many). Offers apply globally or per-location.
- Service Locations are created/removed **only** by Main Admin.
- The Customer App's Service Location dropdown at registration is populated from the Admin-managed list.
- **Why:** Enables neighborhood-scoped catalogs and location-based fulfillment without maps.

**Lifecycle & cascade**
- Adding a location makes it available in the customer registration dropdown.
- Removing a location: **must be confirmed** — cascades to hide its categories/products and affects existing customers and open orders.
  - **Confirmed (Q6): soft-delete.** Removal sets `isActive=false` (hide) and **preserves** historical orders and sales reporting. This applies to locations, categories, and products alike. No hard deletes.

## 2. Category Rules

- Categories (e.g., Fish, Vegetables, Meat) are created by Admin and can be assigned to **MULTIPLE** Service Locations (many-to-many, via multi-select in the Admin form).
- The same category entity can serve multiple locations; each location's customers see the category if it is assigned to their location.
- A category with no active products should still render but show an empty state to the customer.

## 3. Product Rules

- Product fields: **Product Name, Gross Weight, Net Weight, Price, GST applicable (yes/no), GST percentage (if applicable)**, plus a product **image**.
- Products can be assigned to **MULTIPLE** Service Locations (many-to-many). **Stock/inventory is tracked per-location** — a product may exist in multiple locations but has independent stock counts per location.
- Products are managed by Admin (and optionally by Staff with `add_products`, **subject to Admin approval — see §9a**).
- Price is displayed in the customer catalog card.
- **Pricing model (Confirmed Q5):** products are **packaged** (e.g., 1 kg packet, 500g packet). Each package has a **fixed price** regardless of weight — pricing is NOT per-kg. The price is for the whole packet/unit. Price is editable at any time (can change daily). Gross Weight and Net Weight are **informational/display** fields only — they describe the packet but pricing is NOT calculated from them. Weight unit: both grams (g) and kilograms (kg) are supported as display values.
- **Stock/availability flag:** products have a stock count per location. When out of stock at a location, the product card indicates this and defaults the customer to the pre-order flow.

**GST (Confirmed — Requirement #11, Q24):**
- Each product has a `gstApplicable` toggle (yes/no).
- If yes, a `gstPercent` field (e.g., 5, 12, 18) specifies the GST rate.
- **GST is INCLUSIVE in the displayed price.** The price shown to the customer on the product card already includes GST.
- On the invoice and in the cart/checkout, the GST component is **broken out** (base amount + GST amount = total). Example: customer sees "₹210" on the product card (inclusive), but the invoice shows "Base: ₹200, GST 5%: ₹10, Total: ₹210".
- Prices in the system are stored as the **inclusive** (final) price. The base price is back-calculated: `basePrice = price / (1 + gstPercent/100)`.

**Soft-delete (Confirmed Q6):** products are never hard-deleted; deactivation sets `isActive=false` and preserves historical order snapshots and reporting.

**Recommended Products (Confirmed — new requirement):**
- Admin can attach a list of **recommended products** to a **Product** (product-level cross-sell) and to a **Category** (category-level cross-sell).
- These recommendations are surfaced to the customer at **cart/checkout (C07)** so they can quick-add them.
- See §13 for the full rule set and `05_DatabaseConcepts.md` for the relationship model.

**Validation**
- Name required, non-empty.
- Gross/Net weight: positive values; Net ≤ Gross (recommended validation).
- Price: positive number.
- GST percent (when applicable): positive number, common values 5/12/18/28.
- Image: required for good UX (card back reveals the image).

## 4. Customer Registration & Auth Rules (Confirmed Q2/Q3/Q4/Q12/Q14)

- Registration fields: **Name, WhatsApp Number, Delivery Address with Nearest Landmark, Service Location** (dropdown).
- **OTP verification is mandatory:** the WhatsApp/mobile number **must be verified via OTP** (OTP sent via **WhatsApp Business API** as primary channel, with AWS SNS as SMS fallback) before the account becomes active. The OTP Verification screen (C04) is required.
- **Mobile numbers are UNIQUE** (within the customer identity).
- **Cached login / auto-login:** the session is persisted so returning customers are **auto-logged-in** with no repeated friction.
- **Re-entry of an already-verified number skips OTP:** if a user logs out and later re-enters a mobile number that is already verified, they are signed in **without OTP again**. OTP is only required on **first-time** verification.
- After login/registration, the customer lands directly on the **Category page** filtered by their chosen Service Location.
- **Change Service Location after registration (Confirmed Q12):** allowed from Profile. On change, the **cart is re-validated/cleared** because the catalog is location-bound.

**Dual identity (Confirmed Q14):** the same phone number can be both a Customer account (Customer App, OTP, no password) and a Staff account (Staff App, phone + password). The two apps authenticate independently against separate identity records. See `01_UserRoles.md` §8.

**Password reset (Confirmed Q15):** Customers have no password (OTP identity) — no reset flow. Admin self-service forgot-password; Staff passwords are reset by Admin only.

## 5. Cart & Checkout Rules

- Customers add/remove products in the cart before checkout.
- Before placing the order the customer must choose a **Delivery Date + Time Slot** (today or future date — supports pre-orders). If ordering for today, time slots for today are shown; if today's slots are exhausted or a product is out of stock, the customer is prompted/defaulted to select a future date.
- Payment method must be one of exactly two: **Gpay on Delivery** or **Cash on Delivery**. No online payment.
- **GPay/UPI ID display (Confirmed — Requirement #5):** the company's GPay/UPI ID (configured by Admin) is displayed on the checkout page. A "Pay via GPay" button deep-links to the GPay app with the payment amount pre-filled using UPI intent URI (`upi://pay?pa=...&am=...`). Staff still confirms payment on delivery.
- On successful checkout:
  - An order is created in `Pending` state (with delivery date + time slot).
  - An **invoice is auto-generated and downloadable** (with GST/non-GST split).
  - An order confirmation screen is shown.
- Empty cart must show a friendly empty state and block checkout.

**Validation**
- Cart must contain ≥ 1 item to checkout.
- Delivery date + time slot selection required.
- Payment method selection required.
- All cart items must belong to the customer's Service Location (catalog is already location-filtered, so this holds by construction).
- If any cart item is out of stock for today, inform the customer and suggest pre-order for a future date.

## 6. Delivery Time Rules

- Delivery Time options are add/remove managed by Admin.
- Presented to the customer as a **date + time slot picker** at checkout. For today's orders, show today's available slots. For pre-orders, show available future dates and their time slots.
- **Confirmed (Q8): global + per-location.** A GLOBAL set of delivery times exists, and each Service Location may define its own. **Resolution rule: per-location overrides global** — if a location has its own delivery times configured, those are shown; otherwise the global defaults apply.
- **Pre-order support:** delivery times define daily availability windows. When today's slots are exhausted or a product is out of stock, the system presents future available dates. Customers can also proactively choose future dates for pre-orders.

## 7. Order Lifecycle & States

```
Pending ──► Order Confirmed ──► Delivered ──► Payment Confirmed (Completed)
   │
   └──► Cancelled   (only from Pending — see cancellation rule below)
```

| State | Set by | Meaning |
|-------|--------|---------|
| `Pending` | System (on checkout) | Order placed, awaiting staff acknowledgement |
| `Order Confirmed` | Staff (`confirm_order`) | Staff accepted the order; **timestamp + staffId recorded** |
| `Delivered` | Staff (`confirm_delivery`) | Goods handed to customer; **timestamp + staffId recorded** |
| `Payment Confirmed` / `Completed` | Staff (`confirm_payment`) | Payment collected; method recorded (Gpay/Cash); **timestamp + staffId recorded** |
| `Cancelled` | Customer or Staff | Order voided while still Pending (by customer) or before Completed (by staff); stock restored |

**Rules**
- State transitions are forward-only in the happy path; Payment Confirm records `paymentMethodCollected` (Gpay | Cash), which may differ from the customer's stated preference (record actual).
- **Each state transition records a timestamp and the staffId who performed it** (Requirement #2). This supports operational performance tracking (how long between states, which staff handled each step).
- **Cancellation (Confirmed Q10, updated Q26):** the **customer** can cancel **only while the order is `Pending`**. **Staff can cancel orders in any state before `Completed`** — this enables operational corrections and supports stock restoration. Once an order is **`Completed`**, it can no longer be cancelled.
- **Stock impact on cancellation (Q26):** when ANY cancellation occurs (customer or staff), the stock that was decremented at order placement is **restored/incremented back** to the per-location stock count.
- **Payment / Gpay (Confirmed Q13):** "Gpay on Delivery" is a **manual personal transfer**. Company GPay/UPI ID is displayed at checkout (C07) for customer reference. When staff performs **Payment Confirm**, the order becomes **Completed**.
- Admin has read visibility across all states per location (delivered vs pending dashboard).
- **Pre-orders** follow the same lifecycle; they simply have a future `deliveryDate`.

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

- Each staff member can be assigned to **one or more** Service Locations (many-to-many via multi-select in Admin A07).
- Admin sets granular permissions per staff (see `01_UserRoles.md`).
- If `restrict_to_categories` is on, staff sees only orders/products for the allowed categories within their assigned locations.
- If `add_products` is on, staff can add **and edit** products for allowed categories from their own page — **subject to Admin approval (§9a)**.
- If `update_stock` is on, staff can update stock/inventory counts for products at their assigned locations.
- If `record_misc_purchase` is on, staff can record miscellaneous operational purchases.
- If `place_shop_sale` is on, staff can create Shop Sales (walk-in/counter billing).

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
- **Profit/Loss reporting (Confirmed — Requirement #10):** Admin can view revenue vs cost (purchase price × qty sold) per location. Profit = selling revenue − purchase cost. Requires stock purchase data to be maintained.
- **Staff performance tracking (Requirement #2):** state transition timestamps enable reporting on how quickly staff confirm/deliver/complete orders.
- **Miscellaneous purchases** (staff-recorded operational expenses) are reported separately from product sales.
- **Customer purchase history** is looked up by Name or WhatsApp number and lists that customer's orders.

## 11. Invoice Rules

- Auto-generated at checkout, downloadable by the customer.
- **Invoice numbering (Confirmed Q16):** format `M{YEAR}#{SEQUENTIAL}` — e.g., `M2026#00001`. M = Muciriz, followed by 4-digit year, # separator, 5-digit zero-padded sequential number. Resets annually (counter resets to 00001 each new year).
- Should include: invoice number, date/time, customer name + delivery address + landmark, service location, itemized products (name, weight, qty, unit price, GST info, line total), **GST breakdown** (total GST amount, split by rate), order subtotal (before GST), order total (after GST), chosen delivery date + time slot, chosen payment method, and **Muciriz** business branding.
- **GST/Non-GST split (Confirmed — Requirements #11 & #13):** the invoice separates products WITH GST (showing GST amount per line and GST total) from products WITHOUT GST. Mixed orders show both sections clearly.
- **GST display on invoice (Q24):** since prices are stored inclusive of GST, the invoice back-calculates to show base price + GST amount = inclusive price per line.
- Invoice template should be professional and suitable for local grocery-style commerce.
- ✅ **Q16 — FULLY CONFIRMED:** GST confirmed (Requirement #11). Invoice format is PDF. Numbering: `M2026#00001` (annual reset, 5-digit sequential).

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

## 14. Pre-Order Rules (Confirmed — Requirement #3)

**Purpose:** Allow customers to place orders for future delivery dates, enabling planning and handling out-of-stock / exhausted-slot situations gracefully.

**Triggers for pre-order flow:**
1. A product in the cart is **out of stock** for today at the customer's location.
2. All delivery **time slots for today are over** (exhausted/past).
3. Customer **proactively** chooses a future date (they can always pre-order regardless of stock/slot availability).

**Rules:**
- Delivery time selection at checkout becomes a **date + time slot** picker (not just a time slot for today).
- **No limit** on how far ahead a customer can pre-order (Confirmed Q25). Show all available future dates — no artificial cap.
- Each date shows available time slots for that date (from the same delivery time configuration — global or per-location).
- Products have a per-location `stockCount` (or availability flag). When stock = 0 at a location, the product card shows an "Out of stock" indicator and defaults to pre-order flow.
- Pre-orders follow the same order lifecycle (Pending → Confirmed → Delivered → Completed) and are fulfillable by staff on the scheduled delivery date.
- Pre-order status is visible in order history with the scheduled delivery date prominently displayed.

## 15. Multi-Location Scoping Rules (Confirmed — Requirement #4)

**Purpose:** Allow a single category, product, or staff member to serve multiple service locations without duplication.

**Rules:**
- **Categories ↔ Service Locations:** many-to-many. A category can be assigned to multiple locations via multi-select in Admin (A04).
- **Products ↔ Service Locations:** many-to-many. A product can be assigned to multiple locations via multi-select in Admin (A05). 
- **Staff ↔ Service Locations:** many-to-many. A staff member can be assigned to multiple locations via multi-select in Admin (A07).
- **Stock/Inventory is PER-LOCATION:** even though a product exists in multiple locations, its stock count, purchase data, and availability are tracked independently per location (via `ProductLocationStock` entity).
- When the customer browses, they see only categories and products assigned to their location (with stock data for that location).
- Staff see orders for their assigned location(s) only.
- Admin forms for categories, products, and staff use a **multi-select** for Service Locations rather than a single dropdown.
- Removing a location from a category/product/staff assignment does not delete the entity — it just removes the association.

## 16. GPay/UPI ID at Checkout (Confirmed — Requirement #5)

**Purpose:** Give customers the company's payment details upfront so they can prepare for payment on delivery; optionally deep-link to GPay for convenience.

**Rules:**
- Admin configures the company **GPay/UPI ID** from the Admin Settings screen (A13).
- The UPI ID is displayed on the Cart/Checkout screen (C07) when the customer selects "Gpay on Delivery."
- A **"Pay via GPay" button** is shown that deep-links to GPay using the UPI intent URI: `upi://pay?pa={upiId}&pn={merchantName}&am={amount}&cu=INR`.
- This is a **convenience shortcut only** — actual payment confirmation is still done by staff on delivery (Payment Confirm).
- If the UPI ID is not configured by Admin, the button/display is hidden and checkout proceeds as before (manual Gpay transfer without in-app reference).

## 17. Customer Care Contact (Confirmed — Requirement #6)

**Purpose:** Give customers easy access to support without leaving the app.

**Rules:**
- Admin configures a **customer care phone number** and/or **WhatsApp number** from the Admin Settings screen (A13).
- In the customer app, a "Help" or "Contact Us" entry is accessible from Settings (C13) / Profile (C12) / Static pages (C15).
- The contact entry shows the configured number(s) with tap-to-call and tap-to-open-WhatsApp actions (device intents, not API integration).
- If no customer care number is configured, the entry is hidden.

## 18. Admin Edit Customer Details (Confirmed — Requirement #7)

**Purpose:** Allow Admin to correct/update customer information for support scenarios (e.g., wrong address, relocated customer).

**Rules:**
- Admin can view AND edit customer details from the Customer History screen (A12).
- Editable fields: **name, delivery address, nearest landmark, service location**.
- On service location change by Admin, the customer's cart rules apply as normal (cleared/re-validated on next use).
- WhatsApp number is NOT editable by Admin (it is the identity key).
- Changes are logged (who changed what, when) for audit.

## 19. Staff Miscellaneous Purchases (Confirmed — Requirement #8)

**Purpose:** Track operational expenses incurred by staff (e.g., buying plastic bags, packaging materials) separately from product sales revenue.

**Rules:**
- Staff with `record_misc_purchase` permission can record misc purchase entries from the Staff App (S09).
- Each entry includes: **item name** (required), **quantity** (required), **cost/amount** (required, ₹), **optional receipt/image upload**.
- Entries are timestamped and attributed to the staff member and their location(s).
- Admin can view all misc purchases in admin reporting (separate section from product sales).
- Misc purchases are **operational expenses** and NOT included in product sales or order revenue totals.
- They ARE included in profit/loss analysis (as expenses).

## 20. Staff Stock Update (Confirmed — Requirement #9)

**Purpose:** Allow staff in the field to update stock counts without Admin involvement.

**Rules:**
- Staff with `update_stock` permission can update stock/inventory counts for products at their assigned location(s).
- Updates are logged (who, when, old value → new value).
- Stock update can be done from a dedicated screen or section in the Staff App.
- Stock is per-location; staff can only update stock for their assigned locations.
- Reducing stock to 0 marks the product as "out of stock" at that location (triggers pre-order flow for customers).

## 21. Stock Management — Purchase & Selling Data (Confirmed — Requirement #10)

**Purpose:** Full inventory management for the business to track costs, revenues, and profit/loss per product per location.

**Rules:**
- **Purchase data (per product per location):** purchase rate (cost price), gross weight purchased, net weight purchased, quantity purchased, supplier name (optional), purchase date.
- **Selling data:** derived from completed orders — selling price × quantity sold per product per location.
- Admin can record purchase entries (cost price, weight, qty, supplier) from the Admin panel (A15 Inventory Management or extension of A05).
- **Profit/Loss calculation (Confirmed Q27 — SIMPLIFIED):**
  - **Total Sale − Total Expense = Profit/Loss**
  - Total Sale = revenue from all completed orders (sum of order totals).
  - Total Expense = sum of all purchase records (stock purchases) + sum of all miscellaneous purchases (staff expenses).
  - This is a simple aggregate calculation — no per-batch costing, no average cost, no FIFO needed.
- Stock levels are maintained per-location and decremented **when order is placed** (Confirmed Q26). Restored on cancellation.
- Admin reporting (A11) shows profit/loss alongside sales figures.

## 22. Staff Shop Sales (Confirmed — Requirement #12, updated Q28)

**Purpose:** Support walk-in customers or counter sales without requiring customer registration.

**Rules:**
- Staff with `place_shop_sale` permission can create **Shop Sales** from the Staff App (S10 — Shop Sale).
- **Flow:** Staff enters a phone number (optional) and optional customer name → selects products → bill/checkout → generates invoice.
- This is a **"Shop Sale"** — quick billing for walk-in/counter sales without requiring customer registration or account creation.
- Customer name is **optional** (phone number is the only identifier, and even that is just for the bill).
- **No customer record is created.** The sale is stored as a sale record with optional phone/name metadata — NOT linked to a Customer entity.
- Still generates an invoice (using the same invoice template with Muciriz branding; phone/name shown if provided).
- The order/sale is attributed: `placedBy = staff`, `placedByStaffId = current staff`, `saleType = shopSale`.
- Shop Sales follow a simplified lifecycle: they are immediately `Completed` on creation (no pending/fulfillment cycle needed — it's an on-the-spot sale).
- Stock is decremented on sale creation.
- Shop Sales count toward **Total Sales** in profit/loss reporting.
- Payment method recorded at time of sale (Gpay or Cash — staff selects).
- Staff can only create Shop Sales for locations they are assigned to.

