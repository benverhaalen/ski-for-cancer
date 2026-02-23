# DESIGN.md

Design philosophy and decisions for skiforcancerweek.com.

---

## Mission

A single-page fundraising website that gets Theta Chi alumni to donate to the 2026 Ski For Cancer campaign. Every design decision serves this goal. The primary audience is fraternity alumni who receive a link via text or email.

## Aesthetic Direction

Classic, classy, vintage ski heritage. The site should feel like a well-designed poster hanging in a ski lodge — not a modern startup landing page. Think Swiss travel posters, mid-century resort branding, hand-painted lodge signage. The design should convey 57 years of tradition and over $1M raised — institutional weight, not youthful energy.

## Tech Stack

- **Plain HTML/CSS/JS** — no framework, no build step, no dependencies
- **GitHub Pages** — free hosting, auto-deploys on push to `main`, custom domain via CNAME
- **Domain:** www.skiforcancerweek.com
- **Why:** Maximum simplicity, zero maintenance burden. Any fraternity brother can update text directly in GitHub's web UI. No node_modules. Will still work in 10 years.

## Typography

### Heading Font: TBD

- Must be bold/extra-bold weight, ALWAYS UPPERCASE
- Never lowercase or title case for headings
- Should evoke authentic vintage ski poster typography — condensed display, Art Deco, slab serif, or classic geometric sans
- Must be free/open source (SIL Open Font License or equivalent) — available on Google Fonts or self-hostable
- `text-transform: uppercase` enforced in CSS

### Body Font: TBD

- Should convey trust, elegance, sophistication
- Highly readable on screens at body text sizes
- Pairs well with a bold condensed heading font (contrast between display and body)

## Color Palette

### 2026 Campaign: Deep Navy + Snowy White + Red Pop

| Role | Color | Hex | Usage |
|------|-------|-----|-------|
| Primary | Deep Navy | `#0B1F3A` | Main backgrounds, headers, footer, nav |
| Navy Light | Lighter Navy | `#1A3A5C` | Hover states, secondary backgrounds |
| Navy Muted | Muted Blue | `#2D5A7B` | Subtle accents, borders |
| Ice | Ice Blue | `#A8C8E0` | Soft highlights, secondary text on dark |
| Snow | Snowy Off-White | `#F0F4F8` | Alternating section backgrounds |
| White | Pure White | `#FFFFFF` | Main content background |
| Accent / CTA | Red | `#C8181A` | Donate button, progress bar fill, key highlights, urgent CTAs |
| Accent Hover | Dark Red | `#A01416` | Hover state for red elements |
| Text (dark) | Near Black | `#0D1B2A` | Body text on light backgrounds |
| Text (light) | Snow White | `#E8EDF2` | Text on dark/navy backgrounds |
| Text (muted) | Slate | `#5A6B7D` | Secondary text, captions, metadata |

### Color Logic

The palette is a monochromatic navy gradient from `#0B1F3A` through lighter blues to a snowy off-white `#F0F4F8`, evoking a winter mountain landscape — dark sky to bright snow. The single pop color `#C8181A` (red) is used sparingly for maximum impact: donate buttons, progress bar fill, and urgent calls to action. Red creates urgency and draws the eye immediately.

### Year-Over-Year Note

The campaign color theme changes yearly. Deep navy + red is 2026. The palette is defined via CSS custom properties (variables) so future maintainers can swap colors in one place.

## Donation Integration

- **Platform:** JustGiving
- **2026 Campaign:** https://www.justgiving.com/campaign/skiforcancer26
- **Campaign goal:** $100,000
- **App ID:** `95b13ed6`
- **API Endpoint:** `GET https://api.justgiving.com/{appId}/v1/fundraising/pages/{pageShortName}`
- **Key fields:** `grandTotalRaisedExcludingGiftAid`, `fundraisingTarget`, `totalRaisedPercentageOfFundraisingTarget`
- **Display:** Custom progress bar showing amount raised, goal, and percentage
- **CORS strategy:** Try direct fetch first, then JSONP (`?callback=`), then fall back to hardcoded values
- **Fallback:** If API fails, show static amount with link to JustGiving page
- **Donate link:** https://www.justgiving.com/campaign/skiforcancer26

## Layout (Single Page)

Single-page design with sticky nav + anchor links. Every section drives toward the donate action.

### Section Flow (Top to Bottom)

1. **Hero** — Event name in large heading text, centered. Campaign year. One-line mission statement. Big "DONATE NOW" button. Ski4 Cancer logo.
2. **Progress Bar** — Live JustGiving fundraising progress. Amount raised, goal, percentage. Donor/fundraiser count if available.
3. **Impact Numbers** — $1M+ raised since 1969 | 57 years | Largest student-run philanthropy in Wisconsin
4. **About** — What is Ski For Cancer, what is the MACC Fund, where the money goes
5. **History** — Timeline or historical highlights from 1969 to present. Placeholder for historical photos.
6. **Filmstrip Gallery** — Interactive photo gallery (see detailed spec below)
7. **Donate CTA** — Repeated donate section with impact framing
8. **Footer** — MACC Fund logo, Ski4 Cancer logo, links, contact info, Theta Chi connection

### Conversion Strategy

- Donate button in sticky nav (always visible on desktop)
- Hero has the primary CTA above the fold
- Progress bar leverages Goal Gradient Effect (urgency increases as goal approaches)
- Historical total ($1M+) establishes legitimacy and trust
- Donate CTA repeated after emotional content sections
- Mobile: floating bottom bar with donate button
- Red accent color on all donate buttons for maximum visibility

---

## Filmstrip Gallery — Detailed Specification

### Container

A single `<div>` with:
- `display: flex`
- `width: 100%`
- `height: 100vh` (or a fixed height like 480px)
- No gaps between children
- `overflow: hidden`

Direct children are image wrapper `<div>`s — one per photo.

### Image Wrappers

Each wrapper has:
- `flex: 1` at rest
- `position: relative`
- `overflow: hidden`
- `cursor: pointer`
- `transition: flex 0.4s cubic-bezier(0.25, 0.46, 0.45, 0.94)`

Inside each wrapper, an `<img>` with:
- `width: 100%; height: 100%`
- `object-fit: cover`
- `object-position: center`
- `display: block`
- `pointer-events: none`

The image fills the full wrapper regardless of width. As flex compresses the wrapper, the image crops — it never squashes or letterboxes. This is the core mechanic.

### Hover State (Strip Expansion)

On `mouseenter` of a wrapper:
- Apply `flex: 3` to that element (tunable — 3 gives 3x expansion relative to sibling `flex: 1`)
- All siblings passively compress because they remain at `flex: 1`
- The `transition` on each wrapper handles the animation for both expanding and compressing — no JS animation needed, pure CSS

### Year Label (Hover Reveal)

Inside each wrapper, a `<span>` absolutely positioned:
- `position: absolute`
- `bottom: 16px; left: 16px`
- Monospaced font, `font-size: 13px`, `letter-spacing: 0.1em`
- `color: rgba(255, 255, 255, 0.72)`
- `opacity: 0` at rest
- `transition: opacity 0.25s ease`
- `pointer-events: none`
- `z-index: 2`
- `text-shadow: 0 1px 4px rgba(0,0,0,0.4)` — legible over bright images without a backing pill

On parent `mouseenter`: `opacity: 1`. On `mouseleave`: `opacity: 0`.

Year value sourced from:
1. EXIF `DateTimeOriginal` parsed via JS (e.g. exifr library) on page load
2. Fallback: `data-year` attribute on the wrapper `<div>` read at init time

### Click → Lightbox

**Overlay:** A `<div id="overlay">` as direct child of `<body>`, outside the filmstrip:

At rest:
- `position: fixed; inset: 0`
- `background: rgba(0, 0, 0, 0)`
- `backdrop-filter: blur(0px)`
- `z-index: 100`
- `opacity: 0; pointer-events: none`
- `transition: opacity 0.35s ease, backdrop-filter 0.35s ease`

On click, add class `.active`:
- `opacity: 1; pointer-events: all`
- `background: rgba(0, 0, 0, 0.65)`
- `backdrop-filter: blur(6px)`

The backdrop-filter blurs everything behind the overlay (filmstrip + page content). The lightbox image is a child of the overlay, so it is NOT blurred.

**Lightbox container:** A `<div id="lightbox">` inside the overlay:

At rest:
- `position: absolute`
- `top: 50%; left: 50%`
- `transform: translate(-50%, -50%) scale(0.88)`
- `transition: transform 0.38s cubic-bezier(0.34, 1.26, 0.64, 1), opacity 0.3s ease`
- `opacity: 0`
- `max-width: 82vw; max-height: 80vh`
- `border-radius: 3px; overflow: hidden`
- `box-shadow: 0 32px 80px rgba(0,0,0,0.55), 0 0 0 1px rgba(255,255,255,0.06)`

On `.active`:
- `transform: translate(-50%, -50%) scale(1)`
- `opacity: 1`

The cubic-bezier `(0.34, 1.26, 0.64, 1)` gives a slight overshoot on open — not bouncy, just alive. Eases back out linearly on close.

Inside the lightbox, an `<img>` with:
- `width: 100%; height: 100%`
- `object-fit: contain` (NOT cover — full photo always visible, uncropped)
- `display: block`

### Dismiss (Three Triggers)

All call the same `close()` function:
1. Click on overlay itself (`e.target === overlay` check, not propagated from lightbox image)
2. `keydown` listener for Escape
3. Optional × button inside the lightbox container

`close()` removes `.active` from overlay and lightbox. After transition duration (350ms), sets lightbox `<img>` src to `""` to free memory.

### Layering Summary

```
body
├── .filmstrip              (z-index: 1, blurred by overlay backdrop-filter)
└── #overlay                (z-index: 100, fixed, rgba bg + backdrop-filter)
    └── #lightbox           (inside overlay, NOT blurred)
        └── img             (full photo, object-fit: contain)
```

### Mobile Considerations

On mobile (< 768px), the filmstrip should stack vertically or switch to a simpler swipeable carousel since hover is not available. The lightbox should still work on tap.

---

## Design Principles

1. **Donation-first** — Every design choice serves conversion. If it doesn't help someone donate, question whether it belongs.
2. **Trust through heritage** — Lean on the 57-year history, $1M+ raised, and institutional backing (MACC Fund) to build credibility.
3. **Classic over modern** — The aesthetic should feel timeless and classy, not trendy. Vintage ski lodge, not SaaS landing page.
4. **Simple and fast** — No unnecessary JavaScript, no heavy images without lazy loading, no animations that slow the page.
5. **Annually updatable** — Text, images, colors, and campaign links should be easy to swap each year. Use CSS variables for theming. Keep content in obvious, well-commented sections.
6. **Mobile-first** — Alumni will receive links via text/email and open on phones. Design for mobile, enhance for desktop.

## Assets

- `assets/macc-fund-transparent.png` — MACC Fund logo (transparent bg, for light backgrounds)
- `assets/ski4-cancer-logo.png` — 2026 Ski For Cancer logo (white on transparent, for dark backgrounds)
- Future: Event photos, historical images

## File Structure

```
ski-for-cancer/
  index.html          # The entire site
  styles.css          # All styles (CSS custom properties for theming)
  script.js           # JustGiving API, filmstrip gallery, lightbox, interactivity
  assets/
    macc-fund-transparent.png
    ski4-cancer-logo.png
    (event photos, historical images)
  docs/
    DESIGN.md         # This file
  CNAME               # www.skiforcancerweek.com (for GitHub Pages)
  CLAUDE.md
  README.md
  LICENSE
```

## Event History (For Content)

| Year | Milestone |
|------|-----------|
| 1969 | First event at Cascade Mountain — $650 raised for American Cancer Society |
| 1978 | Affiliation with MACC Fund — focus shifts to pediatric cancer research |
| 1981 | Cumulative fundraising reaches $100,000 |
| 2019 | 50th Anniversary — single year raises over $116,000 |
| 2023 | Crosses $1,000,000 cumulative milestone |
| 2025 | $130,782 raised across all platforms |
| 2026 | Current campaign — goal: $100,000, event date: Feb 28 |

## Content Sources

- Theta Chi Psi Chapter website: thetachiuw.org/ski-for-cancer
- JustGiving 2026 campaign: justgiving.com/campaign/skiforcancer26
- St. Baldrick's event page: stbaldricks.org/events/mypage/10811/2025
- Contact email: skiforcancerweek@gmail.com
- Chapter house: 210 Langdon Street, Madison, WI 53703
- Ski location: Alpine Valley Ski Resort, Elkhorn, WI
