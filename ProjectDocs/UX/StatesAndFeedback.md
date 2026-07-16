# Muciriz — UX States, Feedback & Micro-Interactions

> Cross-cutting UX rules for every data-driven screen across all three apps.

## 1. Required States (every data screen)
| State | When | UX |
|-------|------|----|
| **Loading** | Fetching/submitting | Skeletons (lists/cards) or spinner; disable primary action + show inline loader while submitting |
| **Empty** | No data | Friendly illustration + one-line message + clear CTA |
| **Error** | Fetch/submit failure | Message + **Retry**; keep user input where possible |
| **Success** | Content ready / action done | Content render; toast/snackbar for actions |

## 2. Specific Empty States
- Empty cart (customer C07): "Your cart is empty" + browse CTA.
- No categories (C05): "No categories yet for {Location}."
- No products (C06): "No products in this category yet."
- No orders (C09 / S03 / A09): "No orders yet."
- No offers (C11): "No offers right now."
- No recommendations (C07): "Recommended for you" section simply hidden (non-blocking).
- No notifications (C14): "No notifications yet."
- Fresh Admin system (A02/A03): "Create your first Service Location."

## 3. Feedback Patterns
- **Toasts/snackbars:** success (added to cart, order placed, saved, status updated) and non-blocking errors.
- **Inline validation:** form fields show errors adjacent to the field.
- **Confirmation dialogs:** destructive/irreversible or state-changing actions (soft-delete location/category/product/staff/offer, logout, **cancel order — only available while Pending (Q10)**, staff confirmations, approve/reject staff product).
- **Optimistic vs pending:** cart quantity updates can feel instant; order state changes should reflect server confirmation.

## 4. Key Micro-Interactions
- **Product card flip** (C06): tap toggles front (details) ↔ back (image); smooth, reversible.
- **Slow-scrolling category strip** (C06): gentle horizontal motion; items act as buttons; left back arrow returns to categories.
- **Quantity stepper:** add button expands to − / count / + with subtle animation; updates cart badge.
- **Offer banner carousel** (C05): auto-rotates slowly (respect reduced-motion); swipeable.
- **Status chip transitions:** clear change when staff advances an order.

## 5. Loading Details
- Skeleton loaders for lists/cards preferred over blank spinners.
- Image loading: placeholder → fade-in; flip reveal shows spinner until image ready.
- Buttons show inline loader + disabled state during async actions to prevent double submit.

## 6. Offline / Retry
- Detect connectivity loss → global banner/screen (C16 / S08 / A14).
- Retry re-runs the failed request; preserve form/cart input.

## 7. Confirmation & Undo
- Destructive actions: confirm dialog (with cascade warnings where relevant).
- Consider brief "undo" on non-destructive removals (e.g., removing a cart item) [suggestion].

## 8. Accessibility (applies everywhere)
- Text + icon/shape for status, not color alone.
- Adequate contrast (black on white baseline).
- Labeled controls, associated error text, sufficient tap targets (Android), keyboard/focus support (web).
- Screen-reader announcements for state changes (success/error/loading complete).
- Honor reduced-motion.
> Full WCAG conformance requires manual testing with assistive tech and expert review — flagged as a QA step.
