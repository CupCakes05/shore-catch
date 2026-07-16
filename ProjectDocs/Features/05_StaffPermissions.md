# Feature — Staff Permissions

**Overview:** Admin configures granular, per-staff accessibility: which locations and categories a staff sees, and which actions they can perform. Updated with new permissions for stock update, misc purchases, and placing orders for customers.

**Business purpose:** Flexible delegation — some staff only view certain categories' orders; some can add products; some can update stock; some can handle walk-in orders; each customized.

**Users:** Admin (configure), Staff (constrained by config).

**Entry points:** Admin Staff Management (A07); enforced across Staff App.

**Dependencies:** Service Location(s), Categories (for restriction).

**Data used:** Staff.permissions flags, allowedCategoryIds, StaffLocation (many-to-many).

**Permission flags:**
| Permission | Description | Default |
|------------|-------------|---------|
| `view_orders` | See orders for assigned locations | On |
| `restrict_to_categories` | Limit to specific categories | Off |
| `add_products` | Add/edit products (subject to Admin approval) | Off |
| `confirm_order` | Perform Order Confirm | On |
| `confirm_delivery` | Perform Delivery Confirm | On |
| `confirm_payment` | Perform Payment Confirm | On |
| `update_stock` | Update stock/inventory counts | Off |
| `record_misc_purchase` | Record misc operational purchases | Off |
| `place_shop_sale` | Create Shop Sales (walk-in/counter billing) | Off |

**Multi-location assignment (Requirement #4):** staff can be assigned to multiple locations (many-to-many). They see orders and can act across all assigned locations.

**Validation:** phone unique within staff; at least one location required; category selection required when restricting.

**States:** staff created/edited/deactivated; UI adapts to flags; backend enforces.

**Edge cases:**
- Permission changed mid-session → 403 → refresh/re-login.
- **`add_products` approval workflow (Confirmed Q11):** staff adds/edits enter `PendingApproval` and require Admin approval before going live.
- Deactivated staff blocked immediately.
- Staff with multiple locations sees combined order list.
- `update_stock` scoped to assigned locations only.
- `place_shop_sale` scoped to assigned locations only.

**Permissions:** Admin-only management.

**Notifications:** none.

**Errors:** conflict on duplicate phone; validation errors.

**Future considerations:** roles/templates for common permission sets; audit log of staff actions; time-based permissions (e.g., shift schedules).
