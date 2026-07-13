# C14 — Notifications

**App:** Customer (Flutter/Android)

> ✅ **Confirmed required (Q18).** A persistent in-app Notifications inbox is needed, kept separate from Offers. Notifications may also be delivered via **push** and **SMS (AWS SNS)**.

## Purpose
An inbox of announcements/offers/order-status messages, giving customers a persistent place to review them (the top-of-app banner surfaces the latest offer; this screen lists all notifications).

## Users
Authenticated Customer.

## Navigation
- **From:** App bar bell icon / menu.
- **To:** Offers (C11) / relevant product screens.

## Key UI Elements
- List of notifications: title, short message, timestamp, read/unread indicator.
- Tap to expand/detail.
- (Optional) Mark all as read.

## States
- **Loading:** skeleton list.
- **Empty:** "No notifications yet."
- **Error:** Retry.
- **Success:** list.

## Actions
- Tap notification → detail / linked offer.
- Mark read.
- Refresh.

## Business Rules (Confirmed Q18)
- Fed by Admin notifications/offers and order-status events; scoped by location/customer.
- Delivered **in-app (always), via push, and via SMS (AWS SNS)** per notification.
- Read state stored **server-side** (`PATCH /notifications/{id}/read`).

## Validation / Permissions
- Authenticated customer.

## Responsive / Accessibility
- Unread indicated via icon + text, not color alone; timestamps human-readable.

## Dependencies
- `/notifications`, `/notifications/{id}/read`; push provider + AWS SNS (SMS) for delivery.

> Confirmed (Q18): Notifications (C14) is kept **separate** from Offers (C11).
