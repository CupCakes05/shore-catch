# Feature — Pre-Order (Requirement #3)

**Overview:** Customers can place orders for future delivery dates (pre-orders). Triggered when products are out of stock, today's time slots are exhausted, or proactively by customer choice.

**Business purpose:** Ensures the business never loses an order due to temporary stock/slot unavailability. Enables customers to plan ahead and schedule deliveries in advance.

**Users:** Customer (place pre-order), Staff (fulfill on scheduled date), Admin (monitor).

**Entry points:**
- Product List (C06): out-of-stock indicator → pre-order action.
- Cart/Checkout (C07): delivery date picker → future date selection.
- Staff (S10): Shop Sale with future date.

**Dependencies:** Delivery Times (Admin A06); ProductLocationStock (per-location stock); Order entity with `deliveryDate` and `isPreOrder` fields.

**Data used:** ProductLocationStock.stockCount (availability), DeliveryTime options, Order.deliveryDate, Order.isPreOrder.

**Triggering conditions:**
1. **Product out of stock** at customer's location (stock = 0) → product card shows "Out of Stock — Pre-order" badge; adding to cart triggers pre-order date selection.
2. **Today's time slots exhausted** (all active slots for today are past) → delivery date defaults to tomorrow/next available.
3. **Customer choice** → customer can always select a future date from the delivery date picker (proactive pre-order).

**Flow:**
```
Customer adds products to cart → opens Cart/Checkout (C07)
  → If any out-of-stock items OR today's slots exhausted:
    → Show alert: "Pre-order for a future date"
    → Date picker defaults to next available date
  → If all in stock AND today has slots:
    → Date picker shows today (default) + future options
  → Customer selects date → time slots for that date shown
  → Proceeds with checkout as normal
  → Order created with deliveryDate + isPreOrder = true (if future date)
```

**Delivery date + time slot selection:**
- Date picker: today (if available) + **all available future dates** (Confirmed Q25: no limit on how far ahead a customer can pre-order).
- Time slots: same delivery time options (global/per-location) apply to all dates.
- Dates without available slots are disabled.

**Order handling:**
- Pre-orders are marked `isPreOrder = true` with a future `deliveryDate`.
- They follow the same lifecycle: Pending → Order Confirmed → Delivered → Completed.
- Staff sees pre-orders in their order list with delivery date prominently displayed.
- Staff fulfills on or near the scheduled delivery date (no system-enforced timing).

**Validation:**
- Delivery date must be today or future (not past).
- Time slot must be selected for the chosen date.
- Products must be assigned to customer's location (stock check informational, not blocking — pre-order can proceed with 0 stock).
- **No artificial limit** on how far ahead a customer can pre-order (Q25 confirmed).

**States:**
- Product card: "Out of Stock — Pre-order available" badge.
- Cart: pre-order alert banner when triggered.
- Checkout: pre-order date picker; "Pre-order" badge on Place Order button.
- Order history: pre-order orders shown with scheduled delivery date.
- Staff order list: pre-order orders labeled with delivery date.

**Edge cases:**
- Customer has mix of in-stock and out-of-stock items → all go as pre-order if future date selected.
- Stock replenished between pre-order placement and delivery → order still valid; fulfilled normally.
- All dates have no slots → checkout blocked (Admin must add delivery times).
- Customer cancels pre-order while Pending → normal cancellation rules apply.
- Staff confirms pre-order before delivery date → allowed (early fulfillment).

**Permissions:** Customer (own orders). Staff (fulfill within scope). Admin (monitor).

**Notifications:** (future) push on day before scheduled delivery as reminder.

**Errors:** no available dates/slots → message + Admin action needed; network errors → retry.

**Future considerations:** automatic stock reservation for pre-orders; reminder notifications; recurring pre-orders (weekly schedule); capacity limits per date/slot; partial pre-order (some items today, some pre-ordered).
