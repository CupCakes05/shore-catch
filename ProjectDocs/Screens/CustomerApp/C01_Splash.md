# C01 — Splash

**App:** Customer (Flutter/Android)

## Purpose
First screen on launch. Shows branding while the app checks stored session for auto-login and loads bootstrap data (locations list if needed).

## Users
Guest / returning Customer.

## Navigation
- **From:** app cold start.
- **To:**
  - Onboarding (C02) if first launch and never onboarded.
  - Category Home (C05) if valid session (auto-login).
  - Registration (C03) if no/invalid session and onboarding already seen.

## Key UI Elements
- App logo / name (Muciriz) centered.
- Minimal background (white), brand color accent.
- Subtle loading indicator.

## States
- **Loading:** default (checking session via `/auth/me`).
- **Error:** if bootstrap/session check fails due to network → route to Error/No-Connection (C16) with retry, or fall back to Registration.
- No empty state.

## Actions
- None user-initiated; automatic routing.

## Validation / Permissions
- None (pre-auth). Reads stored token only.

## Responsive / Accessibility
- Center-aligned; respects safe areas.
- Logo has accessible label; adequate contrast.

## Dependencies
- Session token store; `/auth/me`; `/locations` (optional prefetch).
