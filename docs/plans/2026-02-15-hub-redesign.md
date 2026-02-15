# Alde Hub Redesign Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Redesign the st.farmasialde.com landing page with glassmorphism header, Phosphor Icons, dark mode, and PWA support.

**Architecture:** Static single-page site served by nginx. All styling is inline CSS in `index.html`. Dark mode via `prefers-color-scheme` media query. PWA via `manifest.json` + favicon. No JS framework, no build step.

**Tech Stack:** HTML, CSS, Phosphor Icons (CDN), nginx:alpine Docker image

**Design doc:** `docs/plans/2026-02-15-hub-redesign-design.md`

---

### Task 1: Create favicon.svg

**Files:**
- Create: `favicon.svg`

**Step 1: Create the SVG file**

Extract the AldeIcon path from pharmacy-timetable's `src/components/AldeIcon.tsx`. Create a standalone SVG with the mortar & pestle shape, `#00aced` fill.

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 259.44 260.39">
  <path fill="#00aced" d="M207.29,259.94c-18.4.91-32.86-7.25-43.18-22.78-13.79-20.74-42.56-24.21-60.76-9-3.4,2.84-6.08,6.63-8.77,10.23-17.7,23.66-49.33,29.23-73,11.76C1,235-6.15,209.19,5.79,184.77Q44.6,105.48,84.25,26.61C94.9,5.32,123.09-5.12,145.64,2.46c15.15,5.1,25.27,15.08,32.14,28.86q36.83,74,73.39,148c6,12.06,10.35,24.46,7.25,38.47C253.38,240.62,233.34,261.58,207.29,259.94ZM172.92,50.1c-.36,3.26-.43,5.89-1,8.41C166.89,82,145.87,95.76,122.11,91.24c-10.14-1.93-20.35-3.61-30.72-3-14.78.92-27.57,5.63-35.15,19.6C44.24,130,34,153.07,22.75,175.57a6,6,0,0,0-.25,2c1.59.55,2.47-.27,3.33-.9C43.34,163.86,63,163.27,82.88,168c37.16,8.83,66.52,29.65,88.64,60.7,12.83,18,30.16,24.07,48.86,17.83,20.69-6.9,33.61-32.35,25.07-51.87-5.27-12.06-11.34-23.71-17.17-35.5q-25.51-51.56-51.06-103.1C176.26,54.11,175.78,51.78,172.92,50.1Z"/>
</svg>
```

**Step 2: Verify**

Run: `file favicon.svg` — should show "SVG Scalable Vector Graphics image"

**Step 3: Commit**

```bash
git add favicon.svg
git commit -m "Add favicon SVG from AldeIcon"
```

---

### Task 2: Generate PWA icon PNGs

**Files:**
- Create: `icons/icon-192.png`
- Create: `icons/icon-512.png`
- Create: `apple-touch-icon.png`

**Step 1: Install rsvg-convert**

```bash
apt-get update && apt-get install -y librsvg2-bin
```

**Step 2: Create a padded SVG for icon generation**

Create a temporary `_icon-src.svg` with white icon on `#00aced` background, with padding:

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512">
  <rect width="512" height="512" rx="0" fill="#00aced"/>
  <g transform="translate(96,96) scale(1.23)">
    <path fill="#ffffff" d="M207.29,259.94c-18.4.91-32.86-7.25-43.18-22.78-13.79-20.74-42.56-24.21-60.76-9-3.4,2.84-6.08,6.63-8.77,10.23-17.7,23.66-49.33,29.23-73,11.76C1,235-6.15,209.19,5.79,184.77Q44.6,105.48,84.25,26.61C94.9,5.32,123.09-5.12,145.64,2.46c15.15,5.1,25.27,15.08,32.14,28.86q36.83,74,73.39,148c6,12.06,10.35,24.46,7.25,38.47C253.38,240.62,233.34,261.58,207.29,259.94ZM172.92,50.1c-.36,3.26-.43,5.89-1,8.41C166.89,82,145.87,95.76,122.11,91.24c-10.14-1.93-20.35-3.61-30.72-3-14.78.92-27.57,5.63-35.15,19.6C44.24,130,34,153.07,22.75,175.57a6,6,0,0,0-.25,2c1.59.55,2.47-.27,3.33-.9C43.34,163.86,63,163.27,82.88,168c37.16,8.83,66.52,29.65,88.64,60.7,12.83,18,30.16,24.07,48.86,17.83,20.69-6.9,33.61-32.35,25.07-51.87-5.27-12.06-11.34-23.71-17.17-35.5q-25.51-51.56-51.06-103.1C176.26,54.11,175.78,51.78,172.92,50.1Z"/>
  </g>
</svg>
```

**Step 3: Generate PNGs**

```bash
mkdir -p icons
rsvg-convert -w 192 -h 192 _icon-src.svg -o icons/icon-192.png
rsvg-convert -w 512 -h 512 _icon-src.svg -o icons/icon-512.png
rsvg-convert -w 180 -h 180 _icon-src.svg -o apple-touch-icon.png
rm _icon-src.svg
```

**Step 4: Verify**

```bash
file icons/icon-192.png icons/icon-512.png apple-touch-icon.png
```

Expected: all show "PNG image data" with correct dimensions.

**Step 5: Cleanup rsvg**

```bash
apt-get remove -y librsvg2-bin && apt-get autoremove -y
```

**Step 6: Commit**

```bash
git add icons/ apple-touch-icon.png
git commit -m "Add PWA and apple-touch icons"
```

---

### Task 3: Create manifest.json

**Files:**
- Create: `manifest.json`

**Step 1: Write the manifest**

```json
{
  "name": "Farmasi Alde ST",
  "short_name": "Alde ST",
  "start_url": "/",
  "display": "standalone",
  "theme_color": "#00aced",
  "background_color": "#ffffff",
  "icons": [
    {
      "src": "/icons/icon-192.png",
      "sizes": "192x192",
      "type": "image/png"
    },
    {
      "src": "/icons/icon-512.png",
      "sizes": "512x512",
      "type": "image/png"
    }
  ]
}
```

**Step 2: Commit**

```bash
git add manifest.json
git commit -m "Add PWA manifest"
```

---

### Task 4: Rewrite index.html

**Files:**
- Modify: `index.html`

This is the main task. Rewrite the full `index.html` with:

**Head section:**
- Keep existing meta charset/viewport
- Update title to "Farmasi Alde ST | Staff Tools"
- Keep Google Fonts link (Nunito + Nunito Sans)
- Add Phosphor Icons CDN: `https://unpkg.com/@phosphor-icons/web@2.1.1`
- Add favicon links: SVG favicon, apple-touch-icon
- Add manifest link
- Add `<meta name="theme-color" content="#00aced">`

**CSS changes (all inline in `<style>`):**
- Body: remove `align-items: center`, add `min-height: 100dvh`, background radial gradient (white center → `#f0f4f8` edges)
- New `.header` section: gradient `#00aced` → `#0090c5`, contains frosted-glass inner with `backdrop-filter: blur(10px)`, `background: rgba(255,255,255,0.1)`, rounded corners
- AldeIcon SVG inline in header, white fill, ~48px
- Title: Nunito 800, white, 1.5rem
- Subtitle "Salak Tinggi": Nunito Sans 400, white/80%, 0.9rem
- Cards: same structure, updated shadows, Phosphor icon class instead of emoji
- `.card__icon`: remove `font-size: 2rem`, add `color: #00aced` for active, `color: #9ca3af` for disabled, size 32px
- Dark mode `@media (prefers-color-scheme: dark)`: body `#1a1a2e` with brand glow, header gradient darkened slightly, cards `#2a2a3e`, text `#e4e4e7`, secondary `#9ca3af`, shadows darker
- Mobile: body padding-top 0, header flush, grid 1-col at 480px
- Footer: subtle, stays centered

**HTML body structure:**
```
<body>
  <header class="header">
    <div class="header__inner">
      <svg (AldeIcon, white fill, 48px)>
      <h1>Farmasi Alde</h1>
      <p>Salak Tinggi</p>
    </div>
  </header>
  <main class="container">
    <div class="grid">
      <a href="/timetable" class="card card--active">
        <i class="ph-bold ph-calendar-dots card__icon"></i>
        <div class="card__title">Staff Timetable</div>
        <div class="card__desc">View and manage staff schedules</div>
        <span class="card__link">Open →</span>
      </a>
      <div class="card card--disabled">
        <i class="ph-bold ph-package card__icon"></i>
        ...Coming Soon badge
      </div>
      <div class="card card--disabled">
        <i class="ph-bold ph-receipt card__icon"></i>
        ...Coming Soon badge
      </div>
    </div>
  </main>
  <footer class="footer">© Farmasi Alde</footer>
</body>
```

**Step 1: Write the complete new `index.html`**

Reference the design doc for exact colors, spacing, and dark mode values.

**Step 2: Verify locally**

```bash
curl -s http://localhost:80/ | head -5
```

(Won't work until deployed, but verify the file is valid HTML.)

**Step 3: Commit**

```bash
git add index.html
git commit -m "Redesign hub page with glassmorphism header and dark mode"
```

---

### Task 5: Update Dockerfile

**Files:**
- Modify: `Dockerfile`

**Step 1: Update COPY to include all static assets**

```dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
COPY favicon.svg /usr/share/nginx/html/favicon.svg
COPY manifest.json /usr/share/nginx/html/manifest.json
COPY apple-touch-icon.png /usr/share/nginx/html/apple-touch-icon.png
COPY icons/ /usr/share/nginx/html/icons/
EXPOSE 80
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD wget -qO- http://0.0.0.0:80/ || exit 1
```

**Step 2: Commit**

```bash
git add Dockerfile
git commit -m "Update Dockerfile to copy all static assets"
```

---

### Task 6: Update CLAUDE.md

**Files:**
- Modify: `CLAUDE.md`

**Step 1: Update to reflect new file structure and design system**

Add notes about:
- Phosphor Icons CDN usage
- Dark mode via `prefers-color-scheme` (no JS)
- PWA manifest and icons
- Brand gradient: `#00aced` → `#0090c5`

**Step 2: Commit**

```bash
git add CLAUDE.md
git commit -m "Update CLAUDE.md with redesign details"
```

---

### Task 7: Build, deploy, and verify

**Step 1: Build the Docker image**

```bash
cd /root/projects/alde-hub
docker build -t app-hack-optical-firewall-t52811:latest .
```

**Step 2: Update the running service**

```bash
docker service update --force --image app-hack-optical-firewall-t52811:latest app-hack-optical-firewall-t52811
```

**Step 3: Verify**

```bash
curl -s -o /dev/null -w "%{http_code}" https://st.farmasialde.com
```

Expected: `200`

**Step 4: Push to GitHub**

```bash
git push
```

This triggers Dokploy auto-deploy for future changes.
