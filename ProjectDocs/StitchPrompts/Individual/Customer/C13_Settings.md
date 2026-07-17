# C13 — Settings | Muciriz Customer App

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Flutter Android.

## Screen
**App:** Customer (Flutter/Android)
**Purpose:** App preferences, customer care contact, legal links, and logout.

## UI Elements
- App bar: "Settings" title + back arrow
- **Grouped list (sections separated by subtle dividers):**
  - **Notifications section:**
    - "Push Notifications" row with toggle switch (teal when on)
  - **Help & Support section:**
    - "Customer Care" row: phone number displayed (e.g., "+91 99876 54321") with tap-to-call icon (phone) and WhatsApp icon (green)
    - Hidden entirely if admin hasn't configured customer care contact
  - **Legal section:**
    - "Privacy Policy" row → webview (C15)
    - "Terms & Conditions" row → webview (C15)
    - "About Muciriz" row → webview (C15)
  - **Account section:**
    - "Logout" row (red text)
- **App version** at bottom center (muted, small): "Version 1.0.0 (Build 12)"

## Interactions
- Toggle notifications → saves preference locally/server
- Tap phone number → device dialer intent
- Tap WhatsApp icon → wa.me/{number} deep-link
- Tap legal links → in-app webview / external browser (C15)
- Tap Logout → confirm dialog "Are you sure you want to logout?" with "Logout" (red) and "Cancel" buttons → clear session → route to C01/C03

## States
- Mostly static (no network fetch for display)
- Loading/Error on logout call
- Customer care section hidden if not configured by admin

## Navigation
- **From:** Profile (C12) or menu
- **To:** Static/Legal (C15), Splash/Registration on logout

## What to Show
- Default state: Notifications toggle ON, Customer Care showing "+91 99876 54321" with call and WhatsApp icons, legal links listed, Logout in red, app version at bottom. Clean grouped list layout.
- Alternate: Logout confirm dialog overlay
