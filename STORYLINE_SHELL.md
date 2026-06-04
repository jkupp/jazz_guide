# Storyline-Style E-Learning Shell
## Reusable Template for Pure HTML/CSS/JS Modules

---

## What This Is

A pattern for building Articulate Storyline-style interactive e-learning modules in plain HTML/CSS/JS — no frameworks, no build tools, no Storyline license. The result is a single `.html` file that:

- Fills the browser window with fixed nav bars and a scrollable content area (Storyline reflow behavior)
- Has a collapsible sidebar course menu with sequential unlock
- Has a bottom bar with slide counter, Prev/Next navigation, and optional media controls
- Runs in any modern browser and deploys to any static host (Vercel, Netlify, GitHub Pages) with zero configuration

This pattern was developed and proven in *A Jazz Lover's Guide to Chicago*. Copy the shell, replace the content.

---

## Quick Start

1. Copy the **Shell HTML** section below into a new `index.html`
2. Replace the Google Fonts import with whatever fonts your design uses (or keep these)
3. Replace the palette CSS variables with your brand colors
4. Add your slides inside `.sl-content`
5. Update `TOTAL` and `HAS_MEDIA` in the script
6. Add your slide content and data arrays

---

## Shell HTML

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Your Course Title</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link href="https://fonts.googleapis.com/css2?family=Bebas+Neue&family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
  <style>
    /* ── RESET + FULL-WINDOW FILL ──────────────────────────────────── */
    * { box-sizing: border-box; margin: 0; padding: 0; }
    html, body { height: 100%; }
    body { background: #111; font-family: 'Inter', sans-serif; }

    /* ── SHELL ─────────────────────────────────────────────────────── */
    .sl-outer {
      width: 100vw; height: 100vh;
      min-width: 320px; min-height: 480px;
      display: flex; flex-direction: column;
      font-family: 'Inter', sans-serif;
      overflow: hidden;
      background: #1a1a1a;
    }

    /* ── TOP BAR ───────────────────────────────────────────────────── */
    .sl-topbar {
      background: #222; height: 44px;
      display: flex; align-items: center; padding: 0 16px; gap: 16px;
      border-bottom: 1px solid #383838; flex-shrink: 0;
    }
    .sl-hamburger {
      cursor: pointer; display: flex; flex-direction: column; gap: 4px;
      padding: 4px; background: none; border: none;
    }
    .sl-hamburger span { display: block; width: 20px; height: 2px; background: #bbb; border-radius: 1px; transition: background 0.15s; }
    .sl-hamburger:hover span { background: #fff; }
    .sl-course-title { font-size: 13px; color: #ccc; font-weight: 400; letter-spacing: 0.02em; }

    /* ── MIDDLE (sidebar + content) ────────────────────────────────── */
    .sl-middle { display: flex; flex: 1; overflow: hidden; }

    /* ── SIDEBAR ───────────────────────────────────────────────────── */
    .sl-sidebar {
      width: 240px; background: #2a2a2a;
      border-right: 1px solid #383838; flex-shrink: 0;
      overflow-y: auto; transition: width 0.25s ease, opacity 0.25s ease;
    }
    .sl-sidebar.collapsed { width: 0; opacity: 0; overflow: hidden; }
    .sl-sidebar-header {
      padding: 14px 16px 10px; font-size: 11px; font-weight: 600;
      color: #777; letter-spacing: 0.12em; text-transform: uppercase;
      border-bottom: 1px solid #383838;
    }
    .sl-menu-item {
      display: flex; align-items: center; justify-content: space-between;
      padding: 10px 16px; font-size: 12px; color: #bbb; cursor: pointer;
      border-left: 3px solid transparent; line-height: 1.3;
      transition: background 0.12s, color 0.12s;
    }
    .sl-menu-item:hover { background: #333; color: #fff; }
    .sl-menu-item.active { background: #333; color: #fff; border-left-color: #E8403A; }
    /* Change border-left-color to your primary accent */
    .sl-menu-item.completed .sl-item-icon { color: #5cb85c; }
    .sl-menu-item.locked { color: #555; cursor: default; }
    .sl-menu-item.locked:hover { background: transparent; color: #555; }
    .sl-item-icon { font-size: 13px; flex-shrink: 0; margin-left: 8px; color: #777; }

    /* ── CONTENT AREA ──────────────────────────────────────────────── */
    .sl-content { flex: 1; overflow: hidden; position: relative; }
    .sl-slide { position: absolute; top: 0; left: 0; right: 0; bottom: 0; display: none; }
    .sl-slide.active { display: flex; flex-direction: column; }

    /* ── BOTTOM BAR ────────────────────────────────────────────────── */
    .sl-bottombar {
      background: #2a2a2a; height: 50px;
      display: flex; align-items: center; padding: 0 16px; gap: 8px;
      border-top: 2px solid #555; flex-shrink: 0;
    }
    .bar-media { display: flex; align-items: center; gap: 8px; flex: 1; }
    .bar-media.hidden { display: none; }
    .bar-nav { display: flex; align-items: center; gap: 8px; margin-left: auto; }
    .bar-btn {
      background: #555; border: 2px solid #ccc; border-radius: 4px;
      cursor: pointer; color: #fff; font-size: 11px; font-weight: 700;
      letter-spacing: 0.08em; text-transform: uppercase; padding: 6px 12px;
      font-family: 'Inter', sans-serif; transition: background 0.12s, border-color 0.12s;
    }
    .bar-btn:hover { background: #666; border-color: #fff; }
    .bar-btn.round { border-radius: 50%; width: 32px; height: 32px; padding: 0; display: flex; align-items: center; justify-content: center; }
    .bar-counter { background: #555; border: 2px solid #ccc; border-radius: 4px; color: #fff; font-size: 12px; font-weight: 600; padding: 4px 10px; font-family: 'Inter', sans-serif; }
    .bar-divider { width: 1px; height: 22px; background: #666; flex-shrink: 0; margin: 0 4px; }
    .bar-progress { flex: 1; height: 5px; background: #555; border-radius: 3px; min-width: 40px; }
    .bar-progress-fill { height: 100%; background: #2B9FE8; border-radius: 3px; width: 0%; transition: width 0.3s; }
  </style>
</head>
<body>

<div class="sl-outer" role="application" aria-label="Your Course Title">

  <!-- TOP BAR -->
  <div class="sl-topbar">
    <button class="sl-hamburger" onclick="toggleSidebar()" aria-label="Toggle menu">
      <span></span><span></span><span></span>
    </button>
    <span class="sl-course-title">Your Course Title</span>
  </div>

  <div class="sl-middle">

    <!-- SIDEBAR -->
    <div class="sl-sidebar" id="sidebar">
      <div class="sl-sidebar-header">Course Menu</div>
      <!-- Add one .sl-menu-item per slide. Slide 0 starts active; rest start locked. -->
      <div class="sl-menu-item active" onclick="goToSlide(0)" id="menu-0">
        <span>Introduction</span><span class="sl-item-icon" id="icon-0">●</span>
      </div>
      <div class="sl-menu-item locked" id="menu-1">
        <span>Section One</span><span class="sl-item-icon" id="icon-1">🔒</span>
      </div>
      <div class="sl-menu-item locked" id="menu-2">
        <span>Section Two</span><span class="sl-item-icon" id="icon-2">🔒</span>
      </div>
      <!-- Add more as needed -->
    </div>

    <!-- CONTENT -->
    <div class="sl-content">

      <!-- SLIDE 0 -->
      <div class="sl-slide active" id="slide-0">
        <!-- Your slide content here -->
      </div>

      <!-- SLIDE 1 -->
      <div class="sl-slide" id="slide-1">
        <!-- Your slide content here -->
      </div>

      <!-- SLIDE 2 -->
      <div class="sl-slide" id="slide-2">
        <!-- Your slide content here -->
      </div>

    </div>
  </div>

  <!-- BOTTOM BAR -->
  <div class="sl-bottombar">
    <div class="bar-media hidden" id="mediaControls">
      <button class="bar-btn round" aria-label="Play">&#9654;</button>
      <div class="bar-progress"><div class="bar-progress-fill" id="mediaProg"></div></div>
      <button class="bar-btn" style="width:32px;height:32px;padding:0;" aria-label="Volume">♪</button>
      <button class="bar-btn" style="width:32px;height:32px;padding:0;font-size:10px;" aria-label="CC">CC</button>
      <div class="bar-divider"></div>
    </div>
    <div class="bar-nav">
      <span class="bar-counter" id="slideCounter">1 / 3</span>
      <div class="bar-divider"></div>
      <button class="bar-btn" id="prevBtn" onclick="prevSlide()">&#8249; Prev</button>
      <button class="bar-btn" id="nextBtn" onclick="nextSlide()">Next &#8250;</button>
    </div>
  </div>

</div>

<script>
  // ── CONFIGURATION ──────────────────────────────────────────────────
  const TOTAL = 3;  // ← number of slides
  // Set to true for any slide index that has audio/video media controls
  const HAS_MEDIA = [false, false, false];

  // ── STATE ──────────────────────────────────────────────────────────
  let current = 0;
  let sidebarOpen = true;

  // ── NAVIGATION ─────────────────────────────────────────────────────
  function toggleSidebar() {
    sidebarOpen = !sidebarOpen;
    document.getElementById('sidebar').classList.toggle('collapsed', !sidebarOpen);
  }

  function goToSlide(n) {
    if (n < 0 || n >= TOTAL) return;
    const target = document.getElementById('menu-' + n);
    // Locked slides can't be reached by jumping ahead via the menu,
    // but the learner can always step forward one slide.
    if (target && target.classList.contains('locked') && n !== current + 1) return;

    document.getElementById('slide-' + current).classList.remove('active');
    const prevMenu = document.getElementById('menu-' + current);
    if (prevMenu) { prevMenu.classList.add('completed'); prevMenu.classList.remove('active'); }
    const prevIcon = document.getElementById('icon-' + current);
    if (prevIcon && !prevIcon.textContent.includes('✓')) prevIcon.textContent = '✓';

    current = n;
    document.getElementById('slide-' + current).classList.add('active');
    const curMenu = document.getElementById('menu-' + current);
    if (curMenu) {
      curMenu.classList.add('active'); curMenu.classList.remove('locked');
      // Wire onclick so this completed slide is revisitable via the sidebar
      if (!curMenu.onclick) { const _n = current; curMenu.onclick = () => goToSlide(_n); }
    }

    if (current + 1 < TOTAL) {
      const next = document.getElementById('menu-' + (current + 1));
      if (next && next.classList.contains('locked')) {
        next.classList.remove('locked');
        const idx = +next.id.replace('menu-', '');
        next.onclick = () => goToSlide(idx);
        const icon = document.getElementById('icon-' + (current + 1));
        if (icon) icon.textContent = '○';
      }
    }
    updateUI();
  }

  function prevSlide() { goToSlide(current - 1); }
  function nextSlide() { goToSlide(current + 1); }

  function updateUI() {
    document.getElementById('prevBtn').disabled = current === 0;
    document.getElementById('prevBtn').style.opacity = current === 0 ? '0.4' : '1';
    document.getElementById('nextBtn').disabled = current === TOTAL - 1;
    document.getElementById('nextBtn').style.opacity = current === TOTAL - 1 ? '0.4' : '1';
    document.getElementById('slideCounter').textContent = (current + 1) + ' / ' + TOTAL;
    document.getElementById('mediaControls').classList.toggle('hidden', !HAS_MEDIA[current]);
  }

  // ── INIT ───────────────────────────────────────────────────────────
  updateUI();
</script>

</body>
</html>
```

---

## Slide Layout Patterns

The `.sl-slide` is a flex column that fills its container. Below are the three patterns used in this project — copy the one that fits your slide.

### Pattern A: Centered / Welcome Slide
```css
.slide-welcome {
  background: #F5F0E8;
  flex: 1;
  display: flex; flex-direction: column;
  align-items: center; justify-content: center;
  text-align: center;
  padding: 28px 48px;
  overflow: hidden; /* decorative shapes don't cause scroll */
  position: relative;
}
```
Use for: title cards, summaries, single-focus content.  
Key trick: decorative shapes are `position:absolute` with `z-index:-1` (or low); text content gets `position:relative; z-index:2`.

### Pattern B: Fixed Header + Scrollable Body
```css
.slide-scrollable {
  background: #F5F0E8;
  flex: 1;
  display: flex; flex-direction: column;
  overflow: hidden;
}
.slide-header { /* title bar */ flex-shrink: 0; }
.intro-band  { /* optional callout band */ flex-shrink: 0; }
.scroll-area {
  flex: 1;
  overflow-y: auto;
  padding: 16px 20px;
}
.scroll-area::-webkit-scrollbar { width: 4px; }
.scroll-area::-webkit-scrollbar-thumb { background: #2B5BA8; border-radius: 2px; }
```
Use for: timelines, long-form content, any slide that needs to scroll.

### Pattern C: Fixed Header + Tab Bar + Scrollable Panel
```css
.slide-tabbed {
  background: #F5F0E8;
  flex: 1;
  display: flex; flex-direction: column;
  overflow: hidden;
}
.tab-bar { display: flex; background: #2a2a2a; flex-shrink: 0; border-bottom: 2px solid #383838; }
.tab-btn {
  font-family: 'Bebas Neue', sans-serif; font-size: 13px; letter-spacing: 0.08em;
  color: #888; padding: 10px 16px; cursor: pointer;
  border-bottom: 3px solid transparent; margin-bottom: -2px;
  background: none; border-top: none; border-left: none; border-right: none;
}
.tab-btn.active { color: #F5F0E8; border-bottom-color: #E8403A; }
.tab-scroll { flex: 1; overflow-y: auto; padding: 16px 18px; }
.tab-panel { display: none; }
.tab-panel.active { display: block; }
```

Tab switching JS:
```javascript
function switchTab(prefix, name, btn) {
  const scroll = document.getElementById(prefix + '-scroll');
  scroll.querySelectorAll('.tab-panel').forEach(p => p.classList.remove('active'));
  document.getElementById(prefix + '-' + name).classList.add('active');
  scroll.closest('.slide-tabbed').querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
  btn.classList.add('active');
  scroll.scrollTop = 0;
}
```

---

## Expand / Collapse Pattern

Used on cards, timeline entries, venue bios. **Collapse the body, not the card.**

```css
.card { /* the container — no overflow:hidden, no max-height */ }
.card-body {
  max-height: 0;
  overflow: hidden;
  transition: max-height 0.3s ease;
  /* optional: also transition padding/border for a clean reveal */
  padding-top: 0; border-top: none;
}
.card.expanded .card-body {
  max-height: 500px; /* generous upper bound */
  padding-top: 8px;
  border-top: 1px solid #eee;
}
.card-toggle { /* always visible, sits outside .card-body */ }
```

```javascript
function toggleExpand(id, btn, e) {
  if (e) e.stopPropagation();
  const el = document.getElementById(id);
  const open = el.classList.toggle('expanded');
  btn.textContent = open ? 'Read less ↑' : 'Read more ↓';
}
```

> **Gotcha:** Don't put the toggle *inside* the collapsing element — it will be hidden when collapsed. Keep it as a sibling after the body.

---

## Data-Driven Rendering Pattern

Keep all content in JS arrays at the top of the script, render with a function. This makes content edits trivial and keeps HTML clean.

```javascript
const ITEMS = [
  { name: 'Item One', preview: 'Short description.', bio: 'Full bio text.', url: null },
  { name: 'Item Two', preview: 'Short description.', bio: 'Full bio text.', url: null },
];

function renderGrid(containerId, data) {
  document.getElementById(containerId).innerHTML = data.map((item, i) => `
    <div class="card">
      <div class="card-preview">${item.preview}</div>
      <div class="card-body" id="body-${i}">${item.bio}</div>
      <div class="card-footer">
        ${item.url
          ? `<a href="${item.url}" target="_blank">↗ Visit</a>`
          : `<span class="placeholder">↗ Link coming</span>`}
        <span onclick="toggleExpand('body-${i}', this, event)">Read more ↓</span>
      </div>
    </div>`).join('');
}

// In init:
renderGrid('my-container', ITEMS);
```

---

## Parallelogram Spotlight Block

```html
<div class="para-wrap">
  <div class="para-shape">
    <div class="para-inner">
      <!-- content here -->
    </div>
  </div>
</div>
```

```css
.para-wrap { /* sizing/positioning wrapper */ }
.para-shape { background: #1A1A1A; transform: skewX(-8deg); padding: 14px 28px; }
.para-inner { transform: skewX(8deg); /* counter-rotates content back to upright */ }
```

---

## SVG Avatar System

Generates a consistent geometric avatar for any person without photos.

```javascript
const SHAPES = ['circle', 'rect', 'diamond', 'triangle'];

function makeAvatar(initials, bgColor, idx) {
  const shape = SHAPES[idx % 4];
  const sc = 'rgba(255,255,255,0.35)'; // stroke color
  const sf = 'rgba(0,0,0,0.18)';       // shape fill
  let s = '';
  if (shape === 'circle')   s = `<circle cx="36" cy="36" r="26" fill="${sf}" stroke="${sc}" stroke-width="2.5"/>`;
  if (shape === 'rect')     s = `<rect x="10" y="10" width="52" height="52" fill="${sf}" stroke="${sc}" stroke-width="2.5"/>`;
  if (shape === 'diamond')  s = `<polygon points="36,9 63,36 36,63 9,36" fill="${sf}" stroke="${sc}" stroke-width="2.5"/>`;
  if (shape === 'triangle') s = `<polygon points="36,11 64,61 8,61" fill="${sf}" stroke="${sc}" stroke-width="2.5"/>`;
  return `<svg viewBox="0 0 72 72" xmlns="http://www.w3.org/2000/svg" width="72" height="72" style="display:block;">
    <rect width="72" height="72" fill="${bgColor}"/>
    ${s}
    <text x="36" y="40" text-anchor="middle" dominant-baseline="middle"
      font-family="'Bebas Neue',Arial Black,sans-serif"
      font-size="20" fill="rgba(255,255,255,0.95)" letter-spacing="2">${initials}</text>
  </svg>`;
}
```

---

## Design Tokens (Swap for Your Brand)

```css
:root {
  --bg:        #F5F0E8;  /* slide background */
  --accent-1:  #E8403A;  /* primary accent (red) */
  --accent-2:  #2B5BA8;  /* secondary accent (blue) */
  --accent-3:  #F2B02E;  /* tertiary accent (gold) */
  --dark:      #1A1A1A;  /* near-black */
  --text:      #3A3530;  /* body text */
  --shell-bg:  #2a2a2a;  /* sidebar + bars */
  --shell-top: #222;     /* topbar */
}
```

---

## Deployment

**Vercel (recommended):**
```
# Just connect the repo — zero config needed for a single HTML file.
# Or from the CLI:
npx vercel
```

**Local preview (for development):**
```json
// .claude/launch.json
{
  "version": "0.0.1",
  "configurations": [{
    "name": "my-course",
    "runtimeExecutable": "npx",
    "runtimeArgs": ["-y", "serve", "-l", "4319", "."],
    "port": 4319
  }]
}
```

> **Note:** Python's `http.server` will fail with a `PermissionError` in Claude Code's sandboxed environment. Use `npx serve` instead.

---

## Checklist for a New Project

- [ ] Copy the shell HTML above into `index.html`
- [ ] Update `<title>` and `.sl-course-title`
- [ ] Update `TOTAL` and `HAS_MEDIA` array length
- [ ] Add sidebar menu items (one per slide, slides 1+ start `locked`)
- [ ] Add slide divs inside `.sl-content`
- [ ] Choose a slide layout pattern (A/B/C) per slide
- [ ] Define your design palette (swap hex values in CSS)
- [ ] Put all content in JS arrays; render via functions
- [ ] Set all `url: null` placeholders — replace with real URLs before publishing
- [ ] Verify on Chrome, Firefox, and Safari at 1024px+ width
- [ ] Test sequential unlock: can't jump ahead, can revisit completed slides
- [ ] Test Prev/Next disabled states at first and last slides

---

*Template extracted from A Jazz Lover's Guide to Chicago, June 2026.*
