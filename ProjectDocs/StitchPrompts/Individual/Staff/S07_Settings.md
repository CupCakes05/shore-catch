# S07 — Settings | Muciriz Staff App

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Flutter Android.

## Screen
**App:** Staff (Flutter/Android)
**Purpose:** Staff app preferences, legal links, and logout.

## UI Elements
- App bar: "Settings" + back arrow
- **Grouped list sections:**
  - **Notifications:**
    - "New Order Alerts" row with toggle switch (teal when on)
  - **Legal:**
    - "Privacy Policy" row (chevron right)
    - "Terms & Conditions" row (chevron right)
    - "About Muciriz" row (chevron right)
  - **Account:**
    - "Logout" row (red text, no chevron)
- **App version** at bottom (muted, centered): "Staff App v1.0.0 (Build 8)"

## Interactions
- Toggle notifications → save preference
- Tap legal links → opens canonical admin-hosted URLs in webview/browser
- Tap Logout → confirm dialog "Are you sure you want to logout?" → "Logout" (red) / "Cancel" → clear session → S02 (Login)

## States
- Mostly static
- Loading/Error on logout

## Navigation
- **From:** Profile (S06) or menu
- **To:** Legal pages (webview); Login (S02) on logout

## What to Show
- Default state: Notifications toggle ON, legal links listed, Logout in red text, app version at bottom. Clean grouped list on white background.
- Alternate: Logout confirmation dialog overlay
