# S01 — Splash | Muciriz Staff App

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Flutter Android.

## Screen
**App:** Staff (Flutter/Android)
**Purpose:** Launch screen with staff branding, checks stored session for auto-login.

## UI Elements
- White background, full-screen
- Centered "Muciriz Staff" text/logo in Deep Sea Teal (#0E7C86) — slightly different from customer app to denote staff context
- Subtle circular loading indicator below (teal)
- No user controls

## Interactions
- Auto-routes after session check:
  - Valid session → Order List (S03)
  - No session → Login (S02)
  - Network failure → Error (S08)

## States
- **Loading (default):** Logo + spinner, checking session via /auth/me
- **Error:** Network failure → S08 (No Connection) or fallback to Login

## Navigation
- **From:** App cold start
- **To:** S02 (Login) or S03 (Order List)

## What to Show
- Default loading state: white screen, centered teal "Muciriz Staff" wordmark with subtle circular progress below. Minimal, professional.
