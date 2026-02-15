# Alde Hub Redesign — Design Doc

**Date:** 2026-02-15
**Scope:** Visual refresh of st.farmasialde.com landing page

## Context

The alde-hub is a static single-page landing (nginx + `index.html`) linking to staff tools. Current design uses emoji icons, flat gray background, and minimal branding. This redesign improves visual polish while keeping the single-HTML-file architecture.

**Audience:** Staff only (internal tool).

## Selected Approach: Glassmorphism Header + Bento Grid

### Header

- Gradient background: `#00aced` → `#0090c5`
- AldeIcon SVG (mortar & pestle) at ~48px, white fill
- "Farmasi Alde" in Nunito 800, white, ~1.5rem
- "Salak Tinggi" subtitle in Nunito Sans 400, white/80% opacity, ~0.9rem
- Frosted-glass inner container: semi-transparent white overlay + `backdrop-filter: blur`
- Soft bottom edge into content area

### Background

- Light: radial gradient from white center to `#f0f4f8` edges
- Dark: `#1a1a2e` base with faint `#00aced` radial glow at ~5% opacity near top

### Cards

- 2-column grid (desktop), 1-column (mobile at 480px)
- Light: white bg, `border-radius: 1rem`, shadow `0 2px 8px rgba(0,0,0,0.06)`
- Active: left border `#00aced`, hover lift + stronger shadow
- Disabled: 50% opacity, no interaction
- Dark: `#2a2a3e` card bg, lighter text, muted border
- Phosphor Icons via CDN (bold weight, ~32px):
  - Timetable → `ph-calendar-dots`
  - Inventory → `ph-package`
  - POS → `ph-receipt`
- Active icons in brand color, disabled in gray

### Mobile

- No vertical centering — content flows from top
- Header flush at top
- Full-width cards with comfortable padding

### Dark Mode

- CSS-only via `@media (prefers-color-scheme: dark)`, no JS
- Body: `#1a1a2e`, Cards: `#2a2a3e`, Text: `#e4e4e7`, Secondary: `#9ca3af`
- Brand `#00aced` unchanged (works on both backgrounds)

### Favicon & PWA

- `favicon.svg` — AldeIcon as standalone SVG file
- Inline SVG favicon link in HTML head
- `apple-touch-icon.png` — 180x180 white icon on `#00aced` bg
- `manifest.json` — name "Farmasi Alde ST", display standalone, theme `#00aced`
- Icons: `icons/icon-192.png`, `icons/icon-512.png`

### File Structure

```
alde-hub/
├── index.html          (redesigned)
├── favicon.svg         (new)
├── manifest.json       (new)
├── apple-touch-icon.png (new, generated)
├── icons/
│   ├── icon-192.png    (new, generated)
│   └── icon-512.png    (new, generated)
├── Dockerfile          (updated: COPY full dir)
├── CLAUDE.md           (updated)
└── docs/plans/
    └── 2026-02-15-hub-redesign-design.md
```

### Dockerfile Change

From `COPY index.html ...` to copying all static assets into nginx html root.
