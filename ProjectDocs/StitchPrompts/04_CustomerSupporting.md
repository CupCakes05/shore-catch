# Batch 4 — Customer Supporting Screens (C09, C10, C11, C12, C13, C14)

> Order tracking, offers, profile, settings, and notifications.

---

Design 6 supporting screens for the **Customer App** (mobile, Android). These follow the same design system established earlier. They're accessed primarily via bottom navigation and from other screens.

---

### Screen 1: Order History (C09)
**Purpose:** List of all active and past orders with status.

**Accessed from:** Bottom nav "Orders" tab.

**UI Elements:**
- App bar: "My Orders" title
- Segmented control at top: "Active" | "Completed" (filters the list)
- **Order cards** (vertical list): each card shows:
  - Order number (e.g., #EZC-2024-0042)
  - Date & time
  - Item count (e.g., "3 items")
  - Total (₹)
  - Status chip (Pending=amber, Order Confirmed=blue, Delivered=teal, Completed=green, Cancelled=red muted)
  - Payment method label
- Pull-to-refresh
- Bottom navigation (Orders tab active/teal)

**Interactions:**
- Tap an order card → Order Detail (C10)
- Toggle Active/Completed segment → filters list
- Pull-to-refresh

**States:**
- Loading: skeleton order cards (3-4 shimmer cards)
- Empty: illustration + "You haven't placed any orders yet" + "Start Shopping" CTA
- Error: retry
- Success: list of order cards

---

### Screen 2: Order Detail (C10)
**Purpose:** Full detail of a single order with status timeline and invoice.

**Accessed from:** Order History (C09), Order Confirmation (C08).

**UI Elements:**
- App bar: "Order #EZC-2024-0042" + back arrow
- **Status timeline** (vertical stepper):
  - Steps: Pending → Order Confirmed → Delivered → Completed
  - Reached steps: teal circle + line; pending steps: gray
  - Each step shows timestamp when reached
- **Order info card:**
  - Items list: product name, weight, qty, unit price, line total
  - Subtotal, Total (bold)
  - Delivery time
  - Payment method (customer preference + actual collected method if completed)
  - Delivery address + nearest landmark
- **"Download Invoice"** button (secondary/outline)
- **"Cancel Order"** button (destructive/red outline) — ONLY visible when status is "Pending"
  - Tap → confirmation dialog: "Cancel this order? This cannot be undone."
  - On confirm → order cancelled, status updates, button disappears

**States:**
- Loading: skeleton
- Error: retry
- Success: full detail
- Cancel action: confirm dialog → loading → success toast "Order cancelled"

---

### Screen 3: Offers (C11)
**Purpose:** Full list of active offers/promotions for the customer's location.

**Accessed from:** Tapping the offers banner on Category Home (C05), or from Notifications.

**UI Elements:**
- App bar: "Offers" + back arrow
- **Offer cards** (vertical list): each card shows:
  - Banner image (if uploaded, spans top of card)
  - Offer title (bold, e.g., "Onam Special 🎉")
  - Discount highlight (e.g., "20% off Vegetables" in teal, prominent)
  - Brief message/description
  - Validity: "Valid till 15 Sep 2024" (muted text)
  - Optional CTA: "Shop Now →" (navigates to target category/product)
- Pull-to-refresh

**States:**
- Loading: shimmer cards
- Empty: illustration + "No offers right now. Check back soon!"
- Error: retry
- Success: offer cards list

---

### Screen 4: Profile (C12)
**Purpose:** View and edit customer profile info and delivery details.

**Accessed from:** Bottom nav "Profile" tab.

**UI Elements:**
- App bar: "Profile"
- Profile header: user name (large), WhatsApp number below (with verified checkmark icon)
- **Info sections (editable):**
  - Name (tap to edit → inline edit or modal)
  - WhatsApp Number (display only — verified, can't change easily)
  - Delivery Address (tap to edit)
  - Nearest Landmark (tap to edit)
  - **Service Location** (dropdown — changeable; shows warning: "Changing location will clear your cart")
- "Save Changes" button (appears when edits are made)
- Links at bottom: Settings, Help, Logout
- Bottom navigation (Profile tab active)

**Interactions:**
- Tap a field → enters edit mode
- Change Service Location → warning dialog → on confirm: saves, clears cart, refreshes catalog
- Save → loading → success toast

**States:**
- Loading: skeleton profile
- Error: save failure → toast
- Success: profile displayed

---

### Screen 5: Settings (C13)
**Purpose:** App preferences, notification controls, legal links, logout.

**Accessed from:** Profile (C12) or nav menu.

**UI Elements:**
- App bar: "Settings" + back arrow
- **Grouped list:**
  - Section "Notifications":
    - Toggle: In-app notifications (on/off)
    - Toggle: Push notifications (on/off)
    - Toggle: SMS notifications (on/off)
  - Section "Legal":
    - Privacy Policy → opens in webview/browser
    - Terms & Conditions → opens in webview/browser
    - About Eze-Cart
    - Help / FAQ
    - Contact Us
  - Section "Account":
    - **Logout** button (red text) → confirmation dialog → clears session → back to Registration
- App version at bottom (muted, small text)

**Interactions:**
- Toggles save instantly (local + server)
- Legal links open hosted URLs in in-app webview
- Logout → confirm dialog ("Are you sure?") → clears session → splash/registration

**States:** Mostly static; loading on logout action.

---

### Screen 6: Notifications (C14)
**Purpose:** Persistent inbox of announcements, offers, and order-status messages.

**Accessed from:** Bell icon in app bar (with unread badge count).

**UI Elements:**
- App bar: "Notifications" + back arrow
- Unread count badge on the bell icon that leads here
- **Notification list:** each item shows:
  - Dot indicator (teal) for unread, gone for read
  - Title (bold if unread)
  - Short message preview (1-2 lines)
  - Timestamp ("2h ago", "Yesterday", "15 Jun")
- Tap a notification → expands or navigates to relevant screen (offer, order)
- "Mark all as read" text link (top-right)
- Pull-to-refresh

**States:**
- Loading: shimmer list
- Empty: illustration + "No notifications yet"
- Error: retry
- Success: notification list with read/unread styling

---

## Show:
1. All 6 screens in their success/content state
2. Order Detail (C10) showing the status timeline with "Order Confirmed" as the current step
3. Order Detail with the "Cancel Order" button visible (Pending state)
4. Profile with the Service Location change warning dialog open
5. Notifications with a mix of read and unread items
6. Order History in empty state
