# A08 — Offers | Muciriz Admin Panel

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Next.js web. Fixed left sidebar + top bar.

## Screen
**App:** Admin Panel (Next.js, web)
**Purpose:** Create/manage promotional offers with target scope (all/category/product), % discount, location scope (global/per-location), and validity window.

## UI Elements
- Sidebar + top bar (standard layout)
- Page title: "Offers & Promotions"
- **Scope filter** (tabs): "All" | "Global" | "Per Location"
- **"Create Offer"** button (teal filled, top-right)
- **Offers table:**
  - Columns: Title, Target, Discount, Scope, Validity, Active, Actions
  - Target: "All categories" / "Category: Fresh Fish" / "Product: Seer Fish"
  - Discount: "20%"
  - Scope: "Global" or "Ernakulam"
  - Validity: "10 Jan – 28 Jan 2026"
  - Active: green dot or toggle
  - Actions: Edit, Activate/Deactivate, Remove

**Create/Edit Offer Modal:**
- Title (text, required): e.g., "20% off Fresh Fish — Onam Special!"
- Message (textarea): description text
- **Target scope** (radio group):
  - ○ All categories
  - ○ Specific category → category picker dropdown appears
  - ○ Specific product → product picker dropdown appears
- **Discount Percent** (number, 0-100): e.g., "20"
- **Banner Image** upload (optional, drag-drop/file picker)
- **Validity:** Start date (picker) + End date (picker)
- **Scope** (radio):
  - ○ Global
  - ○ Specific Location → location dropdown appears
- Save / Cancel

## Interactions
- Create → modal → fill fields (title, target, discount, dates, scope) → save → offer in table
- Edit → pre-filled modal
- Activate/Deactivate inline toggle
- Remove → confirm dialog
- Per-location overrides global (helper text in modal)

## States
- **Loading:** Table skeleton
- **Empty:** "No offers yet. Create your first promotional offer!" + CTA
- **Error:** Retry
- **Success:** Table populated

## Navigation
- **From:** Sidebar
- **To:** N/A (self-contained)

## What to Show
- Table with 3 offers: "20% off Fresh Fish — Onam Special!" (Target: Category Fresh Fish, 20%, Global, Active), "15% off Vegetables" (Target: Category Vegetables, 15%, Ernakulam, Active), "Buy 2 Pomfret 10% off" (Target: Product Pomfret, 10%, Global, Expired/Inactive).
- Create Offer modal open showing target scope radio with "Specific category" selected and category picker showing "Fresh Fish".
