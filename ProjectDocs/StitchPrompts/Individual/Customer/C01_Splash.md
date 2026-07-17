# C01 — Splash Screen | Muciriz Customer App

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Flutter Android.

## Screen
**App:** Customer (Flutter/Android)
**Purpose:** Launch screen showing Muciriz branding while checking stored session for auto-login.

## UI Elements
- White background, full-screen
- Centered Muciriz logo/wordmark in Deep Sea Teal (#0E7C86)
- Subtle circular loading indicator below logo (teal)
- No navigation controls, no user input

## Interactions
- Automatic routing after session check (~1-2s):
  - Valid session → Category Home (C05)
  - First launch (never onboarded) → Onboarding (C02)
  - No session + onboarded → Registration (C03)
  - Network failure → Error state (C16)

## States
- **Loading (default):** Logo + spinner, checking session via /auth/me
- **Error:** Network failure → routes to C16 (No Connection) or falls back to Registration

## Navigation
- **From:** App cold start
- **To:** C02 (Onboarding), C03 (Registration), or C05 (Category Home)

## What to Show
- Default loading state: white screen, centered teal "Muciriz" wordmark with subtle circular progress indicator below
- Clean, minimal, brand-forward
