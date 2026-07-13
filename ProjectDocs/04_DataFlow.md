# Eze-Cart — Data Flow

> For key features, documents the path of data from user action to feedback. Format:
> `User Action → Frontend Validation → API → Backend → Business Logic → Database → (Cache) → Response → Frontend Update → User Feedback`

## 1. Customer Registration

```
Customer fills form (Name, WhatsApp #, Address+Landmark, Service Location)
  → Frontend validation (required fields, WhatsApp # format, location selected)
  → POST /auth/register
  → Backend validates + enforces WhatsApp # UNIQUENESS
  → Sends OTP via AWS SNS → OTP Verification (C04)
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
  → NO  → send OTP via AWS SNS → OTP Verification (C04) → verify → session
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
Customer opens Cart → reviews, adds/removes items
  → Frontend validation: cart not empty
Customer selects Delivery Time (dropdown ← GET /delivery-times?location={id})
Cart/Checkout also loads Recommended Products:
  → GET /cart/recommendations
  → Backend resolves product-level + category-level recommendations for cart contents,
     excludes items already in cart + inactive/unapproved/out-of-location products
  → "Recommended for you" section with quick-add → tapping adds to cart
Customer selects Payment method (Gpay on Delivery | Cash on Delivery)
  → Frontend validation: delivery time + payment method chosen
  → POST /orders  { items[], deliveryTimeId, paymentMethod, ... }
  → Backend validates items belong to customer's location, recomputes totals (authoritative)
  → Creates Order (state = Pending) with item snapshots
  → Triggers invoice generation
  → Response: order summary + invoice URL
  → Frontend: navigate to Order Confirmation; offer invoice download
  → Feedback: success screen + toast; cart cleared
```
Note: server recomputes totals from authoritative prices — never trust client-side totals.

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
Staff logs in (tied to Service Location) → GET /orders?location={assigned}&categories={allowed}
  → Backend enforces staff scope + permissions
  → Returns orders for that location (filtered)
Staff opens order detail → GET /orders/{id}
Staff taps "Order Confirm" (perm: confirm_order)
  → PATCH /orders/{id}/state { state: OrderConfirmed }
Staff taps "Delivery Confirm" (perm: confirm_delivery)
  → PATCH /orders/{id}/state { state: Delivered }
Staff taps "Payment Confirm" (perm: confirm_payment)
  → PATCH /orders/{id}/payment { collectedMethod: Gpay|Cash }  → state: Completed
  → Response: updated order
  → Frontend: status chips update; feedback toast
```
Each transition is validated server-side (correct order, permission present, order within staff scope).

## 7. Admin Catalog Management (cascade by location)

```
Admin adds Service Location → POST /locations → now selectable in customer registration + everywhere scoped
Admin adds Category (with image) → POST /categories {locationId, image} → appears in that location's catalog
Admin adds Product (with image) → POST /products {categoryId, name, grossWt, netWt, price, image} → auto-Approved
Admin sets recommendations → PUT /products/{id}/recommendations, PUT /categories/{id}/recommendations
Admin adds Delivery Time → POST /delivery-times (global if no locationId, else per-location)
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
Admin creates Staff → POST /staff { name, credentials, locationId, permissions{} }
  → Backend stores staff + permission flags
Staff login → permissions loaded → UI gates actions; backend enforces on each request
Admin edits permissions → PATCH /staff/{id} → effective on staff's next request/session refresh
```

## 10. Reporting

```
Orders dashboard → GET /reports/orders?location={id} → delivered vs pending grouped
Total Sales → GET /reports/sales?location={id} → sum of qualifying orders (recommend: Completed only)
Customer history → GET /reports/customers?query={name|whatsapp} → matching customers + their orders
```

## 11. Data Lifecycle Notes

- **Order items are snapshotted** at purchase time (name, weight, price, qty) so later catalog edits/deletes don't alter historical orders or sales.
- **Soft-delete (Q6)** for locations/categories/products referenced by historical orders (preserve reporting).
- **Sales count Completed orders only (Q7);** order lists sorted oldest/pending first (Q22).
- Customer profile edits update the Customers record; historical orders keep the delivery snapshot used at order time (recommended).
