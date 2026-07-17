# C12 — Profile | Muciriz Customer App

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Flutter Android.

## Screen
**App:** Customer (Flutter/Android)
**Purpose:** View and edit customer's personal and delivery information, including service location change.

## UI Elements
- App bar: "Profile" title
- **Display mode (default):**
  - Profile section (card):
    - Name (bold): "Priya Menon"
    - WhatsApp Number: "+91 98765 43210" (with phone icon)
    - Delivery Address: "Flat 4B, Sea View Apartments, MG Road"
    - Nearest Landmark: "Near Lulu Mall"
    - Service Location: "Ernakulam" (with location pin icon)
  - "Edit Profile" button (teal outline, top-right of card)
- **Edit mode (on tap Edit):**
  - Same fields become editable (outlined inputs)
  - WhatsApp number field shows lock icon + note: "Changing number requires OTP verification"
  - Service Location dropdown with warning: "Changing location will clear your current cart"
  - "Save" button (teal filled) + "Cancel" text button
- **Quick links section:**
  - "Order History" → C09
  - "Settings" → C13
- Bottom nav: Profile tab active (teal)

## Interactions
- Tap Edit → fields become editable
- Change WhatsApp number → save triggers OTP re-verification (C04 flow)
- Change Service Location → warning dialog "Changing location will clear your cart. Continue?" → confirm → save
- Save → PATCH profile → success toast "Profile updated"
- Cancel → revert to display mode

## States
- **Loading:** Fetching profile / saving
- **Error:** Inline validation errors; save failure toast
- **Success:** "Profile updated" toast, return to display mode

## Navigation
- **From:** Bottom nav Profile tab
- **To:** Settings (C13), Order History (C09)

## What to Show
- Display mode with sample data: "Priya Menon", phone, address, landmark, location "Ernakulam". Edit button visible. Quick links below.
- Bottom nav with Profile active (teal).
