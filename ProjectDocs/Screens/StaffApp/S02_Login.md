# S02 — Login

**App:** Staff (Flutter/Android)

## Purpose
Authenticate a delivery staff member. Credentials are created by the Main Admin and tied to one or more Service Locations. On login, the app loads the staff's assigned locations and permissions.

## Users
Guest (staff not logged in).

## Navigation
- **From:** Splash (S01) when no valid session.
- **To:** Order List (S03) on success.

## Key UI Elements
- **Phone** field (login identifier — Q14).
- Password field (with show/hide).
- "Login" button (disabled until fields valid).
- **No self-service "forgot password"** — a note directs staff to contact the Admin for a reset (Q15).
- Error message area.

## States
- **Idle:** empty form.
- **Loading:** authenticating (button loader).
- **Error:** invalid credentials, deactivated account, network/server error → clear messages.
- **Success:** navigate to Order List.

## Actions
- Enter credentials; submit (`/auth/login/staff`).
- (Optional) Forgot password flow.

## Business Rules (Confirmed Q14/Q15)
- Staff accounts are created by Admin (no self-registration). **Login = phone + password.**
- The **same phone number** may also be a Customer account; the Staff App authenticates the **staff identity** independently (see `01_UserRoles.md` §8).
- Login returns assigned Service Location + permission flags (drive UI).
- Deactivated staff cannot log in.
- **No staff self-reset** — the **Admin resets** staff passwords (Q15).

## Validation / Permissions
- Required fields; server authenticates and authorizes.

## Responsive / Accessibility
- Labeled fields, appropriate keyboard, show/hide password toggle, error text associated with fields.

## Dependencies
- `/auth/login/staff`, session store.
