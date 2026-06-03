# A Jazz Lover's Guide to Chicago
## Claude Code Handoff Brief

---

## Project Overview

This is a standalone HTML/CSS/JS e-learning course built to demonstrate that a fully functional, visually rich Articulate Storyline-style module can be built without Storyline. The course is designed for jazz appreciators (newcomer to deep fan) who are visiting or new to Chicago.

The course is a single HTML file. No frameworks, no build tools, no dependencies beyond two Google Fonts. It should run in any modern browser and deploy to Vercel as a static site with zero configuration.

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
Each artist gets a unique SVG avatar: solid color background + geometric shape overlay (circle, rect, diamond, triangle) + initials. Shape is determined by `index % 4`, color cycles through palette. Stroke: `rgba(255,255,255,0.35)`, fill: dark/light semi-transparent overlay.

---

## Course Shell / Chrome

### Structure
```
.sl-outer (full height container, border-radius: 6px)
  .sl-topbar (44px, dark #222, hamburger + course title)
  .sl-middle (flex row, fills remaining height)
    .sl-sidebar (240px, collapsible, course menu)
    .sl-content (flex: 1, slide viewport)
  .sl-bottombar (50px, dark #2a2a2a, nav controls)
```

### Sidebar
- Dark `#2a2a2a` background
- Menu items: locked (🔒 / dimmed), active (red left border), completed (✓ green)
- Unlocks sequentially as learner progresses
- Collapsible via hamburger toggle (width: 0 + opacity: 0)

### Bottom Bar
**CRITICAL:** All button styles must be fully inlined (not class-based) due to rendering inconsistency in the chat widget. In the real file, classes are fine.
- Background: `#2a2a2a`, border-top: `2px solid #555`
- All buttons: `background:#555; border:2px solid #ccc; border-radius:4px; color:#fff`
- Play button: circular (`border-radius:50%`)
- Slide counter: `font-weight:600`, same button style
- Prev/Next: `font-weight:700; text-transform:uppercase; letter-spacing:0.08em`
- **Conditional media controls:** A `HAS_MEDIA` array controls visibility of play/progress/volume/CC/settings. Currently all `false`. When audio is added, flip to `true` for that slide index.

### Navigation Logic
- `current` variable tracks active slide (0-indexed)
- `goToSlide(n)` handles all transitions: removes active, marks completed, adds active, unlocks next
- Prev/Next buttons use opacity `0.4` when disabled (not the `disabled` attribute, to avoid style conflicts)
- Progress bar: `(current / (TOTAL - 1)) * 100)%`

---

## Completed Slides

### Slide 0 — Welcome
**Status:** Complete

Layout: centered flex column, cream background, geometric color shapes (red circle TL, yellow rect TR, blue circle BR, blue rect BL).

Key elements:
- Eyebrow label (yellow bg, blue text)
- Title stack: "A Jazz Lover's" (72px Bebas) / "Guide to" (28px) / "Chicago" (72px, blue)
- Red divider bar
- Intro paragraph
- Parallelogram "In this course" overview (4 sections with colored dots)
- Red "Let's go →" CTA button

Decorative: two musical note characters (♬ ♩) as faint opacity-0.12 overlays.

---

### Slide 1 — Chicago's Jazz Story
**Status:** Complete

Layout: fixed header + yellow intro band + scrollable timeline. Header and intro band are `flex-shrink:0`.

Timeline structure:
```
.tl-spine-wrap (relative positioned, padding-left: 16px)
  .tl-spine (absolute, left: 75px, gradient colored vertical line)
  .tl-entry × 7 (flex row: year | dot | card)
    .tl-year (58px, right-aligned Bebas text)
    .tl-dot-wrap (20px, z-index:2)
    .tl-card (flex:1, colored left border, expand/collapse)
```

Expand mechanic: click entry → `.tl-card` toggles `.expanded` class (max-height: 52px → 500px). Toggle text: "Read more ↓" / "Read less ↑".

**7 timeline entries:**
1. 1900s–1920s: The Great Migration (red)
2. 1920s–1930s: Golden Age South Side Clubs (gold)
3. 1930s–1940s: Jazz Moves North (blue)
4. 1950s: Hard Bop, Beehive, Sun Ra (red)
5. 1960s: AACM (blue) — include Nicole Mitchell as AACM mention
6. 1970s–1990s: Institutions Take Root (gold)
7. 2000s–Today: A Living Tradition (near-black)

Tags: colored pill labels for key names/places within each expanded entry.

---

### Slide 2 — Who's Who in Chicago Jazz
**Status:** Complete

Layout: fixed header + 3 tabs + scrollable panel per tab.

**Tab 1: Chicago Originals**
2-column artist grid. Each card:
```
.artist-card
  .artist-card-top
    .artist-avatar (72×72 SVG — see Avatar System above)
    .artist-card-info (name, dates, preview text, colored top border)
  .artist-card-body (expandable bio, max-height: 0 → 200px)
  .artist-card-footer (▶ Listen placeholder | Read more toggle)
```

Artists (in order): Lovie Austin, Lil Hardin Armstrong, Nat King Cole, Dinah Washington, Von Freeman, Johnny Griffin, Clifford Jordan, Eddie Harris, Muhal Richard Abrams, Sun Ra.

Followed by **DuSable High School spotlight card** (dark parallelogram, gold label "✦ Spotlight").

**Tab 2: Chicago Connections**
Same 2-column artist grid format.
Artists: Louis Armstrong, Benny Goodman, Herbie Hancock, Nicole Mitchell.

**Tab 3: The Scene Today**
3-column dark grid cards (`.ww-scene-card`, background `#1A1A1A`).
Top stripe color cycles: red / blue / gold per `nth-child(3n+1/2/3)`.
Each card: name (Bebas), role (gold uppercase), description text, ▶ Link coming placeholder.

Artists: Marquis Hill, Makaya McCraven, Isaiah Collier, Juan Pastor (percussionist/composer, Afro-Peruvian roots, founder of Juan Pastor's Chicano), Kyle & Christian Swan, Charles Rick Heath, Sharel Cassidy, Dee Alexander, Alyssa Allgood, Tammy McCann, Kurt Elling, Patricia Barber, Thaddeus Tukes, Michael Allemana, Fareed Haque.

**Data architecture:** All artist content lives in `ORIGINALS`, `CONNECTIONS`, `TODAY` arrays. `listen: null` for all currently — replace with URL string when ready. Grid rendering is data-driven via `renderOriginals()`, `renderConnections()`, `renderToday()` functions.

---

### Slide 3 — Where to Hear Jazz in Chicago
**Status:** Complete

Layout: fixed header + yellow intro band + 3 tabs + scrollable panel per tab.

**Tab 1: Jazz Clubs** (2-column venue grid)
Each card: colored top border, venue name (Bebas), neighborhood tag (colored pill), preview text, expandable bio, "↗ Visit" link placeholder.

Venues: Jazz Showcase (South Loop, red), Green Mill (Uptown, blue), Andy's Jazz Club (Downtown, gold), Winter's Jazz Club (River North, red), Le Piano (Rogers Park, blue).

**Tab 2: Other Venues** (2-column venue grid, same format)
Venues: Constellation (Irving Park, blue), California Clipper (Humboldt Park, red), Symphony Center (Loop, gold), Ravinia Festival (Highland Park, near-black).

**Tab 3: Festivals & Events** (single-column festival cards)
Each card: colored left accent bar, name (Bebas), when label (colored), description text, "↗ Visit" link placeholder.

Festivals: Chicago Jazz Festival (Labor Day, red), Hyde Park Jazz Festival (Late September, blue), Chicago Winter Jazz Fair (February, gold), Jazz Institute of Chicago (Year-Round, near-black).

**Data architecture:** `CLUBS`, `VENUES`, `FESTIVALS` arrays. `url: null` for all — replace with string when ready. Rendered via `renderVenueGrid()` and `renderFestivals()`.

---

## Slides Still To Build

### Slide 4 — Test Your Jazz IQ (Optional Quiz)
**Status:** Not started

**Mechanic:** "Which came first?" binary comparison quiz that builds a timeline as you go.

**Starting state:**
3 anchor events pre-placed on the timeline as fixed reference points:
- The Great Migration begins (1910)
- AACM founded (1965)
- Chicago Jazz Festival launched (1979)

**Each question:**
1. A new event is presented to the learner
2. The quiz identifies the nearest already-placed neighbor on the timeline
3. Question is posed: "Does [new event] come BEFORE or AFTER [neighbor event]?"
4. Learner clicks Before or After
5. Correct/incorrect feedback shown briefly
6. Event is placed correctly on the growing timeline regardless of answer
7. Next question

**Per round:** 8 events drawn randomly from the bank (excluding the 3 anchors).

**Scoring:** Simple correct/incorrect per question. End screen shows score (X/8), which events they got right, and the correct years for all. Low-stakes framing — "Test your jazz IQ" not "pass/fail."

**Opt-out:** Before the quiz starts, learner is shown a choice: "Take the quiz" or "Skip to Jazz Calendar." Both options advance to their respective slide.

**Event bank (20+ events to draw from randomly):**
Build this out — suggested events include:
- Louis Armstrong arrives in Chicago (1922)
- Sunset Cafe opens on The Stroll (1921)
- Nat King Cole forms his trio in Chicago (1937)
- Dinah Washington joins Lionel Hampton (1943)
- Jazz Showcase founded by Joe Segal (1947)
- Sun Ra arrives in Chicago (1946)
- Sun Ra leaves Chicago for New York (1961)
- Johnny Griffin records "The Little Giant" (1959)
- Eddie Harris records "Exodus" (1961)
- Jazz Institute of Chicago founded (1969)
- AACM founded by Muhal Richard Abrams (1965)
- Chicago Jazz Festival first year (1979)
- Art Ensemble of Chicago forms (1969)
- Hyde Park Jazz Festival first year (2009)
- Green Mill opens (1907)
- DuSable High School opens (1935)
- Captain Walter Dyett begins at DuSable (1931)
- Kurt Elling begins Green Mill residency (1991) *approximate — verify*
- Patricia Barber begins Monday nights at Green Mill (1984) *approximate — verify*
- Chicago Winter Jazz Fair first year (verify)
- Makaya McCraven releases "In the Moment" (2015)
- Jazz Showcase moves to South Loop (verify current location year)

*Note: Verify exact dates for items marked — these are approximate from training data.*

**Visual design:**
- Cream background, consistent with other slides
- Timeline rendered as a horizontal bar with placed events appearing as colored dots with labels
- Question card appears above the timeline: event name, Before/After buttons (red and blue)
- Correct: brief green flash, event placed. Incorrect: brief red flash, correct placement shown.

---

### Slide 5 — Plan Your Jazz Calendar
**Status:** Not started

**Mechanic:** Personal event planner + venue map.

**Left panel — Event list + entry form:**
- Form fields: Date (date picker), Time (time input), Performer(s) (text input), Venue (dropdown: all named venues + "Other / write in")
- "Add to Calendar" button
- Growing list of added events below the form
- Click an event in the list → highlight the corresponding venue pin on the map

**Right panel — Chicago neighborhood map:**
- SVG map of Chicago (styled in course palette — NOT Google Maps embed)
- Venue pins for all named venues (Jazz Showcase, Green Mill, Andy's, Winter's, Le Piano, Constellation, California Clipper, Symphony Center/Orchestra Hall)
- Ravinia gets a note that it's in Highland Park (off the main Chicago map)
- Pin shows venue name on hover
- Pins highlight (gold, enlarged) when corresponding event is selected in the list
- Click a pin → highlight all events at that venue in the list

**Below the map — Venue Calendar Reference:**
Collapsible panel listing all venues with "↗ Calendar" links (currently placeholders, to be filled in).

**Visual design:**
- Two-column layout (form+list left, map right)
- SVG map styled in course palette: neighborhoods as subtle fills, lake as blue, venue pins as colored circles with Bebas initials
- Map neighborhoods to include at minimum: Loop, South Loop, Near North, Uptown, Rogers Park, Hyde Park, Humboldt Park, Irving Park

---

## File Structure (Target)

```
jazz-chicago/
  index.html          ← single file, entire course
  HANDOFF.md          ← this document
  README.md           ← for GitHub
```

No build process. No package.json. Pure HTML/CSS/JS. Deploy to Vercel as static site — zero config needed, just connect the repo.

---

## Known Issues / Nice-to-Haves

- **Avatar shapes:** SVG avatar shape fix (stroke + semi-transparent fill) was validated in a test widget but not yet applied to the full Who's Who slide. Apply the corrected `makeAvatar()` function from the shape test when rebuilding.
- **Bottom bar play button:** Toggle between ▶ and ❚❚ is wired but needs testing in the real file.
- **Listen / Visit links:** All `null` throughout. Owner to source and fill in.
- **Photos:** All avatars are geometric placeholders. Real photos to be sourced (public domain / press photos) and dropped in as `<img>` replacements for the SVG avatars.
- **Audio:** `HAS_MEDIA` array is all `false`. If voiceover or ambient audio is added later, flip the relevant index to `true` and wire up the audio element.
- **Mobile:** Not yet optimized. Course is designed for desktop/laptop viewing consistent with Storyline conventions.

---

## Decisions Made (Don't Re-litigate)

- No dark/noir aesthetic — warm, colorful poster-art palette
- No Storyline `.story` file — pure HTML/JS equivalent
- Sequential unlock navigation (not free navigation) — matches Storyline default
- Media controls hidden when no media — intentional UX improvement over Storyline
- Timeline quiz uses "which came first?" binary comparison, NOT drag-and-drop
- Jazz Calendar uses SVG map, NOT Google Maps embed
- All data (artists, venues, events) in JS arrays at top of script for easy editing
- Fonts loaded from Google Fonts CDN (Bebas Neue + Inter)

---

*Handoff prepared: June 2026. Built collaboratively in Claude.ai chat before moving to Claude Code for completion.*
