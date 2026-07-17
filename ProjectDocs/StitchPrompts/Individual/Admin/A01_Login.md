# A01 — Login | Muciriz Admin Panel

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Next.js web (responsive to tablet).

## Screen
**App:** Admin Panel (Next.js, web)
**Purpose:** Authenticate the main admin into the web console.

## UI Elements
- Centered card (max-width ~400px) on light gray/white background
- **Muciriz logo** (teal) centered above card
- Card contents:
  - Heading: "Admin Login"
  - Email/Username field (outlined, label "Email or Username")
  - Password field (outlined, label "Password", show/hide toggle)
  - **"Login"** button (teal filled, full-width)
  - **"Forgot Password?"** link (teal text, below button) → self-service password reset
- Error message area (red text, below form inside card)

## Interactions
- Enter credentials → Login → POST /auth/login/admin
- Forgot Password → email-based reset (enter email → receive link → set new password)
- Success → Dashboard (A02)
- Error → "Invalid credentials" / "Account locked" / network error (red text)
- Rate-limit: too many attempts → "Too many attempts. Try again later."

## States
- **Idle:** Empty form
- **Loading:** Button loader, fields disabled
- **Error:** Red error text inside card
- **Success:** Redirect to A02 (Dashboard)

## Navigation
- **From:** App root when unauthenticated
- **To:** Dashboard (A02)

## What to Show
- Idle state: centered card with Muciriz logo, empty email and password fields, Login button, Forgot Password link. Professional, minimal web form.
- Error variant: filled fields with red "Invalid email or password" message below them
