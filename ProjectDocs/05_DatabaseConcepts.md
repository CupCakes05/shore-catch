# Muciriz — Data Model / Database Concepts

> Conceptual entities and relationships (no DDL/code). Relational model recommended. Update as new fields/entities surface.

## 1. Entity Relationship Overview

```
ServiceLocation *───* Category *───* Product  (many-to-many via junction tables)
       │                                 │
       │ 1                               │ (snapshot)
       *                                 ▼
   Customer 1───* Order 1───* OrderItem *───1 Product(snapshot)
                    │
                    │ *───1 DeliveryTime
                    │ *───1 PaymentMethod (enum)

ServiceLocation *───* Staff    (many-to-many)
ServiceLocation 1───* DeliveryTime   (null locationId = global; per-location overrides global)
ServiceLocation 1───* Offer          (null locationId = global; targetType All/Category/Product)
Staff *───* Category  (allowed categories, if restricted)

Product + ServiceLocation → ProductLocationStock (per-location stock/inventory)

Product   1───* ProductRecommendation *───1 Product   (source → recommended)
Category  1───* CategoryRecommendation *───1 Product  (source category → recommended product)

Staff 1───* MiscPurchase (operational expense records)
Product + Location 1───* PurchaseRecord (cost/supplier data)

Customer  1───* Notification  (or location-broadcast)
```

## 2. Entities

### ServiceLocation
| Field | Type | Notes |
|-------|------|-------|
| id | id | PK |
| name | string | e.g., "Village A" |
| isActive | bool | soft-delete (Confirmed Q6) — never hard-deleted |
| createdAt | datetime | |

Participates in many-to-many with: Categories, Products, Staff. Owns: Offers, DeliveryTimes (if per-location), Customers, Orders.

### Category
| Field | Type | Notes |
|-------|------|-------|
| id | id | PK |
| name | string | e.g., Fish, Vegetables, Meat |
| imageUrl | string | uploaded from Admin |
| isActive | bool | |
| sortOrder | int | optional, for card ordering |

**Many-to-many with ServiceLocation** via `CategoryLocation` junction table.

### CategoryLocation (junction)
| Field | Type | Notes |
|-------|------|-------|
| categoryId | fk → Category | PK composite |
| locationId | fk → ServiceLocation | PK composite |

### Product
| Field | Type | Notes |
|-------|------|-------|
| id | id | PK |
| categoryId | fk → Category | categorization |
| name | string | required |
| grossWeight | decimal | informational display (describes the packet); unit: g or kg (Q5 confirmed) |
| netWeight | decimal | ≤ grossWeight (recommended validation); informational only |
| price | decimal | selling price — **fixed per packet/unit** (Q5 confirmed: not per-kg); inclusive of GST if applicable (Q24) |
| gstApplicable | bool | whether GST applies to this product (Requirement #11) |
| gstPercent | decimal? | GST rate (e.g., 5, 12, 18, 28); null/0 if not applicable |
| weightUnit | enum | `g` \| `kg` — display unit for gross/net weight (Q5 confirmed) |
| imageUrl | string | shown on card flip |
| isActive | bool | soft-delete (Q6) |
| createdBy | fk → Staff/Admin | who added |
| submittedBy | fk → Staff? | set when a staff member added/edited it |
| approvalStatus | enum | `Approved` \| `PendingApproval` \| `Rejected` (Q11); Admin edits auto-`Approved` |

**Many-to-many with ServiceLocation** via `ProductLocation` junction table.

> **Visibility rule:** a product is shown to customers only when `isActive=true` **and** `approvalStatus=Approved` **and** it is assigned to the customer's Service Location.

### ProductLocation (junction)
| Field | Type | Notes |
|-------|------|-------|
| productId | fk → Product | PK composite |
| locationId | fk → ServiceLocation | PK composite |

### ProductLocationStock (per-location inventory)
| Field | Type | Notes |
|-------|------|-------|
| id | id | PK |
| productId | fk → Product | |
| locationId | fk → ServiceLocation | |
| stockCount | int | current available stock; 0 = out of stock |
| lastUpdatedBy | fk → Staff/Admin | who last updated |
| lastUpdatedAt | datetime | |

> Unique constraint on (productId, locationId). Stock of 0 triggers pre-order flow for customers at that location.

### PurchaseRecord (stock purchase data — Requirement #10)
| Field | Type | Notes |
|-------|------|-------|
| id | id | PK |
| productId | fk → Product | |
| locationId | fk → ServiceLocation | |
| purchaseRate | decimal | cost price per unit |
| grossWeight | decimal | gross weight of purchase batch |
| netWeight | decimal | net weight of purchase batch |
| quantityPurchased | int | units purchased |
| supplierName | string? | optional supplier |
| purchaseDate | date | |
| recordedBy | fk → Admin | |
| createdAt | datetime | |

> Used for profit/loss calculation: Total Expense includes sum of all purchase records + misc purchases (Q27).

### Customer
| Field | Type | Notes |
|-------|------|-------|
| id | id | PK |
| name | string | editable by Admin (Requirement #7) |
| whatsappNumber | string | identity; **UNIQUE** (Q2) |
| isVerified | bool | true once OTP-verified (Q2) |
| deliveryAddress | string | editable by Admin |
| nearestLandmark | string | editable by Admin |
| locationId | fk → ServiceLocation | chosen at registration; **changeable via Profile or by Admin** (Q12, Req #7) |
| createdAt | datetime | |

> Auth/session: **OTP-verified via WhatsApp Business API (primary) / AWS SNS (fallback)**, unique number, **no password** (OTP identity). Session cached for auto-login; re-entry of an already-verified number skips OTP (Q2/Q3/Q4). Customer and Staff are **separate identity records** even if they share a phone number (Q14).

### Staff
| Field | Type | Notes |
|-------|------|-------|
| id | id | PK |
| name | string | |
| phone | string | **login identifier = phone** (Q14); unique within Staff. May equal a Customer's phone (independent record). |
| credential | hashed | password, created/reset by Admin only (Q15) |
| permissions | json/flags | view_orders, restrict_to_categories, add_products, confirm_order, confirm_delivery, confirm_payment, **update_stock**, **record_misc_purchase**, **place_shop_sale** |
| allowedCategoryIds | list<fk> | used when restrict_to_categories = true |
| isActive | bool | |

**Many-to-many with ServiceLocation** via `StaffLocation` junction table (Requirement #4).

### StaffLocation (junction)
| Field | Type | Notes |
|-------|------|-------|
| staffId | fk → Staff | PK composite |
| locationId | fk → ServiceLocation | PK composite |

### DeliveryTime
| Field | Type | Notes |
|-------|------|-------|
| id | id | PK |
| label | string | e.g., "Morning 8–10 AM" |
| locationId | fk → ServiceLocation? | **nullable = global** default; set = per-location (Q8) |
| isActive | bool | |

> **Resolution (Q8):** if a location has its own delivery times (`locationId = that location`), those are shown; otherwise the **global** rows (`locationId = null`) apply. Per-location overrides global. Used with date selection for pre-orders.

### Order
| Field | Type | Notes |
|-------|------|-------|
| id | id | PK |
| orderNumber | string | human-readable invoice number: `M{YEAR}#{SEQ5}` (e.g., M2026#00001). Resets annually. |
| customerId | fk → Customer | |
| locationId | fk → ServiceLocation | denormalized for scope/reporting |
| deliveryDate | date | **delivery date** (today for same-day, future for pre-orders) — Requirement #3 |
| deliveryTimeId | fk → DeliveryTime | chosen time slot |
| isPreOrder | bool | true if deliveryDate is future at time of order placement |
| paymentMethodPreference | enum | GpayOnDelivery \| CashOnDelivery |
| paymentMethodCollected | enum? | set by staff at Payment Confirm (Gpay \| Cash) |
| state | enum | Pending \| OrderConfirmed \| Delivered \| Completed \| Cancelled (customer while Pending; staff before Completed — Q10/Q26) |
| cancelledBy | fk → Customer/Staff? | set if cancelled; records who cancelled |
| cancelledAt | datetime? | timestamp of cancellation |
| total | decimal | server-computed (subtotal + GST) |
| subtotal | decimal | sum of line totals before GST |
| gstTotal | decimal | total GST amount |
| deliveryAddressSnapshot | string | address+landmark at order time |
| invoiceUrl | string | generated invoice |
| placedBy | enum | `customer` \| `staff` — who initiated the order (Requirement #12) |
| placedByStaffId | fk → Staff? | set when staff creates Shop Sale or places order |
| saleType | enum | `regular` \| `shopSale` — regular order vs walk-in shop sale (Q28) |
| shopSalePhone | string? | phone number for shop sales (optional, not linked to Customer) |
| shopSaleCustomerName | string? | optional name for shop sale billing |
| createdAt / updatedAt | datetime | |
| orderConfirmedAt | datetime? | **timestamp of Order Confirm action** (Requirement #2) |
| orderConfirmedBy | fk → Staff? | staff who confirmed |
| deliveredAt | datetime? | **timestamp of Delivery Confirm action** |
| deliveredBy | fk → Staff? | staff who confirmed delivery |
| paymentConfirmedAt | datetime? | **timestamp of Payment Confirm action** |
| paymentConfirmedBy | fk → Staff? | staff who confirmed payment |

### OrderItem (snapshot)
| Field | Type | Notes |
|-------|------|-------|
| id | id | PK |
| orderId | fk → Order | |
| productId | fk → Product | reference (may later be inactive) |
| productNameSnapshot | string | frozen at purchase |
| grossWeightSnapshot | decimal | frozen |
| netWeightSnapshot | decimal | frozen |
| unitPriceSnapshot | decimal | frozen (base price before GST) |
| gstApplicableSnapshot | bool | frozen — whether GST applies |
| gstPercentSnapshot | decimal? | frozen — GST rate at time of purchase |
| gstAmountSnapshot | decimal? | computed GST for this line (unitPrice × qty × gstPercent/100) |
| quantity | int | |
| lineTotal | decimal | unitPrice × quantity (before GST) |
| lineTotalWithGst | decimal | lineTotal + gstAmount |

### Offer
| Field | Type | Notes |
|-------|------|-------|
| id | id | PK |
| locationId | fk → ServiceLocation? | **nullable = global**; set = per-location (per-location overrides global — Q9) |
| targetType | enum | **All \| Category \| Product** (Q1) |
| targetId | fk? | Category id or Product id when targetType ≠ All |
| title / message | string | banner text (e.g., "20% off Vegetables for Onam") |
| discountPercent | decimal? | primary festival-offer model (e.g., 20 = 20%) |
| occasion / specialDate | string/date? | optional festival/occasion framing |
| condition | json? | optional legacy attrs (e.g., {minAmount} / {minQty}) |
| bannerImageUrl | string? | optional |
| startsAt / endsAt | datetime | validity window |
| isActive | bool | |
| appliesDiscount | bool | ⏳ whether discount is applied to cart totals vs display-only — sub-decision pending (linked to Q1) |

### Admin
| Field | Type | Notes |
|-------|------|-------|
| id | id | PK |
| loginIdentifier | string | |
| credential | hashed | |
| role | enum | MainAdmin (single tier for trial) |

### SystemConfig (Admin-configurable settings — Requirements #5, #6)
| Field | Type | Notes |
|-------|------|-------|
| id | id | PK |
| key | string | unique config key |
| value | string/json | config value |
| updatedAt | datetime | |
| updatedBy | fk → Admin | |

**Known config keys:**
- `gpay_upi_id` — Company GPay/UPI ID displayed at checkout (Requirement #5)
- `gpay_merchant_name` — Merchant name for UPI intent
- `customer_care_phone` — Customer care phone number (Requirement #6)
- `customer_care_whatsapp` — Customer care WhatsApp number (Requirement #6)

### MiscPurchase (Staff operational expenses — Requirement #8)
| Field | Type | Notes |
|-------|------|-------|
| id | id | PK |
| staffId | fk → Staff | who recorded it |
| locationId | fk → ServiceLocation | location context |
| itemName | string | e.g., "Plastic bags" |
| quantity | int/decimal | units purchased |
| cost | decimal | total cost (₹) |
| receiptImageUrl | string? | optional receipt/image |
| notes | string? | optional |
| purchaseDate | date | when the purchase was made |
| createdAt | datetime | when recorded |

### ProductRecommendation (product-level cross-sell — Confirmed)
| Field | Type | Notes |
|-------|------|-------|
| id | id | PK |
| sourceProductId | fk → Product | the product being viewed/purchased |
| recommendedProductId | fk → Product | the product to suggest |
| sortOrder | int? | optional ordering |

- Directional: source → recommended. Self-reference ignored. Both must be active/approved and in the same location to surface.

### CategoryRecommendation (category-level cross-sell — Confirmed)
| Field | Type | Notes |
|-------|------|-------|
| id | id | PK |
| sourceCategoryId | fk → Category | the category context |
| recommendedProductId | fk → Product | the product to suggest |
| sortOrder | int? | optional ordering |

> **Surfacing at checkout (C07):** union of product-level recommendations for cart products + category-level recommendations for categories represented in the cart; de-duplicated; excludes items already in cart and any inactive/unapproved/out-of-location products.

### Notification (Confirmed Q18 — in-app inbox)
| Field | Type | Notes |
|-------|------|-------|
| id | id | PK |
| customerId | fk → Customer? | recipient (null = broadcast to a location) |
| locationId | fk → ServiceLocation? | scope for broadcasts |
| title / message | string | content |
| type | enum | Offer \| OrderStatus \| System |
| channels | flags | InApp (always) + Push? + SMS? (Q18) |
| relatedOfferId / relatedOrderId | fk? | deep-link target |
| isRead | bool | read state (server-side) |
| createdAt | datetime | |

- In-app inbox is always written; push (via provider) and SMS (via AWS SNS) are dispatched per the notification's channels.

### StockUpdateLog (audit trail for stock changes — Requirement #9)
| Field | Type | Notes |
|-------|------|-------|
| id | id | PK |
| productId | fk → Product | |
| locationId | fk → ServiceLocation | |
| previousCount | int | stock before change |
| newCount | int | stock after change |
| updatedBy | fk → Staff/Admin | |
| reason | string? | optional note |
| createdAt | datetime | |

## 3. Key Relationships & Cascades

- **Category ↔ ServiceLocation:** many-to-many via `CategoryLocation`. A category appears in a customer's catalog if it is assigned to their location.
- **Product ↔ ServiceLocation:** many-to-many via `ProductLocation`. Stock tracked per-location via `ProductLocationStock`.
- **Staff ↔ ServiceLocation:** many-to-many via `StaffLocation`. Staff see orders for all their assigned locations.
- **Product → Category:** each product belongs to one category (categorization). The product's location assignments are independent (many-to-many).
- Order → OrderItem uses **snapshots** so catalog edits/deletes never mutate history.
- Staff ↔ Category (allowedCategoryIds) only when `restrict_to_categories`.
- **Soft-delete (`isActive=false`) — Confirmed (Q6)** for Location/Category/Product to protect reporting integrity; no hard deletes.
- **Order state transitions** record timestamp + staff for each step (audit & performance tracking).

## 4. Derived / Reporting Views

- **Orders by location** (state grouping: delivered vs pending; **sorted oldest/pending first** — Q22).
- **Sales by location** (sum of `total` for **Completed orders only** — Q7).
- **Profit/Loss by location** (Total Sale − Total Expense, simplified — Q27).
- **Staff performance** (time between state transitions per order, per staff — Requirement #2).
- **Miscellaneous purchases** by staff, by location, by date range (operational expenses — Requirement #8; included in Total Expense for profit/loss).
- **Customer history** (orders by customer, searchable by name/WhatsApp number).
- **Stock levels** per product per location (current counts, low-stock alerts).

## 5. Confirmed Data Decisions

- ✅ WhatsApp number **unique + OTP-verified** (Q2); no customer password. OTP via WhatsApp Business API (primary) / AWS SNS (fallback).
- ✅ Delivery times **global + per-location** with per-location override (Q8). Now supports date + time slot for pre-orders.
- ✅ Offers carry **target scope (All/Category/Product)** and are **global or per-location** with per-location override (Q1/Q9).
- ✅ **Soft-delete** for locations/categories/products (Q6).
- ✅ Order **cancellation by customer only while Pending** (Q10).
- ✅ Product **approvalStatus** for staff-submitted adds/edits (Q11).
- ✅ **Recommended products** at category and product level.
- ✅ **Notification** entity for in-app/push/SMS inbox (Q18).
- ✅ **Multi-location scoping** — categories, products, staff are many-to-many with locations (Requirement #4).
- ✅ **Per-location stock** with stock count, purchase records, and stock update logging (Requirements #9, #10). **Stock decremented on order placement, restored on cancellation (Q26).**
- ✅ **GST fields** on Product and snapshotted on OrderItem (Requirement #11). **Prices are GST-inclusive (Q24).**
- ✅ **Order state transition timestamps** (orderConfirmedAt/By, deliveredAt/By, paymentConfirmedAt/By) (Requirement #2).
- ✅ **Pre-order fields** on Order (deliveryDate, isPreOrder) (Requirement #3).
- ✅ **placedBy / placedByStaffId / saleType** on Order for staff-placed orders and Shop Sales (Requirement #12, Q28).
- ✅ **SystemConfig** for GPay UPI ID and customer care contact (Requirements #5, #6).
- ✅ **MiscPurchase** for staff operational expense tracking (Requirement #8).
- ✅ **Invoice** supports GST/non-GST split (Requirements #11, #13). Numbering: `M{YEAR}#{SEQ5}` (Q16).
- ✅ **Pricing model (Q5):** fixed price per packet, weights informational, weight unit field (g/kg).
- ✅ **Profit/Loss (Q27):** simplified — Total Sale − Total Expense (no per-batch costing).

## 6. Still-Open Data Decisions (Pending Client)

1. ⏳ (Linked to Q1) whether offer `discountPercent` is applied to cart totals or display-only.
