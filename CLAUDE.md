# CLAUDE.md — Alde Hub

## Overview

Static bento-style link hub for `st.farmasialde.com`. Single `index.html` served by Nginx.

## Tech

- Single `index.html` with inline CSS
- Nginx Alpine container
- No build step, no framework, no dependencies

## Deployment

- **URL:** https://st.farmasialde.com
- **Dokploy project:** alde-hub
- **DNS:** st.farmasialde.com (Cloudflare) → 77.42.92.199

## Editing

To add a new app link, copy a card block in `index.html`:
- Active card: Use `card card--active` class, wrap in `<a href="/path">`
- Coming soon: Use `card card--disabled` class, use `<div>` not `<a>`
