# C06 — Product List | Muciriz Customer App

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Flutter Android.

## Screen
**App:** Customer (Flutter/Android)
**Purpose:** Show all products in the selected category with flip-card interaction, stock indicators, and category switching via scroll strip.

## UI Elements
- **App bar:** Category name as title (e.g., "Fresh Fish"), cart icon with badge (right), back arrow (left → C05)
- **Category scroll strip** (horizontal, below app bar):
  - Back arrow (←) on left side → returns to Category Home (C05)
  - Horizontal row of category pills/chips (small image + name): "Fish" (active, teal underline), "Vegetables", "Meat", "Seafood", etc.
  - Slow-scroll behavior, tap to switch
- **Product cards** (vertical list, one per row, centered, ~90% width):
  - **Front face (default):**
    - Product Name (bold, e.g., "Seer Fish / Neymeen")
    - Gross Weight: "1 kg" | Net Weight: "850g"
    - Price: "₹680" (large, bold)
    - GST indicator (if applicable): small text "incl. 5% GST"
    - **Stock badge** (if out of stock): amber chip "Out of Stock — Pre-order available"
    - Tap hint: "Tap to see image" (small, muted)
  - **Back face (on tap):**
    - Full product image (fills card area)
    - Tap hint: "Tap to see details"
  - **Quantity control** (below each card, outside flip area):
    - Default: "Add" button (teal outline, pill shape)
    - If out of stock: "Pre-order" button (amber outline) instead
    - After adding: quantity stepper (− | count | +) in teal
- Card has subtle border/shadow (very light)

## Interactions
- Tap product card body → 3D flip animation to reveal image (back face); tap again → flip back to details
- Tap "Add" → item added to cart, toast "Added to cart", badge increments, button becomes stepper showing "1"
- Tap "Pre-order" (out of stock) → same as Add but with pre-order context
- Stepper +/− → adjust quantity; at 0 → reverts to "Add" button
- Tap category in strip → reloads products for that category (smooth transition)
- Tap ← arrow in strip → Category Home (C05)
- Tap cart icon → Cart (C07)
- Pull-to-refresh
- Flip animation honors reduced-motion (fallback: cross-fade)

## States
- **Loading:** 3 skeleton card placeholders (rounded rectangles) + strip shimmer
- **Empty:** Illustration + "No products in this category yet."
- **Error:** "Couldn't load products" + Retry button
- **Success:** Product cards list + category strip
- **Per-card:** Image loading spinner on flip (back face shows placeholder until loaded)

## Navigation
- **From:** Category Home (C05) or category strip selection
- **To:** Cart (C07), Category Home (C05 via ← arrow)

## What to Show
- Success state: 3 product cards visible — top card showing front face (Seer Fish, ₹680, "incl. 5% GST", quantity stepper showing "2"), middle card flipped showing product image, bottom card showing "Out of Stock — Pre-order available" amber badge with "Pre-order" button. Category strip at top with "Fish" active (teal underline). Cart badge showing "4".
- Clean, centered cards with proper spacing.
