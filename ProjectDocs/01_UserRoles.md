# Muciriz — User Roles & Permissions

> Defines every role, its responsibilities, accessible apps/screens, allowed actions, and data ownership. Update whenever a new role or permission surfaces.

## 1. Role Summary

| Role | App | Scope | Created by |
|------|-----|-------|-----------|
| **Main Admin** | Muciriz Admin (Next.js) | Entire system, all Service Locations | Seeded / system owner |
| **Delivery Staff** | Muciriz Staff (Flutter) | One or more assigned Service Locations (optionally category-restricted) | Main Admin |
| **Customer** | Muciriz (Flutter) | Their own account + one chosen Service Location's catalog | Self-registration |
| **Guest (pre-auth)** | Customer & Staff App | Onboarding/auth screens only | N/A |

> There is a single Admin tier ("Main Admin") in this trial. A multi-tier admin (Super Admin vs sub-admins) is **not** in scope but is flagged as a future suggestion.

## 2. Main Admin

**Responsibilities**
- Own the entire catalog and operational configuration.
- Manage Service Locations (add/remove) — the top-level partition.
- Manage Categories per Service Location (add/remove, image upload). Categories can be assigned to MULTIPLE locations.
- Manage Products per Category (add/remove; name, gross weight, net weight, price, GST; image upload). Products can be assigned to MULTIPLE locations. Stock/inventory is per-location.
- Manage Delivery Time options (add/remove); support date-based time slots for pre-orders.
- Manage Staff accounts and their granular accessibility/permissions. Staff can be assigned to MULTIPLE locations.
- Create and publish Offers/Notifications shown at the top of the Customer App.
- Monitor Orders dashboard by Service Location (delivered vs pending).
- View Total Sales reporting by Service Location, including profit/loss analysis.
- Look up customer purchase history by Name / WhatsApp number.
- **Edit customer details** (name, address, landmark, service location).
- **Configure system settings**: GPay/UPI ID for checkout display, customer care contact number/WhatsApp.
- **Manage stock/inventory**: purchase records (cost price, weights, supplier), view profit/loss.
- **View staff miscellaneous purchases** (operational expenses).

**Data ownership:** All entities. Full read/write.

**Accessible app:** Muciriz Admin only.

## 3. Delivery Staff

**Responsibilities**
- Log in (tied to one or more Service Locations).
- View orders for their assigned location(s) (optionally filtered to specific categories per Admin config).
- Progress each order through its lifecycle:
  - **Order Confirm** — acknowledge/accept the order (timestamp recorded).
  - **Delivery Confirm** — mark the order delivered (timestamp recorded).
  - **Payment Confirm** — record payment collected and its method (Gpay or Cash) (timestamp recorded).
- **Update product stock** for assigned locations (if permitted).
- **Record miscellaneous purchases** (expense tracking for operational procurement).
- **Place Shop Sales** (walk-in / counter quick billing without customer registration).
- Manage their own profile.

**Configurable (granular) permissions — set per staff member by Main Admin:**
| Permission | Description | Default |
|------------|-------------|---------|
| `view_orders` | See orders for assigned location(s) | On |
| `restrict_to_categories` | Limit visible orders/products to specific categories | Off |
| `add_products` | Add/edit products for a category from the staff's own page (subject to Admin approval) | Off |
| `confirm_order` | Perform Order Confirm | On |
| `confirm_delivery` | Perform Delivery Confirm | On |
| `confirm_payment` | Perform Payment Confirm | On |
| `update_stock` | Update stock/inventory counts for assigned locations | Off |
| `record_misc_purchase` | Record miscellaneous operational purchases | Off |
| `place_shop_sale` | Create Shop Sales (walk-in/counter quick billing) | Off |

**Data ownership:** Orders within assigned location(s) (and allowed categories). Read on catalog for their locations; write on products only if `add_products` granted; write on stock if `update_stock` granted; write on misc purchases if `record_misc_purchase` granted.

**Accessible app:** Muciriz Staff (as staff). A staff member may **also** use the Muciriz app (Customer) as a normal customer — see §8.

> **Confirmed (Q11):** When a staff member has `add_products`, products they **add or edit require Admin approval before going live**. Submitted products enter `PendingApproval` and are not visible to customers until an Admin sets them to `Approved` (or `Rejected`). Staff can add and edit; all such changes route through approval.

## 4. Customer

**Responsibilities**
- Register (Name, WhatsApp Number, Delivery Address + Nearest Landmark, Service Location).
- Browse catalog filtered to their chosen Service Location.
- Add products to cart, choose delivery time, checkout with Gpay-on-Delivery or Cash-on-Delivery.
- Download auto-generated invoice.
- View order history and order status.
- Manage profile and settings; view offers/notifications.

**Identity (Confirmed Q2/Q3/Q4):**
- Mobile (WhatsApp) number is the identity and **must be OTP-verified** (OTP via **WhatsApp Business API** primary, AWS SNS fallback). Numbers are **unique**.
- Session is cached/persisted so **auto-login** works for returning customers.
- On logout + re-entry of an **already-verified** number, the customer is signed in **without OTP again** (first verification only).

**Can change Service Location after registration (Confirmed Q12):** Yes, from Profile; changing location **re-validates/clears the cart** since the catalog is location-bound.

**Data ownership:** Their own profile, cart, and orders. Read-only on catalog for their Service Location.

**Accessible app:** Muciriz (Customer App).

> Returning customers are **auto-logged-in** (see auth notes in `02_BusinessRules.md`).

## 5. Guest (Pre-Auth State)

- Applies to a first-time or logged-out user of the Customer App or a logged-out Staff App.
- Can only see onboarding/splash and auth/registration screens.
- Cannot browse the catalog or place orders until registered/logged in.

## 6. Permission Matrix (Cross-App)

| Capability | Main Admin | Staff | Customer |
|------------|:---------:|:-----:|:--------:|
| Manage Service Locations | ✅ | ❌ | ❌ |
| Manage Categories | ✅ | ❌ | ❌ |
| Manage Products | ✅ | ⚙️ (if granted) | ❌ |
| Manage Delivery Times | ✅ | ❌ | ❌ |
| Manage Staff & permissions | ✅ | ❌ | ❌ |
| Create Offers/Notifications | ✅ | ❌ | ❌ |
| View Orders (all locations) | ✅ | ❌ | ❌ |
| View Orders (assigned locations) | ✅ | ✅ | ❌ (own orders only) |
| Order/Delivery/Payment Confirm | ❌* | ✅ | ❌ |
| View Sales reporting | ✅ | ❌ | ❌ |
| View Profit/Loss reporting | ✅ | ❌ | ❌ |
| Customer history lookup | ✅ | ❌ | ❌ |
| Edit customer details | ✅ | ❌ | ❌ |
| Browse catalog | ✅ (view) | ✅ (view) | ✅ |
| Place order / checkout | ❌ | ⚙️ (Shop Sale for walk-ins, if granted) | ✅ |
| Download invoice | ✅ (any) | ❌ | ✅ (own) |
| Update stock/inventory | ✅ | ⚙️ (if granted) | ❌ |
| Record misc purchases | ❌ | ⚙️ (if granted) | ❌ |
| View misc purchases | ✅ | ✅ (own) | ❌ |
| Manage system settings (GPay ID, customer care) | ✅ | ❌ | ❌ |
| Manage stock purchase data | ✅ | ❌ | ❌ |

`⚙️` = conditional on granted permission. `*` Admin oversees but fulfillment confirmations are a staff field action (Admin visibility is read-only unless a manual override is later requested).

## 7. Data Ownership & Isolation Rules

- A Customer only ever sees the catalog and offers for **their** Service Location.
- A Staff member only sees orders for **their** assigned Service Location(s) (and allowed categories).
- Staff stock updates are scoped to their assigned locations only.
- Deleting a Service Location has cascade implications documented in `02_BusinessRules.md`.

## 8. Identity & Role Resolution — Shared Phone Number (Confirmed Q14)

A single phone number can belong to **both** a Customer account and a Staff account. The two apps authenticate **independently**:

- **Customer App (Muciriz)** authenticates against the **Customer identity** (phone + OTP-verified session, cached/auto-login). No password.
- **Staff App (Muciriz Staff)** authenticates against the **Staff identity** (phone **+ password**, credentials created by Admin).
- Customer and Staff are **separate records/tables** keyed on phone; the same phone value may exist once in each. There is no cross-linking requirement — a person can act as a customer in the Muciriz app and as staff in the Muciriz Staff app using the same number.
- **Resolution rule:** the app being used determines the identity resolved. The Muciriz app never resolves a staff role, and the Muciriz Staff app never resolves a customer role. Uniqueness is enforced **within** each identity type (unique customer phone; unique staff phone), not across them.
- Password reset (Confirmed Q15): **Admin** has self-service forgot-password; **Staff** cannot self-reset (Admin resets staff passwords); **Customers** have no password (OTP identity), so no reset flow applies.
