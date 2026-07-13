# Admin Catalog Setup Flow (Cascade by Location)

The recommended order of operations for a fresh system, showing how everything cascades from Service Location.

```
Login (A01) → Dashboard (A02)
1. Create Service Location(s) (A03)        → enables customer registration dropdown + all scoping
2. For each location: create Categories (A04) with images   → appear as customer Category Home cards
3. For each category: add Products (A05) with fields + images → appear as customer flip cards
   3a. (Optional) Set Recommended Products per category (A04) and per product (A05) → shown at checkout (C07)
   3b. Review & approve any staff-submitted products (A05 pending-approval queue) → go live (Q11)
4. Add Delivery Time options (A06): global and/or per-location → checkout dropdown (per-location overrides global — Q8)
5. Create Staff (A07): phone + password, assign location + permissions → staff can log in and fulfill
6. (Optional) Create Offers (A08): target scope All/Category/Product, discount %, global/per-location → appear at top of customer app
```

## Dependencies / Gating
- Customers cannot register meaningfully until ≥ 1 Service Location exists.
- Customers cannot checkout until ≥ 1 Delivery Time exists (and ≥ 1 product in cart).
- Staff cannot operate until an account is created and a location assigned.
- A category with no products shows an empty state to customers.

## Cascade & Removal (Soft-delete — Q6)
- Removing a location impacts its categories/products/customers/orders — **soft-delete** (`isActive=false`); confirmation required (A03).
- Removing a category impacts its products and customer visibility (A04) — soft-delete.
- Removing a product doesn't alter historical orders (snapshots) (A05) — soft-delete.

## Monitoring After Setup
- Orders Dashboard (A09) → delivered vs pending.
- Sales Reports (A11) → totals by location.
- Customer History (A12) → lookup by name/WhatsApp.
