# C15 — Static / Legal Pages | Muciriz Customer App

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Flutter Android.

## Screen
**App:** Customer (Flutter/Android)
**Purpose:** Display legal/informational pages (Privacy Policy, Terms, About) loaded from admin-hosted canonical URLs.

## UI Elements
- App bar: page title (e.g., "Privacy Policy" / "Terms & Conditions" / "About Muciriz") + back arrow
- **WebView container** (full-screen below app bar):
  - Loads the canonical admin-hosted URL (e.g., /privacy, /terms, /about)
  - Linear progress indicator at top while loading (teal)
  - Content renders with readable typography, proper headings, links
- Contact page: may include tap-to-call and WhatsApp links for business contact

## Interactions
- Read/scroll content
- Tap links within content (external browser if needed)
- Back arrow → return to previous screen (C13 or C03)
- Pull-to-refresh / reload if error

## States
- **Loading:** Linear progress bar (teal) at top of webview area
- **Error:** "Page couldn't be loaded. Check your connection." + "Retry" button (for offline/host unreachable)
- **Success:** Content displayed in webview

## Navigation
- **From:** Settings (C13), Registration (C03) consent links
- **To:** Back to previous screen

## What to Show
- Privacy Policy page: app bar "Privacy Policy", webview showing structured content (headings, paragraphs about data collection, WhatsApp number usage, delivery address). Progress bar at ~70% (loading state).
- Also: Error state variant with offline message and retry
