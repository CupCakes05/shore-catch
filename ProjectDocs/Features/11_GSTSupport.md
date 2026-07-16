# Feature — GST Support (Requirement #11)

**Overview:** Products can optionally have GST applied. GST is reflected in product display, cart/checkout totals, and invoices (with proper GST/non-GST split).

**Business purpose:** Tax compliance for products that require GST. Proper invoicing with GST breakdown builds business credibility and supports tax filing.

**Users:** Admin (configure GST per product), Customer (see GST in cart/invoice), Staff (see GST in order details).

**Entry points:**
- Admin: Products (A05) — GST toggle + percentage per product.
- Customer: Product List (C06) — GST indicator; Cart/Checkout (C07) — GST breakdown; Invoice (C08/C10).
- Admin: Order Detail (A10) — GST details; Invoice.

**Dependencies:** Product entity (gstApplicable, gstPercent), Order/OrderItem (GST snapshots).

**Data used:**
- Product: `gstApplicable` (bool), `gstPercent` (decimal).
- OrderItem: `gstApplicableSnapshot`, `gstPercentSnapshot`, `gstAmountSnapshot` (frozen at purchase).

**GST configuration:**
- When adding/editing a product (Admin A05, Staff S05):
  - Toggle: "GST applicable?" (yes/no).
  - If yes: specify GST percentage (common values: 5%, 12%, 18%, 28%).
- GST settings apply to ALL locations where the product is assigned (GST is a product property, not location-specific).

**GST in product display (C06) — CONFIRMED Q24: INCLUSIVE:**
- Product card shows the **inclusive price** (price already includes GST). Example: "₹210" (this already has GST baked in).
- If GST applicable, a small note: "incl. X% GST" for transparency.
- **Confirmed: prices are GST-inclusive.** Customers see the final price on the card.

**GST in cart/checkout (C07):**
- Line items show the inclusive price per unit.
- Order summary **breaks out the GST** (back-calculated from inclusive prices):
  - Total (sum of inclusive prices × quantities).
  - GST breakdown by rate (e.g., "GST 5%: ₹X", "GST 18%: ₹Y") — back-calculated: `gstAmount = inclusivePrice - (inclusivePrice / (1 + rate/100))`.
  - Subtotal (before GST) = Total − Total GST.
  - Grand Total = sum of inclusive prices (same as what customer sees; breakdown is informational).
- Customer sees the same total as the sum of product prices × quantities; the breakdown just clarifies tax.

**GST in invoice (C08, Requirement #13):**
- Invoice splits products into GST and non-GST sections.
- GST items show: product, qty, base price, GST %, GST amount, line total (incl. GST).
- Non-GST items show: product, qty, price, line total.
- Summary: subtotal, GST by rate, total GST, grand total.
- See `03_Invoice.md` for full invoice template.

**GST snapshotting:**
- At order creation, GST settings are snapshotted into each OrderItem (gstApplicableSnapshot, gstPercentSnapshot, gstAmountSnapshot).
- Later changes to a product's GST rate do NOT affect historical orders/invoices.
- The snapshotted inclusive price is stored; base price is back-calculated at invoice time.

**Validation:**
- GST percentage: positive number; common values (5, 12, 18, 28) recommended but not enforced.
- GST calculation (back-calculation from inclusive): `basePrice = inclusivePrice / (1 + gstPercent/100)`, `gstAmount = inclusivePrice - basePrice` (per unit, then × quantity).
- Server recomputes GST totals authoritatively.

**States:**
- Product with GST: card shows GST indicator.
- Product without GST: no indicator.
- Cart with mixed items: shows both sections in summary.

**Edge cases:**
- All items have GST → no "Non-GST" section in invoice.
- No items have GST → no "GST" section.
- GST rate changed on product after order placed → snapshot protects original.
- 0% GST (applicable but 0%) → valid edge case; shows "GST 0%" (effectively no tax).
- Staff-added product with GST → GST data part of approval; Admin can adjust before approving.

**Permissions:** Admin (set GST on products); Customer (view only); Staff with `add_products` (set GST on submitted products, subject to approval).

**Notifications:** none.

**Errors:** invalid GST percentage → validation error; calculation mismatch → server authoritative.

**Future considerations:** HSN codes; GST filing integration; tax exemption for certain customers; reverse charge mechanism; composite vs regular GST.
