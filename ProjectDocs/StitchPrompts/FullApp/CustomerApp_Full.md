# Muciriz — Customer App (Full UI/UX Prompt for Google Stitch)

## DESIGN SYSTEM

**Project:** Muciriz — local fresh-goods ecommerce (fish, vegetables, meat)
**Platform:** Flutter Android mobile app
**Colors:** Background White (#FFFFFF), Text Black (#000000), Accent Deep Sea Teal (#0E7C86), Button text on accent: White. No gradients, no heavy shadows. Clean and minimal.
**Typography:** One clean sans-serif family. English only. Clear hierarchy: screen title, section header, body, caption.
**Components:** Cards (categories, products flip-cards, orders, offers), Status chips (Pending=amber, Confirmed=blue, Delivered=teal, Completed=green, Cancelled=muted red), outlined inputs, toasts, confirm dialogs.
**States:** Every screen needs Loading (skeleton shimmer), Empty (illustration + message + CTA), Error (retry), Success.
**Navigation:** Bottom nav with 4 tabs: Home, Cart (badge count), Orders, Profile. Active tab = teal accent.

---

## NAVIGATION MAP

```
Splash (C01) → Onboarding (C02, first-launch only) → Registration (C03) → OTP (C04) → Category Home (C05)
                                                                                              ↓
Bottom Nav: Home (C05) | Cart (C07) | Orders (C09) | Profile (C12)
                                                                                              ↓
C05 → C06 (Product List) → C07 (Cart/Checkout) → C08 (Order Confirmation) → C09/C10
C05 → C11 (Offers, via banner)
C12 → C13 (Settings) → C15 (Static/Legal)
App bar bell → C14 (Notifications)
Any screen → C16 (Error States)
```

---

## SCREENS

### C01 — Splash Screen
**Purpose:** Launch screen with branding, checks session for auto-login.
**UI Elements:**
- White background, centered Muciriz logo/wordmark in Deep Sea Teal (#0E7C86)
- Subtle circular loading indicator below logo
- No user controls

**Interactions:**
- Auto-routes: valid session → C05 (Category Home); no session + first launch → C02 (Onboarding); no session + onboarded → C03 (Registration)
- Network failure → C16 error state

**States:**
- Loading (default): checking session via /auth/me
- Error: network failure → route to error or fallback to Registration

---

### C02 — Onboarding (First Launch Only)
**Purpose:** 2-3 swipeable slides introducing Muciriz to first-time users.
**UI Elements:**
- Full-screen carousel with page indicator dots at bottom
- Each slide: centered illustration, bold headline, one-line body text
  - Slide 1: "Browse categories in your area" (category grid illustration)
  - Slide 2: "Order fresh goods, choose your delivery time" (delivery illustration)
  - Slide 3: "Pay on delivery — GPay or Cash" (payment illustration)
- "Skip" text button (top-right, muted)
- "Next" button (bottom-center, teal filled) on slides 1-2
- "Get Started" button (bottom-center, teal filled) on last slide

**Interactions:**
- Swipe left/right between slides
- Skip or Get Started → C03 (Registration)
- Sets local flag so onboarding never shows again

**States:**
- Fully static/local, no network states needed

---

### C03 — Registration
**Purpose:** Create customer account with delivery details and location selection.
**UI Elements:**
- App bar: "Create Account" title
- Form fields (outlined inputs, labeled):
  - Name (text, required)
  - WhatsApp Number (phone keyboard, +91 prefix, required, unique)
  - Delivery Address (multiline, required)
  - Nearest Landmark (text, required)
  - Service Location (dropdown, required — populated from /locations)
- Consent text: "By registering, you agree to our Terms and Privacy Policy" with teal hyperlinks
- "Register" button (teal filled, full-width, disabled until all fields valid)

**Interactions:**
- Fill form → tap Register → loader on button → POST /auth/register
- If number already verified → auto sign-in (no OTP), route to C05
- New number → route to C04 (OTP Verification)
- Tap Terms/Privacy → opens in-app webview (C15)

**States:**
- Idle: blank form
- Loading: button shows circular indicator, form disabled
- Error: inline field errors (red text below field); "Number already exists" if duplicate; network error toast
- Success: navigate to C04 or C05
- Location dropdown: own loading shimmer; empty state if no locations configured

---

### C04 — OTP Verification
**Purpose:** Verify WhatsApp number ownership via OTP (WhatsApp Business API primary, SMS fallback).
**UI Elements:**
- Centered layout
- Masked number display: "Code sent to +91 •••• ••1234 via WhatsApp"
- Helper text: "Check your WhatsApp messages for the verification code"
- 6-digit OTP input boxes (auto-advance on entry, paste support)
- "Verify" button (teal filled, disabled until 6 digits entered)
- "Resend Code" text button with countdown timer (e.g., "Resend in 0:45")
- "Edit Number" text link (navigates back to C03)

**Interactions:**
- Enter code → Verify → POST /auth/verify-otp
- Wrong code → inline error "Invalid code, please try again"
- Expired → "Code expired" + enable Resend
- Resend → new OTP sent, countdown resets
- Edit Number → back to Registration form

**States:**
- Idle: waiting for input
- Loading: verifying (button loader)
- Error: invalid/expired code; too many attempts lockout
- Success: session cached → navigate to C05 (Category Home) with welcome toast

---

### C05 — Category Home (Main Home Screen)
**Purpose:** Post-login home showing product categories for the customer's service location.
**UI Elements:**
- App bar: "Muciriz" logo/text (left), cart icon with badge count (right), notification bell (right)
- Top offers banner: horizontal carousel of active offer cards (image + title + "X% off"). Auto-rotates. Hidden if no offers.
- Two-column grid of category cards: each card shows category image (rounded corners) + category name below
- Current location label (small, below app bar or in subtitle)
- Bottom navigation bar: Home (active, teal), Cart (with badge), Orders, Profile

**Interactions:**
- Tap category card → C06 (Product List) for that category
- Tap offer banner → C11 (Offers)
- Tap cart icon → C07 (Cart)
- Tap bell → C14 (Notifications)
- Pull-to-refresh reloads categories + offers
- Bottom nav: navigate between Home/Cart/Orders/Profile

**States:**
- Loading: shimmer grid (6 placeholder cards) + banner shimmer
- Empty: "No categories available for [Location] yet. Check back soon!" + illustration
- Error: "Something went wrong" + Retry button
- Success: populated grid + banner (banner hidden if no offers)

---

### C06 — Product List (Category Products)
**Purpose:** Show all products in a category with flip-card interaction and stock indicators.
**UI Elements:**
- **Category scroll strip** (top): horizontal row of all categories (small image + name), slow-scroll. Back arrow (←) on the left returns to C05. Active category highlighted with teal underline.
- **Product cards** (vertical list, one per row, centered):
  - **Front face:** Product Name (bold), Gross Weight / Net Weight, Price (₹XX, bold), GST indicator if applicable ("incl. 5% GST" in small text), Stock badge ("Out of Stock — Pre-order available" in amber if stock=0)
  - **Back face (on tap):** Full product image
  - **Quantity control** below card: "Add" button (teal outline). Once added: stepper (− count +). If out of stock: "Pre-order" button instead of "Add".
- App bar: category name title, cart icon with badge

**Interactions:**
- Tap card → flips to show image (smooth 3D flip animation); tap again → flips back
- Tap Add/Pre-order → item added to cart, badge increments, brief toast "Added to cart"
- Stepper: increment/decrement quantity; at 0 → remove from cart
- Tap category in strip → reloads product list for that category
- Tap ← arrow → C05 (Category Home)
- Tap cart icon → C07
- Pull-to-refresh

**States:**
- Loading: skeleton cards (3 placeholder) + strip shimmer
- Empty: "No products in this category yet." + illustration
- Error: Retry
- Success: list of flip cards
- Per-card: image loading spinner on flip (back face)

---

### C07 — Cart / Checkout
**Purpose:** Review cart, select delivery date+time, choose payment, view GST breakdown, place order.
**UI Elements:**
- App bar: "Cart" title, item count
- **Cart item list:** each row:
  - Product name, weight info, unit price
  - GST indicator ("incl. GST 5%" small text if applicable)
  - Quantity stepper (− count +)
  - Line total (right-aligned)
  - Remove (X) button (with confirm)
- **Out of stock alert banner** (amber, if any cart item is out of stock for today): "Some items are out of stock today. Select a future delivery date to pre-order."
- **"Recommended for you" section:** horizontal scrollable cards with quick-add button. Hidden if none.
- **Order Summary card:**
  - Subtotal (before GST, back-calculated)
  - GST breakdown lines (e.g., "GST 5%: ₹12", "GST 18%: ₹36")
  - **Total** (bold, large)
- **Delivery Date picker:** calendar-style date selector. Defaults to today (or next available). Future dates for pre-order. No date limit.
- **Delivery Time Slot dropdown:** options for selected date (per-location or global)
- **Payment Method:** segmented control / radio — "GPay on Delivery" | "Cash on Delivery"
- **GPay UPI section** (shown when GPay selected):
  - Company UPI ID displayed (e.g., "muciriz@upi")
  - "Pay via GPay" button (teal, opens UPI deep-link intent)
  - Note: "Staff will confirm payment on delivery"
- **"Place Order" button** (teal filled, full-width, sticky bottom). Shows "Pre-order" label if future date selected. Disabled until all selections made.

**Interactions:**
- Adjust quantities / remove items
- Quick-add recommended products (item moves to cart, disappears from recommendations)
- Select delivery date → time slots reload for that date
- Select time slot
- Select payment method → if GPay, show UPI section
- Tap "Pay via GPay" → opens GPay app (UPI intent with amount)
- Place Order → POST /orders → success → C08; error → toast + retry

**States:**
- Empty cart: illustration "Your cart is empty" + "Browse Products" CTA; checkout section hidden
- Loading: placing order (button loader); time slots loading (dropdown shimmer)
- Error: price changed (409) → update cart; out of stock → suggest future date; network → retry toast
- Pre-order mode: "Pre-order" badge on Place Order button; delivery date highlighted
- Success: navigate to C08

---

### C08 — Order Confirmation
**Purpose:** Confirm order placed successfully, show summary, download invoice.
**UI Elements:**
- Success illustration (checkmark in teal circle) + "Order Placed Successfully!"
- Order number: "M2026#00001" (bold)
- **Pre-order badge** (if future date): "Pre-order confirmed for [date]" in teal chip
- Order summary: items list (name, qty, line total), delivery date + time, payment method, address
- GST breakdown: subtotal + GST lines + total
- **"Download Invoice"** button (outline teal, with PDF icon)
- **"View Order"** button (teal filled) → C10
- **"Continue Shopping"** text button → C05

**Interactions:**
- Download Invoice → opens/saves PDF (includes GST/non-GST split, Muciriz branding, invoice number)
- View Order → C10 (order detail)
- Continue Shopping → C05

**States:**
- Loading: brief (fetching invoice URL)
- Error: invoice download failure → retry; order still confirmed
- Success: default confirmed state

---

### C09 — Order History
**Purpose:** List customer's orders with status chips.
**UI Elements:**
- App bar: "My Orders"
- Optional segmented control: "Active" | "Past"
- Order cards (vertical list):
  - Order number + date
  - Item count + total (₹)
  - Status chip (colored: Pending=amber, Confirmed=blue, Delivered=teal, Completed=green, Cancelled=muted red)
  - Payment method icon/text
  - Pre-order badge if applicable
- Pull-to-refresh
- Bottom nav: Orders tab active (teal)

**Interactions:**
- Tap order card → C10 (Order Detail)
- Pull-to-refresh reloads list
- Filter tabs switch between active/past

**States:**
- Loading: skeleton cards (3-4)
- Empty: "No orders yet" + illustration + "Start Shopping" CTA → C05
- Error: Retry
- Success: order list

---

### C10 — Order Detail
**Purpose:** Full order details with status timeline, cancel option (while Pending), invoice download.
**UI Elements:**
- App bar: "Order #M2026#00001" (back arrow)
- Status chip (prominent, top)
- **Status timeline** (vertical):
  - Order Placed ✓ (date/time)
  - Order Confirmed (✓ or pending dot)
  - Delivered (✓ or pending dot)
  - Completed (✓ or pending dot)
  - Each completed step shows timestamp
- **Pre-order badge** (if applicable): "Delivery scheduled: [future date]"
- Itemized list: product name, weight, qty, unit price, line total
- Order summary: subtotal, GST breakdown, total
- Delivery: date + time slot + address + landmark
- Payment: method (preference + collected if completed)
- **"Download Invoice"** button
- **"Cancel Order"** button (red outline, shown ONLY when status = Pending). Hidden after Order Confirmed.

**Interactions:**
- Download Invoice → PDF
- Cancel Order → confirm dialog ("Are you sure? This cannot be undone.") → POST /orders/{id}/cancel → success toast, status updates to Cancelled
- Back → C09

**States:**
- Loading: skeleton
- Error: Retry
- Success: full detail with timeline
- Cancel: confirm dialog → loading → success/error

---

### C11 — Offers
**Purpose:** Full list of active offers for customer's location.
**UI Elements:**
- App bar: "Offers"
- Offer cards (vertical list):
  - Banner image (if uploaded)
  - Title (bold, e.g., "20% off Vegetables for Onam")
  - Description/message
  - Discount badge (e.g., "20% OFF" in teal chip)
  - Target: "All categories" / specific category/product name
  - Validity: "Valid till DD MMM YYYY"
  - "Shop Now" CTA button (navigates to relevant category/product)

**Interactions:**
- Tap offer / "Shop Now" → navigate to relevant category (C06) or product
- Pull-to-refresh

**States:**
- Loading: skeleton cards
- Empty: "No offers right now. Check back soon!" + illustration
- Error: Retry
- Success: offer list

---

### C12 — Profile
**Purpose:** View/edit customer personal and delivery information.
**UI Elements:**
- App bar: "Profile"
- Display mode: Name, WhatsApp Number, Delivery Address, Nearest Landmark, Service Location
- "Edit" button (top-right or floating)
- Edit mode: same fields as registration (editable), with "Save" and "Cancel" buttons
- Note: changing WhatsApp number requires OTP re-verification
- Note: changing Service Location clears the cart
- Quick links: "Order History" → C09, "Settings" → C13
- Bottom nav: Profile tab active (teal)

**Interactions:**
- Tap Edit → fields become editable
- Change number → OTP flow (C04)
- Change location → warning dialog about cart clearing → confirm → save
- Save → PATCH profile → success toast
- Cancel → revert to display mode

**States:**
- Loading: fetching/saving profile
- Error: validation inline; save failure toast
- Success: updated confirmation toast

---

### C13 — Settings
**Purpose:** App preferences, customer care contact, legal links, logout.
**UI Elements:**
- App bar: "Settings"
- Grouped list sections:
  - **Notifications:** toggle for push notifications
  - **Help & Support:**
    - "Customer Care" row with phone number (tap-to-call) and WhatsApp icon (tap-to-open WhatsApp). Hidden if not configured.
  - **Legal:**
    - Privacy Policy → webview
    - Terms & Conditions → webview
    - About Muciriz → webview
  - **Account:**
    - "Logout" (red text) with confirm dialog
  - App version at bottom (small, muted)

**Interactions:**
- Toggle notifications → save preference
- Tap phone → device dialer
- Tap WhatsApp → wa.me deep-link
- Tap legal links → in-app webview / browser (C15)
- Logout → confirm dialog → clear session → C01/C03

**States:**
- Mostly static
- Loading/Error on logout call
- Customer care hidden if admin hasn't configured it

---

### C14 — Notifications
**Purpose:** In-app notifications inbox (order updates, offers, announcements).
**UI Elements:**
- App bar: "Notifications" + "Mark all read" option
- Notification list:
  - Each: icon (order/offer/announcement), title, short message, timestamp, read/unread indicator (unread = bold + dot)
- Tap to expand/navigate

**Interactions:**
- Tap notification → expand detail or navigate to relevant screen
- Mark all as read
- Pull-to-refresh

**States:**
- Loading: skeleton list
- Empty: "No notifications yet" + bell illustration
- Error: Retry
- Success: notification list

---

### C15 — Static / Legal Pages
**Purpose:** Privacy Policy, Terms, About — loaded from admin-hosted canonical URLs.
**UI Elements:**
- App bar: page title (e.g., "Privacy Policy") + back arrow
- WebView / embedded browser showing the hosted page content
- Loading indicator while page loads

**Interactions:**
- Read content
- Back → return to previous screen

**States:**
- Loading: progress bar while webview loads
- Error: "Page couldn't be loaded" + Retry
- Success: content displayed

---

### C16 — Error States (Global)
**Purpose:** Global error handling screens for the entire app.
**UI Elements for each state:**
- **No Connection:** cloud-offline illustration, "You're offline", "Check your connection and try again", "Retry" button (teal)
- **Server Error (5xx):** warning illustration, "Something went wrong", "We're working on it. Please try again.", "Retry" button
- **Session Expired (401):** lock illustration, "Session expired", "Please sign in again", "Sign In" button → C03
- **Not Found (404):** search illustration, "Not found", "Back to Home" button → C05
- **Maintenance:** tools illustration, "We're updating things", "Please check back shortly", "Retry" button

**Interactions:**
- Retry → reattempts the failed action
- Sign In → clears invalid session, routes to C03
- Back to Home → C05

---

## WHAT TO SHOW

Generate the following key screens with full fidelity:
1. **C05 Category Home** — success state with 6 category cards in 2-column grid, offers banner with one offer, bottom nav, cart badge showing "3"
2. **C06 Product List** — showing 3 product cards (one flipped to image), category strip at top, one item showing "Out of Stock — Pre-order" badge, quantity stepper visible on one card
3. **C07 Cart/Checkout** — 3 items in cart, GST breakdown visible, delivery date picker showing today selected, time slot dropdown, GPay selected with UPI ID shown, "Pay via GPay" button visible, Place Order button enabled
4. **C08 Order Confirmation** — success state with pre-order badge, invoice download button
5. **C10 Order Detail** — status timeline showing Order Confirmed (2 steps complete), Cancel button hidden (past Pending), pre-order badge visible
6. **C01 Splash** — clean loading state
7. **C03 Registration** — form with all fields, location dropdown open
8. **C16 Error** — "No connection" state

For all screens: white background, teal accents, clean minimal aesthetic, proper spacing, realistic sample data (fish/vegetable/meat product names, Indian pricing in ₹).
