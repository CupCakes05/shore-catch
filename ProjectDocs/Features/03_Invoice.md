# Feature — Invoice Generation (GST/Non-GST Split)

**Overview:** On checkout, an invoice is auto-generated and downloadable by the customer (and viewable by Admin). Updated to handle GST/non-GST product split (Requirements #11, #13).

**Business purpose:** Provide a professional receipt/record that properly accounts for tax (GST) where applicable. Builds trust and supports business tax compliance.

**Users:** Customer (download own), Admin (view any).

**Entry points:** Order Confirmation (C08), Order Detail (C10 / A10).

**Dependencies:** Order created; invoice generator service; product GST data snapshotted at purchase time.

**Data used:** Order + OrderItem snapshots (including `gstApplicableSnapshot`, `gstPercentSnapshot`, `gstAmountSnapshot`), customer name + delivery address + landmark (or phone/name for Shop Sales), service location, delivery date + time slot, payment method, subtotal, GST total, order total, **Muciriz** business branding, invoice number (format: M{YEAR}#{SEQ5})/date.

**Invoice template structure (Requirement #13):**
```
┌────────────────────────────────────────────┐
│ INVOICE                    [Muciriz Logo]  │
│ Invoice #: M2026#00123                     │
│ Date: 15 Jan 2026                          │
│────────────────────────────────────────────│
│ Customer: [Name]                           │
│ Address: [Address + Landmark]              │
│ Location: [Service Location]               │
│ Delivery: [Date] — [Time Slot]             │
│ Payment: [Method]                          │
│────────────────────────────────────────────│
│ ITEMS WITH GST:                            │
│ Product | Qty | Base₹ | GST% | GST₹ | Tot  │
│ Fish A  |  2  | ₹200  | 5%   | ₹10  | ₹210│
│ ...                                        │
│────────────────────────────────────────────│
│ ITEMS WITHOUT GST:                         │
│ Product | Qty | Price | Total              │
│ Veggies |  1  | ₹50   | ₹50              │
│ ...                                        │
│────────────────────────────────────────────│
│ Subtotal (before GST): ₹XXX               │
│ GST (5%): ₹XX                             │
│ GST (18%): ₹XX                            │
│ Total GST: ₹XX                            │
│ ─────────────                              │
│ TOTAL: ₹XXXX                              │
│────────────────────────────────────────────│
│ Muciriz | [Contact]                        │
└────────────────────────────────────────────┘
```

> **Note (Q24 — GST Inclusive):** Prices are stored GST-inclusive. The invoice back-calculates: `basePrice = inclusivePrice / (1 + gstPercent/100)`. The "Tot" column shows the original inclusive price.

**GST handling rules:**
- Products WITH `gstApplicable = true`: listed in a "GST Items" section showing base price, GST %, GST amount per line, and line total (incl. GST).
- Products WITHOUT GST: listed in a "Non-GST Items" section showing price and total only.
- Summary shows: subtotal (all items before GST), GST breakdown by rate (e.g., "GST 5%: ₹X", "GST 18%: ₹Y"), total GST, and grand total.
- All GST values are snapshotted from the time of purchase (frozen in OrderItem).

**Validation:** invoice reflects authoritative server totals (including GST calculations).

✅ **Q16 — FULLY CONFIRMED:** GST confirmed. Format is PDF. Numbering: `M{YEAR}#{SEQ5}` (e.g., M2026#00001), resets annually. Muciriz branding.

**States:** invoice generating / ready / download error (retry).

**Edge cases:**
- Invoice not immediately ready → allow re-download later.
- Re-download from order history.
- Order with only GST items → no "Non-GST" section.
- Order with only non-GST items → no "GST" section.
- Mixed order → both sections shown.
- GST rate changed after order placed → snapshot protects original rate.

**Permissions:** customer downloads own; admin any.

**Notifications:** none.

**Errors:** generation/download failure → retry; order remains valid regardless.

**Future considerations:** email/SMS delivery of invoice; branding/logo customization; credit notes for cancellations; integration with accounting/GST filing systems; Shop Sale invoices (simplified format without delivery info).
