# S08 — Error / No-Connection / Maintenance

**App:** Staff (Flutter/Android)

## Purpose
Global fallback screens for connectivity loss, server errors, session expiry, and maintenance.

## Users
Any staff.

## Covered States
| State | Trigger | UI | Action |
|-------|---------|----|--------|
| No connection | Offline | "You're offline" + Retry | Retry |
| Server error (5xx) | Backend failure | "Something went wrong" + Retry | Retry |
| Session expired (401) | Invalid/expired token | Message + go to Login | Re-login |
| Permission changed (403) | Admin revoked access mid-session | Message + refresh permissions/logout | Re-auth |
| Maintenance | Maintenance mode | Maintenance page | Retry later |

## Key UI Elements
- Illustration/icon, headline, body, primary action (Retry / Re-login).

## Actions
- Retry, re-authenticate.

## Business Rules
- On 401 clear session → Login.
- On 403 due to permission change, refresh permissions or route to login gracefully.

## Responsive / Accessibility
- Clear, announced messages; large buttons; not color-only.

## Dependencies
- Global network/error handling layer.
