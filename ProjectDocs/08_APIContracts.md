# Muciriz — Full API Contracts

> Complete request/response contracts for every endpoint. Shared backend serves all 3 apps (Admin, Staff, Customer).
> Base URL: `https://api.muciriz.in/v1` (example). All endpoints require HTTPS.
> Auth: Bearer token in `Authorization` header (except where noted as public).
> Content-Type: `application/json` (except file uploads: `multipart/form-data`).

---

## Conventions

- **Pagination:** `?page=1&limit=20` on list endpoints. Response includes `meta: { page, limit, total, totalPages }`.
- **Sorting:** `?sort=field&order=asc|desc` where applicable.
- **Filtering:** query params per endpoint.
- **Dates:** ISO 8601 (`2026-07-19T10:30:00Z`). Date-only fields: `YYYY-MM-DD`.
- **Currency:** all monetary values are in ₹ (Indian Rupees), stored as numbers (2 decimal places).
- **IDs:** UUID strings (e.g., `"a1b2c3d4-e5f6-..."`) or auto-increment integers — implementation choice.
- **Soft-delete:** `DELETE` endpoints set `isActive=false`; no hard deletes.
- **Error envelope:** `{ "error": { "code": "VALIDATION_ERROR", "message": "...", "fields": [...] } }`

### Standard Error Codes
| HTTP | Code | When |
|------|------|------|
| 400 | `VALIDATION_ERROR` | Invalid input |
| 401 | `UNAUTHENTICATED` | Missing/invalid token |
| 403 | `FORBIDDEN` | Insufficient permissions/scope |
| 404 | `NOT_FOUND` | Resource not found |
| 409 | `CONFLICT` | Duplicate entry / invalid state transition |
| 500 | `SERVER_ERROR` | Internal failure |

---

## 1. Authentication & Session

### 1.1 POST /auth/register
**Consumer:** Customer App (C03)
**Auth:** None (public)

**Request:**
```json
{
  "name": "Arun Kumar",
  "whatsappNumber": "+919876543210",
  "deliveryAddress": "House No 12, MG Road",
  "nearestLandmark": "Near SBI Bank",
  "locationId": "loc_001"
}
```

**Response (201):**
```json
{
  "message": "OTP sent to WhatsApp",
  "otpToken": "temp_token_for_verify",
  "channel": "whatsapp",
  "expiresIn": 300
}
```

**Errors:**
- 409 `DUPLICATE_NUMBER` — number already verified → triggers auto-login (no OTP)
- 400 — validation errors (missing fields, invalid phone format)

---

### 1.2 POST /auth/send-otp
**Consumer:** Customer App (C03, C04)
**Auth:** None

**Request:**
```json
{
  "whatsappNumber": "+919876543210",
  "purpose": "registration" | "reverification"
}
```

**Response (200):**
```json
{
  "message": "OTP sent",
  "channel": "whatsapp" | "sms",
  "expiresIn": 300,
  "otpToken": "temp_token"
}
```

**Notes:** Primary delivery via WhatsApp Business API; falls back to SMS (AWS SNS) if WhatsApp fails.

---

### 1.3 POST /auth/verify-otp
**Consumer:** Customer App (C04)
**Auth:** None (uses otpToken)

**Request:**
```json
{
  "otpToken": "temp_token_for_verify",
  "otp": "123456"
}
```

**Response (200):**
```json
{
  "token": "jwt_session_token",
  "user": {
    "id": "cust_001",
    "name": "Arun Kumar",
    "whatsappNumber": "+919876543210",
    "deliveryAddress": "House No 12, MG Road",
    "nearestLandmark": "Near SBI Bank",
    "locationId": "loc_001",
    "locationName": "Aluva",
    "isVerified": true
  }
}
```

**Errors:**
- 400 `INVALID_OTP` — wrong code
- 400 `OTP_EXPIRED` — expired
- 429 `TOO_MANY_ATTEMPTS` — lockout

---

### 1.4 POST /auth/login/customer
**Consumer:** Customer App (C03 — returning user)
**Auth:** None

**Request:**
```json
{
  "whatsappNumber": "+919876543210"
}
```

**Response (200) — already verified:**
```json
{
  "token": "jwt_session_token",
  "user": { ... },
  "otpRequired": false
}
```

**Response (200) — not verified yet:**
```json
{
  "otpRequired": true,
  "otpToken": "temp_token",
  "channel": "whatsapp",
  "expiresIn": 300
}
```

---

### 1.5 POST /auth/login/staff
**Consumer:** Staff App (S02)
**Auth:** None

**Request:**
```json
{
  "phone": "+919876543210",
  "password": "SecurePass123"
}
```

**Response (200):**
```json
{
  "token": "jwt_session_token",
  "staff": {
    "id": "staff_001",
    "name": "Rahul K",
    "phone": "+919876543210",
    "locations": [
      { "id": "loc_001", "name": "Aluva" },
      { "id": "loc_002", "name": "Perumbavoor" }
    ],
    "permissions": {
      "viewOrders": true,
      "restrictToCategories": false,
      "addProducts": true,
      "confirmOrder": true,
      "confirmDelivery": true,
      "confirmPayment": true,
      "updateStock": true,
      "recordMiscPurchase": false,
      "placeShopSale": true
    },
    "allowedCategoryIds": []
  }
}
```

**Errors:**
- 401 `INVALID_CREDENTIALS`
- 403 `ACCOUNT_DEACTIVATED`

---

### 1.6 POST /auth/login/admin
**Consumer:** Admin Panel (A01)
**Auth:** None

**Request:**
```json
{
  "email": "admin@muciriz.in",
  "password": "AdminPass123"
}
```

**Response (200):**
```json
{
  "token": "jwt_session_token",
  "admin": {
    "id": "admin_001",
    "name": "Main Admin",
    "email": "admin@muciriz.in",
    "role": "MainAdmin"
  }
}
```

---

### 1.7 POST /auth/admin/forgot-password
**Consumer:** Admin Panel (A01)
**Auth:** None

**Request:**
```json
{
  "email": "admin@muciriz.in"
}
```

**Response (200):**
```json
{
  "message": "Password reset link sent to email"
}
```

---

### 1.8 GET /auth/me
**Consumer:** All apps (session validation / auto-login)
**Auth:** Bearer token

**Response (200):**
```json
{
  "role": "customer" | "staff" | "admin",
  "user": { ... }
}
```
Returns the full user object based on role (same shape as login responses).

**Errors:**
- 401 — invalid/expired token

---

### 1.9 POST /auth/logout
**Consumer:** All apps
**Auth:** Bearer token

**Response (200):**
```json
{
  "message": "Logged out successfully"
}
```

---

## 2. Service Locations

### 2.1 GET /locations
**Consumer:** All apps (Customer registration dropdown, Admin management, Staff context)
**Auth:** None (public for registration); Bearer for admin features

**Query params:** `?activeOnly=true` (default true for customer/staff; admin sees all)

**Response (200):**
```json
{
  "data": [
    {
      "id": "loc_001",
      "name": "Aluva",
      "isActive": true,
      "categoriesCount": 4,
      "productsCount": 25,
      "customersCount": 150,
      "ordersCount": 430,
      "createdAt": "2026-01-15T10:00:00Z"
    }
  ],
  "meta": { "total": 4 }
}
```

**Notes:** Customer/Staff apps only need `id` + `name`. Admin gets full counts.

---

### 2.2 POST /locations
**Consumer:** Admin Panel (A03)
**Auth:** Admin

**Request:**
```json
{
  "name": "Angamaly"
}
```

**Response (201):**
```json
{
  "id": "loc_005",
  "name": "Angamaly",
  "isActive": true,
  "createdAt": "2026-07-19T10:00:00Z"
}
```

**Errors:**
- 409 `DUPLICATE_NAME` — location name already exists

---

### 2.3 PATCH /locations/{id}
**Consumer:** Admin Panel (A03)
**Auth:** Admin

**Request:**
```json
{
  "name": "Angamaly Town",
  "isActive": false
}
```

**Response (200):** Updated location object.

---

### 2.4 DELETE /locations/{id}
**Consumer:** Admin Panel (A03)
**Auth:** Admin

**Response (200):**
```json
{
  "message": "Location deactivated",
  "id": "loc_005",
  "isActive": false
}
```

**Notes:** Soft-delete only. Sets `isActive=false`. Preserves all historical data.

---

## 3. Categories

### 3.1 GET /categories
**Consumer:** All apps
**Auth:** Bearer token

**Query params:**
- `?location={locationId}` — filter by location (required for Customer/Staff)
- `?activeOnly=true` — default true for Customer/Staff
- `?page=1&limit=50`

**Response (200):**
```json
{
  "data": [
    {
      "id": "cat_001",
      "name": "Fresh Fish",
      "imageUrl": "https://cdn.muciriz.in/categories/fish.jpg",
      "isActive": true,
      "sortOrder": 1,
      "locations": [
        { "id": "loc_001", "name": "Aluva" },
        { "id": "loc_002", "name": "Perumbavoor" }
      ],
      "productsCount": 12
    }
  ],
  "meta": { "page": 1, "limit": 50, "total": 4, "totalPages": 1 }
}
```

---

### 3.2 POST /categories
**Consumer:** Admin Panel (A04)
**Auth:** Admin

**Request:** `multipart/form-data`
```
name: "Prawns & Crabs"
image: <file>
locationIds: ["loc_001", "loc_002", "loc_003"]
sortOrder: 5
```

**Response (201):**
```json
{
  "id": "cat_005",
  "name": "Prawns & Crabs",
  "imageUrl": "https://cdn.muciriz.in/categories/prawns.jpg",
  "isActive": true,
  "sortOrder": 5,
  "locations": [...],
  "productsCount": 0
}
```

**Errors:**
- 400 — name required, at least one locationId required

---

### 3.3 PATCH /categories/{id}
**Consumer:** Admin Panel (A04)
**Auth:** Admin

**Request:** `multipart/form-data` (all fields optional)
```
name: "Prawns & Shellfish"
image: <file> (optional, replaces existing)
locationIds: ["loc_001", "loc_002"]
sortOrder: 3
isActive: true
```

**Response (200):** Updated category object.

---

### 3.4 DELETE /categories/{id}
**Consumer:** Admin Panel (A04)
**Auth:** Admin

**Response (200):** `{ "message": "Category deactivated", "isActive": false }`

---

### 3.5 GET /categories/{id}/recommendations
**Consumer:** Admin Panel (A04)
**Auth:** Admin

**Response (200):**
```json
{
  "categoryId": "cat_001",
  "recommendations": [
    {
      "id": "rec_001",
      "productId": "prod_010",
      "productName": "Lemon 250g",
      "productImage": "https://cdn.muciriz.in/products/lemon.jpg",
      "sortOrder": 1
    }
  ]
}
```

---

### 3.6 PUT /categories/{id}/recommendations
**Consumer:** Admin Panel (A04)
**Auth:** Admin

**Request:**
```json
{
  "recommendations": [
    { "productId": "prod_010", "sortOrder": 1 },
    { "productId": "prod_011", "sortOrder": 2 }
  ]
}
```

**Response (200):** Updated recommendations list.

---

## 4. Products

### 4.1 GET /products
**Consumer:** All apps
**Auth:** Bearer token

**Query params:**
- `?category={categoryId}` — filter by category
- `?location={locationId}` — filter by location (required for Customer)
- `?approvalStatus=PendingApproval|Approved|Rejected` — Admin approval queue
- `?search={text}` — name search
- `?activeOnly=true` — default true for Customer
- `?page=1&limit=20`

**Response (200):**
```json
{
  "data": [
    {
      "id": "prod_001",
      "categoryId": "cat_001",
      "categoryName": "Fresh Fish",
      "name": "Karimeen (Pearl Spot) 500g",
      "grossWeight": 550,
      "netWeight": 500,
      "weightUnit": "g",
      "price": 320.00,
      "gstApplicable": true,
      "gstPercent": 5,
      "imageUrl": "https://cdn.muciriz.in/products/karimeen.jpg",
      "isActive": true,
      "approvalStatus": "Approved",
      "submittedBy": null,
      "createdBy": "admin_001",
      "locations": [
        { "id": "loc_001", "name": "Aluva" },
        { "id": "loc_002", "name": "Perumbavoor" }
      ],
      "stock": {
        "loc_001": { "count": 25, "status": "inStock" },
        "loc_002": { "count": 0, "status": "outOfStock" }
      }
    }
  ],
  "meta": { "page": 1, "limit": 20, "total": 12, "totalPages": 1 }
}
```

**Notes:**
- Customer sees only `Approved` + `isActive=true` + assigned to their location.
- `stock` object is location-keyed; Customer only sees stock for their location.
- `stock.status`: `"inStock"` | `"lowStock"` (< 5) | `"outOfStock"` (0)

---

### 4.2 POST /products
**Consumer:** Admin Panel (A05), Staff App (S05 — if `addProducts`)
**Auth:** Admin or Staff(addProducts)

**Request:** `multipart/form-data`
```
name: "Seer Fish 1kg"
categoryId: "cat_001"
grossWeight: 1100
netWeight: 1000
weightUnit: "g"
price: 850.00
gstApplicable: true
gstPercent: 5
image: <file>
locationIds: ["loc_001", "loc_002"]
```

**Response (201):**
```json
{
  "id": "prod_015",
  "name": "Seer Fish 1kg",
  "approvalStatus": "Approved",
  ...
}
```

**Notes:**
- Admin creates → `approvalStatus = "Approved"` (auto)
- Staff creates → `approvalStatus = "PendingApproval"`, `submittedBy = staffId`

---

### 4.3 PATCH /products/{id}
**Consumer:** Admin Panel (A05), Staff App (S05)
**Auth:** Admin or Staff(addProducts, own products only)

**Request:** `multipart/form-data` (any fields)
```
name: "Seer Fish 1kg Premium"
price: 900.00
gstPercent: 5
locationIds: ["loc_001", "loc_002", "loc_003"]
image: <file>
```

**Response (200):** Updated product.

**Notes:** Staff edits re-enter `PendingApproval`.

---

### 4.4 PATCH /products/{id}/approval
**Consumer:** Admin Panel (A05)
**Auth:** Admin only

**Request:**
```json
{
  "approvalStatus": "Approved" | "Rejected",
  "rejectionReason": "Image quality too low"
}
```

**Response (200):**
```json
{
  "id": "prod_015",
  "approvalStatus": "Approved",
  "approvedBy": "admin_001",
  "approvedAt": "2026-07-19T12:00:00Z"
}
```

---

### 4.5 DELETE /products/{id}
**Consumer:** Admin Panel (A05)
**Auth:** Admin

**Response (200):** `{ "message": "Product deactivated", "isActive": false }`

---

### 4.6 GET /products/{id}/recommendations
**Consumer:** Admin Panel (A05)
**Auth:** Admin

**Response (200):**
```json
{
  "productId": "prod_001",
  "recommendations": [
    {
      "id": "rec_010",
      "productId": "prod_005",
      "productName": "Lemon 250g",
      "productImage": "...",
      "sortOrder": 1
    }
  ]
}
```

---

### 4.7 PUT /products/{id}/recommendations
**Consumer:** Admin Panel (A05)
**Auth:** Admin

**Request:**
```json
{
  "recommendations": [
    { "productId": "prod_005", "sortOrder": 1 },
    { "productId": "prod_008", "sortOrder": 2 }
  ]
}
```

**Response (200):** Updated list.

---

### 4.8 GET /cart/recommendations
**Consumer:** Customer App (C07)
**Auth:** Customer

**Query params:** `?cartProductIds=prod_001,prod_003` (or read from server cart)

**Response (200):**
```json
{
  "recommendations": [
    {
      "id": "prod_010",
      "name": "Lemon 250g",
      "price": 25.00,
      "gstApplicable": false,
      "imageUrl": "...",
      "weightUnit": "g",
      "grossWeight": 250,
      "stock": { "count": 50, "status": "inStock" },
      "source": "category"
    }
  ]
}
```

**Notes:** Resolves product-level + category-level recommendations; excludes cart items, inactive/unapproved, out-of-location products.

---

## 5. Stock & Inventory

### 5.1 GET /stock
**Consumer:** Admin Panel (A15), Staff App (S05)
**Auth:** Admin or Staff(updateStock)

**Query params:**
- `?location={locationId}` — required
- `?category={categoryId}` — optional filter
- `?status=inStock|lowStock|outOfStock` — optional filter
- `?search={productName}` — optional
- `?page=1&limit=50`

**Response (200):**
```json
{
  "data": [
    {
      "productId": "prod_001",
      "productName": "Karimeen (Pearl Spot) 500g",
      "categoryName": "Fresh Fish",
      "locationId": "loc_001",
      "locationName": "Aluva",
      "stockCount": 25,
      "status": "inStock",
      "lastUpdatedBy": { "id": "staff_001", "name": "Rahul K" },
      "lastUpdatedAt": "2026-07-19T08:00:00Z"
    }
  ],
  "meta": { "page": 1, "limit": 50, "total": 25, "totalPages": 1 }
}
```

---

### 5.2 PATCH /stock/{productId}/{locationId}
**Consumer:** Admin Panel (A15), Staff App (S05)
**Auth:** Admin or Staff(updateStock, assigned location only)

**Request:**
```json
{
  "count": 30,
  "reason": "Morning delivery received"
}
```

**Response (200):**
```json
{
  "productId": "prod_001",
  "locationId": "loc_001",
  "previousCount": 25,
  "newCount": 30,
  "updatedBy": { "id": "staff_001", "name": "Rahul K" },
  "updatedAt": "2026-07-19T09:00:00Z"
}
```

**Errors:**
- 400 — count < 0
- 403 — staff not assigned to this location

---

### 5.3 POST /purchases
**Consumer:** Admin Panel (A15)
**Auth:** Admin

**Request:**
```json
{
  "productId": "prod_001",
  "locationId": "loc_001",
  "purchaseRate": 200.00,
  "grossWeight": 10000,
  "netWeight": 9500,
  "quantityPurchased": 50,
  "supplierName": "Kerala Fish Market",
  "purchaseDate": "2026-07-18"
}
```

**Response (201):**
```json
{
  "id": "pur_001",
  "productId": "prod_001",
  "locationId": "loc_001",
  "purchaseRate": 200.00,
  "grossWeight": 10000,
  "netWeight": 9500,
  "quantityPurchased": 50,
  "supplierName": "Kerala Fish Market",
  "purchaseDate": "2026-07-18",
  "recordedBy": "admin_001",
  "createdAt": "2026-07-19T09:30:00Z"
}
```

---

### 5.4 GET /purchases
**Consumer:** Admin Panel (A15)
**Auth:** Admin

**Query params:**
- `?product={productId}` — optional
- `?location={locationId}` — optional
- `?startDate=2026-07-01&endDate=2026-07-31`
- `?page=1&limit=20`

**Response (200):**
```json
{
  "data": [
    {
      "id": "pur_001",
      "productId": "prod_001",
      "productName": "Karimeen (Pearl Spot) 500g",
      "locationId": "loc_001",
      "locationName": "Aluva",
      "purchaseRate": 200.00,
      "quantityPurchased": 50,
      "supplierName": "Kerala Fish Market",
      "purchaseDate": "2026-07-18",
      "recordedBy": "admin_001",
      "createdAt": "2026-07-19T09:30:00Z"
    }
  ],
  "meta": { ... }
}
```

---

## 6. Delivery Times

### 6.1 GET /delivery-times
**Consumer:** Customer App (C07), Admin Panel (A06)
**Auth:** Bearer token

**Query params:**
- `?location={locationId}` — resolved: per-location if exists, else global
- `?date=2026-07-20` — optional, for checking slot availability on a specific date
- `?activeOnly=true`

**Response (200):**
```json
{
  "data": [
    {
      "id": "dt_001",
      "label": "Morning 7-9am",
      "locationId": null,
      "scope": "global",
      "isActive": true
    },
    {
      "id": "dt_002",
      "label": "Morning 9-11am",
      "locationId": "loc_001",
      "scope": "perLocation",
      "locationName": "Aluva",
      "isActive": true
    },
    {
      "id": "dt_003",
      "label": "Evening 4-6pm",
      "locationId": null,
      "scope": "global",
      "isActive": true
    }
  ],
  "meta": { "total": 3 }
}
```

**Notes:** For Customer, backend resolves: if location has per-location times, return those only; otherwise return global. Admin sees all (both global and per-location).

---

### 6.2 POST /delivery-times
**Consumer:** Admin Panel (A06)
**Auth:** Admin

**Request:**
```json
{
  "label": "Afternoon 1-3pm",
  "locationId": "loc_001" | null
}
```

`locationId = null` → global. `locationId = "loc_001"` → per-location.

**Response (201):** Created delivery time object.

---

### 6.3 PATCH /delivery-times/{id}
**Consumer:** Admin Panel (A06)
**Auth:** Admin

**Request:**
```json
{
  "label": "Afternoon 12-2pm",
  "isActive": false
}
```

**Response (200):** Updated object.

---

### 6.4 DELETE /delivery-times/{id}
**Consumer:** Admin Panel (A06)
**Auth:** Admin

**Response (200):** `{ "message": "Delivery time removed" }`

---

## 7. Orders

### 7.1 POST /orders
**Consumer:** Customer App (C07)
**Auth:** Customer

**Request:**
```json
{
  "items": [
    { "productId": "prod_001", "quantity": 2 },
    { "productId": "prod_005", "quantity": 1 }
  ],
  "deliveryDate": "2026-07-20",
  "deliveryTimeId": "dt_002",
  "paymentMethod": "GpayOnDelivery" | "CashOnDelivery"
}
```

**Response (201):**
```json
{
  "id": "order_001",
  "orderNumber": "M2026#00015",
  "state": "Pending",
  "isPreOrder": true,
  "deliveryDate": "2026-07-20",
  "deliveryTime": { "id": "dt_002", "label": "Morning 9-11am" },
  "paymentMethodPreference": "GpayOnDelivery",
  "items": [
    {
      "id": "oi_001",
      "productId": "prod_001",
      "productNameSnapshot": "Karimeen (Pearl Spot) 500g",
      "grossWeightSnapshot": 550,
      "netWeightSnapshot": 500,
      "unitPriceSnapshot": 304.76,
      "gstApplicableSnapshot": true,
      "gstPercentSnapshot": 5,
      "gstAmountSnapshot": 30.48,
      "quantity": 2,
      "lineTotal": 609.52,
      "lineTotalWithGst": 640.00
    },
    {
      "id": "oi_002",
      "productId": "prod_005",
      "productNameSnapshot": "Lady's Finger 250g",
      "grossWeightSnapshot": 260,
      "netWeightSnapshot": 250,
      "unitPriceSnapshot": 35.00,
      "gstApplicableSnapshot": false,
      "gstPercentSnapshot": 0,
      "gstAmountSnapshot": 0,
      "quantity": 1,
      "lineTotal": 35.00,
      "lineTotalWithGst": 35.00
    }
  ],
  "subtotal": 644.52,
  "gstTotal": 30.48,
  "total": 675.00,
  "deliveryAddressSnapshot": "House No 12, MG Road, Near SBI Bank",
  "customer": {
    "id": "cust_001",
    "name": "Arun Kumar",
    "whatsappNumber": "+919876543210"
  },
  "locationId": "loc_001",
  "locationName": "Aluva",
  "invoiceUrl": "/orders/order_001/invoice",
  "placedBy": "customer",
  "createdAt": "2026-07-19T10:30:00Z"
}
```

**Notes:**
- Server recomputes totals from authoritative prices (never trusts client totals).
- Stock decremented for all items at customer's location.
- Invoice auto-generated.
- `isPreOrder = true` if `deliveryDate` is future.
- GST back-calculated: `basePrice = price / (1 + gstPercent/100)`

**Errors:**
- 400 — empty cart, missing delivery time/date/payment
- 409 — product unavailable, price changed (returns updated prices)

---

### 7.2 POST /orders/shop-sale
**Consumer:** Staff App (S10)
**Auth:** Staff(placeShopSale)

**Request:**
```json
{
  "phone": "+919876543210",
  "customerName": "Walk-in Customer",
  "items": [
    { "productId": "prod_001", "quantity": 1 },
    { "productId": "prod_003", "quantity": 2 }
  ],
  "paymentMethod": "Cash",
  "locationId": "loc_001"
}
```

**Response (201):**
```json
{
  "id": "order_050",
  "orderNumber": "M2026#00050",
  "state": "Completed",
  "saleType": "shopSale",
  "placedBy": "staff",
  "placedByStaffId": "staff_001",
  "placedByStaffName": "Rahul K",
  "shopSalePhone": "+919876543210",
  "shopSaleCustomerName": "Walk-in Customer",
  "items": [...],
  "subtotal": 855.00,
  "gstTotal": 45.00,
  "total": 900.00,
  "paymentMethodCollected": "Cash",
  "invoiceUrl": "/orders/order_050/invoice",
  "locationId": "loc_001",
  "createdAt": "2026-07-19T11:00:00Z"
}
```

**Notes:**
- Immediately `Completed` (no fulfillment lifecycle).
- No customer record created. Phone/name are optional metadata.
- Stock decremented.
- Invoice generated with Muciriz branding.
- Counts toward Total Sales.

---

### 7.3 GET /orders
**Consumer:** All apps
**Auth:** Bearer token (scoped by role)

**Query params:**
- `?customer=me` — Customer's own orders
- `?location={locationId}` — Staff/Admin scope
- `?categories={cat_001,cat_002}` — Staff category filter
- `?state=Pending|OrderConfirmed|Delivered|Completed|Cancelled`
- `?preOrderOnly=true` — filter pre-orders
- `?saleType=regular|shopSale`
- `?placedBy=customer|staff`
- `?startDate=2026-07-01&endDate=2026-07-31`
- `?search={orderNumber|customerName}`
- `?sort=createdAt&order=asc` (default: pending/oldest first)
- `?page=1&limit=20`

**Response (200):**
```json
{
  "data": [
    {
      "id": "order_001",
      "orderNumber": "M2026#00015",
      "state": "Pending",
      "isPreOrder": true,
      "saleType": "regular",
      "placedBy": "customer",
      "customer": {
        "id": "cust_001",
        "name": "Arun Kumar",
        "whatsappNumber": "+919876543210"
      },
      "locationId": "loc_001",
      "locationName": "Aluva",
      "deliveryDate": "2026-07-20",
      "deliveryTime": { "id": "dt_002", "label": "Morning 9-11am" },
      "itemCount": 3,
      "total": 675.00,
      "paymentMethodPreference": "GpayOnDelivery",
      "paymentMethodCollected": null,
      "staffHandling": null,
      "createdAt": "2026-07-19T10:30:00Z"
    }
  ],
  "meta": { ... },
  "summary": {
    "pending": 5,
    "orderConfirmed": 3,
    "delivered": 2,
    "completed": 120,
    "cancelled": 4,
    "preOrders": 3
  }
}
```

**Notes:**
- `summary` returned for Admin/Staff dashboard (order counts by state).
- Staff only see orders for their assigned locations (+ category restriction if set).
- Customer only sees own orders.

---

### 7.4 GET /orders/{id}
**Consumer:** All apps
**Auth:** Bearer token (own order for Customer; scoped for Staff; all for Admin)

**Response (200):**
```json
{
  "id": "order_001",
  "orderNumber": "M2026#00015",
  "state": "OrderConfirmed",
  "isPreOrder": true,
  "saleType": "regular",
  "placedBy": "customer",
  "customer": {
    "id": "cust_001",
    "name": "Arun Kumar",
    "whatsappNumber": "+919876543210",
    "deliveryAddress": "House No 12, MG Road",
    "nearestLandmark": "Near SBI Bank"
  },
  "locationId": "loc_001",
  "locationName": "Aluva",
  "deliveryDate": "2026-07-20",
  "deliveryTime": { "id": "dt_002", "label": "Morning 9-11am" },
  "deliveryAddressSnapshot": "House No 12, MG Road, Near SBI Bank",
  "paymentMethodPreference": "GpayOnDelivery",
  "paymentMethodCollected": null,
  "items": [
    {
      "id": "oi_001",
      "productId": "prod_001",
      "productNameSnapshot": "Karimeen (Pearl Spot) 500g",
      "grossWeightSnapshot": 550,
      "netWeightSnapshot": 500,
      "unitPriceSnapshot": 304.76,
      "gstApplicableSnapshot": true,
      "gstPercentSnapshot": 5,
      "gstAmountSnapshot": 30.48,
      "quantity": 2,
      "lineTotal": 609.52,
      "lineTotalWithGst": 640.00
    }
  ],
  "subtotal": 644.52,
  "gstTotal": 30.48,
  "total": 675.00,
  "invoiceUrl": "/orders/order_001/invoice",
  "timeline": {
    "placed": {
      "at": "2026-07-19T10:30:00Z",
      "by": null
    },
    "orderConfirmed": {
      "at": "2026-07-19T10:45:00Z",
      "by": { "id": "staff_001", "name": "Rahul K" }
    },
    "delivered": null,
    "paymentConfirmed": null,
    "cancelled": null
  },
  "createdAt": "2026-07-19T10:30:00Z",
  "updatedAt": "2026-07-19T10:45:00Z"
}
```

---

### 7.5 PATCH /orders/{id}/state
**Consumer:** Staff App (S04)
**Auth:** Staff (with relevant permission)

**Request:**
```json
{
  "state": "OrderConfirmed" | "Delivered"
}
```

**Response (200):**
```json
{
  "id": "order_001",
  "state": "OrderConfirmed",
  "timeline": {
    "orderConfirmed": {
      "at": "2026-07-19T10:45:00Z",
      "by": { "id": "staff_001", "name": "Rahul K" }
    }
  }
}
```

**Errors:**
- 409 `INVALID_STATE_TRANSITION` — e.g., trying to Deliver before Confirming
- 403 — missing permission (`confirmOrder` / `confirmDelivery`)

---

### 7.6 PATCH /orders/{id}/payment
**Consumer:** Staff App (S04)
**Auth:** Staff(confirmPayment)

**Request:**
```json
{
  "collectedMethod": "Gpay" | "Cash"
}
```

**Response (200):**
```json
{
  "id": "order_001",
  "state": "Completed",
  "paymentMethodCollected": "Gpay",
  "timeline": {
    "paymentConfirmed": {
      "at": "2026-07-19T14:00:00Z",
      "by": { "id": "staff_001", "name": "Rahul K" }
    }
  }
}
```

**Errors:**
- 409 — order not in `Delivered` state

---

### 7.7 POST /orders/{id}/cancel
**Consumer:** Customer App (C10)
**Auth:** Customer (own order only)

**Request:**
```json
{
  "reason": "Changed my mind"
}
```

**Response (200):**
```json
{
  "id": "order_001",
  "state": "Cancelled",
  "cancelledBy": { "type": "customer", "id": "cust_001" },
  "cancelledAt": "2026-07-19T10:35:00Z",
  "stockRestored": true
}
```

**Errors:**
- 409 `CANNOT_CANCEL` — order not in `Pending` state

---

### 7.8 POST /orders/{id}/staff-cancel
**Consumer:** Staff App (S04)
**Auth:** Staff (scoped)

**Request:**
```json
{
  "reason": "Customer unreachable"
}
```

**Response (200):**
```json
{
  "id": "order_001",
  "state": "Cancelled",
  "cancelledBy": { "type": "staff", "id": "staff_001", "name": "Rahul K" },
  "cancelledAt": "2026-07-19T11:00:00Z",
  "stockRestored": true
}
```

**Errors:**
- 409 — order already `Completed`

**Notes:** Staff can cancel orders in any state before `Completed`. Stock restored on cancellation.

---

### 7.9 GET /orders/{id}/invoice
**Consumer:** Customer App (C08, C10), Admin Panel (A10)
**Auth:** Customer (own) or Admin

**Response:** Binary PDF stream with headers:
```
Content-Type: application/pdf
Content-Disposition: attachment; filename="M2026_00015_invoice.pdf"
```

**Invoice contents:**
- Invoice number (M2026#00015), date/time
- Customer name, delivery address, landmark, location
- Itemized products with GST breakdown (base + GST = total per line)
- GST/non-GST split sections
- Subtotal, GST total (by rate), Grand Total
- Delivery date + time slot, payment method
- Muciriz branding

---

## 8. Staff Management

### 8.1 GET /staff
**Consumer:** Admin Panel (A07)
**Auth:** Admin

**Query params:**
- `?location={locationId}` — filter by assigned location
- `?activeOnly=true`
- `?search={name|phone}`
- `?page=1&limit=20`

**Response (200):**
```json
{
  "data": [
    {
      "id": "staff_001",
      "name": "Rahul K",
      "phone": "+919876543210",
      "isActive": true,
      "locations": [
        { "id": "loc_001", "name": "Aluva" },
        { "id": "loc_002", "name": "Perumbavoor" }
      ],
      "permissions": {
        "viewOrders": true,
        "restrictToCategories": false,
        "addProducts": true,
        "confirmOrder": true,
        "confirmDelivery": true,
        "confirmPayment": true,
        "updateStock": true,
        "recordMiscPurchase": false,
        "placeShopSale": true
      },
      "allowedCategoryIds": [],
      "createdAt": "2026-03-01T10:00:00Z"
    }
  ],
  "meta": { ... }
}
```

---

### 8.2 POST /staff
**Consumer:** Admin Panel (A07)
**Auth:** Admin

**Request:**
```json
{
  "name": "Priya S",
  "phone": "+918765432109",
  "password": "InitialPass123",
  "locationIds": ["loc_001", "loc_003"],
  "permissions": {
    "viewOrders": true,
    "restrictToCategories": true,
    "addProducts": false,
    "confirmOrder": true,
    "confirmDelivery": true,
    "confirmPayment": true,
    "updateStock": false,
    "recordMiscPurchase": true,
    "placeShopSale": false
  },
  "allowedCategoryIds": ["cat_001", "cat_002"]
}
```

**Response (201):** Created staff object (same shape as GET).

**Errors:**
- 409 `DUPLICATE_PHONE` — phone already used by another staff member
- 400 — at least one location required

---

### 8.3 PATCH /staff/{id}
**Consumer:** Admin Panel (A07)
**Auth:** Admin

**Request:** (any fields)
```json
{
  "locationIds": ["loc_001", "loc_002", "loc_003"],
  "permissions": {
    "updateStock": true,
    "recordMiscPurchase": true
  },
  "isActive": false
}
```

**Response (200):** Updated staff object.

---

### 8.4 POST /staff/{id}/reset-password
**Consumer:** Admin Panel (A07)
**Auth:** Admin

**Request:**
```json
{
  "newPassword": "NewSecurePass456"
}
```

**Response (200):**
```json
{
  "message": "Password reset successfully",
  "staffId": "staff_001"
}
```

---

### 8.5 DELETE /staff/{id}
**Consumer:** Admin Panel (A07)
**Auth:** Admin

**Response (200):** `{ "message": "Staff deactivated", "isActive": false }`

---

## 9. Customers (Admin Management)

### 9.1 GET /customers
**Consumer:** Admin Panel (A12)
**Auth:** Admin

**Query params:**
- `?search={name|whatsappNumber}` — required (search-based)
- `?location={locationId}` — optional filter
- `?page=1&limit=20`

**Response (200):**
```json
{
  "data": [
    {
      "id": "cust_001",
      "name": "Arun Kumar",
      "whatsappNumber": "+919876543210",
      "deliveryAddress": "House No 12, MG Road",
      "nearestLandmark": "Near SBI Bank",
      "locationId": "loc_001",
      "locationName": "Aluva",
      "isVerified": true,
      "totalOrders": 15,
      "totalSpent": 8500.00,
      "createdAt": "2026-02-10T10:00:00Z"
    }
  ],
  "meta": { ... }
}
```

---

### 9.2 PATCH /customers/{id}
**Consumer:** Admin Panel (A12)
**Auth:** Admin

**Request:**
```json
{
  "name": "Arun Kumar M",
  "deliveryAddress": "House No 14, MG Road",
  "nearestLandmark": "Near Federal Bank",
  "locationId": "loc_002"
}
```

**Response (200):**
```json
{
  "id": "cust_001",
  "name": "Arun Kumar M",
  "whatsappNumber": "+919876543210",
  "deliveryAddress": "House No 14, MG Road",
  "nearestLandmark": "Near Federal Bank",
  "locationId": "loc_002",
  "locationName": "Perumbavoor",
  "updatedAt": "2026-07-19T12:00:00Z",
  "updatedBy": "admin_001"
}
```

**Notes:**
- WhatsApp number is NOT editable (identity key).
- Location change triggers cart re-validation for customer on next use.
- Change is audit-logged.

---

### 9.3 GET /customers/{id}/orders
**Consumer:** Admin Panel (A12)
**Auth:** Admin

**Query params:** `?page=1&limit=20`

**Response (200):**
```json
{
  "data": [
    {
      "id": "order_001",
      "orderNumber": "M2026#00015",
      "state": "Completed",
      "total": 675.00,
      "deliveryDate": "2026-07-20",
      "paymentMethodCollected": "Gpay",
      "isPreOrder": false,
      "createdAt": "2026-07-19T10:30:00Z"
    }
  ],
  "meta": { ... }
}
```

---

## 10. Offers

### 10.1 GET /offers
**Consumer:** Customer App (C05, C11), Admin Panel (A08)
**Auth:** Bearer token

**Query params:**
- `?location={locationId}` — for Customer (resolved: per-location overrides global)
- `?active=true` — Customer only sees active + within validity
- `?page=1&limit=20`

**Response (200):**
```json
{
  "data": [
    {
      "id": "offer_001",
      "title": "20% off Vegetables for Onam",
      "message": "Celebrate Onam with fresh vegetables at 20% discount!",
      "targetType": "Category",
      "targetId": "cat_003",
      "targetName": "Vegetables",
      "discountPercent": 20,
      "bannerImageUrl": "https://cdn.muciriz.in/offers/onam-veggies.jpg",
      "locationId": "loc_001",
      "locationName": "Aluva",
      "scope": "perLocation",
      "startsAt": "2026-08-10T00:00:00Z",
      "endsAt": "2026-08-20T23:59:59Z",
      "isActive": true,
      "appliesDiscount": false
    }
  ],
  "meta": { ... }
}
```

**Notes:** `appliesDiscount` — pending sub-decision. Currently `false` (display-only).

---

### 10.2 POST /offers
**Consumer:** Admin Panel (A08)
**Auth:** Admin

**Request:** `multipart/form-data`
```
title: "20% off Vegetables for Onam"
message: "Celebrate Onam with fresh vegetables at 20% discount!"
targetType: "All" | "Category" | "Product"
targetId: "cat_003" (required if targetType != All)
discountPercent: 20
bannerImage: <file> (optional)
locationId: "loc_001" | null (null = global)
startsAt: "2026-08-10T00:00:00Z"
endsAt: "2026-08-20T23:59:59Z"
isActive: true
```

**Response (201):** Created offer object.

**Errors:**
- 400 — title required, targetId required when targetType is Category/Product, end must be after start

---

### 10.3 PATCH /offers/{id}
**Consumer:** Admin Panel (A08)
**Auth:** Admin

**Request:** Any fields (same as POST).

**Response (200):** Updated offer object.

---

### 10.4 DELETE /offers/{id}
**Consumer:** Admin Panel (A08)
**Auth:** Admin

**Response (200):** `{ "message": "Offer removed" }`

---

## 11. Notifications

### 11.1 GET /notifications
**Consumer:** Customer App (C14)
**Auth:** Customer

**Query params:**
- `?page=1&limit=20`
- `?unreadOnly=true` — optional

**Response (200):**
```json
{
  "data": [
    {
      "id": "notif_001",
      "title": "Order Confirmed!",
      "message": "Your order M2026#00015 has been confirmed and will be delivered tomorrow.",
      "type": "OrderStatus",
      "relatedOrderId": "order_001",
      "relatedOfferId": null,
      "isRead": false,
      "createdAt": "2026-07-19T10:45:00Z"
    },
    {
      "id": "notif_002",
      "title": "20% off Vegetables!",
      "message": "Celebrate Onam with fresh vegetables at a special discount.",
      "type": "Offer",
      "relatedOrderId": null,
      "relatedOfferId": "offer_001",
      "isRead": true,
      "createdAt": "2026-07-18T09:00:00Z"
    }
  ],
  "meta": { ... },
  "unreadCount": 3
}
```

---

### 11.2 PATCH /notifications/{id}/read
**Consumer:** Customer App (C14)
**Auth:** Customer

**Request:** (empty body — action is implicit)

**Response (200):**
```json
{
  "id": "notif_001",
  "isRead": true
}
```

---

### 11.3 POST /notifications
**Consumer:** Admin Panel (A08 — sending notifications)
**Auth:** Admin

**Request:**
```json
{
  "title": "New Offer: 20% off Fish!",
  "message": "Fresh catch at a discount this weekend.",
  "type": "Offer",
  "locationId": "loc_001",
  "channels": ["inApp", "push", "sms"],
  "relatedOfferId": "offer_001"
}
```

**Response (201):**
```json
{
  "id": "notif_batch_001",
  "recipientCount": 150,
  "channels": ["inApp", "push", "sms"],
  "status": "dispatched"
}
```

**Notes:**
- `locationId` = target all customers in that location. `null` = all customers.
- In-app inbox always written. Push (FCM) and SMS (AWS SNS) dispatched per channels.

---

## 12. Miscellaneous Purchases

### 12.1 POST /misc-purchases
**Consumer:** Staff App (S09)
**Auth:** Staff(recordMiscPurchase)

**Request:** `multipart/form-data`
```
itemName: "Plastic bags"
quantity: 100
cost: 500.00
purchaseDate: "2026-07-19"
locationId: "loc_001"
notes: "For packing fish orders"
receiptImage: <file> (optional)
```

**Response (201):**
```json
{
  "id": "misc_001",
  "itemName": "Plastic bags",
  "quantity": 100,
  "cost": 500.00,
  "purchaseDate": "2026-07-19",
  "locationId": "loc_001",
  "locationName": "Aluva",
  "notes": "For packing fish orders",
  "receiptImageUrl": "https://cdn.muciriz.in/receipts/misc_001.jpg",
  "staffId": "staff_001",
  "staffName": "Rahul K",
  "createdAt": "2026-07-19T08:30:00Z"
}
```

---

### 12.2 GET /misc-purchases
**Consumer:** Staff App (S09 — own), Admin Panel (A11)
**Auth:** Staff or Admin

**Query params:**
- `?staff=me` — Staff sees own purchases only
- `?location={locationId}` — Admin filter
- `?startDate=2026-07-01&endDate=2026-07-31`
- `?page=1&limit=20`

**Response (200):**
```json
{
  "data": [
    {
      "id": "misc_001",
      "itemName": "Plastic bags",
      "quantity": 100,
      "cost": 500.00,
      "purchaseDate": "2026-07-19",
      "locationId": "loc_001",
      "locationName": "Aluva",
      "notes": "For packing fish orders",
      "receiptImageUrl": "...",
      "staffId": "staff_001",
      "staffName": "Rahul K",
      "createdAt": "2026-07-19T08:30:00Z"
    }
  ],
  "meta": { ... },
  "totalCost": 2500.00
}
```

---

## 13. Reports (Admin Only)

### 13.1 GET /reports/sales
**Consumer:** Admin Panel (A11 — Sales Tab)
**Auth:** Admin

**Query params:**
- `?location={locationId}` — optional (all if omitted)
- `?startDate=2026-07-01&endDate=2026-07-31`

**Response (200):**
```json
{
  "period": { "start": "2026-07-01", "end": "2026-07-31" },
  "totalSales": 125000.00,
  "totalOrders": 180,
  "totalGst": 6500.00,
  "byLocation": [
    {
      "locationId": "loc_001",
      "locationName": "Aluva",
      "ordersCount": 80,
      "totalSales": 55000.00,
      "gpayAmount": 35000.00,
      "cashAmount": 20000.00
    },
    {
      "locationId": "loc_002",
      "locationName": "Perumbavoor",
      "ordersCount": 60,
      "totalSales": 42000.00,
      "gpayAmount": 28000.00,
      "cashAmount": 14000.00
    }
  ],
  "gstBreakdown": [
    { "rate": 5, "amount": 4200.00 },
    { "rate": 12, "amount": 1800.00 },
    { "rate": 18, "amount": 500.00 }
  ]
}
```

**Notes:** Only counts `Completed` orders.

---

### 13.2 GET /reports/profit-loss
**Consumer:** Admin Panel (A11 — Profit/Loss Tab, A15)
**Auth:** Admin

**Query params:**
- `?location={locationId}` — optional
- `?startDate=2026-07-01&endDate=2026-07-31`

**Response (200):**
```json
{
  "period": { "start": "2026-07-01", "end": "2026-07-31" },
  "totalSale": 125000.00,
  "totalExpense": 95000.00,
  "purchaseExpense": 85000.00,
  "miscExpense": 10000.00,
  "netProfitLoss": 30000.00,
  "isProfit": true,
  "byLocation": [
    {
      "locationId": "loc_001",
      "locationName": "Aluva",
      "totalSale": 55000.00,
      "totalExpense": 40000.00,
      "netProfitLoss": 15000.00,
      "isProfit": true
    }
  ]
}
```

**Notes:** Total Expense = sum(PurchaseRecords) + sum(MiscPurchases). Simplified model (Q27).

---

### 13.3 GET /reports/staff-performance
**Consumer:** Admin Panel (A11 — Staff Performance Tab)
**Auth:** Admin

**Query params:**
- `?location={locationId}` — optional
- `?startDate=2026-07-01&endDate=2026-07-31`

**Response (200):**
```json
{
  "data": [
    {
      "staffId": "staff_001",
      "staffName": "Rahul K",
      "ordersHandled": 45,
      "avgConfirmTimeMinutes": 12,
      "avgDeliveryTimeMinutes": 85,
      "avgPaymentTimeMinutes": 5,
      "totalCompletedOrders": 42
    },
    {
      "staffId": "staff_002",
      "staffName": "Priya S",
      "ordersHandled": 38,
      "avgConfirmTimeMinutes": 8,
      "avgDeliveryTimeMinutes": 60,
      "avgPaymentTimeMinutes": 3,
      "totalCompletedOrders": 36
    }
  ]
}
```

**Notes:**
- `avgConfirmTimeMinutes`: avg time from order placed → Order Confirmed
- `avgDeliveryTimeMinutes`: avg time from Order Confirmed → Delivered
- `avgPaymentTimeMinutes`: avg time from Delivered → Payment Confirmed

---

### 13.4 GET /reports/misc-purchases
**Consumer:** Admin Panel (A11 — Misc Purchases Tab)
**Auth:** Admin

**Query params:**
- `?location={locationId}`
- `?startDate=2026-07-01&endDate=2026-07-31`
- `?page=1&limit=20`

**Response (200):**
```json
{
  "data": [
    {
      "id": "misc_001",
      "itemName": "Plastic bags",
      "quantity": 100,
      "cost": 500.00,
      "purchaseDate": "2026-07-19",
      "staffName": "Rahul K",
      "locationName": "Aluva"
    }
  ],
  "meta": { ... },
  "totalExpense": 10000.00
}
```

---

### 13.5 GET /reports/customers
**Consumer:** Admin Panel (A12)
**Auth:** Admin

**Query params:**
- `?query={name|whatsappNumber}` — required (search)

**Response (200):**
```json
{
  "data": [
    {
      "id": "cust_001",
      "name": "Arun Kumar",
      "whatsappNumber": "+919876543210",
      "locationName": "Aluva",
      "totalOrders": 15,
      "totalSpent": 8500.00
    }
  ]
}
```

---

### 13.6 GET /reports/orders
**Consumer:** Admin Panel (A09 — dashboard summary)
**Auth:** Admin

**Query params:**
- `?location={locationId}`

**Response (200):**
```json
{
  "summary": {
    "pending": 5,
    "orderConfirmed": 3,
    "delivered": 2,
    "completed": 120,
    "cancelled": 4,
    "preOrders": 3,
    "todayOrders": 8,
    "todaySales": 4500.00
  }
}
```

---

## 14. System Configuration

### 14.1 GET /config
**Consumer:** Customer App (C07, C13), Admin Panel (A13)
**Auth:** Bearer token (Customer gets public subset; Admin gets all)

**Response (200) — Customer:**
```json
{
  "gpayUpiId": "muciriz@okaxis",
  "gpayMerchantName": "Muciriz Fresh Goods",
  "customerCarePhone": "+919876500000",
  "customerCareWhatsapp": "+919876500000"
}
```

**Response (200) — Admin:**
```json
{
  "gpayUpiId": "muciriz@okaxis",
  "gpayMerchantName": "Muciriz Fresh Goods",
  "customerCarePhone": "+919876500000",
  "customerCareWhatsapp": "+919876500000",
  "updatedAt": "2026-07-15T10:00:00Z",
  "updatedBy": "admin_001"
}
```

---

### 14.2 PUT /config/{key}
**Consumer:** Admin Panel (A13)
**Auth:** Admin

**Valid keys:** `gpayUpiId`, `gpayMerchantName`, `customerCarePhone`, `customerCareWhatsapp`

**Request:**
```json
{
  "value": "muciriz@okaxis"
}
```

**Response (200):**
```json
{
  "key": "gpayUpiId",
  "value": "muciriz@okaxis",
  "updatedAt": "2026-07-19T12:00:00Z",
  "updatedBy": "admin_001"
}
```

---

## 15. File Uploads

### 15.1 POST /uploads
**Consumer:** Admin Panel, Staff App
**Auth:** Admin or Staff(addProducts/recordMiscPurchase)

**Request:** `multipart/form-data`
```
file: <image file>
type: "category" | "product" | "offer" | "receipt"
```

**Response (201):**
```json
{
  "url": "https://cdn.muciriz.in/products/karimeen_new.jpg",
  "filename": "karimeen_new.jpg",
  "size": 245000,
  "mimeType": "image/jpeg"
}
```

**Notes:**
- Accepted types: JPEG, PNG, WebP
- Max size: 5MB
- Returns a permanent CDN URL usable in product/category/offer creation

**Errors:**
- 400 — file too large, invalid type
- 413 — payload too large

---

## 16. Customer Profile (Self-Service)

### 16.1 PATCH /auth/profile
**Consumer:** Customer App (C12)
**Auth:** Customer

**Request:**
```json
{
  "name": "Arun Kumar M",
  "deliveryAddress": "House No 14, MG Road",
  "nearestLandmark": "Near Federal Bank",
  "locationId": "loc_002"
}
```

**Response (200):**
```json
{
  "id": "cust_001",
  "name": "Arun Kumar M",
  "whatsappNumber": "+919876543210",
  "deliveryAddress": "House No 14, MG Road",
  "nearestLandmark": "Near Federal Bank",
  "locationId": "loc_002",
  "locationName": "Perumbavoor",
  "updatedAt": "2026-07-19T12:00:00Z"
}
```

**Notes:**
- Changing `locationId` triggers cart re-validation/clear (communicated to user).
- WhatsApp number change requires re-verification (sends OTP via `/auth/send-otp`).

---

### 16.2 PATCH /auth/profile/phone
**Consumer:** Customer App (C12 — number change)
**Auth:** Customer

**Request:**
```json
{
  "newWhatsappNumber": "+919876599999"
}
```

**Response (200):**
```json
{
  "message": "OTP sent to new number for verification",
  "otpToken": "temp_token",
  "channel": "whatsapp",
  "expiresIn": 300
}
```

**Notes:** After OTP verification on new number, the phone is updated. Must be unique.

---

## 17. Admin Profile

### 17.1 PATCH /auth/admin/profile
**Consumer:** Admin Panel (A13)
**Auth:** Admin

**Request:**
```json
{
  "name": "Admin User",
  "email": "admin@muciriz.in"
}
```

**Response (200):** Updated admin profile.

---

### 17.2 POST /auth/admin/change-password
**Consumer:** Admin Panel (A13)
**Auth:** Admin

**Request:**
```json
{
  "currentPassword": "OldPass123",
  "newPassword": "NewPass456"
}
```

**Response (200):**
```json
{
  "message": "Password changed successfully"
}
```

**Errors:**
- 401 — current password incorrect
- 400 — new password too weak

---

---

## Appendix A: Endpoint Summary by Consumer App

### Customer App (Flutter)
| Method | Endpoint | Screen |
|--------|----------|--------|
| POST | /auth/register | C03 |
| POST | /auth/send-otp | C03, C04 |
| POST | /auth/verify-otp | C04 |
| POST | /auth/login/customer | C03 |
| GET | /auth/me | C01 |
| POST | /auth/logout | C13 |
| PATCH | /auth/profile | C12 |
| PATCH | /auth/profile/phone | C12 |
| GET | /locations | C03 |
| GET | /categories | C05, C06 |
| GET | /products | C06 |
| GET | /delivery-times | C07 |
| GET | /offers | C05, C11 |
| GET | /cart/recommendations | C07 |
| POST | /orders | C07 |
| GET | /orders | C09 |
| GET | /orders/{id} | C10 |
| GET | /orders/{id}/invoice | C08, C10 |
| POST | /orders/{id}/cancel | C10 |
| GET | /notifications | C14 |
| PATCH | /notifications/{id}/read | C14 |
| GET | /config | C07, C13 |

### Staff App (Flutter)
| Method | Endpoint | Screen |
|--------|----------|--------|
| POST | /auth/login/staff | S02 |
| GET | /auth/me | S01, S06 |
| POST | /auth/logout | S07 |
| GET | /orders | S03 |
| GET | /orders/{id} | S04 |
| PATCH | /orders/{id}/state | S04 |
| PATCH | /orders/{id}/payment | S04 |
| POST | /orders/{id}/staff-cancel | S04 |
| POST | /orders/shop-sale | S10 |
| GET | /categories | S05, S10 |
| GET | /products | S05, S10 |
| POST | /products | S05 |
| PATCH | /products/{id} | S05 |
| POST | /uploads | S05, S09 |
| GET | /stock | S05 |
| PATCH | /stock/{productId}/{locationId} | S05 |
| POST | /misc-purchases | S09 |
| GET | /misc-purchases | S09 |

### Admin Panel (Next.js)
| Method | Endpoint | Screen |
|--------|----------|--------|
| POST | /auth/login/admin | A01 |
| POST | /auth/admin/forgot-password | A01 |
| GET | /auth/me | Auto-login |
| POST | /auth/logout | A13 |
| PATCH | /auth/admin/profile | A13 |
| POST | /auth/admin/change-password | A13 |
| GET/POST/PATCH/DELETE | /locations | A03 |
| GET/POST/PATCH/DELETE | /categories | A04 |
| GET/PUT | /categories/{id}/recommendations | A04 |
| GET/POST/PATCH/DELETE | /products | A05 |
| PATCH | /products/{id}/approval | A05 |
| GET/PUT | /products/{id}/recommendations | A05 |
| GET/POST/PATCH/DELETE | /delivery-times | A06 |
| GET/POST/PATCH/DELETE | /staff | A07 |
| POST | /staff/{id}/reset-password | A07 |
| GET/POST/PATCH/DELETE | /offers | A08 |
| GET | /orders | A09 |
| GET | /orders/{id} | A10 |
| GET | /orders/{id}/invoice | A10 |
| GET | /customers | A12 |
| PATCH | /customers/{id} | A12 |
| GET | /customers/{id}/orders | A12 |
| GET | /stock | A15 |
| PATCH | /stock/{productId}/{locationId} | A15 |
| POST/GET | /purchases | A15 |
| POST | /notifications | A08 |
| GET/PUT | /config/{key} | A13 |
| GET | /config | A13 |
| POST | /uploads | A04, A05, A08 |
| GET | /reports/sales | A11 |
| GET | /reports/profit-loss | A11, A15 |
| GET | /reports/staff-performance | A11 |
| GET | /reports/misc-purchases | A11 |
| GET | /reports/customers | A12 |
| GET | /reports/orders | A02, A09 |

---

## Appendix B: Authentication & Authorization Matrix

| Endpoint Pattern | Customer | Staff | Admin |
|------------------|:--------:|:-----:|:-----:|
| /auth/register, /auth/send-otp, /auth/verify-otp | ✅ (public) | — | — |
| /auth/login/customer | ✅ (public) | — | — |
| /auth/login/staff | — | ✅ (public) | — |
| /auth/login/admin | — | — | ✅ (public) |
| /auth/me, /auth/logout | ✅ | ✅ | ✅ |
| /locations (GET) | ✅ | ✅ | ✅ |
| /locations (write) | — | — | ✅ |
| /categories (GET) | ✅ (own loc) | ✅ (assigned locs) | ✅ |
| /categories (write) | — | — | ✅ |
| /products (GET) | ✅ (approved+active) | ✅ (assigned) | ✅ (all) |
| /products (POST/PATCH) | — | ⚙️ addProducts | ✅ |
| /products/{id}/approval | — | — | ✅ |
| /stock (GET) | — | ⚙️ updateStock | ✅ |
| /stock (PATCH) | — | ⚙️ updateStock | ✅ |
| /purchases | — | — | ✅ |
| /delivery-times (GET) | ✅ | — | ✅ |
| /delivery-times (write) | — | — | ✅ |
| /orders (POST) | ✅ | — | — |
| /orders/shop-sale | — | ⚙️ placeShopSale | — |
| /orders (GET) | ✅ (own) | ✅ (scoped) | ✅ (all) |
| /orders/{id} (GET) | ✅ (own) | ✅ (scoped) | ✅ |
| /orders/{id}/state | — | ⚙️ confirm perms | — |
| /orders/{id}/payment | — | ⚙️ confirmPayment | — |
| /orders/{id}/cancel | ✅ (own, Pending) | — | — |
| /orders/{id}/staff-cancel | — | ✅ (scoped) | — |
| /orders/{id}/invoice | ✅ (own) | — | ✅ |
| /offers (GET) | ✅ (active) | — | ✅ |
| /offers (write) | — | — | ✅ |
| /staff | — | — | ✅ |
| /customers | — | — | ✅ |
| /notifications (GET) | ✅ | — | — |
| /notifications (POST) | — | — | ✅ |
| /misc-purchases (POST) | — | ⚙️ recordMiscPurchase | — |
| /misc-purchases (GET) | — | ✅ (own) | ✅ (all) |
| /reports/* | — | — | ✅ |
| /config (GET) | ✅ (public subset) | — | ✅ |
| /config (PUT) | — | — | ✅ |
| /uploads | — | ⚙️ (when permitted) | ✅ |
| /cart/recommendations | ✅ | — | — |

`⚙️` = requires specific permission flag

---

## Appendix C: Order State Machine

```
                    ┌─────────────────────────────────────────────┐
                    │                                             │
  POST /orders      │    PATCH /orders/{id}/state                 │
       │            │         │                                   │
       ▼            │         ▼                                   │
   ┌────────┐   ────┤    ┌──────────────┐    PATCH /state    ┌─────────┐
   │Pending │───────┤    │OrderConfirmed│─────────────────── │Delivered│
   └────────┘       │    └──────────────┘                    └─────────┘
       │            │                                             │
       │            │                         PATCH /payment      │
       │ cancel     │                              │              │
       ▼            │                              ▼              │
  ┌──────────┐      │                        ┌──────────┐        │
  │Cancelled │◄─────┘                        │Completed │◄───────┘
  └──────────┘                               └──────────┘
    (stock restored)

  Customer cancel: only from Pending
  Staff cancel: any state before Completed (stock restored)
```

### State Transition Rules:
| From | To | Endpoint | Permission | Timestamp Field |
|------|----|----------|-----------|-----------------|
| — | Pending | POST /orders | Customer | createdAt |
| Pending | OrderConfirmed | PATCH /state | confirmOrder | orderConfirmedAt + orderConfirmedBy |
| OrderConfirmed | Delivered | PATCH /state | confirmDelivery | deliveredAt + deliveredBy |
| Delivered | Completed | PATCH /payment | confirmPayment | paymentConfirmedAt + paymentConfirmedBy |
| Pending | Cancelled | POST /cancel | Customer (own) | cancelledAt + cancelledBy |
| Any (< Completed) | Cancelled | POST /staff-cancel | Staff (scope) | cancelledAt + cancelledBy |

---

## Appendix D: Invoice Number Format

```
M{YEAR}#{SEQUENTIAL_5_DIGIT}

Examples:
  M2026#00001  (first order of 2026)
  M2026#00015  (15th order of 2026)
  M2027#00001  (resets on new year)
```

- `M` = Muciriz
- `YEAR` = 4-digit year
- `#` = separator
- `SEQUENTIAL` = 5-digit zero-padded, auto-incrementing, resets annually

---

## Appendix E: GST Calculation

Products store **GST-inclusive prices**. The breakdown is back-calculated:

```
Given: price = ₹320 (inclusive), gstPercent = 5%

basePrice = price / (1 + gstPercent/100)
          = 320 / 1.05
          = ₹304.76

gstAmount = price - basePrice
          = 320 - 304.76
          = ₹15.24

Per line (qty = 2):
  lineTotal (base) = 304.76 × 2 = ₹609.52
  gstAmount (line) = 15.24 × 2 = ₹30.48
  lineTotalWithGst = 320 × 2 = ₹640.00
```

Invoice groups GST by rate:
```
Items with GST 5%:  Base ₹609.52 + GST ₹30.48 = ₹640.00
Items with no GST:  ₹35.00
──────────────────────────────────────────────
Subtotal (before GST): ₹644.52
GST 5%:                ₹30.48
Grand Total:           ₹675.00
```

---

*End of API Contracts Document*
