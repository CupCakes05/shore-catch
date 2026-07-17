# A13 — Profile / Settings | Muciriz Admin Panel

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Next.js web. Fixed left sidebar + top bar.

## Screen
**App:** Admin Panel (Next.js, web)
**Purpose:** Admin profile management and system-wide configuration including GPay/UPI ID and customer care contact.

## UI Elements
- Sidebar + top bar (standard layout)
- Page title: "Settings"
- **Two sections (cards or tabs):**

### Profile Section (card)
- Name display: "Admin User"
- Email/Username display: "admin@muciriz.com"
- **Change Password form:**
  - Current Password (outlined)
  - New Password (outlined, with strength indicator)
  - Confirm New Password (outlined)
  - "Update Password" button (teal outline)
- **"Logout"** button (red outline, bottom of card)

### System Configuration Section (card)
- Section header: "System Configuration" (with gear icon)
- Helper text: "These settings are displayed to customers in the app"
- **GPay/UPI ID** field (outlined): e.g., "muciriz@okaxis"
  - Helper: "Displayed at checkout when customer selects GPay"
- **Merchant Name** field: e.g., "Muciriz Fresh"
  - Helper: "Shown in GPay payment intent"
- **Customer Care Phone** field: e.g., "+91 99876 54321"
  - Helper: "Shown in customer app Help section"
- **Customer Care WhatsApp** field: e.g., "+91 99876 54321"
  - Helper: "For tap-to-WhatsApp in customer app"
- **"Save Configuration"** button (teal filled)
- Note (muted): "Leave fields empty to hide from customer app"

## Interactions
- Change Password → validate (current must be correct, new must match confirm, strength rules) → save → toast "Password updated"
- Save Configuration → PUT /config → toast "Configuration saved"
- Logout → confirm dialog → clear session → A01 (Login)
- Empty UPI field → GPay section hidden from customer checkout
- Empty customer care fields → help section hidden from customer app

## States
- **Loading/Error/Success** on password save or config save
- Password form: inline validation (mismatch, strength)
- Config save: success toast "Configuration saved"

## Navigation
- **From:** Sidebar, top bar profile menu
- **To:** A01 (Login) on logout

## What to Show
- Both sections visible: Profile card with change password form (fields empty), System Configuration card with UPI ID filled ("muciriz@okaxis"), Merchant Name ("Muciriz Fresh"), Customer Care Phone filled, WhatsApp number filled. Save Configuration button. Clean two-card layout.
