# Suggestions — Security & Privacy

> Suggestions only. Even for a trial, these reduce real risk. Since there's no payment gateway, the main risk surfaces are auth and permissions.

## Authentication & Identity
- ✅ **Confirmed (Q2):** customer WhatsApp number is **OTP-verified (AWS SNS)** and **unique**. Prevents impersonation, fake/duplicate accounts, and order spam.
- **Secure token storage** on device for cached auto-login; use expiry + refresh; clear on logout. Note: re-entry of an already-verified number skips OTP (Q3) — ensure the "already verified" check is server-side and rate-limited.
- **Rate-limit** login attempts (admin/staff) and OTP requests to resist brute force/abuse.
- **Shared phone number (Q14):** the same phone can be a customer and a staff account; enforce uniqueness **within** each identity type and keep the two auth paths isolated so a customer session can never assume staff privileges.
- Consider **2FA for the Admin** account — it controls the entire system.

## Authorization
- **Enforce all role + scope + permission checks server-side.** Never trust the client to hide/disable actions alone.
- Staff scope: verify location + allowed categories on every order/product request.
- Revoke access immediately when a staff account is deactivated or permissions change.

## Data Protection & Privacy
- Customer PII (name, WhatsApp number, address) — limit exposure to Admin; avoid logging PII; reference by key not value in logs.
- Publish a Privacy Policy describing data use (C15) and obtain consent at registration.
- Restrict customer-history lookup (A12) to Admin; consider an access/audit trail.

## Input & Upload Safety
- Validate/sanitize all inputs (server-side) — names, numbers, weights, prices.
- Restrict image uploads by type and size; scan/validate; store safely; serve via controlled URLs.
- Use parameterized queries / ORM to prevent injection.

## Operational
- **Audit log** of sensitive Admin/Staff actions (create/remove catalog, permission changes, order state changes) — aids trust and debugging.
- Use HTTPS everywhere; secure cookies/headers for the Next.js admin.
- Idempotent, guarded order-state transitions to prevent double-confirm/race issues.

## Network-Exposed Services Note
- The backend API is network-exposed and serves three clients. Ensure it is not left unauthenticated: every non-public endpoint must require a valid token and enforce role/scope. Public endpoints (e.g., `/locations` for the registration dropdown, legal pages) should be explicitly whitelisted and read-only.
