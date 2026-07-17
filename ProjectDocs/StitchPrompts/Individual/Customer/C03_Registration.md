# C03 — Registration | Muciriz Customer App

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Flutter Android.

## Screen
**App:** Customer (Flutter/Android)
**Purpose:** Create new customer account with delivery details and location selection. Primary entry point — no separate login screen (returning users auto-login from splash).

## UI Elements
- App bar: "Create Account" title (black text, white bg)
- Scrollable form with outlined input fields:
  - **Name** (text input, label "Full Name", required)
  - **WhatsApp Number** (phone keyboard, "+91" prefix shown, label "WhatsApp Number", required, unique)
  - **Delivery Address** (multiline text area, label "Delivery Address", required)
  - **Nearest Landmark** (text input, label "Nearest Landmark", required)
  - **Service Location** (dropdown, label "Service Location", required, options from /locations — e.g., "Ernakulam", "Fort Kochi", "Mattancherry")
- Consent text below form: "By registering, you agree to our Terms and Privacy Policy" (Terms and Privacy Policy are teal hyperlinks)
- **"Register"** button (teal filled, full-width, rounded corners, white text). Disabled (muted) until all fields valid.

## Interactions
- Fill all fields → Register button enables
- Tap Register → button shows circular loader, form disables
- New number → POST /auth/register → route to OTP Verification (C04)
- Already-verified number → auto sign-in without OTP → Category Home (C05)
- Tap Terms/Privacy → opens webview (C15)
- Inline field errors appear below each field (red text) on validation failure
- Location dropdown has its own loading state (shimmer) while fetching locations

## States
- **Idle:** Blank form, Register button disabled
- **Loading:** Button loader + form disabled
- **Error:** Inline field errors (red text); "Number already registered" prompt; network error toast at bottom
- **Success:** Navigate to C04 (or C05 if auto-sign-in)
- **Location dropdown states:** Loading shimmer; Empty ("No locations available"); Populated (list)

## Navigation
- **From:** Onboarding (C02) or Splash (C01)
- **To:** OTP Verification (C04) or Category Home (C05)

## What to Show
- Success state: form filled with sample data (Name: "Priya Menon", WhatsApp: "+91 98765 43210", Address: "Flat 4B, Sea View Apartments, MG Road", Landmark: "Near Lulu Mall", Location dropdown open showing "Ernakulam" selected). Register button enabled (teal).
- Also show: error state variant with inline error on WhatsApp field ("This number is already registered")
