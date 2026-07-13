# Eze-Cart — Design Language

> Consolidated design system notes from client branding requirements. Applies to all three apps for a consistent, minimal look.

## 1. Color System (Max 2–3 Colors)
- **Background:** White (#FFFFFF).
- **Text (body):** Black (#000000).
- **Button text:** White or Black (depending on button fill contrast).
- **Accent/Brand color:** **deep sea teal `#0E7C86`** (Confirmed Q20) — used for primary buttons, active states, links, and status highlights. Chosen to evoke the sea/fish-forward product range while pairing cleanly with white background / black text.
- Optional single secondary accent only if within the 2–3 color total.
- Keep the palette strictly minimal; avoid gradients and heavy shadows.

> Confirmed (Q20): brand accent = `#0E7C86`. Everything else stays black/white. Ensure text on the accent uses white for adequate contrast.

## 2. Typography
- One clean, legible sans-serif family.
- Clear hierarchy: screen title, section header, body, caption.
- High contrast (black on white) for readability.
- **Language (Confirmed Q17): English only** for now. No Malayalam/bilingual UI required; a Latin-script sans-serif is sufficient.

## 3. Layout & Spacing
- Generous whitespace, uncluttered screens.
- Consistent spacing scale and corner radius across apps.
- Cards for categories (customer), products (flip cards), orders, and offers.

## 4. Components (shared vocabulary)
- **Buttons:** primary (filled accent), secondary (outline/text). Button text white or black per contrast.
- **Cards:** category card, product flip card (front details / back image), order card, offer banner card.
- **Status chips:** Pending / Order Confirmed / Delivered / Completed / Cancelled — use text + color (never color alone).
- **Inputs:** labeled fields, inline validation errors.
- **Dropdowns:** service location, delivery time, payment method.
- **Dialogs:** confirmation for destructive/irreversible actions.
- **Toasts/Snackbars:** success/error feedback.

## 5. Imagery
- Category & product images uploaded by Admin/Staff.
- Product flip card reveals the product image on tap.
- Provide placeholder for missing/loading images; all images have alt/labels.

## 6. Motion
- Subtle, purposeful: product card flip, slow-scrolling category strip, banner carousel.
- Honor reduced-motion preferences (fallbacks: cross-fade instead of flip; no auto-scroll).

## 7. Platform Notes
- Customer & Staff apps: Flutter, Android — follow Android touch-target and gesture norms.
- Admin: Next.js web — responsive to tablet; table-centric layouts.

## 8. Tone
- Clean, minimal, friendly. Short, plain-language copy in empty/error/success states.
