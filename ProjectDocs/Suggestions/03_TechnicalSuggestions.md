# Suggestions — Technical & Architecture

> Suggestions only, appropriate to a small trial. Prioritized for simplicity + future headroom.

## Data Model
- **Snapshot order items** (name/weight/price at purchase) so catalog edits/deletes never corrupt historical orders/sales. (Reflected in `05_DatabaseConcepts.md`.)
- ✅ **Soft-delete confirmed (Q6):** `isActive` for locations/categories/products rather than hard delete — protects reporting integrity.
- Denormalize `locationId` onto Order for fast, safe scoping and reporting.
- ✅ **Product `approvalStatus` confirmed (Q11)** for staff-submitted adds/edits; **recommendation tables** (product/category → product) for cross-sell; **Notification** entity for the inbox.

## API Design
- Consistent scoping convention: always pass/enforce `location` (and category filters) on catalog/order endpoints.
- Standard error envelope + meaningful HTTP codes (400/401/403/404/409/5xx) so clients render the right state.
- Make order-state transition endpoints idempotent and validate current state (reject out-of-order with 409).

## Caching / Performance
- Short-TTL caching or cache-invalidation on catalog reads (catalog changes are infrequent). Fine to skip for the trial given small data.
- Paginate product and order lists.

## Frontend
- **Shared design system** across the two Flutter apps (colors, typography, components) to keep the 2–3 color minimal look consistent and reduce duplication.
- Centralized network/error/offline handling layer (drives the global error/empty/loading states).
- Consider a lightweight state management approach in Flutter for cart + session.

## Notifications (Confirmed Q18)
- In-app inbox (C14) via `/notifications`, plus top banner via `/offers`.
- **Push** (e.g., FCM) + **SMS via AWS SNS** are confirmed delivery channels for offers + order status; also usable for staff new-order alerts.
- AWS SNS also delivers OTP codes (Q2).

## Invoices
- Generate PDF server-side for consistency; store URL on the order; allow re-download.
- Define a numbering scheme and include business branding.

## Play Store Compliance — Hosted Legal Pages (Confirmed approach)
- Both Flutter apps collect personal data (name, mobile/WhatsApp #, address, OTP via AWS SNS), so the **Play Store requires a publicly hosted Privacy Policy URL** and a matching **Data Safety** declaration.
- **Confirmed:** host **public, unauthenticated legal pages** on the existing **Next.js Admin Panel** (`/privacy`, `/terms`, optional `/about`, `/contact`) — reusing an already-hosted web app avoids standing up separate hosting. Keep them **static/hardcoded** (not a CMS, per Q19).
- Treat the hosted pages as the **canonical single source of truth**; the mobile apps **link/webview** to them (C15, C13, C03, S07) so legal edits don't force an app resubmission.
- Keep the public routes cacheable and reachable without a session; ensure they're excluded from any admin auth middleware/route guards.
- Keep the **Data Safety form in sync** with the Privacy Policy on every submission and whenever data collection changes.

## Folder / Project Structure (conceptual)
- Backend organized by domain (auth, catalog, orders, offers, staff, reporting, invoices, uploads).
- Admin (Next.js) organized by feature route with shared UI components and an API client layer; keep the **public legal routes** (`/privacy`, `/terms`, optional `/about`, `/contact`) separate from the authenticated console routes and outside admin auth guards.
- Flutter apps organized by feature (auth, catalog, cart, orders, profile) with shared design + networking packages.

## Testing / QA
- Add tests for order-state transitions and permission enforcement (highest-risk logic).
- Manual accessibility pass (contrast, labels, focus, reduced-motion) — full WCAG needs assistive-tech testing + expert review.

## Scalability Headroom (not required now)
- Entities designed so a location can grow categories/products without schema change.
- Reporting can move from on-read computation to nightly rollups if data grows.
- Multi-tier admin (Super Admin + sub-admins) if operations expand beyond a single operator.
