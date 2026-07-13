# C05 — Category Home

**App:** Customer (Flutter/Android)

## Purpose
The post-login home. Shows product categories available in the customer's **Service Location** (chosen at registration), plus the offers/announcements banner at the top. This is the browse entry point.

## Users
Authenticated Customer.

## Navigation
- **From:** Splash auto-login (C01), Registration/OTP success (C03/C04), or bottom-nav Home.
- **To:**
  - Product List (C06) when a category card is tapped.
  - Offers (C11) when banner tapped.
  - Cart (C07) via cart icon.
  - Profile (C12) / Settings (C13) via nav.

## Key UI Elements
- **Top offers banner** (carousel if multiple active offers) — from `/offers?location`. Tappable → Offers screen.
- **Category cards** displayed in a two-column layout — roughly "one on left and one on right" per the requirement — each card shows category image + name.
- App bar with app name/logo, cart icon (with item-count badge), and profile/menu access.
- Current Service Location label; changing location is done via Profile (Confirmed Q12 — clears/re-validates cart).
- Bottom navigation: Home, Cart, Orders, Profile (recommended).

## States
- **Loading:** skeleton grid of category cards + banner shimmer.
- **Empty:** no categories configured for this location → friendly empty state ("No categories yet for {Location}. Please check back soon.").
- **Error:** fetch failure → error view with Retry.
- **Success:** grid of categories + banner.
- Offers banner has its own empty state (simply hidden if no active offers).

## Actions
- Tap category → Product List (C06) with categoryId.
- Tap offer banner → Offers (C11) / offer detail.
- Tap cart → Cart (C07).
- Pull-to-refresh to reload categories + offers.

## Business Rules
- Categories fetched are strictly scoped to the customer's Service Location.
- Offers shown are active + within validity + scoped to location.

## Validation / Permissions
- Requires authenticated customer session.

## Responsive / Accessibility
- Two-column grid adapts to screen width; cards have accessible labels (category name); banner is swipeable and screen-reader friendly; adequate contrast on white background.

## Dependencies
- `/categories?location={id}`, `/offers?location={id}&active=true`, cart state.
