# Prompts

Implementation prompts for tools (Figma AI, Google Stitch, Google AI Studio, Lovable, Bolt, Cursor, Kiro, Claude, ChatGPT, etc.) are generated **on request** and **only from the latest documented state** of `ProjectDocs/`.

## How prompts will be structured (when generated)
Each generated prompt contains: business context, screen purpose, components, layout, interactions, validation, states (loading/empty/error/success), navigation (source/destination), animations/micro-interactions, responsive behavior, accessibility, theme (2–3 colors: white bg `#FFFFFF`, black text `#000000`, accent sea teal `#0E7C86`), English-only copy, and design language.

## Generated Prompts

| # | File | Target Tool | Purpose |
|---|------|-------------|---------|
| 1 | `GoogleStudio_AdminPanel.md` | Google AI Studio / Firebase Studio | Regenerate all 15 admin screens with standardized components, consistent colors, and full navigation from Stitch export |
| 2 | `NextSteps_AfterScreens.md` | — (Decision Guide) | Guidance on next steps after admin screens: backend in Antigravity vs Google Studio |

## To request a prompt
Specify:
- Which app (Customer / Staff / Admin) and which screen (e.g., C06 Product List).
- Target tool (e.g., Figma AI vs Bolt vs Google Studio vs Antigravity) so the prompt can be tailored.
