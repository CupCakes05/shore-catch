# C14 — Notifications | Muciriz Customer App

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Flutter Android.

## Screen
**App:** Customer (Flutter/Android)
**Purpose:** In-app notifications inbox for order updates, offers, and announcements. Separate from Offers screen.

## UI Elements
- App bar: "Notifications" title + back arrow + "Mark all read" option (overflow menu or text button)
- **Notification list** (vertical, full-width):
  - Each notification row:
    - Icon (left): order icon (📦), offer icon (🎁), or announcement icon (📢)
    - Title (bold if unread, normal if read): e.g., "Order Confirmed", "New offer available!"
    - Short message: "Your order M2026#00015 has been confirmed"
    - Timestamp (right, muted): "2h ago" / "Yesterday"
    - **Unread indicator:** bold text + small teal dot (left of icon)
  - Tap to expand/navigate
- Pull-to-refresh

## Interactions
- Tap notification → navigate to relevant screen (order detail, offers) or expand detail
- Mark all read → all dots disappear, text unbolds
- Pull-to-refresh → reloads notifications

## States
- **Loading:** Skeleton list (4-5 rows shimmer)
- **Empty:** Bell illustration + "No notifications yet. We'll let you know about orders and offers!"
- **Error:** "Couldn't load notifications" + Retry
- **Success:** Notification list

## Navigation
- **From:** App bar bell icon (any screen)
- **To:** Order Detail (C10), Offers (C11), or just expands in place

## What to Show
- Success state: 5 notifications — 2 unread (bold + teal dot): "Order Confirmed" and "New offer: 20% off Fish!", 3 read: "Order Delivered", "Order Completed", "Welcome to Muciriz!". Timestamps visible. Clean list layout.
