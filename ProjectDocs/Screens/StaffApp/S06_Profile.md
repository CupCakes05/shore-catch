# S06 — Profile

**App:** Staff (Flutter/Android)

## Purpose
Show the staff member's own profile: name, assigned Service Location, and current permissions (read-only, as these are Admin-controlled).

## Users
Authenticated Staff.

## Navigation
- **From:** Order List (S03) / menu.
- **To:** Settings (S07).

## Key UI Elements
- Name, login identifier, assigned Service Location.
- Permissions summary (read-only chips: e.g., "Can confirm delivery", "Can add products").
- (Optional) Change password entry — if self-service reset is enabled.

## States
- **Loading:** profile fetch.
- **Error:** Retry.
- **Success:** profile shown.

## Actions
- View profile.
- (Optional) Change password.
- Navigate to Settings.

## Business Rules
- Staff cannot change their own location or permissions (Admin-only).
- Editable fields limited (confirm whether staff can edit name/contact).

## Validation / Permissions
- Authenticated; own profile.

## Responsive / Accessibility
- Read-only info clearly labeled; permission chips have text.

## Dependencies
- `/auth/me`.
