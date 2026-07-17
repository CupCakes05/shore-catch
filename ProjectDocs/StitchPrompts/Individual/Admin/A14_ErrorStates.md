# A14 — Error States | Muciriz Admin Panel

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Next.js web. Fixed left sidebar + top bar.

## Screen
**App:** Admin Panel (Next.js, web)
**Purpose:** Global web error handling — 404, server errors, session expiry, fresh system guidance.

## UI Elements — 6 State Variants:

### 1. 404 Not Found
- Centered content (with sidebar still visible):
  - "404" large number (teal, faded)
  - Illustration (magnifying glass / broken page)
  - Headline: "Page not found"
  - Body: "The page you're looking for doesn't exist or has been moved."
  - "Back to Dashboard" link/button (teal)

### 2. Server Error (5xx)
- Centered content:
  - Warning illustration
  - Headline: "Something went wrong"
  - Body: "Our servers are having trouble. Please try again."
  - "Retry" button (teal filled)

### 3. Session Expired (401)
- Full-page (no sidebar):
  - Lock illustration
  - Headline: "Session expired"
  - Body: "Please sign in again to continue."
  - Redirects automatically to A01 (Login) after 3s, or "Sign In" button

### 4. Forbidden (403)
- Centered content:
  - Shield/lock illustration
  - Headline: "Access denied"
  - Body: "You don't have permission to view this page."
  - "Back to Dashboard" button

### 5. Fresh System (No Data)
- Dashboard area:
  - Welcome illustration (waving hand)
  - Headline: "Welcome to Muciriz!"
  - Body: "Get started by creating your first Service Location. Everything else builds from there."
  - "Create Service Location" button (teal filled, prominent)

### 6. Network Offline
- **Top banner** (amber/yellow, persistent, full-width above content):
  - "⚠️ You're offline — check your internet connection"
  - "Retry" text button (right side)

## Interactions
- Retry → reattempts failed request
- Back to Dashboard → A02
- Sign In → A01
- Create Service Location → A03
- Offline banner persists until connection restored

## States
- These ARE the global error states. Each has clear recovery.

## Navigation
- **From:** Any route/screen
- **To:** Dashboard (A02), Login (A01), or Locations (A03)

## What to Show
- Primary: 404 page with sidebar visible, centered "Page not found" with Back to Dashboard link
- Secondary: Fresh System "Welcome to Muciriz!" with Create Location CTA
