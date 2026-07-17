# Batch 2 — Customer Auth Flow (C01 → C02 → C03 → C04 → C05)

> First-time customer journey: app launch through to the home screen.

---

Design the **Customer App** (mobile, Android) first-time user flow — 5 screens connected as a journey:

## Flow: Splash → Onboarding → Registration → OTP → Category Home

---

### Screen 1: Splash (C01)
**Purpose:** First screen on cold start. Branding + session check.

**UI Elements:**
- Centered Eze-Cart logo/wordmark on white background
- Subtle circular loading indicator below the logo (teal accent)
- No other UI — purely automatic

**Behavior:**
- If valid cached session → skip to Category Home (C05)
- If first launch → Onboarding (C02)
- If no session but onboarding seen → Registration (C03)
- Network error → error screen with retry

**States:** Loading only (default state). Error → offline/retry screen.

---

### Screen 2: Onboarding (C02)
**Purpose:** 2–3 intro slides on first launch. Sets expectations.

**UI Elements:**
- Full-screen carousel with 3 slides:
  - Slide 1: Icon/illustration + "Browse categories in your area" + brief subtitle
  - Slide 2: Icon/illustration + "Order fresh goods, choose delivery time" + subtitle
  - Slide 3: Icon/illustration + "Pay on delivery — Gpay or Cash" + subtitle
- Page indicator dots at bottom (teal active, gray inactive)
- "Skip" text button (top-right, muted)
- "Next" button (primary, bottom) — changes to "Get Started" on last slide

**Interactions:**
- Swipe left/right between slides
- Skip → goes to Registration
- Get Started (last slide) → Registration
- Marks "onboarding seen" locally so it never shows again

**States:** Static content only, no loading needed.

---

### Screen 3: Registration (C03)
**Purpose:** New customer sign-up. Captures delivery info + service location.

**UI Elements:**
- App bar: "Create Account" title
- Form fields (outlined inputs with labels):
  - Name (text, required)
  - WhatsApp Number (phone input with +91 prefix, required, unique)
  - Delivery Address (multiline, required)
  - Nearest Landmark (text, required)
  - Service Location (dropdown, required — fetched from server)
- "Register" primary button (full-width, disabled until all valid)
- Below button: "By registering, you agree to our [Terms] and [Privacy Policy]" — links in teal
- Inline validation errors below each field in red

**Interactions:**
- Fill fields → validation runs on blur
- Service Location dropdown shows options from server (has its own loading spinner)
- Tap Register → button shows loader, disables → sends OTP → navigates to OTP screen
- If the number is already verified (returning user re-entering number) → signs in directly, skips OTP, goes to Category Home
- Network error → toast at bottom with retry

**States:**
- Idle: blank form
- Loading: button spinner while submitting
- Error: inline field errors + server errors (toast)
- Dropdown loading: spinner inside the dropdown while locations load
- Dropdown empty: "No service locations available" message

---

### Screen 4: OTP Verification (C04)
**Purpose:** Verify phone number ownership via SMS code (AWS SNS).

**UI Elements:**
- App bar: back arrow + "Verify Number"
- Text: "Enter the code sent to +91 •••• ••1234" (masked number)
- 6 individual digit boxes (auto-advance on input, paste supported)
- "Verify" primary button (full-width)
- "Resend code" text link with countdown timer (e.g., "Resend in 0:45")
- "Wrong number? Edit" text link → goes back to Registration

**Interactions:**
- Type digits → auto-advance to next box
- Paste full code → fills all boxes
- Tap Verify → loader → success → Category Home with welcome toast
- Wrong code → shake animation + "Invalid code, please try again" error
- Expired → "Code expired. Tap Resend."
- Resend → countdown resets, new code sent
- Edit number → back to Registration (C03)

**States:**
- Idle: empty boxes
- Loading: verifying (button spinner)
- Error: invalid/expired code message
- Resend disabled during countdown, enabled after
- Too many attempts: lockout message ("Too many attempts. Try again later.")

---

### Screen 5: Category Home (C05)
**Purpose:** The main home screen after login. Shows categories for the customer's service location + offers banner.

**UI Elements:**
- App bar: "Eze-Cart" title/logo (left), cart icon with red badge (right)
- **Offers banner carousel** at top: horizontal auto-scrolling cards showing active offers (e.g., "20% off Vegetables for Onam 🎉"). Tappable.
- **Category grid** below: 2-column card grid. Each card has:
  - Category image (rounded corners, fills card top)
  - Category name below image (centered, bold)
  - Subtle card border/shadow
- Pull-to-refresh gesture
- **Bottom navigation bar:** Home (active, teal), Cart (with badge), Orders, Profile
- Current service location shown as subtitle under app title or as a small chip

**Interactions:**
- Tap a category card → navigates to Product List (C06) for that category
- Tap offers banner → Offers list screen (C11)
- Tap cart icon → Cart/Checkout (C07)
- Pull down → refresh categories + offers
- Bottom nav tabs switch between main sections

**States:**
- Loading: shimmer skeleton (2-column grid of gray card shapes + banner placeholder)
- Empty: no categories → illustration + "No categories available for [Location] yet. Check back soon!"
- Error: error illustration + "Something went wrong" + "Retry" button
- Success: grid of category cards + offers banner (banner hidden if no active offers)

---

## Transitions between screens:
- Splash → Onboarding: instant (no animation, splash disappears)
- Onboarding → Registration: slide left
- Registration → OTP: slide left (forward navigation)
- OTP → Category Home: slide left + welcome toast ("Welcome to Eze-Cart!")
- Category Home is the persistent home (bottom nav stays)

## Show:
1. All 5 screens in their success/content state as a connected flow
2. The Registration screen in its error state (validation errors showing)
3. The OTP screen in its error state (wrong code)
4. The Category Home in its loading (skeleton) state
5. The Category Home in its empty state
