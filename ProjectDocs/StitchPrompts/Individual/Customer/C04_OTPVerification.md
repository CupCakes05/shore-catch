# C04 — OTP Verification | Muciriz Customer App

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Flutter Android.

## Screen
**App:** Customer (Flutter/Android)
**Purpose:** Verify WhatsApp number ownership via OTP. Sent via WhatsApp Business API (primary) with AWS SNS SMS fallback.

## UI Elements
- Centered layout, white background
- Teal WhatsApp/message icon at top
- Masked number display (bold): "Code sent to +91 •••• ••1234 via WhatsApp"
- Helper text (muted): "Check your WhatsApp messages for the 6-digit verification code"
- **6-digit OTP input:** 6 individual square boxes, large font, auto-advance on entry, paste support, teal bottom border on active box
- **"Verify"** button (teal filled, full-width, disabled until 6 digits entered)
- **"Resend Code"** text button (muted, with countdown timer: "Resend in 0:45") — enabled after countdown
- **"Edit Number"** text link (teal) — navigates back to Registration

## Interactions
- Enter digits → auto-advance between boxes; paste fills all
- 6 digits entered → Verify button enables
- Tap Verify → loader → POST /auth/verify-otp
- Wrong code → inline error below boxes "Invalid code. Please try again." (red)
- Expired code → "Code expired" message + Resend enabled
- Too many attempts → "Too many attempts. Try again later." lockout message
- Resend → sends new OTP, countdown resets
- Edit Number → back to C03 (Registration)

## States
- **Idle:** Waiting for input, Resend countdown running
- **Loading:** Verifying (button loader)
- **Error:** Invalid code (inline red); Expired (message + resend); Lockout (message)
- **Success:** Session cached → navigate to C05 (Category Home) with "Welcome!" toast

## Navigation
- **From:** Registration (C03)
- **To:** Category Home (C05) on success; back to C03 on Edit Number

## What to Show
- Default state: 6 empty OTP boxes, masked number shown, countdown timer at "0:32", Verify button disabled (muted)
- Error variant: 6 boxes filled with wrong code, red error text "Invalid code. Please try again." below boxes
