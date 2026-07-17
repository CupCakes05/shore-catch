# Batch 5 — Staff App (S01 → S02 → S03 → S04 → S05)

> The complete delivery staff app: login through order fulfillment and optional product management.

---

Design the **Staff App** (mobile, Android, Flutter). This is a separate app from the Customer App but uses the same design system (white bg, black text, #0E7C86 accent). It's simpler — focused on order fulfillment. 5 main screens.

---

### Screen 1: Splash (S01)
**Purpose:** Staff branding + session check.

**UI Elements:**
- Centered logo/wordmark: "Eze-Cart Staff" (or "Eze-Cart" with a "Staff" subtitle)
- White background, subtle teal loading indicator
- Automatic routing: valid session → Order List; no session → Login

**States:** Loading only. Error → offline screen.

---

### Screen 2: Login (S02)
**Purpose:** Staff authentication with phone + password.

**UI Elements:**
- App bar: "Staff Login"
- Form:
  - Phone number field (with +91 prefix, keyboard: phone)
  - Password field (with show/hide toggle)
- "Login" primary button (full-width, disabled until both filled)
- Below button: muted text "Forgot password? Contact your admin." (not a link — just informational)
- Error message area (below form or as toast)

**Interactions:**
- Fill both fields → Login enables
- Tap Login → button loader → success → Order List (S03)
- Invalid credentials → inline error "Invalid phone or password"
- Deactivated account → "Account deactivated. Contact admin."
- Network error → toast with retry

**States:**
- Idle: empty form
- Loading: button spinner
- Error: error messages
- Success: navigate to Order List

**Design notes:**
- Same phone number may also be registered as a customer. The Staff App authenticates the staff identity independently. No confusion for the user — this is a separate app.
- No self-password-reset. Admin handles it.

---

### Screen 3: Order List (S03) — Staff Home
**Purpose:** Staff's main screen. Shows orders for their assigned service location.

**UI Elements:**
- App bar: "Orders" title + location label (e.g., "📍 Kochi North"), overflow menu (Profile, Settings)
- **Status filter tabs** (horizontal, scrollable): All | Pending | Confirmed | Delivered | Completed
  - Active tab: teal underline; others: gray
- **Order cards** (vertical list): each card shows:
  - Order number + date
  - Customer name
  - Delivery time slot
  - Item count + order total (₹)
  - Payment preference icon (Gpay/Cash)
  - **Status chip** (color-coded: Pending=amber, Confirmed=blue, Delivered=teal, Completed=green, Cancelled=muted red)
- Sort: pending/oldest first (default)
- Pull-to-refresh
- **Floating action button (FAB)** or nav entry for "Add Product" — ONLY shown if staff has `add_products` permission (hidden otherwise)
- Simple navigation: bottom bar or app bar menu with Orders (active), Profile, Settings

**Interactions:**
- Tap an order card → Order Detail (S04)
- Tap status filter → list filters
- Pull to refresh
- Tap FAB/Add Product → Add Product screen (S05)

**States:**
- Loading: skeleton cards
- Empty: "No orders right now" + illustration (scoped to their location/category)
- Error: retry
- Success: order list

---

### Screen 4: Order Detail — Fulfillment (S04)
**Purpose:** The core working screen. View order details and perform fulfillment actions step by step.

**UI Elements:**
- App bar: "Order #EZC-2024-0042" + back arrow
- **Status timeline** (vertical stepper, same style as customer app):
  - Pending → Order Confirmed → Delivered → Completed
  - Current step highlighted (teal filled); future steps gray
- **Customer info section:**
  - Name
  - WhatsApp number (tappable → opens device WhatsApp/dialer)
  - Delivery address + nearest landmark
- **Order info:**
  - Delivery time slot
  - Items table: product name, weight, qty, unit price, line total
  - Order total (₹)
  - Payment preference: "Gpay on Delivery" or "Cash on Delivery"
- **Action buttons (state-aware, shown one at a time based on current status):**
  - When **Pending**: → **"Confirm Order"** button (primary, teal, full-width)
  - When **Order Confirmed**: → **"Confirm Delivery"** button (primary)
  - When **Delivered**: → **"Confirm Payment"** button (primary) — on tap, shows a bottom sheet:
    - "How was payment collected?"
    - Two options: "Gpay" | "Cash" (radio/cards)
    - "Confirm" button in the bottom sheet
  - When **Completed**: no action button; shows "✓ Order Complete" in green
  - When **Cancelled**: shows "Order was cancelled by customer" in muted text
- Each action button has a confirm dialog before executing

**Interactions:**
- Tap "Confirm Order" → dialog "Confirm this order?" → Yes → loader → status advances → toast "Order confirmed" → timeline updates
- Tap "Confirm Delivery" → dialog → loader → status advances → toast
- Tap "Confirm Payment" → bottom sheet (Gpay/Cash selection) → Confirm → loader → status = Completed → toast "Payment confirmed. Order complete!"
- Tap WhatsApp number → opens WhatsApp/phone dialer
- Out-of-order attempt (shouldn't happen due to UI gating) → error toast
- Buttons hidden if staff lacks the specific permission

**States:**
- Loading: detail skeleton; action in-progress (button spinner)
- Error: action failure → toast "Something went wrong" + retry; out-of-order → "Invalid action for current status"
- Success: status updated, timeline advances, toast shown

---

### Screen 5: Add Product (S05) — Permission Gated
**Purpose:** Staff with permission can add/edit products for approval. Products go live only after admin approves.

**UI Elements:**
- App bar: "Add Product" + back arrow
- **Category dropdown** (limited to staff's allowed categories)
- Product form:
  - Product Name (text, required)
  - Gross Weight (number + unit label)
  - Net Weight (number + unit label, must be ≤ Gross)
  - Price ₹ (number, required)
  - Product Image (upload button → camera/gallery picker; shows preview)
- **"Submit for Approval"** button (primary, full-width)
  - Label makes clear it's not instant-live
- Below the form: **"My Submissions"** section — list of products this staff has submitted:
  - Each row: product name, category, status badge:
    - 🟡 "Pending Approval" (amber)
    - 🟢 "Approved" (green)
    - 🔴 "Rejected" (red) — with "Edit & Resubmit" action
  - Tap approved/pending → view only; Tap rejected → edit form to resubmit

**Interactions:**
- Fill form → image upload shows progress bar
- Tap Submit → loader → success toast "Submitted for approval" → form resets, item appears in "My Submissions" with Pending badge
- Validation: Net ≤ Gross, price > 0, name required, image recommended
- Tap a rejected product → pre-fills form for editing → "Resubmit for Approval"

**States:**
- Idle: blank form
- Loading: submit in progress; image uploading
- Error: validation inline; upload/server error toast
- Success: form resets; submission appears in list below
- Submissions empty: "No submissions yet"

---

## Transitions:
- Splash → Login: fade/instant
- Login → Order List: slide left
- Order List → Order Detail: slide left
- Order Detail back → Order List: slide right
- Order List → Add Product: slide up / new screen forward
- Payment bottom sheet: slides up from bottom

## Show:
1. All 5 screens in success state as a flow
2. Order Detail in "Pending" state (showing "Confirm Order" button)
3. Order Detail in "Delivered" state (showing "Confirm Payment" button with the payment method bottom sheet open)
4. Order Detail in "Completed" state (showing green checkmark, no action buttons)
5. Order List with status filter on "Pending" (showing only pending orders)
6. Add Product with the "My Submissions" section showing mixed statuses
