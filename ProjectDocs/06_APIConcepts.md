# Muciriz — API Concepts

> Conceptual endpoint inventory (no implementation). Groups endpoints by domain and consumer app. Scope/role enforced server-side on every call.

## Conventions
- REST/JSON over HTTPS. Token in Authorization header.
- All catalog/order endpoints are **scoped by Service Location** and by **role/permission**.
- Server is authoritative for prices, totals, and state transitions.

## 1. Auth & Session
| Method | Endpoint | Consumer | Purpose |
|--------|----------|----------|---------|
| POST | /auth/register | Customer | Register (Name, WhatsApp #, address+landmark, locationId) → triggers OTP via WhatsApp Business API (primary) / AWS SNS (fallback) |
| POST | /auth/send-otp | Customer | (Re)send OTP to a number via WhatsApp/SMS |
| POST | /auth/verify-otp | Customer | **Required** — verify OTP; marks number verified; issues session |
| POST | /auth/login/customer | Customer | Re-entry of a number: if already verified → sign in **without OTP**; else send OTP |
| GET | /auth/me | Customer/Staff/Admin | Validate token / auto-login; return profile |
| POST | /auth/login/staff | Staff | Staff login (**phone + password**, tied to location(s)) |
| POST | /auth/login/admin | Admin | Admin login |
| POST | /auth/admin/forgot-password | Admin | Admin self-service reset (Q15) |
| POST | /auth/logout | All | Invalidate session |

> Customer identity is OTP-based (no password). OTP delivered via WhatsApp Business API (primary) with AWS SNS SMS fallback (Requirement #1). Staff uses phone+password (Admin-reset only). Same phone may exist as both a customer and a staff record; each endpoint resolves its own identity type (Q14).

## 2. Service Locations (Admin write; all read)
| Method | Endpoint | Consumer | Purpose |
|--------|----------|----------|---------|
| GET | /locations | Customer(reg), Admin | List active locations (registration dropdown) |
| POST | /locations | Admin | Add location |
| PATCH | /locations/{id} | Admin | Rename / activate-deactivate |
| DELETE | /locations/{id} | Admin | Remove (soft-disable recommended) |

## 3. Categories (Admin/Staff-with-perm write; scoped read)
| Method | Endpoint | Consumer | Purpose |
|--------|----------|----------|---------|
| GET | /categories?location={id} | Customer, Staff, Admin | Categories for a location |
| POST | /categories | Admin (Staff if permitted) | Add category (+image) |
| PATCH | /categories/{id} | Admin | Edit |
| DELETE | /categories/{id} | Admin | Remove (soft-disable) |

## 4. Products
| Method | Endpoint | Consumer | Purpose |
|--------|----------|----------|---------|
| GET | /products?category={id}&location={id} | Customer, Staff, Admin | Products in a category (customers see active + approved only; filtered by location stock) |
| POST | /products | Admin / Staff(add_products) | Add product; Admin → auto-approved, Staff → `PendingApproval` (Q11) |
| PATCH | /products/{id} | Admin / Staff(add_products) | Edit; staff edits re-enter `PendingApproval` |
| GET | /products?approvalStatus=PendingApproval&location={id} | Admin | Approval queue |
| PATCH | /products/{id}/approval | Admin | Approve / reject staff-submitted product (Q11) |
| DELETE | /products/{id} | Admin | Remove (soft-disable) |
| PUT | /products/{id}/locations | Admin | Assign product to multiple locations (multi-select) |

### Recommended Products (Confirmed)
| Method | Endpoint | Consumer | Purpose |
|--------|----------|----------|---------|
| GET | /products/{id}/recommendations | Admin | List product-level recommended products |
| PUT | /products/{id}/recommendations | Admin | Set product-level recommended products |
| GET | /categories/{id}/recommendations | Admin | List category-level recommended products |
| PUT | /categories/{id}/recommendations | Admin | Set category-level recommended products |
| GET | /cart/recommendations | Customer | Resolved recommendations for current cart (shown at C07) |

### Stock / Inventory (Requirements #9, #10)
| Method | Endpoint | Consumer | Purpose |
|--------|----------|----------|---------|
| GET | /stock?product={id}&location={id} | Admin, Staff | Current stock level for product at location |
| PATCH | /stock/{productId}/{locationId} | Staff(update_stock), Admin | Update stock count |
| GET | /stock?location={id} | Admin, Staff | All stock levels for a location |
| POST | /purchases | Admin | Record a purchase entry (cost price, qty, supplier, location) |
| GET | /purchases?product={id}&location={id} | Admin | Purchase history for a product at a location |
| GET | /reports/profit-loss?location={id} | Admin | Profit/loss report |

## 5. Delivery Times
| Method | Endpoint | Consumer | Purpose |
|--------|----------|----------|---------|
| GET | /delivery-times?location={id} | Customer, Admin | Resolved options: per-location if present, else global (Q8) |
| POST | /delivery-times | Admin | Add option (global if no locationId, else per-location) |
| DELETE | /delivery-times/{id} | Admin | Remove |

## 6. Cart & Orders
| Method | Endpoint | Consumer | Purpose |
|--------|----------|----------|---------|
| GET | /cart | Customer | (If server cart) current cart |
| PUT | /cart | Customer | Update cart items |
| POST | /orders | Customer, Staff(place_shop_sale) | Place order (items, deliveryDate, deliveryTimeId, paymentMethod); staff uses /orders/shop-sale instead |
| GET | /orders/{id} | Customer(own), Staff(scope), Admin | Order detail (includes state transition timestamps) |
| GET | /orders?location={id}&categories={} | Staff, Admin | Orders list by scope |
| GET | /orders?customer=me | Customer | Order history |
| PATCH | /orders/{id}/state | Staff(perm) | OrderConfirm / DeliveryConfirm transitions (records timestamp + staffId) |
| PATCH | /orders/{id}/payment | Staff(confirm_payment) | Record collected method → Completed (records timestamp + staffId) |
| GET | /orders/{id}/invoice | Customer(own), Admin | Download invoice (PDF with GST/non-GST split) |
| POST | /orders/{id}/cancel | Customer(own) | Cancel **only while Pending** (Q10); rejects with 409 once Order Confirmed |
| POST | /orders/{id}/staff-cancel | Staff(scope) | Staff cancel — allowed before Completed (Q26); restores stock |

> Order/list queries are sorted **oldest/pending first** for staff & admin (Q22). Orders include deliveryDate for pre-order support.

## 7. Offers / Notifications
| Method | Endpoint | Consumer | Purpose |
|--------|----------|----------|---------|
| GET | /offers?location={id}&active=true | Customer | Active offers (per-location + global; per-location overrides — Q9) |
| POST | /offers | Admin | Create offer (targetType All/Category/Product, targetId, discountPercent, validity, global/per-location) |
| PATCH | /offers/{id} | Admin | Edit / activate-deactivate |
| DELETE | /offers/{id} | Admin | Remove |

### Notifications (Confirmed Q18)
| Method | Endpoint | Consumer | Purpose |
|--------|----------|----------|---------|
| GET | /notifications | Customer | In-app inbox list |
| PATCH | /notifications/{id}/read | Customer | Mark read |
| POST | /notifications | Admin | Send notification (channels: in-app + push + SMS via AWS SNS) |

## 8. Staff & Permissions (Admin only)
| Method | Endpoint | Consumer | Purpose |
|--------|----------|----------|---------|
| GET | /staff | Admin | List staff |
| POST | /staff | Admin | Create staff (locations[] + permissions) |
| PATCH | /staff/{id} | Admin | Edit permissions / location assignments |
| DELETE | /staff/{id} | Admin | Deactivate staff |

## 8a. Staff Operations (Staff App)
| Method | Endpoint | Consumer | Purpose |
|--------|----------|----------|---------|
| POST | /misc-purchases | Staff(record_misc_purchase) | Record a miscellaneous purchase |
| GET | /misc-purchases?staff=me | Staff | View own misc purchases |
| GET | /misc-purchases?location={id} | Admin | View all misc purchases for a location |
| POST | /orders/shop-sale | Staff(place_shop_sale) | Create a Shop Sale (walk-in billing; no customer account needed) |

## 8b. Customer Management (Admin)
| Method | Endpoint | Consumer | Purpose |
|--------|----------|----------|---------|
| PATCH | /customers/{id} | Admin | Edit customer details (name, address, landmark, locationId) |

## 9. Reporting (Admin only)
| Method | Endpoint | Consumer | Purpose |
|--------|----------|----------|---------|
| GET | /reports/orders?location={id} | Admin | Delivered vs pending dashboard |
| GET | /reports/sales?location={id} | Admin | Total sales by location |
| GET | /reports/profit-loss?location={id} | Admin | Profit/loss by location (Total Sale − Total Expense; simplified Q27) |
| GET | /reports/staff-performance?location={id} | Admin | Staff performance (transition times) |
| GET | /reports/misc-purchases?location={id} | Admin | Miscellaneous purchase expenses |
| GET | /reports/customers?query={name\|whatsapp} | Admin | Customer purchase history lookup |

## 9a. System Configuration (Admin only)
| Method | Endpoint | Consumer | Purpose |
|--------|----------|----------|---------|
| GET | /config | Admin, Customer (public subset) | Get system config (customer gets: gpay_upi_id, customer_care_*) |
| PUT | /config/{key} | Admin | Update a config value |

## 10. Files / Images
| Method | Endpoint | Consumer | Purpose |
|--------|----------|----------|---------|
| POST | /uploads | Admin / Staff(perm) | Upload category/product image → returns URL |

## Error & Response Concepts
- Standard error envelope: code, message, field-level validation errors.
- Common states surfaced to UI: 400 validation, 401 unauthenticated (route to login), 403 forbidden (scope/permission), 404 not found, 409 conflict (e.g., duplicate WhatsApp #, invalid state transition), 5xx server (retry UI).
- State-transition endpoint must reject out-of-order transitions (e.g., Payment Confirm before Delivery) with 409.
