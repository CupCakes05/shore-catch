# C12 — Profile

**App:** Customer (Flutter/Android)

## Purpose
View and edit the customer's personal and delivery information, including the ability to change the Service Location (if allowed).

## Users
Authenticated Customer.

## Navigation
- **From:** Bottom nav / app bar menu.
- **To:** Settings (C13); Order History (C09).

## Key UI Elements
- Display: Name, WhatsApp Number, Delivery Address, Nearest Landmark, Service Location.
- **Edit** mode with the same fields as registration. **Editing the WhatsApp number requires OTP re-verification** (Q2) and the number must remain unique.
- Save / Cancel buttons.
- Quick links: Order History, Settings.

## States
- **Loading:** while fetching/saving profile.
- **Error:** validation errors inline; save failure toast.
- **Success:** updated confirmation toast.

## Actions
- Edit fields; change Service Location (if allowed — note it changes the catalog scope).
- Save (`PATCH` profile).
- Navigate to Settings / Orders.

## Business Rules (Confirmed Q12/Q2)
- **Changing Service Location is allowed** from Profile; it changes the catalog scope and **re-validates/clears the in-progress cart** (Q12).
- **Editing WhatsApp number requires OTP re-verification** (via AWS SNS) and the new number must remain unique (Q2).

## Validation / Permissions
- Authenticated; own profile only.
- Same field validations as Registration (C03).

## Responsive / Accessibility
- Labeled fields, inline errors, accessible dropdown, clear edit/save affordances.

## Dependencies
- `/auth/me`, profile update endpoint, `/locations`.
