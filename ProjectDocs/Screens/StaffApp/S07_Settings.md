# S07 — Settings

**App:** Staff (Flutter/Android)

## Purpose
Staff app preferences and account controls: notifications toggle, legal links, logout, version info.

## Users
Authenticated Staff.

## Navigation
- **From:** Profile (S06) / menu.
- **To:** Static/Legal pages; returns to Login after logout.

## Key UI Elements
- Notifications preference toggle (new-order alerts — in-app; push is future).
- Legal links: Privacy, Terms, About, Help/Contact. These open the **canonical Admin-hosted public URLs** (`/privacy`, `/terms`, optional `/about`, `/contact`) via browser / in-app webview — the same hosted pages used for the Staff app's Play Store listing (the Staff app also collects personal data: name, phone, OTP via AWS SNS).
- **Logout** (with confirm dialog).
- App version.

## States
- Mostly static; Loading/Error on logout.

## Actions
- Toggle notifications.
- Open legal/help.
- Logout → clear session → Login (S02).

## Business Rules
- Logout clears stored token.

## Validation / Permissions
- Authenticated.

## Responsive / Accessibility
- Grouped list, labeled toggles, confirmed destructive logout.

## Dependencies
- `/auth/logout`, local prefs.
- Admin-hosted public legal pages (`/privacy`, `/terms`, optional `/about`, `/contact`) — canonical legal content (static/hardcoded in Next.js, not an admin CMS per Q19).
