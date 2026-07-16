# Muciriz — Data Flow

> For key features, documents the path of data from user action to feedback. Format:
> `User Action → Frontend Validation → API → Backend → Business Logic → Database → (Cache) → Response → Frontend Update → User Feedback`

## 1. Customer Registration

```
Customer fills form (Name, WhatsApp #, Address+Landmark, Service Location)
  → Frontend validation (required fields, WhatsApp # format, location selected)
  → POST /auth/register
  → Backend validates + enforces WhatsApp # UNIQUENESS
  → Sends OTP via WhatsApp Business API (primary) / AWS SNS (fallback) → OTP Verification (C04)
  → POST /auth/verify-otp → backend verifies code, marks number verified
  → Creates/activates Customer record (scoped to chosen Service Location)
  → Issues session token (cached for auto-login)
  → Response: customer profile + token
  → App stores token, navigates to Category page (location-filtered)
  → Feedback: welcome toast / lands on catalog
```
Origin: user input. Stored: Customers table (isVerified=true). Consumed by: Category page, Profile, Admin customer lookup.

## 1a. Returning Number Re-entry (skip OTP — Q3)

```
Logged-out user enters mobile number → POST /auth/login/customer
  → Backend checks: number exists AND isVerified?
  → YES → issue session immediately (NO OTP) → Category page
  → NO  → send OTP via WhatsApp Business API / AWS SNS → OTP Verification (C04) → verify → session
```

## 2. Returning Customer Auto-Login

```
App launch → checks stored token/session locally
  → GET /auth/me (validate token)
  → Backend confirms customer + returns profile (incl. Service Location)
  → Response: valid → skip auth screens
  → Frontend: navigate straight to Category page
  → Feedback: no login friction; if token invalid → route to login/register
```

## 3. Browse Catalog (Category → Products)

```
Category page loads → GET /categories?location={id}
  → Backend returns categories for that Service Location (with images)
  → Rendered as left/right cards
Customer taps a category → GET /products?category={id}
  → Backend returns products (name, gross wt, net wt, price, image)
  → Rendered as downward list of center cards (flip reveals image); quantity add button per card
  → Category scroll strip (slow-scrolling) rendered from same categories list; back arrow returns to Category page
```
Origin: Admin-managed catalog. Consumed by: Customer catalog screens.

## 4. Add to Cart & Checkout

```
Customer taps quantity add on a product card → cart updated locally (and/or server cart)
  - If product out of stock: "Pre-order" label shown; adds to cart as pre-order item
Customer opens Cart → reviews, adds/removes items
  → Frontend validation: cart not empty
  → Cart shows GST breakdown (subtotal, GST by rate, total)
Cart/Checkout also loads Recommended Products:
  → GET /cart/recommendations
  → Backend resolves product-level + category-level recommendations for cart contents,
     excludes items already in cart + inactive/unapproved/out-of-location products
  → "Recommended for you" section with quick-add → tapping adds to cart
Customer selects Delivery Date (today or future — pre-order support):
  → If today's slots exhausted or items out of stock → default to next available date
  → GET /delivery-times?location={id}&date={selectedDate}
Customer selects Delivery Time Slot (from available slots for selected date)
Customer selects Payment method (Gpay on Delivery | Cash on Delivery)
  → If Gpay selected: display company UPI ID (from GET /config) + "Pay via GPay" deep-link button
  → Frontend validation: delivery date + time slot + payment method chosen
  → POST /orders { items[], deliveryDate, deliveryTimeId, paymentMethod, ... }
  → Backend validates items belong to customer's location, recomputes totals (including GST, authoritative)
  → Creates Order (state = Pending, isPreOrder if future date) with item snapshots (incl. GST)
  → **Decrements stock** for all items at the customer's location (Q26)
  → Records state transition timestamps (createdAt = now)
  → Triggers invoice generation (with GST/non-GST split)
  → Response: order summary + invoice URL
  → Frontend: navigate to Order Confirmation; offer invoice download
  → Feedback: success screen + toast; cart cleared
```
Note: server recomputes totals (including GST) from authoritative prices — never trust client-side totals.

## 5. Invoice Generation & Download

```
On order creation → Invoice service builds invoice from order snapshot
  → Stored/derivable; invoice URL returned
Customer taps Download (confirmation screen or order history)
  → GET /orders/{id}/invoice
  → Response: PDF stream/URL
  → Frontend: downloads/opens invoice
```

## 6. Staff Fulfillment Lifecycle

```
Staff logs in (tied to Service Location(s)) → GET /orders?location={assigned}&categories={allowed}
  → Backend enforces staff scope + permissions (across multiple assigned locations)
  → Returns orders for those locations (filtered); pre-orders shown with delivery date
Staff opens order detail → GET /orders/{id}
Staff taps "Order Confirm" (perm: confirm_order)
  → PATCH /orders/{id}/state { state: OrderConfirmed }
  → Backend records orderConfirmedAt = now, orderConfirmedBy = staffId
Staff taps "Delivery Confirm" (perm: confirm_delivery)
  → PATCH /orders/{id}/state { state: Delivered }
  → Backend records deliveredAt = now, deliveredBy = staffId
Staff taps "Payment Confirm" (perm: confirm_payment)
  → PATCH /orders/{id}/payment { collectedMethod: Gpay|Cash }  → state: Completed
  → Backend records paymentConfirmedAt = now, paymentConfirmedBy = staffId
  → Response: updated order
  → Frontend: status chips update; feedback toast; timestamps shown in timeline
```
Each transition is validated server-side (correct order, permission present, order within staff scope). Timestamps enable performance reporting.

## 7. Admin Catalog Management (multi-location)

```
Admin adds Service Location → POST /locations → now selectable in customer registration + everywhere scoped
Admin adds Category (with image) → POST /categories {name, image, locationIds[]} → assigned to multiple locations
Admin adds Product (with image) → POST /products {categoryId, name, grossWt, netWt, price, gstApplicable, gstPercent, image, locationIds[]} → auto-Approved; assigned to multiple locations
Admin sets initial stock per location → PATCH /stock/{productId}/{locationId} {count}
Admin records purchase data → POST /purchases {productId, locationId, purchaseRate, qty, supplier}
Admin sets recommendations → PUT /products/{id}/recommendations, PUT /categories/{id}/recommendations
Admin adds Delivery Time → POST /delivery-times (global if no locationId, else per-location)
Admin configures system settings → PUT /config/gpay_upi_id, PUT /config/customer_care_phone, etc.
  → All changes reflected on next customer/staff fetch (cache-invalidate or short TTL)
```

## 7a. Staff Product Add/Edit + Approval (Q11)

```
Staff (add_products) submits product add/edit → POST/PATCH /products → approvalStatus = PendingApproval
  → NOT visible to customers
Admin opens approval queue → GET /products?approvalStatus=PendingApproval&location={id}
Admin approves/rejects → PATCH /products/{id}/approval { Approved | Rejected }
  → Approved + isActive → visible to customers; Rejected → stays hidden; staff notified
```

## 8. Offers / Notifications

```
Admin creates Offer (targetType All/Category/Product, targetId, discountPercent, validity, global/per-location) → POST /offers
Customer App on open / refresh → GET /offers?location={id}&active=true
  → Backend returns active offers within validity window (per-location overrides global — Q9)
  → Frontend renders banner at top of Customer App
  → Discount application to cart totals vs display-only is a pending sub-decision (linked to Q1)
```

## 8a. Notifications Fan-out (Q18)

```
Admin sends notification → POST /notifications { channels: in-app, push, SMS }
  → Backend writes in-app inbox record (always)
  → Dispatches push (provider) + SMS (AWS SNS) as configured
Customer App → GET /notifications (inbox), PATCH /notifications/{id}/read
```

## 9. Staff Management & Permissions

```
Admin creates Staff → POST /staff { name, credentials, locationIds[], permissions{} }
  → Backend stores staff + permission flags + location assignments (many-to-many)
Staff login → permissions + locations loaded → UI gates actions; backend enforces on each request
Admin edits permissions/locations → PATCH /staff/{id} → effective on staff's next request/session refresh
```

## 10. Reporting

```
Orders dashboard → GET /reports/orders?location={id} → delivered vs pending grouped
Total Sales → GET /reports/sales?location={id} → sum of qualifying orders (recommend: Completed only)
Customer history → GET /reports/customers?query={name|whatsapp} → matching customers + their orders
```

## 11. Data Lifecycle Notes

- **Order items are snapshotted** at purchase time (name, weight, price, GST, qty) so later catalog edits/deletes don't alter historical orders or sales.
- **Soft-delete (Q6)** for locations/categories/products referenced by historical orders (preserve reporting).
- **Sales count Completed orders only (Q7);** order lists sorted oldest/pending first (Q22).
- **Stock counts** decremented on order placement (Q26); restored on cancellation. Replenished via stock updates (staff/admin).
- **State transition timestamps** (Requirement #2) are immutable once set; they reflect the exact moment staff performed each action.
- **GST snapshots** protect historical invoice accuracy when product GST rates change.

## 12. Staff Misc Purchase Recording (Requirement #8)

```
Staff opens S09 → fills form (item name, qty, cost, optional receipt image)
  → POST /misc-purchases
  → Backend validates, stores record (staffId, locationId, details)
  → Response: success
  → Frontend: toast "Purchase recorded"; entry appears in history
Admin views in A11 Misc Purchases tab → GET /reports/misc-purchases?location={id}&dateRange
```

## 13. Staff Stock Update (Requirement #9)

```
Staff opens S05 (stock section) → selects location → sees product list with current stock
  → Taps product → enters new stock count
  → PATCH /stock/{productId}/{locationId} { count: newValue }
  → Backend validates (count ≥ 0), creates StockUpdateLog, updates ProductLocationStock
  → Response: updated stock
  → Frontend: count updated; "Stock updated" toast
  → If stock went to 0 → product shows "Out of Stock" to customers at that location
```

## 14. Staff Shop Sale (Requirement #12, updated Q28)

```
Staff opens S10 (Shop Sale) → enters phone number (optional) → enters customer name (optional)
  → Staff selects products from their assigned location's catalog
  → Staff selects payment method (Gpay / Cash)
  → POST /orders/shop-sale { phone?, customerName?, items[], paymentMethod, locationId }
  → Backend creates order (placedBy=staff, placedByStaffId, saleType=shopSale, state=Completed immediately)
  → Decrements stock for all items
  → Triggers invoice generation (Muciriz branding, phone/name if provided)
  → Response: invoice URL + sale confirmation
  → Frontend: success screen with invoice download
  → Sale counts toward Total Sales in profit/loss
```
Note: No customer record is created. No delivery date/time needed (it's an on-the-spot sale). The sale is immediately Completed.

## 15. Admin Edit Customer (Requirement #7)

```
Admin opens A12 → searches customer by name/phone → selects customer
  → Clicks "Edit" → edits name/address/landmark/location
  → PATCH /customers/{id} { name, deliveryAddress, nearestLandmark, locationId }
  → Backend validates, logs audit (who changed what when)
  → Response: updated customer
  → Frontend: confirmation toast
  → If location changed: customer's cart will be re-validated on next use
```

## 16. Admin Inventory / Purchase Recording (Requirement #10)

```
Admin opens A15 → Purchase Records tab → "Record Purchase"
  → Fills: product, location, purchase rate, qty, weights, supplier, date
  → POST /purchases
  → Backend stores PurchaseRecord
  → Response: success
  → Profit/loss calculations now include this cost data
Admin views Profit/Loss → GET /reports/profit-loss?location={id}&dateRange
  → Backend computes: Total Sale (sum of Completed order totals) − Total Expense (sum of PurchaseRecords + sum of MiscPurchases)
  → Response: aggregate profit/loss breakdown
```
