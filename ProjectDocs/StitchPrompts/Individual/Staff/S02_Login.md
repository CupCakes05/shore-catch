# S02 — Login | Muciriz Staff App

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Flutter Android.

## Screen
**App:** Staff (Flutter/Android)
**Purpose:** Authenticate staff member using phone + password (credentials set by Admin).

## UI Elements
- Centered card layout on white background
- "Muciriz Staff" heading (teal, above form)
- Subtitle: "Sign in to your account"
- **Phone field** (outlined input, phone keyboard, "+91" prefix, label "Phone Number")
- **Password field** (outlined input, show/hide toggle icon, label "Password")
- **"Login"** button (teal filled, full-width, disabled until both fields have input)
- Note below button (muted gray text): "Forgot password? Contact your Admin for a reset."
- Error message area (below button, red text for invalid credentials)

## Interactions
- Enter phone + password → Login button enables
- Tap Login → button shows loader, form disables → POST /auth/login/staff
- Success → S03 (Order List), loads assigned locations + permissions
- Error: "Invalid credentials" / "Account deactivated" / network error → red text message
- Show/hide password toggle

## States
- **Idle:** Empty form, Login button disabled (muted teal)
- **Loading:** Button circular loader, fields non-interactive
- **Error:** Red error text below button; fields remain filled
- **Success:** Route to S03

## Navigation
- **From:** Splash (S01) when no valid session
- **To:** Order List (S03)

## What to Show
- Idle state: clean centered form with empty phone and password fields, Login button disabled, "Forgot password? Contact your Admin" note visible. Professional, minimal.
- Error variant: fields filled, red text "Invalid phone or password" below button
