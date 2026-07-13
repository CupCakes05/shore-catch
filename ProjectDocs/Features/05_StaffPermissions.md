# Feature — Staff Permissions

**Overview:** Admin configures granular, per-staff accessibility: which orders/categories a staff sees and which actions they can perform.

**Business purpose:** Flexible delegation — some staff only view certain categories' orders; some can add products; each customized.

**Users:** Admin (configure), Staff (constrained by config).

**Entry points:** Admin Staff Management (A07); enforced across Staff App.

**Dependencies:** Service Location, Categories (for restriction).

**Data used:** Staff.permissions flags, allowedCategoryIds, locationId.

**Permission flags:** `view_orders`, `restrict_to_categories`, `add_products`, `confirm_order`, `confirm_delivery`, `confirm_payment`.

**Validation:** identifier unique; location required; category selection required when restricting.

**States:** staff created/edited/deactivated; UI adapts to flags; backend enforces.

**Edge cases:**
- Permission changed mid-session → 403 → refresh/re-login.
- **`add_products` approval workflow (Confirmed Q11):** staff adds/edits enter `PendingApproval` and require Admin approval before going live. See `02_BusinessRules.md` §9a.
- Deactivated staff blocked immediately.

**Permissions:** Admin-only management.

**Notifications:** none.

**Errors:** conflict on duplicate identifier; validation errors.

**Future considerations:** roles/templates for common permission sets; audit log of staff actions. (Approval queue for staff-added products is now a confirmed requirement — Q11.)
