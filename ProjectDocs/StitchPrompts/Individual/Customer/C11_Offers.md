# C11 — Offers | Muciriz Customer App

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Flutter Android.

## Screen
**App:** Customer (Flutter/Android)
**Purpose:** Full list of active promotional offers for the customer's service location with discount info and target scope.

## UI Elements
- App bar: "Offers" title + back arrow
- **Offer cards** (vertical list, full-width cards with subtle shadow):
  - Banner image (top, full-width, if uploaded — otherwise teal gradient)
  - Title (bold, e.g., "20% off Fresh Fish — Onam Special!")
  - Description/message text
  - **Discount badge:** teal chip "20% OFF"
  - **Target:** "Applies to: Fresh Fish category" or "All categories" or specific product name
  - **Validity:** "Valid till 28 Jan 2026" (muted text)
  - **"Shop Now"** CTA button (teal outline pill) → navigates to relevant category/product
- Pull-to-refresh

## Interactions
- Tap "Shop Now" or offer card → navigate to relevant category (C06) or product
- Pull-to-refresh → reloads offers
- Back arrow → C05

## States
- **Loading:** Skeleton cards (2-3 placeholder rectangles)
- **Empty:** Illustration (gift box) + "No offers right now. Check back soon!"
- **Error:** "Couldn't load offers" + Retry
- **Success:** Offer cards list

## Navigation
- **From:** Category Home (C05) offers banner, Notifications (C14)
- **To:** Product List (C06) or Category Home (C05)

## What to Show
- Success state: 3 offer cards — one with fish banner image "20% off Fresh Fish — Onam Special!" (target: Fresh Fish category), one with vegetable image "15% off All Vegetables" (target: Vegetables category), one without image "Buy 2 Get 10% off Pomfret" (target: specific product). Each with discount badges and validity dates.
