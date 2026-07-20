# Admin Panel Integration Checklist

> Track progress page-by-page. Check off each item as integration is completed.
> Use this alongside the Antigravity prompt to verify nothing is missed.

---

## Infrastructure Setup

- [ ] `lib/api.ts` — Axios instance with auth interceptor + error handling
- [ ] `context/AuthContext.tsx` — User state, login/logout, token management
- [ ] `context/LocationContext.tsx` — Global location selector state
- [ ] `components/AuthGuard.tsx` — Protects all routes except /login
- [ ] `hooks/` folder — One hook per resource (SWR or React Query based)
- [ ] Toast library installed and provider added to layout
- [ ] `.env.local` configured with `NEXT_PUBLIC_API_URL`
- [ ] Removed all mock/fake data files

---

## Page Integration Status

### A01 — Login (`/login`)
- [ ] Login form submits to `POST /auth/login/admin`
- [ ] JWT token stored on success
- [ ] Redirects to `/dashboard` on success
- [ ] Shows error message on invalid credentials
- [ ] "Forgot Password" calls `POST /auth/admin/forgot-password`
- [ ] Auto-redirects to dashboard if already authenticated
- [ ] Loading state on submit button
- [ ] No social login / no registration / no OTP (remove if present)

---

### A02 — Dashboard (`/dashboard`)
- [ ] KPI cards fetch from `GET /reports/orders`
- [ ] Recent orders table fetches from `GET /orders?page=1&limit=5`
- [ ] KPIs respond to location selector change
- [ ] Quick action buttons navigate correctly
- [ ] Clicking order row navigates to `/orders/{id}`
- [ ] No charts or SVG visualizations (remove if present)

---

### A03 — Service Locations (`/locations`)
- [ ] Table fetches from `GET /locations`
- [ ] "Add Location" modal → `POST /locations`
- [ ] Edit modal → `PATCH /locations/{id}`
- [ ] Deactivate → confirm dialog → `PATCH /locations/{id}` `{isActive: false}`
- [ ] Search/filter works
- [ ] Toast on success/error
- [ ] Table refreshes after CRUD

---

### A04 — Categories (`/categories`)
- [ ] Table fetches from `GET /categories?location={selected}`
- [ ] "Add Category" modal with image upload → `POST /categories`
- [ ] Edit modal with image replace → `PATCH /categories/{id}`
- [ ] Locations multi-select works
- [ ] "Manage Recommendations" drawer → GET + PUT recommendations
- [ ] Deactivate → `DELETE /categories/{id}`
- [ ] Table refreshes after CRUD

---

### A05 — Products (`/products`)
- [ ] Table fetches from `GET /products?location={}&category={}&approvalStatus={}`
- [ ] Category dropdown filter populated from API
- [ ] Status tabs filter (All/Approved/Pending/Rejected)
- [ ] "Add Product" drawer with image upload → `POST /products`
- [ ] Edit drawer → `PATCH /products/{id}`
- [ ] Approve button → `PATCH /products/{id}/approval` `{approvalStatus: "Approved"}`
- [ ] Reject button with reason → `PATCH /products/{id}/approval` `{approvalStatus: "Rejected"}`
- [ ] Recommendations drawer → GET + PUT
- [ ] Deactivate → `DELETE /products/{id}`
- [ ] Pagination works
- [ ] Location selector scopes the data

---

### A06 — Delivery Times (`/delivery-times`)
- [ ] Table fetches from `GET /delivery-times`
- [ ] Tabs (Global / Per Location) filter client-side by `scope`
- [ ] "Add" modal with label + scope radio → `POST /delivery-times`
- [ ] Edit modal → `PATCH /delivery-times/{id}`
- [ ] Delete → `DELETE /delivery-times/{id}`
- [ ] Warning banner shows if any location has 0 active times

---

### A07 — Staff Management (`/staff`)
- [ ] Table fetches from `GET /staff?location={selected}`
- [ ] "Add Staff" drawer (name, phone, password, locations, permissions) → `POST /staff`
- [ ] Edit drawer → `PATCH /staff/{id}`
- [ ] Permission toggles work correctly
- [ ] Category restriction picker shows only when `restrictToCategories` is toggled
- [ ] "Reset Password" modal → `POST /staff/{id}/reset-password`
- [ ] Deactivate → confirm → `DELETE /staff/{id}`

---

### A08 — Offers (`/offers`)
- [ ] Table fetches from `GET /offers`
- [ ] "Create Offer" modal with target picker + image → `POST /offers`
- [ ] Target radio (All/Category/Product) shows appropriate picker
- [ ] Edit modal → `PATCH /offers/{id}`
- [ ] Delete → `DELETE /offers/{id}`
- [ ] Date range picker for validity period
- [ ] Scope (Global/per-location) selector

---

### A09 — Orders Dashboard (`/orders`)
- [ ] Summary cards populated from `GET /orders` → `summary` object
- [ ] Table fetches from `GET /orders?location={}&state={}&preOrderOnly={}&search={}`
- [ ] Status filter dropdown works
- [ ] Date range picker works
- [ ] Pre-order toggle works
- [ ] Search works (order number / customer name)
- [ ] Clicking row navigates to `/orders/{id}`
- [ ] Default sort: pending first, oldest first
- [ ] Pagination works
- [ ] No dispatch/courier features (remove if present)

---

### A10 — Order Detail (`/orders/[id]`)
- [ ] Fetches from `GET /orders/{id}`
- [ ] Header shows status chip + pre-order badge + placed-by label
- [ ] Customer info section (name, phone, address)
- [ ] Delivery details section (date, time slot, location)
- [ ] Payment info section (preference, collected method)
- [ ] Items table with GST columns
- [ ] Totals section: subtotal + GST breakdown + grand total
- [ ] Timeline stepper shows state transitions with timestamps/staff names
- [ ] "Download Invoice" button → `GET /orders/{id}/invoice` → PDF download
- [ ] Read-only (no edit buttons on this page)

---

### A11 — Sales Reports (`/reports`)
- [ ] Date range filter + location filter at top
- [ ] Sales tab → `GET /reports/sales` → headline ₹ + location table
- [ ] Profit/Loss tab → `GET /reports/profit-loss` → 3 KPI cards (green/red)
- [ ] Staff Performance tab → `GET /reports/staff-performance` → table
- [ ] Misc Purchases tab → `GET /reports/misc-purchases` → paginated table
- [ ] All tabs respond to date range + location filters

---

### A12 — Customer History (`/customers`)
- [ ] Search bar calls `GET /customers?search={query}`
- [ ] Results displayed as cards
- [ ] Clicking card shows profile summary + order history (`GET /customers/{id}/orders`)
- [ ] "Edit Customer" modal → `PATCH /customers/{id}`
- [ ] Phone number displayed but not editable in edit modal
- [ ] No loyalty tiers or badges (remove if present)

---

### A13 — Profile & Settings (`/settings`)
- [ ] Profile section shows current admin info (from AuthContext)
- [ ] Change password form → `POST /auth/admin/change-password`
- [ ] System Config section loads from `GET /config`
- [ ] Each config field saves via `PUT /config/{key}`
- [ ] Logout button → `POST /auth/logout` → clear token → redirect `/login`
- [ ] Success/error toasts on all actions

---

### A14 — Error States (Global)
- [ ] Custom 404 page exists (`/not-found` or Next.js `not-found.tsx`)
- [ ] Error boundary wraps the app (catches rendering errors)
- [ ] 401 interceptor auto-redirects to /login with toast
- [ ] Offline detection banner (optional but nice)

---

### A15 — Inventory Management (`/inventory`)
- [ ] Stock Levels tab → `GET /stock?location={selected}`
- [ ] Inline ± buttons → `PATCH /stock/{productId}/{locationId}`
- [ ] Stock status chips (green/amber/red based on count)
- [ ] Purchase Records tab → `GET /purchases?location={}&dates={}`
- [ ] "Record Purchase" modal → `POST /purchases`
- [ ] Profit/Loss tab → `GET /reports/profit-loss`
- [ ] 3 KPI cards (Total Sales, Total Purchases, Net)
- [ ] "View Full Reports" link navigates to `/reports`

---

## Final Verification

- [ ] `npm run build` passes without errors
- [ ] No TypeScript errors
- [ ] No console.log statements left
- [ ] No hardcoded mock data remains anywhere
- [ ] All API calls use the centralized `api.ts` client
- [ ] Token is cleared and user is redirected on any 401
- [ ] Location selector affects all relevant pages
- [ ] Responsive down to 768px (tablet) — check sidebar collapse

---

## Notes / Issues Found During Integration

| # | Issue | Status | Notes |
|---|-------|--------|-------|
| 1 | | | |
| 2 | | | |
| 3 | | | |

---

*Keep this file updated as you work through integration.*
