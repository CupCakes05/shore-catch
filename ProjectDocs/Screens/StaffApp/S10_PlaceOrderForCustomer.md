# S10 — Shop Sale (Requirement #12, Confirmed Q28)

**App:** Staff (Flutter/Android)

> ⚠️ **Permission-gated screen.** Visible only when the staff member has `place_shop_sale`.

## Purpose
Allow staff to create **Shop Sales** — quick billing for walk-in/counter customers. No customer registration or account creation required. Just enter optional phone/name, select products, collect payment, and generate an invoice.

## Users
Authenticated Staff with `place_shop_sale`.

## Navigation
- **From:** Menu / main navigation entry point (only if permitted).
- **To:** Sale confirmation with invoice → back to Order List.

## Key UI Elements

### Step 1: Customer Info (Optional)
- **Phone number input** field (optional — for the bill only).
- **Customer name** field (optional — for the bill only).
- "Skip" or "Continue" button (both fields are optional).
- Helper text: "Phone and name are optional — just for the invoice."

### Step 2: Product Selection
- Category list (scoped to staff's assigned location).
- Product list per category with quantity add (similar to customer C06 but simplified).
- **Stock indicators** (current stock shown; warn if low).
- Cart summary (items selected + running total with GST breakdown).
- **Prices shown inclusive** of GST (same as customer sees).

### Step 3: Payment & Review
- **Payment method** selector: "Gpay" | "Cash" (actual method — collected at point of sale).
- Order summary: items (with GST breakdown), total.
- Phone/name shown if provided.
- **"Complete Sale"** button.

### Step 4: Confirmation
- "Sale completed!" with sale number (M2026#XXXXX).
- "Download Invoice" button.
- "New Sale" button → reset to Step 1.
- "Back to Orders" → S03.

## States
- **Loading:** placing sale.
- **Empty:** no products available (shouldn't happen if catalog exists).
- **Error:** sale creation failure; network → retry.
- **Success:** sale placed → confirmation with invoice download.

## Actions
- (Optional) Enter phone/name.
- Select products + quantities.
- Choose payment method (Gpay or Cash).
- Complete sale → `POST /orders/shop-sale`.

## Business Rules
- Sale is recorded with `placedBy = staff`, `placedByStaffId`, `saleType = shopSale`.
- **No customer record is created.** Phone/name are optional metadata stored on the sale record.
- Sale is **immediately Completed** (no Pending → fulfillment cycle).
- **Stock is decremented** for all items at the staff's location.
- **Invoice is generated** with Muciriz branding, using the same numbering sequence (M{YEAR}#{SEQ5}).
- Payment method records the actual collection method (it's on-the-spot).
- Shop Sales count toward **Total Sales** in profit/loss reporting.
- Products shown are scoped to the staff's assigned location.
- Staff can only create Shop Sales for locations they are assigned to.
- No delivery date/time needed (counter sale).

## Validation / Permissions
- Requires `place_shop_sale` permission.
- At least one product selected.
- Payment method required.
- Phone format valid if provided (not required).

## Responsive / Accessibility
- Simple flow; clear labels; phone input with proper keyboard; accessible product selection; large "Complete Sale" button.

## Dependencies
- `/categories`, `/products`, `/orders/shop-sale`, `/stock`.
