# A01 — Admin Login

**App:** Admin Panel (Next.js, web)

## Purpose
Authenticate the Main Admin into the web console.

## Users
Main Admin (unauthenticated).

## Navigation
- **From:** app root when unauthenticated.
- **To:** Dashboard (A02) on success.

## Key UI Elements
- Email/username field.
- Password field (show/hide).
- "Login" button.
- Error message area.
- **"Forgot password"** — self-service reset for Admin (Confirmed Q15).

## States
- **Idle:** empty form.
- **Loading:** authenticating.
- **Error:** invalid credentials, locked account, network/server error.
- **Success:** redirect to Dashboard.

## Actions
- Enter credentials; submit (`/auth/login/admin`).

## Business Rules
- Single Main Admin account for the trial.
- **Admin has self-service forgot-password (Q15)** via `/auth/admin/forgot-password`. (Staff cannot self-reset; Admin resets staff passwords.)
- Recommended: rate-limit login attempts; consider 2FA (suggestion).

## Validation / Permissions
- Required fields; server authenticates.

## Responsive / Accessibility
- Labeled fields, keyboard accessible, error text associated, works down to tablet width.

## Dependencies
- `/auth/login/admin`, session/cookie.
