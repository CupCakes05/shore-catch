# A06 — Delivery Times | Muciriz Admin Panel

**Design:** White (#FFF) bg, Accent Teal (#0E7C86), Black text, sans-serif, Next.js web. Fixed left sidebar + top bar.

## Screen
**App:** Admin Panel (Next.js, web)
**Purpose:** Manage delivery time slot options. Supports global defaults and per-location overrides. These slots are what customers select at checkout for any delivery date (including pre-orders).

## UI Elements
- Sidebar + top bar (standard layout)
- Page title: "Delivery Times"
- **Scope selector** (tabs or toggle): "Global" | "Per Location" (with location dropdown when selected)
- **"Add Delivery Time"** button (teal filled, top-right)
- **Warning banner** (amber, if no active times): "⚠️ No active delivery times! Customers cannot checkout without at least one delivery time."
- **Delivery Times table:**
  - Columns: Label, Scope (Global / location name), Active (toggle), Actions
  - Example rows:
    - "Morning 6–8 AM" | Global | Active ✓
    - "Morning 8–10 AM" | Global | Active ✓
    - "Afternoon 12–2 PM" | Ernakulam | Active ✓
    - "Evening 5–7 PM" | Global | Inactive
  - Actions: Edit, Activate/Deactivate, Remove
- Helper text below table: "Per-location times override global times. If a location has its own delivery times, customers there see those instead of global defaults."

**Add/Edit Modal:**
- Label (text, required): e.g., "Morning 8–10 AM"
- Scope: Global (radio selected by default) | Specific Location (radio + location dropdown)
- Save / Cancel

**Remove Confirmation:**
- "Remove 'Evening 5–7 PM'? Customers will no longer see this time slot."
- Remove (red) / Cancel

## Interactions
- Add → modal → save → row appears
- Edit → pre-filled modal
- Toggle active/inactive inline
- Remove → confirm dialog
- Scope tab: Global shows all global; Per Location shows location-specific filtered view

## States
- **Loading:** Table skeleton
- **Empty:** Warning + "No delivery times configured. Add one so customers can checkout." + CTA
- **Error:** Retry
- **Success:** Table populated

## Navigation
- **From:** Sidebar
- **To:** N/A (self-contained management)

## What to Show
- Table with 4 delivery times (2 Global, 2 per-location "Ernakulam"), scope tabs showing "Global" active, Add button top-right. Helper text about override behavior visible below table.
- No warning banner (since times exist and are active).
