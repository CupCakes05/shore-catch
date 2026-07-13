# C16 — Error / No-Connection / Maintenance

**App:** Customer (Flutter/Android)

## Purpose
Global fallback screens for connectivity loss, unexpected errors, session expiry, and maintenance — ensuring the app never shows a dead end.

## Users
Any.

## Navigation
- **From:** any screen when a global error condition occurs.
- **To:** retry back to the originating screen, or route to Registration on session expiry.

## Covered States
| State | Trigger | UI | Action |
|-------|---------|----|--------|
| No connection | Offline / network failure | Illustration + "You're offline" + Retry | Retry when back online |
| Server error (5xx) | Backend failure | "Something went wrong" + Retry | Retry |
| Session expired (401) | Invalid/expired token | Message + go to Registration/login | Re-auth |
| Not found (404) | Missing resource | "Not found" + back | Back |
| Maintenance | Backend in maintenance mode | Maintenance page | Retry later |

## Key UI Elements
- Illustration/icon, headline, short body, primary action (Retry / Re-login), secondary (Back/Home).

## States
- These ARE the states; each provides a clear recovery path.

## Actions
- Retry, back, re-authenticate.

## Business Rules
- On 401, clear invalid session and route to Registration (breaks auto-login loop safely).

## Responsive / Accessibility
- Clear, screen-reader-announced messages; large actionable buttons; not reliant on color alone.

## Dependencies
- Global network/error handling layer.
