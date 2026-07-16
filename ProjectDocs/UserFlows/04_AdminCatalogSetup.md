# Admin Catalog Setup Flow (Cascade by Location)

The recommended order of operations for a fresh system, showing how everything cascades from Service Location.

```
Login (A01) → Dashboard (A02)
1. Create Service Location(s) (A03)        → enables customer registration dropdown + all scoping
2. Create Categories (A04) with images, assigning to multiple locations (multi-select)
   → appear as customer Category Home cards for assigned locations
3. For each category: add Products (A05) with fields + images + GST settings, assigning to multiple locations
   → appear as customer flip cards for assigned locations
   3a. (Optional) Set Recommended Products per category (A04) and per product (A05) → shown at checkout (C07)
   3b. Review & approve any staff-submitted products (A05 pending-approval queue) → go live (Q11)
4. Add Delivery Time options (A06): global and/or per-location → checkout date+slot picker (per-location overrides global — Q8); supports pre-orders
5. Create Staff (A07): phone + password, assign to MULTIPLE locations (multi-select) + permissions
   → new permissions: update_stock, record_misc_purchase, place_shop_sale
   → staff can log in and fulfill across assigned locations
6. (Optional) Create Offers (A08): target scope All/Category/Product, discount %, global/per-location → appear at top of customer app
7. Configure System Settings (A13): GPay/UPI ID, merchant name, customer care phone/WhatsApp
   → GPay shown at checkout; customer care shown in customer app
8. Set up Inventory (A15): initial stock counts per product per location; record purchase data for profit/loss
```

## Dependencies / Gating
- Customers cannot register meaningfully until ≥ 1 Service Location exists.
- Customers cannot checkout until ≥ 1 Delivery Time exists (and ≥ 1 product in cart).
- Staff cannot operate until an account is created and at least one location assigned.
- A category with no products shows an empty state to customers.
- GPay display at checkout requires UPI ID configured in A13.
- Profit/loss reporting requires purchase data entered in A15.

## Multi-Location Notes (Requirement #4)
- Categories are assigned to multiple locations at creation time (or edited later).
- Products are assigned to multiple locations; stock is tracked per-location.
- Staff are assigned to multiple locations; they operate across all of them.
- This replaces the previous one-location-per-entity model.

## Cascade & Removal (Soft-delete — Q6)
- Removing a location impacts its category/product associations and existing customers/orders — **soft-delete** (`isActive=false`); confirmation required (A03).
- Removing a category from a location just removes the association; soft-delete deactivates entirely.
- Removing a product doesn't alter historical orders (snapshots) (A05) — soft-delete.

## Monitoring After Setup
- Orders Dashboard (A09) → delivered vs pending; pre-orders with dates.
- Sales Reports (A11) → totals by location + profit/loss + staff performance + misc purchases.
- Customer History (A12) → lookup by name/WhatsApp; edit customer details.
- Inventory Management (A15) → stock levels, purchase records, profit/loss.
