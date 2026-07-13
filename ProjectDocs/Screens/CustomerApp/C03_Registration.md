# C03 — Registration

**App:** Customer (Flutter/Android)

## Purpose
Create a new customer account and capture delivery + location data that scopes the entire catalog experience. This is the primary entry (no separate password login screen in the trial — returning users auto-login).

## Users
Guest (new customer).

## Navigation
- **From:** Onboarding (C02) or Splash (C01) when no valid session.
- **To:**
  - **OTP Verification (C04)** — always required for first-time verification (Q2).
  - Category Home (C05) after OTP success.
  - If a **logged-out user re-enters an already-verified number**, they are signed in directly **without OTP** (Q3) → Category Home (C05).

## Key UI Elements / Form Fields
| Field | Type | Validation |
|-------|------|-----------|
| Name | text | required, non-empty |
| WhatsApp Number | phone | required, valid format (+91), **UNIQUE + OTP-verified** (Q2), checked server-side |
| Delivery Address | multiline text | required |
| Nearest Landmark | text | required (part of delivery address per requirements) |
| Service Location | dropdown | required; options from `/locations` (Admin-managed) |

- Primary "Register" button (disabled until valid).
- Consent note with links to **Terms** and **Privacy Policy**. These point to the **canonical Admin-hosted public URLs** (`/terms`, `/privacy`) — opened in browser / in-app webview via C15 — which are also the Play Store Privacy Policy URL. Keeps one source of truth so legal edits don't require an app update.

## States
- **Loading:** while submitting (button shows loader, disabled).
- **Empty/Idle:** blank form.
- **Error:**
  - Field-level validation errors inline.
  - WhatsApp number already registered **and verified** → **sign in directly without OTP** (Q3), no error.
  - Network/server error → toast/banner with retry.
- **Success:** navigate onward; welcome toast.
- Service Location dropdown has its own loading/empty (no locations configured) state.

## Actions
- Fill fields; select service location.
- Submit → `POST /auth/register`.
- Tap legal links.

## Business Rules (Confirmed Q2/Q3/Q4)
- New number → OTP via AWS SNS → verify (C04) → account active → session cached for **auto-login**.
- Mobile numbers are **unique**.
- Re-entry of an **already-verified** number signs the user in **without OTP**.
- Chosen Service Location determines the catalog shown post-login (changeable later via Profile — Q12).

## Validation / Permissions
- Pre-auth. Client-side + server-side validation. Number must be unique and verified before the account is usable.

## Responsive / Accessibility
- Labeled inputs, error text associated with fields, adequate tap targets, keyboard type = phone for number field, dropdown accessible.

## Dependencies
- `/locations`, `/auth/register`, `/auth/send-otp`, `/auth/login/customer`, session store, AWS SNS (OTP).
