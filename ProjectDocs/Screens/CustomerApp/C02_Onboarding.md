# C02 — Onboarding

**App:** Customer (Flutter/Android)

## Purpose
Briefly introduce Eze-Cart on first launch: browse local fresh goods, order, pay on delivery. Sets expectations before registration.

## Users
First-time Guest.

## Navigation
- **From:** Splash (C01) on first launch.
- **To:** Registration (C03) via "Get Started"; can Skip to Registration.

## Key UI Elements
- 2–3 slides (carousel) with illustration/icon + short headline + one line of body.
  - Slide 1: Browse categories in your area.
  - Slide 2: Order fresh goods, choose your delivery time.
  - Slide 3: Pay on delivery — Gpay or Cash.
- Page indicator dots.
- "Skip" (top-right) and "Next" / "Get Started" (last slide) buttons.

## States
- Static content; no data fetch. No loading/empty/error states needed (fully local).

## Actions
- Swipe between slides.
- Skip → Registration.
- Next → advance; Get Started → Registration.
- Mark onboarding as seen (local flag) so it doesn't reappear.

## Validation / Permissions
- None (pre-auth).

## Responsive / Accessibility
- Swipeable; dots also tappable.
- Text legible; images have alt/labels; honor reduced-motion (limit auto-animation).

## Dependencies
- Local "onboardingSeen" flag.
