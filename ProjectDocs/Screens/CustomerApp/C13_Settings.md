# C13 — Settings

**App:** Customer (Flutter/Android)

## Purpose
App preferences and account controls: notifications toggle, legal links, logout, app version.

## Users
Authenticated Customer.

## Navigation
- **From:** Profile (C12) / menu.
- **To:** Static/Legal pages (C15); returns to Splash/Registration after logout.

## Key UI Elements
- Notifications preference toggle(s) — in-app / push / SMS (Confirmed Q18); opens Notifications inbox (C14).
- No language preference — **English only** (Confirmed Q17).
- Legal links: Privacy Policy, Terms, About, Help/FAQ, Contact (→ C15). Legal pages open the **canonical Admin-hosted public URLs** (`/privacy`, `/terms`, optional `/about`, `/contact`) via browser / in-app webview — same URLs used for the Play Store listing.
- **Logout** button (with confirm dialog).
- App version / build info.

## States
- Mostly static.
- **Loading/Error:** on logout call.
- Toggle persists locally/server.

## Actions
- Toggle notifications.
- Open legal/help pages.
- Logout → clear session → route to Splash/Registration.

## Business Rules
- Logout clears the cached session/token (disables auto-login). On next login, re-entering an **already-verified** number signs the user in **without OTP** (Q3).

## Validation / Permissions
- Authenticated.

## Responsive / Accessibility
- Grouped list with labels; toggles have accessible states; destructive logout confirmed.

## Dependencies
- `/auth/logout`, local prefs.
- Admin-hosted public legal pages (`/privacy`, `/terms`, optional `/about`, `/contact`) — canonical legal content via C15 (static/hardcoded in Next.js, not an admin CMS per Q19).
