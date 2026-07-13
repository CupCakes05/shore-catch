# A02 — Dashboard (Home)

**App:** Admin Panel (Next.js, web)

## Purpose
Landing overview after login. Surfaces key operational metrics and quick navigation to management areas. Respects the globally selected Service Location.

## Users
Main Admin.

## Navigation
- **From:** Login (A01).
- **To:** any management screen via sidebar; Orders Dashboard (A09), Sales (A11) via KPI cards.

## Key UI Elements
- **Global Service Location selector** (top bar) — "All locations" or a specific one.
- KPI cards: total orders (pending vs delivered), total sales (period), active offers, staff count, categories/products count.
- Quick links / shortcuts to: Locations, Categories, Products, Delivery Times, Staff, Offers, Orders, Sales, Customer Lookup.
- (Optional) Recent orders mini-list.

## States
- **Loading:** KPI skeletons.
- **Empty:** fresh system (no locations yet) → guidance to create first Service Location.
- **Error:** metrics fetch failure → Retry.
- **Success:** populated KPIs.

## Actions
- Change global location scope.
- Navigate to any section.

## Business Rules
- KPIs scoped by selected location (or aggregated for "All").
- Sales metric definition consistent with Sales Reports (confirm: Completed orders only).

## Validation / Permissions
- Admin only.

## Responsive / Accessibility
- Card grid reflows; KPIs labeled; charts (if any) have text alternatives.

## Dependencies
- `/reports/orders`, `/reports/sales`, counts endpoints.
