# S08 — Error States | Muciriz Staff App

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Flutter Android.

## Screen
**App:** Staff (Flutter/Android)
**Purpose:** Global fallback error screens for the staff app.

## UI Elements — 5 State Variants:

### 1. No Connection
- Cloud-offline illustration (teal/gray line art)
- "You're offline" (bold headline)
- "Check your connection and try again" (muted body)
- "Retry" button (teal filled)

### 2. Server Error (5xx)
- Warning triangle illustration
- "Something went wrong"
- "Please try again in a moment"
- "Retry" button (teal filled)

### 3. Session Expired (401)
- Lock illustration
- "Session expired"
- "Please sign in again"
- "Sign In" button (teal filled) → S02

### 4. Permission Changed (403)
- Alert/shield illustration
- "Your permissions were updated"
- "Your admin has changed your access. Please sign in again."
- "Re-login" button (teal filled) → S02

### 5. Maintenance
- Tools/wrench illustration
- "Under maintenance"
- "We'll be back shortly"
- "Retry" button (teal outline)

## Interactions
- Retry → reattempts failed action
- Sign In / Re-login → clears session, routes to S02

## States
- These ARE the error states. Each has one clear recovery action.

## Navigation
- **From:** Any screen when error occurs
- **To:** Retry (same screen) or S02 (Login)

## What to Show
- Primary: "No Connection" state centered on white background with retry button
- Secondary: "Permission Changed" state (unique to staff app)
