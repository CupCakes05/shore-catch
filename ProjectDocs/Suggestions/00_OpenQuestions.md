# Open Questions / Decisions to Confirm

> Tracks client decisions. Items are either **✅ CONFIRMED** (decided and propagated into the living spec) or **⏳ PENDING** (still to ask the client).
>
> As of this revision, all original questions are confirmed except Q23. Q5, Q16, Q24–Q28 have been confirmed and propagated.

## Status Summary

| # | Topic | Status |
|---|-------|--------|
| 1 | Offers model (target scope) | ✅ Confirmed |
| 2 | Customer identity / OTP verification | ✅ Confirmed (updated: WhatsApp OTP primary) |
| 3 | Already-registered / login caching | ✅ Confirmed |
| 4 | Auto-login | ✅ Confirmed |
| 5 | Pricing model (per-unit vs per-kg, weights) | ✅ **CONFIRMED** — fixed price per packet; weights informational |
| 6 | Soft-delete vs hard-delete | ✅ Confirmed |
| 7 | Sales inclusion rule | ✅ Confirmed |
| 8 | Delivery times: global vs per-location | ✅ Confirmed |
| 9 | Offers scope: global vs per-location | ✅ Confirmed |
| 10 | Order cancellation | ✅ Confirmed (updated: staff can also cancel) |
| 11 | Staff-added products approval | ✅ Confirmed |
| 12 | Change Service Location after registration | ✅ Confirmed |
| 13 | Gpay on Delivery details | ✅ Confirmed (updated: UPI ID displayed + deep-link) |
| 14 | Staff login identifier | ✅ Confirmed |
| 15 | Password reset flows | ✅ Confirmed |
| 16 | Invoice format & numbering / GST | ✅ **CONFIRMED** — format M2026#00001 |
| 17 | Language (bilingual?) | ✅ Confirmed |
| 18 | Notifications inbox & delivery channels | ✅ Confirmed |
| 19 | Static/legal content ownership | ✅ Confirmed |
| 20 | Brand accent color | ✅ Confirmed |
| 21 | Customer primary navigation | ✅ Confirmed |
| 22 | Order sorting | ✅ Confirmed |
| 23 | Legal copy authoring + Play Data Safety | ⏳ **PENDING client input** |
| 24 | GST Display — Inclusive vs Exclusive | ✅ **CONFIRMED** — inclusive display, breakdown on invoice |
| 25 | Pre-Order Date Range | ✅ **CONFIRMED** — no limit on future dates |
| 26 | Stock Decrement Trigger | ✅ **CONFIRMED** — on order placement; restored on cancel |
| 27 | Profit/Loss Costing Method | ✅ **CONFIRMED** — simplified: Total Sale − Total Expense |
| 28 | Staff Place Order / Shop Sale | ✅ **CONFIRMED** — "Shop Sale" for walk-ins; no customer account required |
| NEW | Recommended Products (cross-sell) | ✅ Confirmed |
| R1 | WhatsApp OTP | ✅ Confirmed (Requirement #1) |
| R2 | Order state transition timestamps | ✅ Confirmed (Requirement #2) |
| R3 | Pre-Order feature | ✅ Confirmed (Requirement #3) |
| R4 | Multi-location scoping | ✅ Confirmed (Requirement #4) |
| R5 | GPay/UPI ID at checkout | ✅ Confirmed (Requirement #5) |
| R6 | Customer care contact | ✅ Confirmed (Requirement #6) |
| R7 | Admin edit customer details | ✅ Confirmed (Requirement #7) |
| R8 | Staff misc purchases | ✅ Confirmed (Requirement #8) |
| R9 | Staff update stock | ✅ Confirmed (Requirement #9) |
| R10 | Stock management + profit/loss | ✅ Confirmed (Requirement #10) |
| R11 | GST support | ✅ Confirmed (Requirement #11) |
| R12 | Staff place order / Shop Sale | ✅ Confirmed (Requirement #12) |
| R13 | Invoice GST/non-GST split | ✅ Confirmed (Requirement #13) |

---

## ✅ Confirmed Decisions (Original)

### 1–12, 14–15, 17–22, NEW (Recommended Products)
Previously confirmed and propagated. See earlier version of this document for full details.

### 16. Invoice — FULLY CONFIRMED (Requirements #11, #13)
- **GST confirmed:** invoices now include GST/non-GST product split, GST breakdown by rate, subtotal, GST total, grand total.
- **Format confirmed:** PDF.
### R1. WhatsApp OTP (Requirement #1)
---

## ✅ Confirmed — New Requirements (R1–R13)

### R1. WhatsApp OTP (Requirement #1)
OTP sent via **WhatsApp Business API** (Meta Cloud API / Twilio) as **primary channel**. AWS SNS SMS as **fallback** if WhatsApp delivery fails. Customer experience: OTP arrives on WhatsApp (faster, free for customer). C04 screen updated. See `07_Integrations.md`.

### R2. Order State Transition Timestamps (Requirement #2)
Each fulfillment action (Order Confirm, Delivery Confirm, Payment Confirm) records **exact timestamp + staffId**. Stored in Order entity: `orderConfirmedAt/By`, `deliveredAt/By`, `paymentConfirmedAt/By`. Enables staff performance tracking in reporting (A11). See `05_DatabaseConcepts.md`, S04, A10.

### R3. Pre-Order (Requirement #3)
Customers can order for future delivery dates. Triggered by: out of stock, today's slots exhausted, or proactive choice. Delivery time selection at checkout is now **date + time slot**. Order has `deliveryDate` + `isPreOrder` fields. Products have per-location stock; stock = 0 shows "Out of Stock — Pre-order" badge. See `Features/09_PreOrder.md`.

### R4. Multi-Location Scoping (Requirement #4)
Categories, Products, and Staff can be assigned to **MULTIPLE** service locations (many-to-many). Replaces previous one-location-per-entity model. Stock/inventory remains per-location. Admin forms use multi-select. See junction tables in `05_DatabaseConcepts.md`, screens A04/A05/A07.

### R5. GPay/UPI ID at Checkout (Requirement #5)
Admin configures company GPay/UPI ID in system settings (A13). Displayed at checkout (C07) when customer selects "Gpay on Delivery." "Pay via GPay" button deep-links to GPay app with amount pre-filled (UPI intent URI). Staff still confirms payment on delivery. See `07_Integrations.md` §4.

### R6. Customer Care Contact (Requirement #6)
Admin configures customer care phone + WhatsApp number in system settings (A13). Shown in customer app Settings/Help (C13) with tap-to-call and tap-to-WhatsApp. Hidden if not configured. Uses device intents, not API integration.

### R7. Admin Edit Customer Details (Requirement #7)
Admin can view AND edit customer details (name, address, landmark, service location) from Customer History (A12). WhatsApp number NOT editable (identity key). Changes audit-logged. Service location change re-validates customer cart on next use.

### R8. Staff Miscellaneous Purchases (Requirement #8)
Staff with `record_misc_purchase` permission records operational expenses (item name, qty, cost, optional receipt). New screen S09. Admin views in reporting (A11). Separate from product sales. Included in profit/loss as operational cost. See `Features/13_StaffMiscPurchases.md`.

### R9. Staff Update Stock (Requirement #9)
Staff with `update_stock` permission can update stock counts at assigned locations. Done from S05 (stock section). Changes audit-logged. Stock = 0 triggers pre-order flow. See `Features/10_StockManagement.md`.

### R10. Stock Management — Purchase & Selling Data (Requirement #10)
### R12. Staff Shop Sale (Requirement #12)
Staff with `place_shop_sale` permission can create **Shop Sales** for walk-in/counter customers. This is quick billing without requiring customer registration. Enter phone number (optional customer name) → select products → bill/checkout → generates invoice. No customer account created or needed. The phone number and optional name are just for the bill/invoice. Orders are recorded as `placedBy=staff` with `saleType=shopSale`. New screen S10 (Shop Sale). See `Features/12_StaffPlaceOrder.md`.
### R11. GST Support (Requirement #11)
Per-product GST toggle + percentage. Reflected in product display (C06), cart/checkout (C07 GST breakdown), and invoice (GST/non-GST split). Snapshotted at order time. See `Features/11_GSTSupport.md`.

### R12. Staff Place Order for Customer (Requirement #12)
### R13. Invoice GST/Non-GST Split (Requirement #13)
Invoice template handles mixed GST/non-GST products. Invoice numbering confirmed: M2026#00001 with Muciriz branding. See `Features/03_Invoice.md`.

## ⏳ Pending — To Ask Client Later

### 23. Legal copy authoring (Privacy Policy & Terms text) — **PENDING**
Hosting mechanism confirmed. Actual legal copy still needs to be authored/provided. Privacy Policy must now also describe WhatsApp OTP (in addition to the data previously described).

---

## ✅ Recently Confirmed (Q5, Q16, Q24–Q28)

### Q5. Pricing Model — CONFIRMED
- Products are **packaged** (e.g., 1 kg packet, 500g packet).
- Each package has a **fixed price** regardless of weight (not per-kg pricing).
- The price is for the whole packet/unit.
- Price is editable at any time (can change daily if needed).
- Gross Weight and Net Weight are **informational/display** fields (they describe the packet, but pricing is NOT calculated from them).
- Weight unit: support both grams (g) and kilograms (kg) — the product listing just shows the package weight as info.

### Q16. Invoice Numbering — CONFIRMED
- Format: `M2026#00001` (M = Muciriz, followed by year, # separator, then 5-digit sequential number).
- Resets annually (counter resets to 00001 each new year).
- Examples: M2026#00001, M2026#00002, ... M2026#99999, then next year: M2027#00001.

### Q24. GST Display — CONFIRMED: INCLUSIVE
- Product prices are shown **inclusive** of GST (the displayed price already includes GST).
- On the invoice and cart, the GST component is **broken out/specified** (show the base amount + GST amount = total).
- Customer sees "₹210" on the product card (inclusive), but the invoice shows "Base: ₹200, GST 5%: ₹10, Total: ₹210".

### Q25. Pre-Order Date Range — CONFIRMED
- **No limit** on how far ahead a customer can pre-order.
- Show all available future dates (no artificial cap like 7 days).

### Q26. Stock Decrement — CONFIRMED
- Stock is decremented **when order is placed** (on Pending), NOT on completion.
- Stock is reserved at order time.
- **Staff can also cancel orders** — when staff or customer cancels an order, the stock is **restored/incremented back**.
- Staff cancel capability added to S04 (Order Detail). Staff can cancel orders in any state before Completed.
- On ANY cancellation (customer or staff), stock count goes back up.

### Q27. Profit/Loss — CONFIRMED (SIMPLIFIED)
- **Total Sale − Total Expense = Profit/Loss** (simple aggregate formula).
- No need for average cost or FIFO per-product batch tracking.
- Total Sale = revenue from all completed orders.
- Total Expense = sum of all purchase records (stock purchases) + sum of all miscellaneous purchases (staff expenses).
- Profit = Total Sale − Total Expense.

### Q28. Staff Shop Sale — CONFIRMED
- Staff can bill against a phone number **without creating a full user account**.
- Called a **"Shop Sale"** (not a regular order placed for a registered customer).
- Customer name is **optional** (phone number is the only identifier, and even that is just for the bill).
- For walk-in/counter sales — quick billing without requiring customer registration.
- Still generates an invoice.
- S10 renamed from "Place Order for Customer" to "Shop Sale".
- Flow: enter phone number (optional customer name) → select products → bill/checkout → generates invoice.
- No need to create a customer record or merge later — it's just a sale record with an optional phone/name.

### Q28. Staff-Created Customer — Verification Later? — **TO CONFIRM**
When staff creates a customer record (S10), the customer has no OTP verification. If that customer later downloads the app and registers with the same phone:
- Should the existing record (with orders) seamlessly merge?
- Should OTP verification complete the account?
**Recommendation: yes, merge seamlessly.** Specced this way. 
