# Batch 3 — Customer Shopping Flow (C05 → C06 → C07 → C08)

> The core commerce journey: browse → add to cart → checkout → confirmation.

---

Design the **Customer App** shopping/checkout flow — 4 screens connected as a journey. The user starts from Category Home (already designed in Batch 2) and proceeds through purchase.

## Flow: Category Home → Product List → Cart/Checkout → Order Confirmation

---

### Screen 1: Product List (C06)
**Purpose:** Shows all products in the selected category. Features a unique flip-card interaction and a category navigation strip.

**UI Elements:**
- **Category scroll strip** at the very top (below app bar):
  - Horizontal scrolling strip of all categories (small circular image + name below each)
  - Slow auto-scroll (gentle continuous motion)
  - Current category highlighted (teal border/underline)
  - **Back arrow (←)** on the LEFT side of the strip → taps returns to Category Home
  - Tapping any category in the strip switches to that category's products (in-place reload)
- App bar: category name as title, cart icon with badge (right)
- **Product cards — vertical list, one card at a time going downward, each centered:**
  - Card is a **flip card** (3D flip on tap):
    - **Front face:** Product Name (bold), Gross Weight, Net Weight, Price (₹) — clean text layout on white card
    - **Back face:** Product image (fills the card)
  - Tap the card → flips with a smooth 3D rotation animation
  - Tap again → flips back to details
  - **Below each card:** Quantity add button. Initially shows "+ Add" (teal outlined button). Once tapped, transforms into a stepper: [ − ] 1 [ + ]
- Pull-to-refresh

**Interactions:**
- Tap card → 3D flip animation (front ↔ back). Reduced-motion: cross-fade instead.
- Tap "+ Add" → item added to cart, button becomes stepper showing "1", cart badge increments, subtle toast "Added to cart"
- Stepper +/− adjusts quantity (min 0 removes item, max TBD)
- Tap category in scroll strip → products reload for that category (loading skeleton briefly)
- Tap back arrow in strip → navigate back to Category Home (C05)
- Tap cart icon → Cart/Checkout (C07)

**States:**
- Loading: skeleton cards (3 placeholder cards stacked) + shimmer strip
- Empty: "No products in this category yet" + illustration
- Error: retry
- Success: list of flip cards + strip
- Per-card: image loading on flip (placeholder/spinner until loaded)

---

### Screen 2: Cart / Checkout (C07)
**Purpose:** Review cart, adjust items, see recommendations, pick delivery time & payment, place order.

**UI Elements:**
- App bar: "Cart" title, back arrow
- **Cart items list:** each row shows:
  - Product name + weight info (small text)
  - Unit price
  - Quantity stepper [ − ] qty [ + ]
  - Line total (price × qty) on the right
  - Remove button (trash icon or "×") — with confirm dialog
- **"Recommended for you" section** (horizontal scrollable cards below cart items):
  - Header: "You may also like" with a small teal accent line
  - Horizontal scroll of small product cards (image + name + price + "Add" button)
  - Excludes items already in cart
  - Tap "Add" → product added to cart above, disappears from recommendations, cart badge updates
  - Hidden if no recommendations or cart is empty
- **Order Summary section:**
  - Subtotal
  - Total (bold, large)
- **Delivery Time dropdown** (outlined, with label "Select delivery time")
  - Options fetched from server (per-location or global)
  - Required — shows validation hint if not selected on submit attempt
- **Payment method** — two option cards/radio buttons:
  - "Gpay on Delivery" (with small gpay-style icon)
  - "Cash on Delivery" (with cash icon)
  - Required selection
- **"Place Order" button** (primary, full-width, sticky at bottom)
  - Disabled until: cart ≥ 1 item + delivery time selected + payment selected
  - Shows loader when processing
- "Continue shopping" text link (above or below cart items) → goes back to categories

**Interactions:**
- Adjust quantity via stepper; line total updates instantly
- Remove item → confirm dialog ("Remove [product] from cart?") → removes, updates totals
- Quick-add from recommendations → item appears in cart list, recommendation card animates away
- Select delivery time from dropdown
- Tap a payment option → selected state (teal border/check)
- Tap "Place Order" → button shows loader → on success → Order Confirmation (C08)
- If server detects price change (409) → dialog showing updated prices, ask to confirm
- Network error → toast with retry

**States:**
- Empty cart: illustration + "Your cart is empty" + "Start Shopping" CTA button (no checkout controls shown)
- Loading: skeleton (while placing order — button spinner; delivery time dropdown has own loading)
- Error: inline prompts for missing selections; server error toast
- Success: navigates to Order Confirmation

---

### Screen 3: Order Confirmation (C08)
**Purpose:** Success! Order placed. Show summary and invoice download.

**UI Elements:**
- Large teal checkmark icon/animation at top (success celebration)
- "Order Placed Successfully!" heading
- Order number (e.g., "#EZC-2024-0042")
- **Order summary card:**
  - Items list (name × qty)
  - Total
  - Delivery time selected
  - Payment method
  - Delivery address + landmark
- **"Download Invoice"** button (outlined/secondary) — downloads PDF
- **"View Order"** button (text link → Order Detail C10)
- **"Continue Shopping"** button (primary) → Category Home (C05)

**Interactions:**
- Download Invoice → opens/saves PDF (system share sheet on mobile)
- View Order → navigates to Order Detail (C10)
- Continue Shopping → navigates to Category Home (C05, clears nav stack)

**States:**
- Success: default (the confirmation itself is the success state)
- Loading: brief if invoice URL is loading
- Error: invoice download failure → "Retry download" (order is still confirmed regardless)

---

## Transitions:
- Category Home → Product List: slide left (forward)
- Product List → Cart: slide left (or slide up from bottom)
- Cart → Order Confirmation: slide left + subtle confetti/success animation
- Order Confirmation → Category Home: reset to home (clear stack)

## Show:
1. All 4 screens in success state as a connected flow
2. Product List showing the flip card interaction (one card mid-flip showing both faces)
3. Cart with 2-3 items + the recommendations section + delivery time selected + payment selected (ready to order)
4. Cart in empty state
5. Product card quantity stepper in action (showing "2" with +/− buttons)
