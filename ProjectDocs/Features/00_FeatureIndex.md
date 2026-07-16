# Muciriz — Feature Index

Feature specs cross-reference the screens, data, and flows that implement them.

| Feature | File | Primary Apps |
|---------|------|--------------|
| Location-scoped catalog (multi-location) | `01_LocationScopedCatalog.md` | Admin, Customer, Staff |
| Cart & checkout (with pre-order, GST, GPay) | `02_CartCheckout.md` | Customer |
| Invoice generation (GST/non-GST split) | `03_Invoice.md` | Customer, Admin |
| Order fulfillment lifecycle (with timestamps) | `04_OrderFulfillment.md` | Staff, Admin, Customer |
| Staff permissions | `05_StaffPermissions.md` | Admin, Staff |
| Offers & notifications | `06_Offers.md` | Admin, Customer |
| Reporting (sales, profit/loss, performance) | `07_Reporting.md` | Admin |
| Recommended products (cross-sell) | `08_RecommendedProducts.md` | Admin, Customer |
| Pre-order | `09_PreOrder.md` | Customer, Staff, Admin |
| Stock/Inventory management | `10_StockManagement.md` | Admin, Staff |
| GST support | `11_GSTSupport.md` | Admin, Customer |
| Staff Shop Sales | `12_StaffPlaceOrder.md` | Staff |
| Staff miscellaneous purchases | `13_StaffMiscPurchases.md` | Staff, Admin |

Each feature doc follows: Overview, Business purpose, Users, Entry points, Dependencies, Data used, Validation, States, Edge cases, Permissions, Notifications, Errors, Future considerations.
