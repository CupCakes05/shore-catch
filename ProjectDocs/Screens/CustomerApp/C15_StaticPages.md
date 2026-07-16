# C15 — Static / Legal Pages

**App:** Customer (Flutter/Android)

## Purpose
Standard informational/legal formalities expected of a professional app: Privacy Policy, Terms & Conditions, About, Help/FAQ, Contact.

## Users
Any (linked from Settings/Registration; some viewable pre-auth for legal consent).

## Navigation
- **From:** Settings (C13), Registration (C03) consent links.
- **To:** back.

## Canonical Source — Admin-Hosted Public URLs (Confirmed)
The **canonical** legal content lives at the **public, unauthenticated pages hosted by the Next.js Admin Panel** (e.g. `/privacy`, `/terms`, optional `/about`, `/contact`). These provide the **stable public URLs required by the Google Play Store** (this app collects personal data: name, WhatsApp #, address, OTP via AWS SNS).

- **Recommended model (single source of truth):** the app **links out to / renders the hosted URLs** (open in browser or in an in-app webview) rather than shipping a hard-bundled copy. This keeps one canonical source so legal edits don't require an app update or Play resubmission.
- This slightly revises the earlier "static bundled content (Q19)" stance: content is still **not an admin-managed CMS** and remains **static/hardcoded**, but it is now served as **public static pages from the Next.js app** and consumed by the app, rather than bundled only inside the app.
- The **Privacy Policy** `/privacy` URL is the same link supplied to the Play Console listing, keeping app, listing, and Data Safety declaration consistent.

## Pages Covered
| Page | Canonical URL (Admin-hosted) | Purpose |
|------|------------------------------|---------|
| Privacy Policy | `/privacy` | How customer data (name, WhatsApp #, address, OTP) is collected & used — **also the Play Store Privacy Policy URL** |
| Terms & Conditions | `/terms` | Usage terms, order/delivery/payment terms |
| About | `/about` (optional) | About Muciriz |
| Help / FAQ | in-app / `/contact` | Common questions (ordering, delivery times, payment on delivery) |
| Contact / Customer Care | `/contact` (optional) | Business contact info (phone/WhatsApp/email); **also see C13 for in-app customer care contact** (Requirement #6) |

## Key UI Elements
- Title + scrollable content. Legal pages (Privacy, Terms, About, Contact) **render/link the Admin-hosted canonical URLs** (in-app webview or external browser).
- For Contact: tap-to-call / tap-to-WhatsApp links (uses device dialer/WhatsApp; not an API integration).

## States
- **Webview/link pages:** loading indicator while the hosted page loads; error state with retry if the page can't be reached (offline/host down).
- Any purely in-app FAQ text remains static (no loading/error needed for that text).

## Actions
- Read; open legal pages (webview/browser); tap contact links.

## Validation / Permissions
- Public/legal pages accessible pre-auth where needed for consent. The hosted URLs are public (no login required).

## Responsive / Accessibility
- Readable typography, headings, sufficient contrast, links labeled; webview content inherits the hosted page's responsive styling.

## Dependencies
- **Admin-hosted public legal pages** (Next.js `/privacy`, `/terms`, optional `/about`, `/contact`) — the canonical source (Confirmed). Still **not** an admin-managed CMS (Q19); pages are static/hardcoded in the Next.js app.
- Network access (for webview/external links); optional bundled fallback copy is allowed, but the hosted URL remains canonical for the Play listing.
- **Open item:** actual Privacy Policy & Terms copy still to be authored/provided (see `Suggestions/00_OpenQuestions.md`).
