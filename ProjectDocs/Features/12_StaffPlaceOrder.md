# Feature — Shop Sale (Requirement #12, Confirmed Q28)

**Overview:** Staff can create **Shop Sales** — quick billing for walk-in/counter customers without requiring customer registration or account creation. This replaces the original "Staff Place Order for Customer" concept.

**Business purpose:** Capture revenue from walk-in customers or counter sales instantly without the friction of customer registration. Enables the business to serve anyone who walks in, even without the app. Keeps billing simple for in-person transactions.

**Users:** Staff with `place_shop_sale` permission.

**Entry points:** Staff App — S10 (Shop Sale).

**Dependencies:** Products/Categories (for selection), Order entity (with `saleType = shopSale`), Invoice generation.

**Data used:**
- Optional: phone number (just for the bill), customer name (just for the bill).
- Products: selection + quantity.
- Payment method: Gpay or Cash (collected at point of sale).
- Order: `placedBy = 'staff'`, `placedByStaffId`, `saleType = 'shopSale'`.

**Flow:**
```
Staff opens S10 (Shop Sale)
  → Enters phone number (OPTIONAL — just for the bill)
  → Enters customer name (OPTIONAL — just for the bill)
  → Selects products + quantities (from their assigned location's catalog)
  → Selects payment method (Gpay / Cash — actual method since it's an on-the-spot sale)
  → Reviews summary → "Complete Sale"
  → Order/sale created: saleType=shopSale, placedBy=staff, state=Completed immediately
  → Stock decremented for all items
  → Invoice generated (Muciriz branding; phone/name shown if provided)
  → Success screen with invoice download
```

**Key differences from regular orders:**
- **No customer account needed.** Phone/name are optional metadata for the bill — NOT linked to a Customer entity.
- **No delivery date/time.** It's an on-the-spot, in-person sale.
- **Immediately Completed.** No Pending → fulfillment cycle needed.
- **No customer lookup or creation.** No merge logic. Just a sale record.
- **Payment collected at point of sale** (not "on delivery").

**Invoice for Shop Sales:**
- Same Muciriz invoice template (M{YEAR}#{SEQ5} numbering, shared sequence).
- Shows: phone (if provided), customer name (if provided), items, GST breakdown, total, payment method, date/time, staff name.
- No delivery address/date/time slot sections (since it's a counter sale).

**Validation:**
- At least one product selected.
- Products must be in a location the staff is assigned to.
- Payment method required (Gpay or Cash).
- Phone number format valid IF provided (optional field).

**States:**
- Product selection: standard catalog browse for staff's location.
- Sale placement: loading → success (invoice) / error.
- Success: invoice displayed with download option.

**Edge cases:**
- No products available → empty state.
- Staff tries to sell more than stock allows → show stock indicator; enforce stock ≥ 0.
- Network failure during sale creation → retry; sale not created until confirmed.

**Permissions:** Requires `place_shop_sale`. Scoped to staff's assigned locations.

**Notifications:** None (no customer to notify — it's a walk-in).

**Errors:** Sale creation failure → retry. Stock insufficient → inform staff.

**Future considerations:** Receipt printing (thermal printer); batch Shop Sales; daily counter sales summary; linking Shop Sales to customer accounts retroactively if desired.
