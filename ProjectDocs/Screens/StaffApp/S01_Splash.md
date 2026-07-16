# S01 — Splash

**App:** Staff (Flutter/Android)

## Purpose
Launch screen showing branding while checking the stored staff session.

## Users
Guest / returning Staff.

## Navigation
- **From:** cold start.
- **To:** Order List (S03) if valid session; Login (S02) otherwise.

## Key UI Elements
- Logo/app name (Muciriz Staff), minimal white background, loading indicator.

## States
- **Loading:** default (session check via `/auth/me`).
- **Error:** network failure → Error/No-Connection (S08) or fall back to Login.

## Actions
- Automatic routing.

## Validation / Permissions
- Reads stored token; no user input.

## Responsive / Accessibility
- Centered; accessible logo label.

## Dependencies
- Session store, `/auth/me`.
