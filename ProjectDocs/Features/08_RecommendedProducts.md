# Feature — Recommended Products (Cross-sell / Upsell)

> Confirmed new requirement. Lets Admin suggest related products that customers can quick-add at checkout, increasing basket size.

**Overview:** Admin configures recommended products at two levels — per **Category** and per **specific Product**. At cart/checkout the relevant recommendations are shown with quick-add.

**Business purpose:** Cross-sell / upsell — nudge customers to add complementary items (e.g., recommend lemon & spices with fish), increasing average order value.

**Users:** Main Admin (configure), Customer (see & quick-add).

**Entry points:**
- Admin: Categories (A04 → Manage Recommended Products) and Products (A05 → Manage Recommended Products).
- Customer: Cart / Checkout (C07 → "Recommended for you / You may also like" section).

**Dependencies:** Products & Categories must exist; recommendations reference active + approved products in the same Service Location.

**Data used:** `ProductRecommendation` (sourceProductId → recommendedProductId) and `CategoryRecommendation` (sourceCategoryId → recommendedProductId). See `05_DatabaseConcepts.md`.

**Resolution (at C07):**
- Union of product-level recommendations for products in the cart + category-level recommendations for categories represented in the cart.
- De-duplicate; **exclude items already in the cart**; exclude inactive/unapproved/out-of-location products; ignore self-recommendation.

**Validation:**
- Recommended targets must be active, approved, and in the customer's Service Location.
- Recommendations are display/quick-add only — they do not alter pricing.

**States:** loading (fetching recommendations) / list shown / empty (section hidden) / error (section hidden, non-blocking).

**Edge cases:**
- Empty cart → no recommendations.
- All recommendations already in cart → section hidden.
- Soft-deleted/deactivated or rejected products drop out automatically.

**Permissions:** Admin-only configuration; customers see resolved list.

**Notifications:** none.

**Errors:** recommendation fetch failure is non-blocking (checkout still works; section hidden/retry).

**Future considerations:** automatic recommendations from co-purchase data; per-customer personalization; recommendations on product detail as well as checkout; ranking/limits.
