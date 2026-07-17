# Batch 1 — Foundation (Design System Context)

> Paste this as your first prompt in Google Stitch. It establishes the design system, brand, and shared rules for all subsequent batches.

---

I'm designing a complete UI/UX for a small ecommerce system called **Muciriz**. It has 3 apps:
1. **Muciriz** (Customer App) — Flutter, Android, mobile
2. **Muciriz Staff** (Staff App) — Flutter, Android, mobile
3. **Muciriz Admin** (Admin Panel) — Next.js, web (desktop/tablet)

## Design System

**Colors (strict 2–3 color palette):**
- Background: White (#FFFFFF)
- Text: Black (#000000)
- Primary accent: Deep Sea Teal (#0E7C86) — used for buttons, active states, links, highlights
- Button text on accent: White (#FFFFFF)
- No gradients, no heavy shadows. Clean and minimal.

**Typography:**
- One clean sans-serif family (e.g., Inter, Poppins, or system default)
- Clear hierarchy: screen title (bold, large), section header (medium), body (regular), caption (small, muted)
- English only

**Layout principles:**
- Generous whitespace, uncluttered
- Cards-based: category cards, product cards, order cards, offer cards
- Consistent spacing scale and corner radius (8px small, 12px medium, 16px large)
- Mobile: single-column with cards; Admin: sidebar + content area with tables

**Components vocabulary:**
- Buttons: primary (filled #0E7C86, white text), secondary (outline/text)
- Cards: with subtle border or very light shadow (no heavy elevation)
- Status chips: colored text labels — Pending (amber), Order Confirmed (blue), Delivered (teal), Completed (green), Cancelled (red/muted)
- Inputs: outlined with label, inline validation error in red below
- Dropdowns: standard select with chevron
- Dialogs/modals: for confirmations (especially destructive actions)
- Toasts/snackbars: success (green), error (red), info (teal)
- Bottom sheet: for mobile selections

**Customer App navigation:**
- Bottom navigation bar with 4 tabs: Home (categories icon), Cart (bag icon + badge), Orders (list icon), Profile (person icon)
- Active tab: #0E7C86 tint; inactive: gray
- Cart badge shows item count (red/accent circle)

**Staff App navigation:**
- Simple: Orders (home), Profile, Settings
- Bottom nav or top app bar with tabs

**Admin Panel layout:**
- Fixed left sidebar navigation (dark or white with teal active indicator)
- Top bar: global Service Location selector dropdown, admin avatar/menu
- Content area: tables with pagination, search, filters; forms in modals/drawers

**States (every data screen must show all of these):**
- Loading: skeleton shimmer placeholders matching the layout
- Empty: friendly illustration + message + CTA
- Error: error icon + message + "Retry" button
- Success/Content: the actual data

**Motion/Animation:**
- Subtle and purposeful only
- Product card flip (3D rotate to reveal image on back)
- Slow horizontal auto-scroll for category strip
- Page transitions: slide forward/back (mobile)
- Honor reduced-motion preferences

**Brand personality:**
- Fresh, local, friendly — evokes a neighborhood fish/vegetable market
- Professional but approachable
- Sea-themed accent color reinforces the seafood/fresh goods identity

---

Please keep this design system consistent across ALL screens I'll prompt next. I'll send screens in flow-based batches. For each batch, design the screens showing the user journey (how they connect), include all states (loading, empty, error, success), and show interactions/transitions between screens.
