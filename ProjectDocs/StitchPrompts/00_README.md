# Muciriz — Google Stitch UI/UX Prompts

## Overview
This directory contains self-contained prompts designed to be pasted directly into **Google Stitch** to generate UI/UX screens for the Muciriz project (local fresh-goods ecommerce: fish, vegetables, meat).

## Structure

```
StitchPrompts/
├── 00_README.md              ← This file
├── FullApp/                  ← Complete app prompts (all screens + design system)
│   ├── CustomerApp_Full.md   ← 16 screens, Flutter Android
│   ├── StaffApp_Full.md      ← 10 screens, Flutter Android
│   └── AdminPanel_Full.md    ← 15 screens, Next.js web
├── Individual/               ← Per-screen prompts (self-contained, paste individually)
│   ├── Customer/             ← C01–C16 (16 screens)
│   │   ├── C01_Splash.md
│   │   ├── C02_Onboarding.md
│   │   ├── C03_Registration.md
│   │   ├── C04_OTPVerification.md
│   │   ├── C05_CategoryHome.md
│   │   ├── C06_ProductList.md
│   │   ├── C07_CartCheckout.md
│   │   ├── C08_OrderConfirmation.md
│   │   ├── C09_OrderHistory.md
│   │   ├── C10_OrderDetail.md
│   │   ├── C11_Offers.md
│   │   ├── C12_Profile.md
│   │   ├── C13_Settings.md
│   │   ├── C14_Notifications.md
│   │   ├── C15_StaticPages.md
│   │   └── C16_ErrorStates.md
│   ├── Staff/                ← S01–S10 (10 screens)
│   │   ├── S01_Splash.md
│   │   ├── S02_Login.md
│   │   ├── S03_OrderList.md
│   │   ├── S04_OrderDetail.md
│   │   ├── S05_AddProduct.md
│   │   ├── S06_Profile.md
│   │   ├── S07_Settings.md
│   │   ├── S08_ErrorStates.md
│   │   ├── S09_MiscPurchases.md
│   │   └── S10_ShopSale.md
│   └── Admin/                ← A01–A15 (15 screens)
│       ├── A01_Login.md
│       ├── A02_Dashboard.md
│       ├── A03_ServiceLocations.md
│       ├── A04_Categories.md
│       ├── A05_Products.md
│       ├── A06_DeliveryTimes.md
│       ├── A07_StaffManagement.md
│       ├── A08_Offers.md
│       ├── A09_OrdersDashboard.md
│       ├── A10_OrderDetail.md
│       ├── A11_SalesReports.md
│       ├── A12_CustomerHistory.md
│       ├── A13_ProfileSettings.md
│       ├── A14_ErrorStates.md
│       └── A15_InventoryManagement.md
```

**Total: 44 prompt files** (3 full-app + 16 customer + 10 staff + 15 admin)

## How to Use

### Full-App Prompts
- Paste the entire content of a `*_Full.md` file into Google Stitch
- These include the complete design system, all screens, navigation map, and render instructions
- Best for generating a comprehensive UI prototype of an entire app

### Individual Screen Prompts
- Paste a single `C##_*.md`, `S##_*.md`, or `A##_*.md` file into Stitch
- Each is self-contained with a brief design system reminder, full UI element description, interactions, states, and what to render
- Best for refining or regenerating a specific screen

## Design System Summary
- **Brand:** Muciriz
- **Colors:** White (#FFFFFF) bg, Black (#000000) text, Deep Sea Teal (#0E7C86) accent
- **Typography:** Clean sans-serif, English only
- **Platform:** Customer & Staff = Flutter Android | Admin = Next.js web
- **States:** Every screen has Loading (skeleton), Empty (illustration + CTA), Error (retry), Success
- **Status Chips:** Pending=amber, Confirmed=blue, Delivered=teal, Completed=green, Cancelled=muted red

## Key Features Reflected
1. Multi-location product/category/staff assignment
2. Per-location stock management with pre-order when out of stock
3. Pre-order with delivery date + time slot picker (no date limit)
4. GST per-product (toggle + %), prices displayed inclusive, breakdown in cart/invoice
5. GPay/UPI company ID at checkout with deep-link button
6. Staff order cancellation (before Completed, stock restored)
7. Shop Sales for walk-ins (no customer account, immediately completed)
8. Misc Purchases expense recording by staff
9. Invoice numbering: M{YEAR}#{5-digit sequence} (e.g., M2026#00001)
10. Profit/Loss: Total Sale − Total Expense (simple)
11. Staff performance timestamps on order state transitions
12. Customer care contact configurable from admin
13. Fixed price per packet (weights are informational)

## Legacy Files (Archived)
The following files from the previous prompt structure are retained for reference but superseded by the new structure:
- `01_Foundation.md` through `07_AdminOperations.md` — old grouped prompts
- `CustomerApp_Full.md`, `StaffApp_Full.md`, `AdminPanel_Full.md` (root level) — old full-app prompts

The new prompts live in `FullApp/` and `Individual/` subdirectories.
