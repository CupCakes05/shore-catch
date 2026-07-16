# A13 — Admin Profile / Settings

**App:** Admin Panel (Next.js, web)

## Purpose
Manage the Admin's own account, app-level settings (password change, preferences, logout), and **system-wide configuration** (GPay/UPI ID, customer care contact).

## Users
Main Admin.

## Navigation
- **From:** top bar account menu / sidebar.

## Key UI Elements

### Profile Section
- Profile info: name, email/username.
- Change password form (current + new + confirm).
- **Logout** button.

### System Configuration Section (Requirements #5, #6)
- **GPay/UPI ID** field — the company's UPI ID displayed to customers at checkout (e.g., `merchant@upi`). Used in the UPI deep-link.
- **GPay Merchant Name** field — merchant name for the UPI intent display.
- **Customer Care Phone** field — phone number shown in customer app's Help/Contact section.
- **Customer Care WhatsApp** field — WhatsApp number for customer support (uses `wa.me` deep-link).
- Save button for system config changes.

### Other
- (Optional) App preferences (e.g., default location scope).
- Legal/about links.
- (Suggestion) 2FA setup, audit log link.

## States
- **Loading/Error/Success** on save (profile and config).
- Password form validation states.
- Config save confirmation toast.

## Actions
- Edit profile; change password.
- **Update system configuration** (GPay UPI ID, merchant name, customer care phone/WhatsApp) → `PUT /config/{key}`.
- Logout.

## Business Rules
- Single Main Admin; strong password recommended.
- Logout clears session.
- **GPay/UPI ID (Requirement #5):** if set, displayed on customer checkout (C07) with deep-link. If empty, GPay display is hidden from customers.
- **Customer care contact (Requirement #6):** if set, shown in customer app Settings/Help. If empty, contact section hidden from customers.

## Validation / Permissions
- Password rules (length/complexity). Admin only.
- UPI ID format validation (e.g., contains '@').
- Phone number format validation for customer care fields.

## Responsive / Accessibility
- Labeled fields, inline validation, confirmed logout; config section clearly separated from profile.

## Dependencies
- Admin profile/password endpoints, `/auth/logout`, `/config`.
