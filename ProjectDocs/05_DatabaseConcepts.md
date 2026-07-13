# Eze-Cart — Data Model / Database Concepts

> Conceptual entities and relationships (no DDL/code). Relational model recommended. Update as new fields/entities surface.

## 1. Entity Relationship Overview

```
ServiceLocation 1───* Category 1───* Product
       │                                 │
       │ 1                               │ (snapshot)
       *                                 ▼
   Customer 1───* Order 1───* OrderItem *───1 Product(snapshot)
                    │
                    │ *───1 DeliveryTime
                    │ *───1 PaymentMethod (enum)
ServiceLocation 1───* Staff
ServiceLocation 1───* DeliveryTime   (null locationId = global; per-location overrides global)
ServiceLocation 1───* Offer          (null locationId = global; targetType All/Category/Product)
Staff *───* Category  (allowed categories, if restricted)

Product   1───* ProductRecommendation *───1 Product   (source → recommended)
Category  1───* CategoryRecommendation *───1 Product  (source category → recommended product)
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

Owns: Categories, Staff, Offers, (DeliveryTimes if per-location), Customers, Orders.

### Category
| Field | Type | Notes |
|-------|------|-------|
| id | id | PK |
| locationId | fk → ServiceLocation | scope |
| name | string | e.g., Fish, Vegetables, Meat |
| imageUrl | string | uploaded from Admin |
| isActive | bool | |
| sortOrder | int | optional, for card ordering |

### Product
| Field | Type | Notes |
|-------|------|-------|
| id | id | PK |
| categoryId | fk → Category | scope (implies location) |
| name | string | required |
| grossWeight | decimal | ⏳ unit pending (Q5: g/kg) |
| netWeight | decimal | ≤ grossWeight (recommended validation) |
| price | decimal | ⏳ pricing basis pending (Q5: fixed per unit vs per-kg) |
| imageUrl | string | shown on card flip |
| isActive | bool | soft-delete (Q6) |
| createdBy | fk → Staff/Admin | who added |
| submittedBy | fk → Staff? | set when a staff member added/edited it |
| approvalStatus | enum | `Approved` \| `PendingApproval` \| `Rejected` (Q11); Admin edits auto-`Approved` |

> **Visibility rule:** a product is shown to customers only when `isActive=true` **and** `approvalStatus=Approved`. Staff-submitted adds/edits enter `PendingApproval` until an Admin approves/rejects.

### Customer
| Field | Type | Notes |
|-------|------|-------|
| id | id | PK |
| name | string | |
| whatsappNumber | string | identity; **UNIQUE** (Q2) |
| isVerified | bool | true once OTP-verified (Q2) |
| deliveryAddress | string | |
| nearestLandmark | string | |
| locationId | fk → ServiceLocation | chosen at registration; **changeable via Profile** (Q12) |
| createdAt | datetime | |

> Auth/session: **OTP-verified via AWS SNS**, unique number, **no password** (OTP identity). Session cached for auto-login; re-entry of an already-verified number skips OTP (Q2/Q3/Q4). Customer and Staff are **separate identity records** even if they share a phone number (Q14). See `03_SystemArchitecture.md` and `01_UserRoles.md` §8.

### Staff
| Field | Type | Notes |
|-------|------|-------|
| id | id | PK |
| name | string | |
| phone | string | **login identifier = phone** (Q14); unique within Staff. May equal a Customer's phone (independent record). |
| credential | hashed | password, created/reset by Admin only (Q15) |
| locationId | fk → ServiceLocation | assigned location |
| permissions | json/flags | view_orders, restrict_to_categories, add_products, confirm_order, confirm_delivery, confirm_payment |
| allowedCategoryIds | list<fk> | used when restrict_to_categories = true |
| isActive | bool | |

### DeliveryTime
| Field | Type | Notes |
|-------|------|-------|
| id | id | PK |
| label | string | e.g., "Morning 8–10 AM" |
| locationId | fk → ServiceLocation? | **nullable = global** default; set = per-location (Q8) |
| isActive | bool | |

> **Resolution (Q8):** if a location has its own delivery times (`locationId = that location`), those are shown; otherwise the **global** rows (`locationId = null`) apply. Per-location overrides global.

### Order
| Field | Type | Notes |
|-------|------|-------|
| id | id | PK |
| orderNumber | string | human-readable, used on invoice |
| customerId | fk → Customer | |
| locationId | fk → ServiceLocation | denormalized for scope/reporting |
| deliveryTimeId | fk → DeliveryTime | chosen at checkout |
| paymentMethodPreference | enum | GpayOnDelivery \| CashOnDelivery |
| paymentMethodCollected | enum? | set by staff at Payment Confirm (Gpay \| Cash) |
| state | enum | Pending \| OrderConfirmed \| Delivered \| Completed \| Cancelled (customer-cancel only while Pending — Q10) |
| cancelledBy | fk → Customer? | set if customer cancelled while Pending |
| total | decimal | server-computed |
| deliveryAddressSnapshot | string | address+landmark at order time |
| invoiceUrl | string | generated invoice |
| createdAt / updatedAt | datetime | |
| confirmedBy / deliveredBy / paymentBy | fk → Staff | audit trail |

### OrderItem  (snapshot)
| Field | Type | Notes |
|-------|------|-------|
| id | id | PK |
| orderId | fk → Order | |
| productId | fk → Product | reference (may later be inactive) |
| productNameSnapshot | string | frozen at purchase |
| grossWeightSnapshot | decimal | frozen |
| netWeightSnapshot | decimal | frozen |
| unitPriceSnapshot | decimal | frozen |
| quantity | int | |
| lineTotal | decimal | unitPrice × quantity |

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

### ProductRecommendation  (product-level cross-sell — Confirmed new requirement)
| Field | Type | Notes |
|-------|------|-------|
| id | id | PK |
| sourceProductId | fk → Product | the product being viewed/purchased |
| recommendedProductId | fk → Product | the product to suggest |
| sortOrder | int? | optional ordering |

- Directional: source → recommended. Self-reference ignored. Both must be active/approved and in the same location to surface.

### CategoryRecommendation  (category-level cross-sell — Confirmed new requirement)
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

## 3. Key Relationships & Cascades

- ServiceLocation → Category → Product: strict hierarchy; product scope derives location via category.
- Order → OrderItem uses **snapshots** so catalog edits/deletes never mutate history.
- Staff ↔ Category (allowedCategoryIds) only when `restrict_to_categories`.
- **Soft-delete (`isActive=false`) — Confirmed (Q6)** for Location/Category/Product to protect reporting integrity; no hard deletes.

## 4. Derived / Reporting Views

- **Orders by location** (state grouping: delivered vs pending; **sorted oldest/pending first** — Q22).
- **Sales by location** (sum of `total` for **Completed orders only** — Q7).
- **Customer history** (orders by customer, searchable by name/WhatsApp number).

## 5. Confirmed Data Decisions

- ✅ WhatsApp number **unique + OTP-verified** (Q2); no customer password.
- ✅ Delivery times **global + per-location** with per-location override (Q8).
- ✅ Offers carry **target scope (All/Category/Product)** and are **global or per-location** with per-location override (Q1/Q9).
- ✅ **Soft-delete** for locations/categories/products (Q6).
- ✅ Order **cancellation by customer only while Pending** (Q10).
- ✅ Product **approvalStatus** for staff-submitted adds/edits (Q11).
- ✅ **Recommended products** at category and product level (new requirement).
- ✅ **Notification** entity for in-app/push/SMS inbox (Q18).

## 6. Still-Open Data Decisions (Pending Client)

1. ⏳ **Q5:** weight unit (g/kg) and whether price is per-kg or per listed unit; purpose of Gross vs Net weight.
2. ⏳ **Q16:** invoice numbering scheme and whether a tax/GST field/entity is needed.
3. ⏳ (Linked to Q1) whether offer `discountPercent` is applied to cart totals or display-only.
