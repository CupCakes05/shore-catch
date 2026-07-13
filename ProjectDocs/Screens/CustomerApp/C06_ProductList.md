# C06 — Product List (Category Products)

**App:** Customer (Flutter/Android)

## Purpose
Show all products in the selected category for the customer's Service Location, with a distinctive **flip card** interaction and per-product quantity add. Includes a slow-scrolling category strip for quick switching and a back arrow to return to the Category page.

## Users
Authenticated Customer.

## Navigation
- **From:** Category Home (C05) via a category card, or via the category scroll strip on this screen.
- **To:**
  - Cart (C07) via cart icon / "Go to cart".
  - Back to Category Home (C05) via the **back arrow** on the left of the category scroll strip.

## Key UI Elements
- **Product cards, one-by-one going downward** (vertical list). Each product is a **centered card**.
  - **Front of card:** product details — Name, Gross Weight, Net Weight, Price.
  - **Back of card (on tap/flip):** product **image**. Tapping flips between the two sides (front/back like two sides of a card).
  - **Quantity add control** below each card: add button; when quantity > 0, show quantity stepper (− / count / +).
- **Category scroll strip:** a slow-scrolling horizontal strip of all categories (image + name) behaving like buttons; tapping switches the displayed category's products.
  - **Back arrow** on the **left side** of the scroll strip → returns to Category Home (C05).
- App bar with cart icon + badge.

## States
- **Loading:** skeleton product cards; strip shimmer.
- **Empty:** category has no products → empty state ("No products in this category yet.").
- **Error:** fetch failure → Retry.
- **Success:** list of flip cards + strip.
- Per-card image loading state on flip (spinner/placeholder until image loads).

## Actions
- Tap card → flip to reveal image (tap again to flip back).
- Tap quantity add / stepper → update cart quantity for that product (with subtle feedback + cart badge update).
- Tap a category in the scroll strip → load that category's products (updates list in place).
- Tap back arrow → Category Home (C05).
- Tap cart icon → Cart (C07).
- Pull-to-refresh.

## Business Rules
- Products scoped to selected category (and thus location).
- Adding quantity updates cart (local and/or server cart per architecture).
- Price shown is authoritative-display; server recomputes at checkout.

## Interaction Notes
- Flip animation should be smooth and reversible; honor reduced-motion (fallback to cross-fade).
- Scroll strip motion is intentionally slow/gentle.

## Validation / Permissions
- Authenticated customer; quantity ≥ 0 (removing brings to 0).

## Responsive / Accessibility
- Cards centered and readable at various widths; flip control has an accessible label ("Show image / Show details"); quantity stepper has clear labels and adequate tap targets; strip items labeled with category name.

## Dependencies
- `/products?category={id}`, `/categories?location={id}` (for strip), cart state.
