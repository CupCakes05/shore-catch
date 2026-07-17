# Muciriz — Customer App (Full Prompt for Google Stitch)

> One prompt for the entire Customer App. 16 screens. Flutter, Android, mobile.

---

Design a complete mobile app UI/UX for **Muciriz** (Customer App, Flutter, Android). This is a local fresh-goods ecommerce app where customers browse categories, add products to cart, and order for delivery. Design ALL 16 screens below as one cohesive app with connected navigation.

---

## DESIGN SYSTEM

**Colors (strict 2–3 palette):**
- Background: White (#FFFFFF)
- Text: Black (#000000)
- Primary accent: Deep Sea Teal (#0E7C86) — buttons, active states, links, highlights
- Button text on accent: White
- No gradients, no heavy shadows. Clean and minimal.

**Typography:** One clean sans-serif (Inter/Poppins/system). Clear hierarchy: title (bold, large), section header (medium), body (regular), caption (small, muted). English only.

**Layout:** Generous whitespace. Cards-based (category cards, product flip cards, order cards, offer cards). Consistent 8/12/16px spacing and corner radius.

**Navigation:** Bottom nav bar with 4 tabs: Home (categories), Cart (bag + badge), Orders (list), Profile (person). Active tab: teal; inactive: gray. Cart badge = red circle with count.

**Components:**
- Buttons: primary (filled teal, white text), secondary (outline), destructive (red outline)
- Cards: subtle border or very light shadow
- Status chips: Pending (amber), Order Confirmed (blue), Delivered (teal), Completed (green), Cancelled (muted red)
- Inputs: outlined with floating label, inline red error below
- Toasts: success (green), error (red), info (teal)
- Dialogs: for destructive confirmations

**States (every data screen needs all of these):**
- Loading: skeleton shimmer matching layout
- Empty: friendly illustration + message + CTA
- Error: icon + message + "Retry" button
- Success: actual data

**Motion:** Product card 3D flip, slow horizontal category strip auto-scroll, page slide transitions. Honor reduced-motion.

---

## SCREENS

---

### C01 — Splash
**Purpose:** Branding + auto-login session check on cold start.

**UI:**
- Centered Muciriz logo on white bg
- Subtle teal circular loader below logo

**Behavior:** Auto-routes — valid session → Home (C05); first launch → Onboarding (C02); no session → Registration (C03). Network error → error screen with retry.

---

### C02 — Onboarding (first launch only)
**Purpose:** 2–3 intro slides before registration.

**UI:**
- Full-screen carousel, 3 slides:
  - "Browse categories in your area" + icon
  - "Order fresh goods, choose delivery time" + icon
  - "Pay on delivery — Gpay or Cash" + icon
- Page indicator dots (teal active, gray inactive)
- "Skip" (top-right, muted), "Next" button → "Get Started" on last slide

**Interactions:** Swipe slides; Skip/Get Started → Registration. Marks onboarding seen locally.

---

### C03 — Registration
**Purpose:** New customer sign-up with delivery info.

**UI:**
- App bar: "Create Account"
- Form fields (outlined, labeled):
  - Name (text)
  - WhatsApp Number (phone, +91 prefix)
  - Delivery Address (multiline)
  - Nearest Landmark (text)
  - Service Location (dropdown — server-fetched)
- "Register" button (primary, full-width, disabled until valid)
- Consent text: "By registering, you agree to our Terms and Privacy Policy" (teal links)
- Inline validation errors per field

**Interactions:** Fill → validate on blur → Register → loader → sends OTP → navigates to C04. If number already verified (returning user) → signs in directly → C05. Dropdown has loading/empty states.

**States:** Idle (blank), Loading (button spinner), Error (field + server errors), Dropdown loading.

---

### C04 — OTP Verification
**Purpose:** Verify phone via SMS code.

**UI:**
- App bar: back arrow + "Verify Number"
- "Code sent to +91 •••• ••1234"
- 6 digit input boxes (auto-advance, paste support)
- "Verify" button (primary)
- "Resend code" with countdown timer
- "Wrong number? Edit" link

**Interactions:** Enter code → Verify → loader → success → C05 with welcome toast. Wrong code → shake + error. Expired → "Code expired. Tap Resend." Too many attempts → lockout. Edit → back to C03.

**States:** Idle, Loading (verifying), Error (invalid/expired), Resend countdown.

---

### C05 — Category Home (Main Home)
**Purpose:** Post-login home. Categories for the customer's service location + offers banner.

**UI:**
- App bar: "Muciriz" + cart icon with badge
- **Offers banner** (top): horizontal auto-carousel of active offers (e.g., "20% off Vegetables 🎉"). Tappable.
- **Category grid** (2 columns): cards with category image (top, rounded) + name (below, bold center)
- Service location as subtitle/chip
- Bottom nav (Home active)
- Pull-to-refresh

**Interactions:** Tap category → C06. Tap banner → C11. Tap cart → C07. Pull refresh.

**States:** Loading (shimmer grid + banner placeholder), Empty ("No categories for [Location] yet"), Error (retry), Success (grid + banner). Banner hidden if no offers.

---

### C06 — Product List
**Purpose:** Products in selected category. Flip cards + quantity add + category strip.

**UI:**
- **Category scroll strip** (top, below app bar):
  - Horizontal slow-scrolling strip of all categories (circle image + name)
  - Current category: teal border
  - **Back arrow ←** on left side → returns to C05
  - Tap any category → reload products for that category
- App bar: category name, cart icon + badge
- **Product cards (vertical list, centered, one per row):**
  - **Flip card:**
    - Front: Product Name (bold), Gross Weight, Net Weight, Price (₹)
    - Back: Product image (fills card)
    - Tap → 3D flip between front/back
  - **Below each card:** "+ Add" button (teal outline). After adding → stepper: [ − ] qty [ + ]
- Pull-to-refresh

**Interactions:** Tap card → flip. Tap "+ Add" → becomes stepper, cart badge increments, toast "Added". Stepper adjusts qty (0 removes). Tap strip category → reload. Back arrow → C05. Cart icon → C07.

**States:** Loading (skeleton cards + strip shimmer), Empty ("No products in this category"), Error (retry), Success. Per-card image loading on flip.

---

### C07 — Cart / Checkout
**Purpose:** Review cart, adjust, recommendations, delivery time, payment, place order.

**UI:**
- App bar: "Cart" + back arrow
- **Cart items list:** each row = product name + weight, unit price, stepper [ − qty + ], line total, remove (×)
- **"You may also like" section** (horizontal scroll, below items):
  - Small product cards: image + name + price + "Add" button
  - Hidden if empty or cart empty
- **Order summary:** Subtotal, Total (bold)
- **Delivery Time dropdown** (outlined, labeled "Select delivery time") — required
- **Payment method** — two selectable cards:
  - "Gpay on Delivery" (with icon)
  - "Cash on Delivery" (with icon)
  - Selected = teal border + check
- **"Place Order"** button (primary, sticky bottom, disabled until valid)
- "Continue shopping" link

**Interactions:** Adjust qty → totals update. Remove → confirm dialog. Quick-add recommendation → item moves to cart, disappears from recs. Select time + payment → Place Order enables. Tap Place Order → loader → success → C08. Price mismatch (409) → dialog with updated prices. Error → toast + retry.

**States:** Empty cart (illustration + "Your cart is empty" + "Start Shopping" CTA, no checkout controls), Loading (button spinner), Error (toast/inline), Success → navigates to C08.

---

### C08 — Order Confirmation
**Purpose:** Order placed successfully. Summary + invoice download.

**UI:**
- Large teal checkmark animation
- "Order Placed Successfully!" heading
- Order # (e.g., "#M2026#00042")
- Summary card: items (name × qty), total, delivery time, payment, address
- "Download Invoice" button (secondary)
- "View Order" link → C10
- "Continue Shopping" button (primary) → C05

**States:** Success (default), Loading (invoice URL), Error (download failure → retry).

---

### C09 — Order History
**Purpose:** List of all orders with status.

**Accessed via:** Bottom nav "Orders" tab.

**UI:**
- App bar: "My Orders"
- Segmented control: "Active" | "Completed"
- **Order cards** (list): order #, date, item count, total ₹, status chip, payment method
- Pull-to-refresh
- Bottom nav (Orders active)

**Interactions:** Tap card → C10. Toggle segment filters. Pull refresh.

**States:** Loading (skeleton), Empty ("No orders yet" + "Start Shopping"), Error (retry), Success.

---

### C10 — Order Detail
**Purpose:** Full single-order detail with status timeline.

**UI:**
- App bar: "Order #..." + back
- **Status timeline** (vertical stepper): Pending → Confirmed → Delivered → Completed (teal = reached, gray = pending, timestamps)
- Items list: name, weight, qty, price, line total
- Totals, delivery time, payment method (preference + collected if done)
- Address + landmark
- "Download Invoice" button
- **"Cancel Order"** button (red outline) — ONLY when Pending. Tap → confirm dialog → cancel → toast.

**States:** Loading, Error, Success. Cancel: confirm dialog → loading → success toast.

---

### C11 — Offers
**Purpose:** Full list of active promotions.

**Accessed from:** Banner on C05, Notifications.

**UI:**
- App bar: "Offers" + back
- **Offer cards** (list): banner image (if any), title (bold, e.g., "Onam Special 🎉"), discount ("20% off Vegetables" in teal), message, validity ("Valid till 15 Sep"), optional "Shop Now →" CTA
- Pull-to-refresh

**States:** Loading (shimmer), Empty ("No offers right now"), Error, Success.

---

### C12 — Profile
**Purpose:** View/edit profile and delivery info.

**Accessed via:** Bottom nav "Profile" tab.

**UI:**
- App bar: "Profile"
- Header: name (large) + WhatsApp number (verified ✓ icon)
- Editable sections: Name, WhatsApp (display only), Delivery Address, Nearest Landmark, **Service Location** (dropdown — change triggers warning: "Changing location will clear your cart")
- "Save Changes" button (appears on edit)
- Links: Settings, Help, Logout
- Bottom nav (Profile active)

**Interactions:** Edit fields → Save. Change location → warning dialog → confirm → saves, clears cart. Save → loader → toast.

**States:** Loading (skeleton), Error (save failure toast), Success.

---

### C13 — Settings
**Purpose:** Preferences, notifications, legal, logout.

**UI:**
- App bar: "Settings" + back
- Grouped list:
  - **Notifications:** toggles for In-app / Push / SMS
  - **Legal:** Privacy Policy, Terms, About, Help/FAQ, Contact (each → opens web URL)
  - **Account:** Logout (red text) → confirm dialog → clears session → C03
- App version (bottom, muted)

---

### C14 — Notifications
**Purpose:** Persistent inbox of announcements/offers/order updates.

**Accessed from:** Bell icon in app bar (with unread badge).

**UI:**
- App bar: "Notifications" + back
- List: teal dot (unread) + title (bold if unread) + message preview + timestamp
- "Mark all as read" (top-right)
- Tap → expands or navigates to relevant screen
- Pull-to-refresh

**States:** Loading (shimmer), Empty ("No notifications yet"), Error, Success (mix of read/unread).

---

### C15 — Static / Legal Pages
**Purpose:** Privacy Policy, Terms, About, FAQ, Contact.

**UI:** Full-screen webview or scrollable text page with title. Contact page: tap-to-call/WhatsApp links.

---

### C16 — Error / Offline
**Purpose:** Global error and no-connection screen.

**UI:**
- Centered: offline/error illustration + "No internet connection" or "Something went wrong"
- "Retry" button (primary)
- Shown when network fails during critical loads

---

## NAVIGATION MAP

```
Splash (C01)
  ├→ Onboarding (C02) → Registration (C03) → OTP (C04) → Home (C05)
  └→ Home (C05) [auto-login]

Bottom Nav:
  Home (C05) → Product List (C06) → Cart (C07) → Confirmation (C08)
  Cart (C07) [tab]
  Orders (C09) → Order Detail (C10)
  Profile (C12) → Settings (C13)

Other:
  Bell icon → Notifications (C14)
  Banner → Offers (C11)
  Legal links → Static Pages (C15)
  Network fail → Error (C16)
```

## WHAT TO SHOW

Design all 16 screens. Additionally show these key states/interactions:
1. Product List (C06): one card mid-flip (showing both faces in transition)
2. Cart (C07): populated with 3 items + recommendations section + delivery time + payment selected
3. Cart (C07): empty state
4. Category Home (C05): loading skeleton state
5. Order Detail (C10): status timeline at "Order Confirmed" step + Cancel button visible (Pending)
6. Order Detail (C10): Completed state (full green timeline, no action buttons)
7. Registration (C03): with validation errors showing
8. Notifications (C14): mix of read/unread items
9. Profile (C12): service location change warning dialog
