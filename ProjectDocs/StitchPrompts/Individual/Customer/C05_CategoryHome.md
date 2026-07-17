# C05 — Category Home | Muciriz Customer App

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Flutter Android.

## Screen
**App:** Customer (Flutter/Android)
**Purpose:** Post-login home screen. Shows product categories for the customer's service location and offers banner. Primary browse entry point.

## UI Elements
- **App bar:** "Muciriz" logo/text (left, teal), notification bell icon (right), cart icon with red badge count (right)
- **Location subtitle** (small, below app bar): "📍 Ernakulam"
- **Offers banner** (top, horizontal auto-carousel):
  - Card per offer: background image or teal gradient, bold white text ("20% off Fish for Onam!"), "Shop Now" tappable
  - Page dots below carousel
  - Hidden entirely if no active offers
- **Category grid** (2-column layout, equal-sized cards):
  - Each card: rounded-corner image (category photo), category name below (bold, black)
  - e.g., "Fresh Fish", "Vegetables", "Meat & Chicken", "Seafood Special", "Ready to Cook", "Pickles"
- **Bottom navigation bar** (white bg, teal active):
  - Home (fish icon, active=teal), Cart (bag icon + badge), Orders (list icon), Profile (person icon)

## Interactions
- Tap category card → Product List (C06) for that category
- Tap offer banner → Offers screen (C11)
- Tap cart icon → Cart (C07)
- Tap bell icon → Notifications (C14)
- Bottom nav tabs → respective screens
- Pull-to-refresh → reloads categories + offers
- Offers carousel auto-rotates (3s interval)

## States
- **Loading:** Shimmer grid (6 placeholder cards in 2×3 grid) + banner shimmer rectangle
- **Empty:** Illustration (empty box) + "No categories available for Ernakulam yet. Check back soon!"
- **Error:** Warning icon + "Something went wrong" + "Retry" button (teal)
- **Success:** Populated grid + banner

## Navigation
- **From:** Splash (C01) auto-login, Registration/OTP success (C03/C04), Bottom nav Home
- **To:** C06 (Product List), C07 (Cart), C11 (Offers), C14 (Notifications), C12 (Profile)

## What to Show
- Success state: 6 category cards in 2-column grid (Fish, Vegetables, Meat, Seafood, Ready to Cook, Pickles) with realistic food images, offers banner showing "20% off Fresh Fish — Onam Special!" with fish image, cart badge showing "3", bottom nav with Home active (teal)
- Also show: Loading skeleton variant (shimmer rectangles in same grid layout)
