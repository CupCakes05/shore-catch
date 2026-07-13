# Eze-Cart — API Concepts

> Conceptual endpoint inventory (no implementation). Groups endpoints by domain and consumer app. Scope/role enforced server-side on every call.

## Conventions
- REST/JSON over HTTPS. Token in Authorization header.
- All catalog/order endpoints are **scoped by Service Location** and by **role/permission**.
- Server is authoritative for prices, totals, and state transitions.

## 1. Auth & Session
| Method | Endpoint | Consumer | Purpose |
|--------|----------|----------|---------|
| POST | /auth/register | Customer | Register (Name, WhatsApp #, address+landmark, locationId) → triggers OTP via AWS SNS |
| POST | /auth/send-otp | Customer | (Re)send OTP to a number via AWS SNS |
| POST | /auth/verify-otp | Customer | **Required** — verify OTP; marks number verified; issues session |
| POST | /auth/login/customer | Customer | Re-entry of a number: if already verified → sign in **without OTP**; else send OTP |
| GET | /auth/me | Customer/Staff/Admin | Validate token / auto-login; return profile |
| POST | /auth/login/staff | Staff | Staff login (**phone + password**, tied to location) |
| POST | /auth/login/admin | Admin | Admin login |
| POST | /auth/admin/forgot-password | Admin | Admin self-service reset (Q15) |
| POST | /auth/logout | All | Invalidate session |

> Customer identity is OTP-based (no password). Staff uses phone+password (Admin-reset only). Same phone may exist as both a customer and a staff record; each endpoint resolves its own identity type (Q14).

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
| GET | /products?category={id} | Customer, Staff, Admin | Products in a category (customers see active + approved only) |
| POST | /products | Admin / Staff(add_products) | Add product; Admin → auto-approved, Staff → `PendingApproval` (Q11) |
| PATCH | /products/{id} | Admin / Staff(add_products) | Edit; staff edits re-enter `PendingApproval` |
| GET | /products?approvalStatus=PendingApproval&location={id} | Admin | Approval queue |
| PATCH | /products/{id}/approval | Admin | Approve / reject staff-submitted product (Q11) |
| DELETE | /products/{id} | Admin | Remove (soft-disable) |

### Recommended Products (Confirmed new requirement)
| Method | Endpoint | Consumer | Purpose |
|--------|----------|----------|---------|
| GET | /products/{id}/recommendations | Admin | List product-level recommended products |
| PUT | /products/{id}/recommendations | Admin | Set product-level recommended products |
| GET | /categories/{id}/recommendations | Admin | List category-level recommended products |
| PUT | /categories/{id}/recommendations | Admin | Set category-level recommended products |
| GET | /cart/recommendations | Customer | Resolved recommendations for current cart (shown at C07) |

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
| POST | /orders | Customer | Place order (items, deliveryTimeId, paymentMethod) |
| GET | /orders/{id} | Customer(own), Staff(scope), Admin | Order detail |
| GET | /orders?location={id}&categories={} | Staff, Admin | Orders list by scope |
| GET | /orders?customer=me | Customer | Order history |
| PATCH | /orders/{id}/state | Staff(perm) | OrderConfirm / DeliveryConfirm transitions |
| PATCH | /orders/{id}/payment | Staff(confirm_payment) | Record collected method → Completed |
| GET | /orders/{id}/invoice | Customer(own), Admin | Download invoice (PDF) |
| POST | /orders/{id}/cancel | Customer(own) | Cancel **only while Pending** (Q10); rejects with 409 once Order Confirmed |

> Order/list queries are sorted **oldest/pending first** for staff & admin (Q22).

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
| POST | /staff | Admin | Create staff (location + permissions) |
| PATCH | /staff/{id} | Admin | Edit permissions / assignment |
| DELETE | /staff/{id} | Admin | Deactivate staff |

## 9. Reporting (Admin only)
| Method | Endpoint | Consumer | Purpose |
|--------|----------|----------|---------|
| GET | /reports/orders?location={id} | Admin | Delivered vs pending dashboard |
| GET | /reports/sales?location={id} | Admin | Total sales by location |
| GET | /reports/customers?query={name\|whatsapp} | Admin | Customer purchase history lookup |

## 10. Files / Images
| Method | Endpoint | Consumer | Purpose |
|--------|----------|----------|---------|
| POST | /uploads | Admin / Staff(perm) | Upload category/product image → returns URL |

## Error & Response Concepts
- Standard error envelope: code, message, field-level validation errors.
- Common states surfaced to UI: 400 validation, 401 unauthenticated (route to login), 403 forbidden (scope/permission), 404 not found, 409 conflict (e.g., duplicate WhatsApp #, invalid state transition), 5xx server (retry UI).
- State-transition endpoint must reject out-of-order transitions (e.g., Payment Confirm before Delivery) with 409.
