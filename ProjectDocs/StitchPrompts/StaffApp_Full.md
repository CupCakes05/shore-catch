# Muciriz — Staff App (Full Prompt for Google Stitch)

> One prompt for the entire Staff App. 8 screens. Flutter, Android, mobile.

---

Design a complete mobile app UI/UX for **Muciriz Staff** (Staff App, Flutter, Android). This is a delivery staff app for order fulfillment — staff see orders for their assigned location, confirm orders, confirm delivery, confirm payment, and can cancel orders. They can also create Shop Sales for walk-in customers. Design ALL 10 screens below as one cohesive app.

---

## DESIGN SYSTEM

**Colors (strict 2–3 palette):**
- Background: White (#FFFFFF)
- Text: Black (#000000)
- Primary accent: Deep Sea Teal (#0E7C86) — buttons, active states, links, highlights
- Button text on accent: White
- No gradients, no heavy shadows. Clean and minimal.

**Typography:** One clean sans-serif. Clear hierarchy: title (bold), section header (medium), body (regular), caption (muted). English only.

**Layout:** Card-based order list. Clean detail screens. Generous whitespace.

**Navigation:** Simple — bottom nav or top app bar tabs: Orders (home), Profile, Settings. "Add Product" entry only shown if staff has permission.

**Components:**
- Buttons: primary (filled teal), secondary (outline), destructive (red)
- Order cards: subtle border, status chip prominent
- Status chips: Pending (amber), Order Confirmed (blue), Delivered (teal), Completed (green), Cancelled (muted red)
- Inputs: outlined with floating label
- Bottom sheets: for payment method selection
- Toasts: success/error
- Confirm dialogs: for all state-change actions

**States (every screen):**
- Loading: skeleton shimmer
- Empty: illustration + message
- Error: icon + message + "Retry"
- Success: actual data

---

## SCREENS

---

### S01 — Splash
**Purpose:** Branding + session check.

**UI:**
- Centered: "Muciriz" logo with "Staff" subtitle
- White bg, teal circular loader

**Behavior:** Valid session → Order List (S03). No session → Login (S02). Error → offline screen (S08).

---

### S02 — Login
**Purpose:** Staff authentication (phone + password).

**UI:**
- App bar: "Staff Login"
- Form:
  - Phone number (+91, phone keyboard)
  - Password (show/hide toggle)
- "Login" button (primary, full-width, disabled until filled)
- Below: muted text "Forgot password? Contact your admin."
- Error message area

**Interactions:** Fill → Login → loader → success → S03. Invalid → "Invalid phone or password". Deactivated → "Account deactivated. Contact admin." Network → toast + retry.

**States:** Idle, Loading (spinner), Error, Success.

---

### S03 — Order List (Home)
**Purpose:** Staff home. Orders for assigned service location.

**UI:**
- App bar: "Orders" + location badge (e.g., "📍 Kochi North"), overflow menu
- **Status filter tabs** (horizontal scroll): All | Pending | Confirmed | Delivered | Completed
  - Active tab: teal underline
- **Order cards** (vertical list): each shows:
  - Order # + date
  - Customer name
  - Delivery time slot
  - Item count + total (₹)
  - Payment preference icon (Gpay/Cash)
  - Status chip
- Sort: pending/oldest first
- Pull-to-refresh
- **FAB** (floating action button, bottom-right): "+" for Add Product — ONLY shown if staff has add_products permission
- Bottom nav: Orders (active), Profile, Settings

**Interactions:** Tap order → S04. Filter tabs. Pull refresh. FAB → S05.

**States:** Loading (skeleton cards), Empty ("No orders right now"), Error (retry), Success.

---

### S04 — Order Detail (Fulfillment)
**Purpose:** Core working screen. View order + perform fulfillment actions step-by-step.

**UI:**
- App bar: "Order #M2026#00042" + back arrow
- **Status timeline** (vertical stepper):
  - Pending → Order Confirmed → Delivered → Completed
  - Reached: teal circle + connecting line; future: gray
  - Timestamps on reached steps
- **Customer info section:**
  - Name
  - WhatsApp number (tappable → opens WhatsApp/dialer)
  - Delivery address + nearest landmark
- **Order info:**
  - Delivery time slot
  - Items table: product name, weight, qty, price, line total
  - Order total (₹)
  - Payment preference: "Gpay on Delivery" or "Cash on Delivery"
- **Action button area (ONE button at a time, based on current status):**
  - **Pending →** "Confirm Order" (primary, full-width, teal)
  - **Order Confirmed →** "Confirm Delivery" (primary)
  - **Delivered →** "Confirm Payment" (primary) — tapping opens bottom sheet:
    - "How was payment collected?"
    - Two option cards: "Gpay" | "Cash"
    - "Confirm" button in sheet
  - **Completed →** no button; green "✓ Order Complete" label
  - **Cancelled →** muted text "Order was cancelled by customer"
- Each action → confirm dialog first ("Confirm this order?")

**Interactions:**
- "Confirm Order" → dialog → Yes → loader → status advances → toast "Order confirmed" → timeline updates
- "Confirm Delivery" → dialog → loader → toast "Delivery confirmed"
- "Confirm Payment" → bottom sheet (Gpay/Cash) → Confirm → loader → Completed → toast "Payment confirmed!"
- Tap WhatsApp number → device WhatsApp/dialer
- Error → toast "Something went wrong"

**States:** Loading (skeleton), Error (action failure toast), Success (updated timeline).

---

### S05 — Add Product (Permission-Gated)
**Purpose:** Authorized staff can add/edit products for admin approval.

**UI (only accessible if staff has add_products permission):**
- App bar: "Add Product" + back
- **Category dropdown** (only staff's allowed categories)
- Product form:
  - Product Name (text, required)
  - Gross Weight (number + unit)
  - Net Weight (number, ≤ Gross)
  - Price ₹ (number, > 0)
  - Image upload (button → camera/gallery; shows preview thumbnail)
- **"Submit for Approval"** button (primary, full-width)
- **"My Submissions" section** (below form, scrollable):
  - List: product name, category, status badge:
    - 🟡 "Pending Approval" (amber chip)
    - 🟢 "Approved" (green chip)
    - 🔴 "Rejected" (red chip) + "Edit & Resubmit" action
  - Tap rejected → pre-fills form for edit + resubmit

**Interactions:** Fill form → upload image (progress bar) → Submit → loader → toast "Submitted for approval" → form resets → item appears in submissions with Pending badge. Validation: Net ≤ Gross, price > 0, name required.

**States:** Idle (blank form), Loading (submit/upload), Error (validation inline, server toast), Success (form reset + submission listed). Submissions empty: "No submissions yet."

---

### S06 — Profile
**Purpose:** Staff profile info (read-only).

**UI:**
- App bar: "Profile" + back
- Info display:
  - Name
  - Phone number
  - Assigned location (chip)
  - Permissions list (chips: "Orders", "Confirm Order", "Confirm Delivery", "Confirm Payment", "Add Products")
- No edit capability (Admin manages staff accounts)

---

### S07 — Settings
**Purpose:** Preferences, legal links, logout.

**UI:**
- App bar: "Settings" + back
- Grouped list:
  - Notifications toggle (new-order alerts)
  - Legal: Privacy Policy, Terms, About, Contact (links → webview)
  - Logout (red text) → confirm dialog → clears session → S02
- App version (muted, bottom)

---

### S08 — Error / Offline
**Purpose:** Global error and no-connection fallback.

**UI:**
- Centered: offline illustration + "No internet connection" or "Something went wrong"
- "Retry" button (primary)

---

## NAVIGATION MAP

```
Splash (S01)
  ├→ Login (S02) → Order List (S03)
  └→ Order List (S03) [auto-login]

Main:
  Order List (S03) → Order Detail (S04)
  Order List (S03) → Add Product (S05) [if permitted]

Nav:
  Orders (S03) | Profile (S06) | Settings (S07)

Errors:
  Any network fail → S08
```

## WHAT TO SHOW

Design all 8 screens. Additionally show these key states/interactions:
1. Order Detail (S04) in **Pending** state — showing "Confirm Order" button
2. Order Detail (S04) in **Delivered** state — showing "Confirm Payment" button with the **payment method bottom sheet open** (Gpay/Cash options visible)
3. Order Detail (S04) in **Completed** state — green checkmark, no buttons
4. Order List (S03) with status filter on "Pending" (amber tab active, showing only pending orders)
5. Order List (S03) empty state
6. Add Product (S05) with "My Submissions" showing mixed statuses (one pending, one approved, one rejected)
7. Login (S02) with error state ("Invalid phone or password")
