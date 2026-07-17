# C16 — Error States | Muciriz Customer App

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Flutter Android.

## Screen
**App:** Customer (Flutter/Android)
**Purpose:** Global fallback error/empty screens for connectivity loss, server errors, session expiry, and maintenance.

## UI Elements — 5 State Variants:

### 1. No Connection
- Centered layout:
  - Cloud-with-X illustration (teal/gray line art)
  - Headline: "You're offline" (bold, black)
  - Body: "Check your internet connection and try again" (muted gray)
  - "Retry" button (teal filled, centered)

### 2. Server Error (5xx)
- Centered layout:
  - Warning triangle illustration (teal/gray)
  - Headline: "Something went wrong"
  - Body: "We're working on it. Please try again."
  - "Retry" button (teal filled)

### 3. Session Expired (401)
- Centered layout:
  - Lock illustration (teal/gray)
  - Headline: "Session expired"
  - Body: "Please sign in again to continue"
  - "Sign In" button (teal filled) → C03

### 4. Not Found (404)
- Centered layout:
  - Magnifying glass illustration
  - Headline: "Page not found"
  - Body: "The content you're looking for doesn't exist"
  - "Back to Home" button (teal filled) → C05

### 5. Maintenance
- Centered layout:
  - Tools/wrench illustration (teal/gray)
  - Headline: "We're updating things"
  - Body: "Muciriz will be back shortly. Thanks for your patience!"
  - "Retry" button (teal outline)

## Interactions
- Retry → reattempts the failed action
- Sign In → clears invalid session, routes to C03 (Registration)
- Back to Home → C05 (Category Home)

## States
- These ARE the error states. Each provides a clear single recovery path.

## Navigation
- **From:** Any screen when error condition occurs
- **To:** Retry (same screen), C03 (re-auth), or C05 (home)

## What to Show
- Primary: "No Connection" state — cloud illustration, headline, body text, Retry button. White background, plenty of whitespace, centered vertically.
- Secondary: "Session Expired" state with Sign In button
