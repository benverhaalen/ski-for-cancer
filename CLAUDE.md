# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Website for **Ski For Cancer** — Theta Chi Psi Chapter's philanthropy at UW-Madison, running since 1969. Raises money for the MACC Fund (Midwest Athletes Against Childhood Cancer) and St. Baldrick's Foundation. Largest student-run philanthropy in Wisconsin. Over $1M raised lifetime. The primary audience is fraternity alumni receiving a link via text/email and being driven to donate.

## Tech Stack

- **Plain HTML/CSS/JS** — no framework, no build step, no dependencies
- **GitHub Pages** — auto-deploys on push to `main`, custom domain via CNAME
- **Domain:** www.skiforcancerweek.com
- **Why:** Zero maintenance burden. Any fraternity brother can edit text in the GitHub web UI. No node_modules, no build step, no dependency rot.

## Design Reference

Full design decisions documented in `docs/DESIGN.md`. Key points:

- **Colors:** Deep navy `#0B1F3A` (primary, with lighter variations to snowy off-white), Red `#C8181A` (accent/pop)
- **Layout:** Single-page with sticky nav, JustGiving progress bar, filmstrip gallery, donation-focused
- **Aesthetic:** Classic, classy, vintage ski heritage — NOT modern/trendy. Think vintage ski lodge, not startup landing page.
- **Heading font:** Bold/ExtraBold, UPPERCASE ONLY — never lowercase or title case
- **Font choice is TBD** — see DESIGN.md for direction. Body font should convey trust and elegance.

## Key Files

```
index.html     — entire site (single page)
styles.css     — all styles, CSS custom properties for yearly theme changes
script.js      — JustGiving API integration + filmstrip gallery + interactivity
docs/DESIGN.md — design philosophy, color palette, gallery spec, layout decisions
```

## JustGiving Integration

- App ID: `95b13ed6`
- 2026 Campaign: https://www.justgiving.com/campaign/skiforcancer26
- Campaign goal: $100,000
- API: `GET https://api.justgiving.com/{appId}/v1/fundraising/pages/{pageShortName}`
- Key fields: `grandTotalRaisedExcludingGiftAid`, `fundraisingTarget`, `totalRaisedPercentageOfFundraisingTarget`
- Fallback to hardcoded values if API/CORS fails

## Event Context

- **Founded:** 1969 at Cascade Mountain ($650 raised)
- **Affiliated with MACC Fund since:** 1978
- **Crossed $1M cumulative:** 2023
- **2025 total:** $130,782 ($114,518 via St. Baldrick's, rest via JustGiving)
- **2026 campaign:** JustGiving only, event date Feb 28 2026
- **Location:** Alpine Valley Ski Resort, Elkhorn, WI
- **Chapter house:** 210 Langdon Street, Madison, WI 53703
- **Contact:** skiforcancerweek@gmail.com

## Assets

- `assets/macc-fund-transparent.png` — MACC Fund logo (transparent bg, for light backgrounds)
- `assets/ski4-cancer-logo.png` — 2026 Ski For Cancer logo (white on transparent, for dark backgrounds)
