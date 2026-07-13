# A14 — Error / Empty / 404 (Web)

**App:** Admin Panel (Next.js, web)

## Purpose
Global web error, empty, and not-found handling so the console degrades gracefully.

## Users
Main Admin.

## Covered States
| State | Trigger | UI | Action |
|-------|---------|----|--------|
| 404 Not Found | Unknown route | "Page not found" + link home | Go to Dashboard |
| Server error (5xx) | Backend/API failure | "Something went wrong" + Retry | Retry |
| Session expired (401) | Expired session | Redirect to Login | Re-login |
| Forbidden (403) | Unauthorized action | "You don't have access" | Back |
| Global empty | Fresh system | Guidance to create first Service Location | Create location |
| Network offline | Connectivity loss | Banner + retry | Retry |

## Key UI Elements
- Illustration/icon, headline, body, primary/secondary actions, consistent with the minimal design.

## Actions
- Retry, navigate home, re-login.

## Business Rules
- On 401 redirect to Login (A01).

## Responsive / Accessibility
- Clear messaging, keyboard-focusable actions, not color-only.

## Dependencies
- Global error boundary / interceptor.
