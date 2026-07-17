# A12 — Customer History | Muciriz Admin Panel

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Next.js web. Fixed left sidebar + top bar.

## Screen
**App:** Admin Panel (Next.js, web)
**Purpose:** Look up customers by name/phone, view purchase history, and edit customer details (name, address, location).

## UI Elements
- Sidebar + top bar (standard layout)
- Page title: "Customer Lookup"
- **Search bar** (prominent, full-width): "Search by name or WhatsApp number" (with search icon)
- **Results panel** (appears after search):
  - Customer cards: Name, WhatsApp, Location, Total Orders, Total Spend (₹)
  - Click to expand/select
- **Selected customer detail** (below or side panel):
  - **Profile summary card:**
    - Name: "Priya Menon" (bold)
    - WhatsApp: "+91 98765 43210"
    - Address: "Flat 4B, Sea View Apartments, MG Road"
    - Landmark: "Near Lulu Mall"
    - Location: "Ernakulam"
    - **"Edit Customer"** button (teal outline)
  - **Order history table:**
    - Columns: Order #, Date, Total (₹), Status (chip), Payment, Delivery Date, Pre-order
    - Pagination
    - Click order → A10

**Edit Customer Modal:**
- "Edit Customer Details"
- Editable fields:
  - Name (required)
  - Delivery Address (multiline)
  - Nearest Landmark
  - Service Location (dropdown)
- Non-editable (displayed, grayed/disabled):
  - WhatsApp Number: "+91 98765 43210" (with lock icon + note: "Identity key — cannot be changed")
- Confirmation: "Update customer details?" → Save / Cancel
- Note: "Changing location will affect customer's catalog on next visit"

## Interactions
- Type in search → results appear (name partial match, phone exact/partial)
- Click customer → expanded detail panel with profile + orders
- "Edit Customer" → modal → edit fields → save → PATCH /customers/{id} → toast "Customer updated"
- Click order in history → A10 (Order Detail)
- On location change: warning that customer's cart will be re-validated

## States
- **Idle:** "Search for a customer by name or WhatsApp number" (prompt text, illustration)
- **Loading:** Searching; saving edits
- **Empty:** "No customer found matching '[query]'"
- **Error:** Search error / save error → retry
- **Success:** Results + customer detail with order history

## Navigation
- **From:** Sidebar
- **To:** Order Detail (A10) from order clicks

## What to Show
- Customer selected: "Priya Menon" profile card visible with all details, "Edit Customer" button, order history table showing 5 orders (mix of statuses, one pre-order). Edit modal open showing editable name/address/location with WhatsApp disabled (grayed, lock icon).
