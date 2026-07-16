# C04 — OTP Verification (Required)

**App:** Customer (Flutter/Android)

> ✅ **Confirmed required (Q2).** Every first-time customer number must be OTP-verified. OTP is sent via **WhatsApp Business API** (primary channel) with **AWS SNS SMS** as fallback (Requirement #1). (Re-entry of an already-verified number skips this screen — Q3.)

## Purpose
Verify ownership of the WhatsApp/phone number provided at registration, preventing impersonation and duplicate/fake accounts. Numbers are unique.

## Users
Guest completing registration.

## Navigation
- **From:** Registration (C03).
- **To:** Category Home (C05) on success; back to Registration on failure/edit number.

## Key UI Elements
- Masked number display ("Code sent to +91 •••• ••1234 on WhatsApp").
- Note: "OTP sent to your WhatsApp. Check your messages." (with fallback note if SMS is used).
- OTP input (4–6 digit boxes).
- "Verify" button.
- "Resend code" with countdown timer (resend may use WhatsApp or SMS fallback).
- "Edit number" link.

## States
- **Loading:** verifying / resending.
- **Error:** wrong code, expired code, too many attempts (lockout message).
- **Success:** proceed to home.
- Resend disabled during countdown.

## Actions
- Enter code, verify (`/auth/verify-otp`).
- Resend after countdown.
- Edit number → back to C03.

## Validation / Permissions
- Numeric code length check; server verifies.

## Responsive / Accessibility
- Auto-advance between OTP boxes; paste support; screen-reader announces errors and countdown.

## Dependencies
- **WhatsApp Business API** (primary OTP delivery) + **AWS SNS** (SMS fallback) (see `07_Integrations.md`), `/auth/send-otp`, `/auth/verify-otp`.
