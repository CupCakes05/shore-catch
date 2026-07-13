# A13 — Admin Profile / Settings

**App:** Admin Panel (Next.js, web)

## Purpose
Manage the Admin's own account and app-level settings (password change, preferences, logout).

## Users
Main Admin.

## Navigation
- **From:** top bar account menu.

## Key UI Elements
- Profile info: name, email/username.
- Change password form (current + new + confirm).
- (Optional) App preferences (e.g., default location scope).
- Legal/about links.
- **Logout** button.
- (Suggestion) 2FA setup, audit log link.

## States
- **Loading/Error/Success** on save and logout.
- Password form validation states.

## Actions
- Edit profile; change password; logout.

## Business Rules
- Single Main Admin; strong password recommended.
- Logout clears session.

## Validation / Permissions
- Password rules (length/complexity). Admin only.

## Responsive / Accessibility
- Labeled fields, inline validation, confirmed logout.

## Dependencies
- Admin profile/password endpoints, `/auth/logout`.
