# Feature — Invoice Generation

**Overview:** On checkout, an invoice is auto-generated and downloadable by the customer (and viewable by Admin).

**Business purpose:** Provide a record/receipt; professionalism and trust for the trial.

**Users:** Customer (download own), Admin (view any).

**Entry points:** Order Confirmation (C08), Order Detail (C10 / A10).

**Dependencies:** Order created; invoice generator service.

**Data used:** Order + OrderItem snapshots, customer name + delivery address + landmark, service location, delivery time, payment method, totals, business branding, invoice number/date.

**Validation:** invoice reflects authoritative server totals.

⏳ **Pending (Q16 — client input):** invoice **format** (PDF assumed), **numbering scheme**, and whether a **tax/GST field** is needed. Still awaiting client answer.

**States:** invoice generating / ready / download error (retry).

**Edge cases:**
- Invoice not immediately ready → allow re-download later.
- Re-download from order history.

**Permissions:** customer downloads own; admin any.

**Notifications:** none.

**Errors:** generation/download failure → retry; order remains valid regardless.

**Future considerations:** PDF format + numbering scheme confirmation (Q16 pending); tax/GST fields (Q16 pending); email/SMS delivery of invoice; branding/logo customization.
