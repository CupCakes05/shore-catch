# Muciriz — Staff App (Full UI/UX Prompt for Google Stitch)

## DESIGN SYSTEM

**Project:** Muciriz — local fresh-goods ecommerce (fish, vegetables, meat)
**Platform:** Flutter Android mobile app
**Colors:** Background White (#FFFFFF), Text Black (#000000), Accent Deep Sea Teal (#0E7C86), Button text on accent: White. No gradients, no heavy shadows. Clean and minimal.
**Typography:** One clean sans-serif family. English only. Clear hierarchy.
**Components:** Order cards, Status chips (Pending=amber, Confirmed=blue, Delivered=teal, Completed=green, Cancelled=muted red), outlined inputs, toasts, confirm dialogs, permission-gated UI elements.
**States:** Every screen needs Loading (skeleton shimmer), Empty (illustration + message + CTA), Error (retry), Success.
**Navigation:** Simple nav — Orders (home), Profile, Settings. Permission-gated entries: Add Product/Stock Update, Misc Purchases, Shop Sale accessible via menu/FAB when permitted.

---

## NAVIGATION MAP

```
Splash (S01) → Login (S02) → Order List (S03, home)
                                    ↓
S03 → S04 (Order Detail/Fulfillment)
S03 → S05 (Add Product & Stock Update, if permitted)
S03 → S06 (Profile) → S07 (Settings)
S03 → S09 (Misc Purchases, if permitted)
S03 → S10 (Shop Sale, if permitted)
Any → S08 (Error States)
```

---

## SCREENS

### S01 — Splash
**Purpose:** Launch screen with branding, checks staff session.
**UI Elements:**
- White background, centered "Muciriz Staff" text/logo in Deep Sea Teal
- Subtle loading indicator below

**Interactions:**
- Auto-routes: valid session → S03 (Order List); no session → S02 (Login)
- Network failure → S08

**States:**
- Loading (default)
- Error: network → S08 or fall back to Login

---

### S02 — Login
**Purpose:** Authenticate staff member (phone + password, created by Admin).
**UI Elements:**
- Centered card/form layout on white background
- "Muciriz Staff" heading
- Phone field (phone keyboard, +91 format)
- Password field (with show/hide toggle)
- "Login" button (teal filled, full-width, disabled until both fields filled)
- Note below: "Forgot password? Contact your Admin for a reset." (muted text)
- Error message area (below button, red text)

**Interactions:**
- Enter phone + password → Login → POST /auth/login/staff
- Success → S03 (Order List), loads permissions + assigned locations
- Error → inline message ("Invalid credentials" / "Account deactivated" / network error)

**States:**
- Idle: empty form
- Loading: button loader, form disabled
- Error: clear message below form
- Success: route to S03

---

### S03 — Order List (Home)
**Purpose:** Staff home screen showing orders for their assigned locations.
**UI Elements:**
- App bar: "Orders" title, assigned location label (subtitle), profile icon (right)
- **Status filter tabs** (horizontal, scrollable): All | Pending | Confirmed | Delivered | Completed | Cancelled
- **Order cards** (vertical list):
  - Order number (e.g., "M2026#00015")
  - Customer name
  - Delivery date + time slot
  - Item count + total (₹)
  - Payment preference icon (GPay/Cash)
  - Status chip (colored)
  - **Pre-order badge** (teal outline chip, if delivery date is future)
  - **"Placed by: Staff"** indicator if applicable
- Pull-to-refresh
- **FAB (Floating Action Button)** or menu with permission-gated entries:
  - "Add Product" (if `add_products`)
  - "Update Stock" (if `update_stock`)
  - "Misc Purchase" (if `record_misc_purchase`)
  - "Shop Sale" (if `place_shop_sale`)
- Bottom nav or menu: Orders (active), Profile, Settings

**Interactions:**
- Tap order → S04 (Order Detail)
- Filter tabs → reload with status filter
- Pull-to-refresh
- FAB items → navigate to S05/S09/S10 respectively
- Profile icon → S06

**States:**
- Loading: skeleton cards (3-4)
- Empty: "No orders right now" + illustration
- Error: Retry
- Success: order list, sorted pending/oldest first

---

### S04 — Order Detail (Fulfillment)
**Purpose:** Core working screen — view order details and perform fulfillment actions with timestamps.
**UI Elements:**
- App bar: "Order #M2026#00015" + back arrow
- **Order header:**
  - Status chip (large, prominent)
  - Pre-order badge (if applicable): "Delivery: [future date]"
  - "Placed by: Customer/Staff" indicator
- **Customer section:**
  - Name, WhatsApp number (tap-to-call icon, WhatsApp icon)
  - Delivery address + nearest landmark
- **Delivery info:** Date + Time Slot (highlighted for pre-orders)
- **Items list:**
  - Product name, weight, qty, unit price
  - GST info per item (if applicable): "incl. 5% GST"
  - Line total
- **Order totals:**
  - Subtotal
  - GST breakdown (e.g., "GST 5%: ₹12")
  - Total (bold)
- **Payment:** Preference shown (GPay/Cash); collected method shown once completed
- **Fulfillment timeline with timestamps:**
  - Order Placed: [timestamp] ✓
  - Order Confirmed: [timestamp] by [staff name] ✓ (or pending)
  - Delivered: [timestamp] by [staff name] ✓ (or pending)
  - Payment Confirmed: [timestamp] by [staff name] — [method] ✓ (or pending)
  - Cancelled: [timestamp] by [staff name] (if applicable)
- **Action buttons (state-aware, permission-gated):**
  - "Confirm Order" (teal filled, shown when Pending + has `confirm_order`)
  - "Confirm Delivery" (teal filled, shown when Order Confirmed + has `confirm_delivery`)
  - "Confirm Payment" (teal filled, shown when Delivered + has `confirm_payment`) — opens method selector (GPay/Cash)
  - **"Cancel Order"** (red outline, shown in any state before Completed + has permission) — opens confirm dialog

**Interactions:**
- Tap Confirm Order → confirm dialog → PATCH state → success toast, timeline updates
- Tap Confirm Delivery → confirm dialog → PATCH state → success toast
- Tap Confirm Payment → method selector (GPay/Cash) → confirm → PATCH → order Completed
- Tap Cancel → "Cancel this order? Stock will be restored." confirm dialog → POST cancel → Cancelled, stock restored
- Tap phone → device dialer; tap WhatsApp icon → wa.me link
- Each action records timestamp + staffId

**States:**
- Loading: fetching detail; action in progress (button loader)
- Error: out-of-order transition (409) → message; permission missing → button hidden; network → retry
- Success: timeline updates, toast, chip updates

---

### S05 — Add Product & Stock Update
**Purpose:** Staff adds/edits products (for approval) and updates stock counts.
**UI Elements:**

**Product Add/Edit Section (requires `add_products`):**
- Section header: "Add Product"
- Category selector dropdown (limited to staff's allowed categories)
- Form fields (outlined):
  - Product Name (required)
  - Gross Weight (number + unit)
  - Net Weight (number + unit, must be ≤ Gross)
  - Price (₹, required)
  - GST Applicable (toggle switch)
  - GST Percentage (number field, shown when toggle on: 5, 12, 18, or 28)
- Image upload button (camera/gallery picker) with preview
- "Submit for Approval" button (teal filled)
- **My Submissions list** below: products submitted by this staff with status badges (Pending Approval = amber, Approved = green, Rejected = red). Rejected items show "Revise & Resubmit" option.

**Stock Update Section (requires `update_stock`):**
- Section header: "Update Stock"
- Location selector (if multiple assigned)
- Product list with current stock count per item
- Each product row: name, current stock count, "Edit" icon
- Tap edit → inline number input → "Save" button
- Confirmation: "Stock updated for [product] at [location]"

**Interactions:**
- Fill product form → upload image → Submit → "Submitted for approval" toast, form resets
- Tap stock edit → enter new count → Save → PATCH /stock → "Stock updated" toast
- Rejected product → tap → opens form pre-filled for revision

**States:**
- Loading: saving product; uploading image; saving stock
- Empty: no submissions yet; no products at location (stock section)
- Error: validation inline; upload failure; network → retry
- Success: toast confirmations

---

### S06 — Profile
**Purpose:** View staff member's own profile (read-only permissions and location).
**UI Elements:**
- App bar: "Profile" + back arrow
- Profile card:
  - Name (bold)
  - Phone (login identifier)
  - Assigned Location(s) (chips)
- Permissions section (read-only chips in teal outline):
  - e.g., "View Orders", "Confirm Order", "Confirm Delivery", "Confirm Payment", "Add Products", "Update Stock", "Misc Purchases", "Shop Sale"
- "Settings" button/link → S07

**Interactions:**
- View only (staff cannot edit own profile/permissions)
- Tap Settings → S07

**States:**
- Loading: profile fetch
- Error: Retry
- Success: profile displayed

---

### S07 — Settings
**Purpose:** Staff app preferences, legal links, logout.
**UI Elements:**
- App bar: "Settings" + back arrow
- Grouped list:
  - **Notifications:** toggle for new-order alerts
  - **Legal:** Privacy Policy, Terms, About (open canonical URLs in webview/browser)
  - **Account:** "Logout" (red text)
- App version at bottom (muted)

**Interactions:**
- Toggle notifications
- Tap legal links → webview
- Logout → confirm dialog → clear session → S02

**States:**
- Mostly static
- Loading/Error on logout

---

### S08 — Error States (Global)
**Purpose:** Global fallback screens for staff app.
**UI Elements:**
- **No Connection:** offline illustration, "You're offline", Retry button
- **Server Error (5xx):** warning illustration, "Something went wrong", Retry
- **Session Expired (401):** lock icon, "Session expired", "Sign In" button → S02
- **Permission Changed (403):** alert icon, "Your permissions were updated", "Re-login" button
- **Maintenance:** tools illustration, "Under maintenance", Retry later

**Interactions:**
- Retry → reattempt
- Sign In / Re-login → S02

---

### S09 — Misc Purchases
**Purpose:** Record operational expenses (plastic bags, packaging, ice, etc.).
**UI Elements:**
- App bar: "Miscellaneous Purchases" + back arrow
- **Record form:**
  - Item Name (text, required) — e.g., "Plastic bags"
  - Quantity (number, required)
  - Cost / Amount (₹, required)
  - Receipt/Image upload (optional — camera/gallery)
  - Purchase Date (date picker, defaults today)
  - Notes (optional, multiline)
  - "Save" button (teal filled)
- **Purchase History** below form:
  - Date-grouped list of past entries (staff's own)
  - Each row: date, item name, qty, cost (₹), receipt indicator (📎 if attached)
  - Pull-to-refresh

**Interactions:**
- Fill form → optional image → Save → POST /misc-purchases → "Purchase recorded" toast, form resets, entry appears in history
- Tap past entry → view details / receipt image
- Pull-to-refresh history

**States:**
- Loading: saving; loading history
- Empty: "Record your first purchase" CTA
- Error: validation inline; upload failure; network → retry
- Success: toast + history updated

---

### S10 — Shop Sale (Walk-in Billing)
**Purpose:** Quick billing for walk-in/counter customers. No customer account needed. Immediately completed.
**UI Elements:**

**Step 1 — Customer Info (Optional):**
- "New Shop Sale" heading
- Phone number field (optional, phone keyboard)
- Customer name field (optional)
- Helper text: "Phone and name are optional — just for the invoice"
- "Continue" button (teal) / "Skip" text link

**Step 2 — Product Selection:**
- Category tabs/chips (scoped to staff's assigned location)
- Product grid/list per category:
  - Product name, price, stock indicator
  - "Add" button → quantity stepper
- Running cart summary bar at bottom: "X items • ₹XXX" + "Review" button
- Stock indicators visible (warn if low/zero)

**Step 3 — Payment & Review:**
- Order summary: items (name, qty, price, GST if applicable), subtotal, GST breakdown, total
- Phone/name shown if provided
- Payment method selector: "GPay" | "Cash" (actual method — collected now)
- **"Complete Sale"** button (teal filled, full-width)

**Step 4 — Confirmation:**
- Success checkmark: "Sale Completed!"
- Sale number: "M2026#00042" (Muciriz invoice numbering)
- Total amount
- "Download Invoice" button (outline teal)
- "New Sale" button (teal filled) → reset to Step 1
- "Back to Orders" text link → S03

**Interactions:**
- Optional customer info → select products → choose payment → Complete Sale
- POST /orders/shop-sale → immediately Completed, stock decremented, invoice generated
- Download Invoice → PDF with Muciriz branding, GST split
- New Sale → reset flow

**States:**
- Loading: placing sale
- Error: creation failure → retry
- Success: confirmation with invoice

---

## WHAT TO SHOW

Generate the following key screens with full fidelity:
1. **S03 Order List** — success state with 4 order cards (one Pending with pre-order badge, one Confirmed, one Delivered, one Completed), status filter showing "All" active, FAB expanded showing permission-gated options
2. **S04 Order Detail** — order in "Order Confirmed" state, timeline showing 2 steps complete with timestamps, "Confirm Delivery" button visible, Cancel button visible, pre-order badge, GST info in items
3. **S10 Shop Sale Step 2** — product selection with 2 items added (steppers visible), running cart summary bar at bottom, stock indicator on one product showing "Low Stock"
4. **S05 Add Product** — form filled with sample fish product, GST toggle on showing 5%, image uploaded preview visible, "Submit for Approval" button
5. **S09 Misc Purchases** — form filled with "Plastic bags, qty 100, ₹500", history below showing 3 past entries date-grouped
6. **S02 Login** — idle state with phone and password fields empty

For all screens: white background, teal accents, clean minimal aesthetic, realistic sample data (Indian names, fish/vegetable/meat context, ₹ pricing).
