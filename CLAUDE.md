# CLAUDE.md — Alde Hub

## Overview

Static bento-style link hub for `st.farmasialde.com` (Farmasi Alde, Salak Tinggi branch). Single `index.html` with inline CSS served by Nginx.

## Tech

- Single `index.html` with inline CSS (no framework, no build step)
- Nginx Alpine container
- Phosphor Icons via CDN (`@phosphor-icons/web@2.1.1`)
- Dark mode via `@media (prefers-color-scheme: dark)` — pure CSS, no JS
- PWA support: `manifest.json` + favicon + apple-touch-icon

## Brand

- Primary color: `#00aced`, dark variant: `#0090c5`
- Fonts: Nunito (display, 700-800) + Nunito Sans (body, 400-700) via Google Fonts CDN
- Header: glassmorphism gradient with frosted-glass inner container

## File Structure

- `index.html` — the entire app (HTML + inline CSS)
- `favicon.svg` — SVG favicon with dark mode support
- `manifest.json` — PWA manifest
- `apple-touch-icon.png` — iOS home screen icon (180x180)
- `icons/` — PWA icons (192x192, 512x512)

## Deployment

- **URL:** https://st.farmasialde.com
- **Docker service:** `app-hack-optical-firewall-t52811`
- **DNS:** st.farmasialde.com (Cloudflare) → 5.223.53.100

## Editing

To add a new app link, copy a card block in `index.html`:
- Active card: `card card--active` class, wrap in `<a href="/path">`, use Phosphor icon class `ph-bold ph-<icon-name>`
- Coming soon: `card card--disabled` class, use `<div>` not `<a>`
- Icon reference: https://phosphoricons.com (use bold weight)
