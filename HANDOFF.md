# A Jazz Lover's Guide to Chicago
## Claude Code Handoff Brief

---

## Project Overview

A standalone HTML/CSS/JS e-learning course built to demonstrate that a fully functional, visually rich Articulate Storyline-style module can be built without Storyline. The course is designed for jazz appreciators (newcomer to deep fan) who are visiting or new to Chicago.

The course is a **single HTML file**. No frameworks, no build tools, no dependencies beyond two Google Fonts. It runs in any modern browser and deploys to Vercel as a static site with zero configuration.

---

## Visual Design System

### Palette
| Role | Value |
|------|-------|
| Background (warm cream) | `#F5F0E8` |
| Red (primary accent) | `#E8403A` |
| Blue (secondary accent) | `#2B5BA8` |
| Gold (tertiary accent) | `#F2B02E` |
| Near-black | `#1A1A1A` |
| Body text | `#3A3530` |

### Typography
- **Display / Headers:** Bebas Neue (Google Fonts)
- **Body:** Inter (Google Fonts)
- Import: `https://fonts.googleapis.com/css2?family=Bebas+Neue&family=Inter:wght@400;500;600;700&display=swap`

### Design Principles
- Inspired by vintage Chicago Jazz Festival posters (1979–1989 era)
- Bold geometric shapes (circles, rectangles) as decorative elements
- Flat, no gradients or drop shadows on content (subtle `box-shadow: 2px 2px 0` only)
- Poster-art aesthetic: colorful, modern, warm — NOT dark/noir
- Parallelogram shapes used for featured/spotlight content (`transform: skewX(-8deg)` with inner `skewX(8deg)` counter-rotation)

### Avatar System
Each artist gets a unique SVG avatar: solid color background + geometric shape overlay (circle, rect, diamond, triangle) + initials. Shape determined by `index % 4`, color cycles through palette. Stroke: `rgba(255,255,255,0.35)`, fill: dark/light semi-transparent overlay.

---

## Course Shell / Chrome

### Responsive Layout
The shell fills the full browser window (Storyline-style reflow). `.sl-outer` is `width:100vw; height:100vh` with `min-width:320px; min-height:480px` floors. `html, body` are `height:100%` with no padding. The topbar and bottombar are fixed-height (`flex-shrink:0`); the middle content area grows to fill whatever remains.

### Structure
```
.sl-outer (100vw × 100vh)
  .sl-topbar (44px, dark #222, hamburger + course title)
  .sl-middle (flex row, fills remaining height)
    .sl-sidebar (240px, collapsible, course menu)
    .sl-content (flex: 1, slide viewport)
  .sl-bottombar (50px, dark #2a2a2a, nav controls)
```

### Sidebar
- Dark `#2a2a2a` background
- Menu items: locked (🔒 / dimmed), active (red left border), completed (✓ green)
- Sequential unlock: each slide unlocks the next on arrival
- **Completed items are clickable** — can navigate back to any visited slide
- `onclick` handlers wired dynamically via `goToSlide()` when a slide is first reached
- Collapsible via hamburger toggle (width: 0 + opacity: 0)

### Bottom Bar
- Background: `#2a2a2a`, border-top: `2px solid #555`
- All buttons: `background:#555; border:2px solid #ccc; border-radius:4px; color:#fff`
- Play button: circular (`border-radius:50%`)
- Slide counter: `font-weight:600`
- Prev/Next: `font-weight:700; text-transform:uppercase; letter-spacing:0.08em`
- **Conditional media controls:** `HAS_MEDIA` array controls visibility of play/progress/volume/CC/settings. Currently all `false`. Flip to `true` when audio is added.

### Navigation Logic
- `current` variable tracks active slide (0-indexed)
- `goToSlide(n)` handles all transitions: removes active, marks completed, adds active, unlocks next, wires onclick
- Forward navigation always allowed (stepping `current + 1`); sidebar jumping requires target to not be locked
- Prev/Next buttons use opacity `0.4` when disabled

---

## Completed Slides

### Slide 0 — Welcome
Centered flex column, cream background, geometric color shapes. Title stack, red divider, intro paragraph, parallelogram "In this course" overview (4 sections), red "Let's go →" CTA. Decorative musical note overlays at opacity 0.12.

---

### Slide 1 — Chicago's Jazz Story
Fixed header + yellow intro band + scrollable vertical timeline.

**Timeline structure:**
- `.tl-entry` is a flex row: year column | dot | `.tl-card`
- `.tl-card` contains: `.tl-card-title` + `.tl-card-body` (collapses) + `.tl-toggle` (always visible)
- Collapse mechanic: `.expanded` class is toggled on `.tl-card`; CSS rule `.tl-card.expanded .tl-card-body` transitions `max-height` from `0` → `600px`. The toggle is a sibling of `.tl-card-body` inside `.tl-card` (not inside the body), so it stays visible when collapsed.

7 eras: Great Migration (red) → South Side Golden Age (gold) → Jazz Moves North (blue) → Hard Bop / Sun Ra (red) → AACM (blue) → Institutions Take Root (gold) → A Living Tradition (near-black).

---

### Slide 2 — Who's Who in Chicago Jazz
Fixed header + 3 tabs + scrollable panel per tab.

**Tab 1: Chicago Originals** — 2-column artist grid (10 artists) + DuSable spotlight card  
**Tab 2: Chicago Connections** — 2-column grid (4 artists)  
**Tab 3: The Scene Today** — 3-column dark grid (15 artists)

Data in `ORIGINALS`, `CONNECTIONS`, `TODAY` arrays. `listen: null` throughout — replace with URL when ready.

---

### Slide 3 — Where to Hear Jazz in Chicago
Fixed header + yellow intro band + 3 tabs.

**Tab 1: Jazz Clubs** — Jazz Showcase (South Loop), Green Mill (Uptown), Andy's Jazz Club (Near North), Winter's Jazz Club (River North), Le Piano (Rogers Park)  
**Tab 2: Other Venues** — Constellation (Irving Park), California Clipper (Humboldt Park), Symphony Center (Loop), Ravinia Festival (Highland Park)  
**Tab 3: Festivals & Events** — Chicago Jazz Festival, Hyde Park Jazz Festival, Chicago Winter Jazz Fair, Jazz Institute of Chicago

Data in `CLUBS`, `VENUES`, `FESTIVALS` arrays. `url: null` throughout — replace with string when ready.

---

### Slide 4 — Test Your Jazz IQ
Fixed header + scrollable stage with 3 screens (`#q-intro`, `#q-play`, `#q-results`). Navigating to the slide always resets to the intro screen.

**Intro screen:** Explains the mechanic. "Take the quiz" → play; "Skip to Calendar →" → slide 5.

**Play screen:**
- 3 fixed anchors pre-placed: Great Migration (1910), AACM founded (1965), First Chicago Jazz Festival (1979)
- Each round: sticky event card at top names the new event; the nearest already-placed neighbor is highlighted in the timeline with a "Compare" badge. A red "▲ Here — earlier" slot sits above it; a blue "▼ Here — later" slot sits below.
- Clicking a slot records the answer, places the event in correct chronological order (gold flash), shows feedback, and auto-advances after 1.7s.
- 8 rounds drawn randomly from the 18-event bank (anchors excluded from draw).
- Tie rule: if new event year equals neighbor year, either answer accepted.

**Results screen:** X/8 score, tiered blurb, chronological results list, "Play again" / "Continue →".

**Event bank (18 events, years 1907–2015):** See `QUIZ_EVENTS` array. Dates for Patricia Barber's Green Mill start (1994) are approximate.

---

### Slide 5 — Plan Your Jazz Calendar
Fixed header + two-column body (left: form + lineup; right: map). Both columns scroll independently.

**Left — form + lineup:**
- Fields: date, time (`step="900"` → :00/:15/:30/:45), performer(s), venue dropdown (9 named venues + "Other / write in…")
- "+ Add to Lineup" button (validates that performer, date, and venue are all present)
- Events persist via `localStorage` (`jazz_calendar_events` key)
- Each saved event: performer, venue, formatted date/time, **＋ Google** calendar link, **＋ Apple / .ics** download, × delete
- Clicking an event selects its venue (highlights pin + shows popover)

**Right — SVG map:**
- Hand-built Chicago: Lake Michigan (east), Chicago River (fork visible), 8 labeled neighborhood zones
- 8 on-map pins + Ravinia (upper-left, ↑ arrow, dashed white ring indicating off-map non-contiguous)
- Selecting a pin shows a **floating popover** anchored at the pin: name, neighborhood, address, ↗ Calendar link, 📍 Google Maps link. Popover flips left/right to stay on-map.
- Count badge on pins when shows are booked; pin enlarges and turns gold when selected

**Data:** `MAP_VENUES` and `OFFMAP_VENUES` arrays, each with `{ id, name, hood, initials, color, x, y, address, url }`. All `url: null` — fill in. All `address` values are best-known approximations — **verify before publishing.**

**Calendar export:** `googleCalUrl()` builds Google Calendar deep-link; `downloadIcs()` generates valid VCALENDAR `.ics` blob. Both default missing time to 8:00 PM and assume 2-hour duration (`CAL_DURATION_MS`).

---

## File Structure

```
jazz-chicago/
  index.html           ← single file, entire course
  HANDOFF.md           ← this document
  STORYLINE_SHELL.md   ← reusable shell template for other projects
  README.md            ← for GitHub
  .claude/
    launch.json        ← preview server (npx serve -l 4319 .)
    memory/            ← Claude Code project memory
```

---

## Open Items (Content — Owner to Fill In)

| Item | Location | Action |
|------|----------|--------|
| Artist listen links | `ORIGINALS`, `CONNECTIONS`, `TODAY` arrays | Replace `listen: null` with URL string |
| Venue calendar links | `CLUBS`, `VENUES`, `FESTIVALS`, `MAP_VENUES`, `OFFMAP_VENUES` | Replace `url: null` with URL string |
| Venue addresses | `MAP_VENUES`, `OFFMAP_VENUES` | Verify street addresses before publishing |
| Artist photos | All slides with SVG avatars | Replace `makeAvatar()` SVG with `<img>` elements |
| Audio / voiceover | `HAS_MEDIA` array | Flip index to `true`; wire audio element |

---

## Decisions Made (Don't Re-litigate)

- No dark/noir aesthetic — warm, colorful poster-art palette
- No Storyline `.story` file — pure HTML/JS equivalent
- Sequential unlock navigation (not free navigation) — completed slides revisitable via sidebar
- Media controls hidden when no media — intentional UX improvement over Storyline
- Timeline quiz uses "which slot?" placement flanking the comparison event, NOT drag-and-drop
- Jazz Calendar uses hand-built SVG map, NOT Google Maps embed
- Ravinia is a map pin with dashed off-map ring, not a separate chip
- All data in JS arrays at top of script for easy editing
- Fonts loaded from Google Fonts CDN (Bebas Neue + Inter)
- Calendar export: Google link + universal `.ics` (no separate Outlook web link)
- Show duration defaults: 8 PM start, 2-hour run when user omits time
- Mobile not optimized — desktop/laptop viewing, consistent with Storyline conventions

---

*Handoff updated: June 2026. Slides 0–3 built in Claude.ai chat; slides 4–5, responsive shell, quiz redesign, and calendar/map built in Claude Code.*
