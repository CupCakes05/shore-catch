# S06 — Profile | Muciriz Staff App

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Flutter Android.

## Screen
**App:** Staff (Flutter/Android)
**Purpose:** View staff member's own profile including assigned locations and permissions (read-only, admin-controlled).

## UI Elements
- App bar: "Profile" + back arrow
- **Profile card** (white, centered, subtle border):
  - Staff name (bold, large): "Rajesh Kumar"
  - Phone (login ID): "+91 90123 45678"
  - Role: "Delivery Staff"
- **Assigned Locations section:**
  - Location chips (teal outline): "Ernakulam", "Fort Kochi"
- **Permissions section** (read-only, cannot edit):
  - Permission chips (teal filled with white text for granted, gray outline for not granted):
    - ✅ View Orders
    - ✅ Confirm Order
    - ✅ Confirm Delivery
    - ✅ Confirm Payment
    - ✅ Add Products
    - ✅ Update Stock
    - ☐ Misc Purchases (gray, not granted)
    - ☐ Shop Sales (gray, not granted)
  - Note (muted): "Permissions are managed by your Admin"
- **"Settings"** button/link (bottom) → S07

## Interactions
- View only (staff cannot edit their own profile or permissions)
- Tap Settings → S07

## States
- **Loading:** Profile fetch shimmer
- **Error:** "Couldn't load profile" + Retry
- **Success:** Profile displayed with all info

## Navigation
- **From:** Order List (S03) profile icon / menu
- **To:** Settings (S07)

## What to Show
- Full profile: "Rajesh Kumar", phone, 2 location chips, permissions section with 6 granted (teal) and 2 not granted (gray). Settings link at bottom. Clean, read-only card layout.
