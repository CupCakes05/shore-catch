# Customer Registration & Auto-Login Flow

## Registration (New Customer) — Confirmed Q2
```
Splash (C01) → Onboarding (C02, first launch) → Registration (C03)
Customer enters: Name, WhatsApp Number, Delivery Address + Nearest Landmark, Service Location (dropdown)
→ Submit (server enforces UNIQUE number)
→ OTP sent via WhatsApp Business API (primary) / AWS SNS SMS (fallback) → OTP Verification (C04)  [REQUIRED for first-time verification]
→ Verify code → number marked verified
→ Session token cached on device
→ Category Home (C05) scoped to chosen Service Location
```

## Auto-Login (Returning Customer) — Confirmed Q4
```
Splash (C01) → check cached token → GET /auth/me
→ valid  → Category Home (C05)  (no login friction)
→ invalid/expired → Registration / number entry (C03)
```

## Re-entry of Already-Verified Number (skip OTP) — Confirmed Q3
```
Logged-out user enters mobile number → POST /auth/login/customer
→ number exists AND already verified → sign in DIRECTLY (no OTP) → Category Home (C05)
→ not verified / new → send OTP via WhatsApp Business API (primary) / AWS SNS (fallback) → OTP Verification (C04)
```

## Alternate / Failure
- **Already-verified number re-entered:** signed in without OTP (Q3) — no error.
- **No locations configured:** registration can't proceed; show message (Admin must add locations).
- **Validation errors:** inline per field.
- **Network / OTP delivery error:** retry (resend OTP); no account activated until verified.
- **Logout (Settings C13):** clears token → next launch shows number entry; re-entry of the verified number skips OTP.

## Dual Identity Note (Q14)
- The same phone number can also be a **Staff** account used in the Staff App (phone + password). The Customer App resolves the **customer** identity only; the two apps authenticate independently. See `01_UserRoles.md` §8.

## Security Notes
- Identity is now hardened: **OTP verification via WhatsApp Business API (primary) / AWS SNS (fallback) + unique numbers** (Q2). See `Suggestions/02_SecuritySuggestions.md`.
