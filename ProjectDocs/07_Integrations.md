# Muciriz — Integrations

> What the system integrates with — and deliberately does NOT — for the trial.

## 1. Explicitly Excluded (Out of Scope)

| Integration | Status | Reason |
|-------------|--------|--------|
| Online payment gateway (Razorpay/Stripe/etc.) | ❌ Excluded | Trial uses Gpay-on-Delivery / Cash-on-Delivery only; no online processing |
| Google Maps / geolocation | ❌ Excluded | Delivery uses text address + nearest landmark; no map |
| ERP / external logistics | ❌ Excluded | Trial simplicity |

## 2. Payment Handling (UPI Display + Manual Transfer)

- Payment is **recorded, not processed.** The customer chooses an intent (Gpay on Delivery or Cash on Delivery); the staff records the **actually collected** method at Payment Confirm, which moves the order to **Completed**.
- **GPay/UPI ID display (Confirmed — Requirement #5):** The company's GPay/UPI ID (configured by Admin in system settings) is displayed on the checkout page (C07). A "Pay via GPay" button uses the **UPI intent URI** (`upi://pay?pa={upiId}&pn={merchantName}&am={amount}&cu=INR`) to deep-link to GPay on the customer's device.
- This is a **convenience link only** — there is NO payment gateway integration. The deep-link opens GPay with pre-filled details; actual payment verification/confirmation is still performed manually by staff on delivery.
- If the customer's device doesn't have GPay installed, the intent gracefully fails and the customer is informed to pay manually on delivery.

## 3. Confirmed Third-Party Integrations

| Integration | Status | Purpose |
|-------------|--------|---------|
| **WhatsApp Business API** (via Meta Cloud API / Twilio) | ✅ Confirmed (Requirement #1) | **Primary OTP delivery channel** — sends OTP codes to customer's WhatsApp for number verification |
| **AWS SNS** | ✅ Confirmed | **Fallback OTP delivery** (SMS) if WhatsApp delivery fails; also sends **SMS notifications** (Q18) |
| **Push provider (e.g., Firebase Cloud Messaging)** | ✅ Confirmed | Delivers **push notifications** for offers & order status (Q18) |

### WhatsApp Business API — OTP Delivery (Requirement #1)

**Purpose:** Send OTP verification codes directly to the customer's WhatsApp instead of (or in addition to) SMS.

**Integration approach:**
- Use **Meta Cloud API** (direct) or a provider like **Twilio WhatsApp** to send templated OTP messages.
- Requires a registered WhatsApp Business Account and an approved message template for OTP delivery.
- **Fallback strategy:** if WhatsApp delivery fails (e.g., customer doesn't have WhatsApp, delivery timeout), fall back to SMS OTP via AWS SNS.

**Architecture:**
```
Customer registers → Backend generates OTP
  → Attempt 1: Send OTP via WhatsApp Business API (template message)
  → If delivery confirmed → customer receives on WhatsApp
  → If delivery fails/times out → Fallback: Send OTP via AWS SNS (SMS)
```

**Configuration needed:**
- WhatsApp Business Account ID
- Phone Number ID (sending number)
- Access Token (API key)
- Approved OTP message template ID
- Fallback timeout threshold (e.g., 30 seconds)

**Customer experience:** OTP arrives as a WhatsApp message (faster, free for customer, no SMS charges). C04 screen notes that OTP is sent to WhatsApp.

## 4. UPI Deep-Link (Requirement #5)

**Purpose:** Allow customers to quickly open GPay with payment details pre-filled for convenience before delivery.

**Technical details:**
- UPI intent URI format: `upi://pay?pa={upiId}&pn={merchantName}&am={amount}&cu=INR`
- Parameters filled from: `SystemConfig.gpay_upi_id`, `SystemConfig.gpay_merchant_name`, and order total.
- This is a **device-level intent** (Android Intent), not a server-side API integration.
- No payment verification callback — staff confirms payment manually.

**Fallback:** If no UPI app handles the intent, show a toast: "No UPI app found. Please pay manually on delivery."

## 5. Required Internal Services (Not Third-Party)

| Service | Purpose |
|---------|---------|
| File/Image storage | Category & product image uploads (Admin/Staff), misc purchase receipts (Staff), served by URL |
| Invoice generator | Auto-generate downloadable invoice (PDF) with GST/non-GST split |
| OTP service | Generates/validates OTP codes server-side; delivery via WhatsApp Business API (primary) + AWS SNS (fallback) |
| Notifications fan-out | Writes to in-app inbox and dispatches push + SMS per notification |
| Stock management service | Tracks per-location stock, purchase records, profit/loss calculations |

## 6. Notification Delivery (Confirmed Q18)

- **In-app inbox (C14):** always populated; Customer App also pulls active offers from `/offers` for the top banner.
- **Push:** delivered via the push provider (e.g., FCM) for offers & order-status updates.
- **SMS:** delivered via **AWS SNS**.

## 7. Play Store Distribution & Hosted Privacy Policy (Confirmed)

- Both Flutter apps (Customer + Staff) are distributed via the **Google Play Store**. Both collect **personal data** — name, WhatsApp/mobile number, delivery address, and OTP (via WhatsApp Business API / AWS SNS).
- Google Play **requires a publicly hosted Privacy Policy URL** (a real, publicly reachable web link) on the store listing for any app collecting personal data. An in-app page alone is not sufficient.
- **Confirmed approach:** the **Next.js Admin Panel hosts public, unauthenticated legal pages** (`/privacy`, `/terms`, optional `/about`, `/contact`). The `/privacy` URL is the link supplied to the Play Console; these pages are the **canonical source** of the legal content.
- The **Play Data Safety form** must be filled to declare the collected data (name, mobile number, delivery address, phone-number-based OTP) and **must remain consistent** with the published Privacy Policy.
- Content is **static/hardcoded** in the Next.js app (consistent with Q19 — not an admin-managed CMS).
- **Open item:** the actual legal copy (Privacy Policy & Terms text) still needs to be authored/provided — see `Suggestions/00_OpenQuestions.md`.

## 8. Customer Care Contact (Requirement #6)

- Not a third-party API integration — uses **device intents** (tap-to-call, tap-to-open-WhatsApp).
- Phone number and WhatsApp number configured by Admin in `SystemConfig`.
- Customer app uses `tel:` URI for calling and `https://wa.me/{number}` for WhatsApp deep-link.

## 9. Potential Future Integrations (Suggestions Only — Not Confirmed)

Recorded in `Suggestions/03_TechnicalSuggestions.md`:
- Real UPI/payment gateway if the trial graduates to production.
- Maps for delivery routing if operations scale.
- WhatsApp Business API for transactional messages beyond OTP (order confirmations, delivery updates).
- Accounting/GST filing integration for tax compliance.
