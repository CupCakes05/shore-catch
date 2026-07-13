# Eze-Cart — Integrations

> What the system integrates with — and deliberately does NOT — for the trial.

## 1. Explicitly Excluded (Out of Scope)

| Integration | Status | Reason |
|-------------|--------|--------|
| Online payment gateway (Razorpay/Stripe/etc.) | ❌ Excluded | Trial uses Gpay-on-Delivery / Cash-on-Delivery only; no online processing |
| Google Maps / geolocation | ❌ Excluded | Delivery uses text address + nearest landmark; no map |
| ERP / external logistics | ❌ Excluded | Trial simplicity |
| WhatsApp Business API automation | ❌ Excluded | WhatsApp number is used as contact/identity only; customer messaging uses SMS via AWS SNS, not WhatsApp automation |

## 2. Payment Handling (Non-Integration)

- Payment is **recorded, not processed.** The customer chooses an intent (Gpay on Delivery or Cash on Delivery); the staff records the **actually collected** method at Payment Confirm, which moves the order to **Completed**.
- **Confirmed (Q13):** "Gpay on Delivery" is a **purely manual personal transfer** at delivery time. The app does **not** integrate with Gpay APIs and does **not** display any UPI ID or QR code in-app.

## 3. Confirmed Third-Party Integrations

| Integration | Status | Purpose |
|-------------|--------|---------|
| **AWS SNS** | ✅ Confirmed | Sends **OTP codes** for customer mobile-number verification (Q2) and **SMS notifications** (Q18) |
| **Push provider (e.g., Firebase Cloud Messaging)** | ✅ Confirmed | Delivers **push notifications** for offers & order status (Q18) |

## 4. Required Internal Services (Not Third-Party)

| Service | Purpose |
|---------|---------|
| File/Image storage | Category & product image uploads (Admin/Staff), served by URL |
| Invoice generator | Auto-generate downloadable invoice (PDF recommended) |
| OTP service | Generates/validates OTP codes server-side; delivery via AWS SNS |
| Notifications fan-out | Writes to in-app inbox and dispatches push + SMS per notification |

## 5. Notification Delivery (Confirmed Q18)

- **In-app inbox (C14):** always populated; Customer App also pulls active offers from `/offers` for the top banner.
- **Push:** delivered via the push provider (e.g., FCM) for offers & order-status updates.
- **SMS:** delivered via **AWS SNS**.

## 6. Play Store Distribution & Hosted Privacy Policy (Confirmed)

- Both Flutter apps (Customer + Staff) are distributed via the **Google Play Store**. Both collect **personal data** — name, WhatsApp/mobile number, delivery address, and OTP (via AWS SNS).
- Google Play **requires a publicly hosted Privacy Policy URL** (a real, publicly reachable web link) on the store listing for any app collecting personal data. An in-app page alone is not sufficient.
- **Confirmed approach:** the **Next.js Admin Panel hosts public, unauthenticated legal pages** (`/privacy`, `/terms`, optional `/about`, `/contact`). The `/privacy` URL is the link supplied to the Play Console; these pages are the **canonical source** of the legal content.
- The **Play Data Safety form** must be filled to declare the collected data (name, mobile number, delivery address, phone-number-based OTP) and **must remain consistent** with the published Privacy Policy.
- Content is **static/hardcoded** in the Next.js app (consistent with Q19 — not an admin-managed CMS).
- **Open item:** the actual legal copy (Privacy Policy & Terms text) still needs to be authored/provided — see `Suggestions/00_OpenQuestions.md`.

## 7. Potential Future Integrations (Suggestions Only — Not Confirmed)

Recorded in `Suggestions/03_TechnicalSuggestions.md`:
- Real UPI/payment gateway if the trial graduates to production.
- Maps for delivery routing if operations scale.
