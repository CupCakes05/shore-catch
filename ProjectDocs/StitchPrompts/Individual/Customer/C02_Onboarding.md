# C02 — Onboarding | Muciriz Customer App

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Flutter Android.

## Screen
**App:** Customer (Flutter/Android)
**Purpose:** 2-3 swipeable intro slides shown on first launch only. Introduces Muciriz value proposition before registration.

## UI Elements
- Full-screen carousel (3 slides), white background
- Each slide:
  - Centered illustration/icon (teal tinted, friendly, minimal)
  - Bold headline (black)
  - One-line body text (gray/muted)
- Slide content:
  - **Slide 1:** Fish/vegetable illustration. "Browse fresh categories in your area"
  - **Slide 2:** Delivery box illustration. "Order fresh goods, choose your delivery time"
  - **Slide 3:** Payment illustration. "Pay on delivery — GPay or Cash"
- Page indicator dots (3 dots, active = teal filled, inactive = gray outline) at bottom
- "Skip" text button (top-right corner, muted gray)
- "Next" button (bottom-center, teal filled pill) on slides 1-2
- "Get Started" button (bottom-center, teal filled pill) on slide 3

## Interactions
- Swipe left/right between slides
- Tap "Next" → advance one slide
- Tap "Skip" → immediately go to Registration (C03)
- Tap "Get Started" (last slide) → Registration (C03)
- Sets local "onboardingSeen" flag → never shows again

## States
- Fully static/local. No loading, empty, or error states needed (no network call).

## Navigation
- **From:** Splash (C01) on first launch
- **To:** Registration (C03)

## What to Show
- Slide 2 active: "Order fresh goods, choose your delivery time" with delivery illustration, dot indicator showing middle dot active, Skip visible top-right, Next button bottom
- Also show slide 1 and 3 as smaller previews to convey the carousel nature
