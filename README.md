# ☽ Hilal — হিলাল

### *Moon Sighting, Understood. — চাঁদ দেখা, সহজভাবে।*

> **A fully self-contained, bilingual (English & Bengali), astronomically crescent moon visibility web application — Everything runs in your browser.**

---

## ✨ Live Preview

Open `MoonSighting` directly in any modern browser. No server needed. No installation. Just download and open.

[![Live Site](https://img.shields.io/badge/Live%20Site-MoonSighting-blue)](https://www.mydul.me/Hilal/)

---

## 🌙 What Is This?

**Hilal Guide** is a comprehensive educational and practical reference tool for determining Islamic crescent moon (*hilal*) visibility. It combines:

- **Rigorous astronomical calculations** (Jean Meeus algorithms + Odeh 2006 criterion)
- **Islamic scholarly context** (hadith references, fiqh perspectives, authority comparisons)
- **Practical tools** (interactive world map, visibility checker, step-by-step methods)
- **Full bilingual support** — every word in both English and Bengali (বাংলা)

The project was born from a real problem: moon sighting disputes in Bangladesh and across the Muslim world cause unnecessary confusion every Ramadan and Eid. The solution is not more expensive telescopes — it is better understanding of what astronomy can already tell us, combined with proper Islamic principles.

---

## 🗺️ The Map — How It Works

The centrepiece of the application is a **self-drawn crescent visibility world map** — rendered entirely in JavaScript on an HTML5 Canvas with no external map tiles, no iframes, and no dependency on any third-party map service.

### Astronomical Engine

The map engine implements the full **Jean Meeus "Astronomical Algorithms"** pipeline:

```
Hijri month + year
      ↓
Lunation number k  (calibrated offset: Shawwal 1447 = 19 Mar 2026 01:26 UTC ✓)
      ↓
New Moon JD  (Julian Day of conjunction, accurate to ±2 minutes)
      ↓
Moon & Sun ecliptic longitudes at local sunset for every grid point
      ↓
Arc of Light (ARCL) = angular separation between Moon and Sun
      ↓
Arc of Vision (ARCV) = altitude difference at sunset
      ↓
Crescent width W + Odeh visibility threshold V
      ↓
q-value = ARCV − V  →  Zone classification
```

### Odeh 2006 Zone Classification

| Zone | q-value | Colour | Meaning |
|------|---------|--------|---------|
| A | q ≥ 0.216 | 🟢 Dark Green | Easy naked-eye sighting |
| B | −0.014 ≤ q < 0.216 | 🟡 Yellow-Green | May need optical aid |
| C | −0.160 ≤ q < −0.014 | 🟡 Yellow | Only with optical instruments |
| D | q < −0.160, arcl ≥ 5° | 🟠 Orange | Extremely marginal |
| X | Moon below horizon / before conjunction | ⬛ Dark Grey | Not visible |

### Rendering

- **2° grid** of q-values computed across the globe
- **Bilinear pixel interpolation** — every pixel blends the four nearest grid points, producing smooth curved colour bands (not blocky squares)
- **Semi-transparent land overlay** — continent shapes drawn on top at ~32% opacity so zone colours show through land areas
- **Crisp border strokes** — white-blue coastlines for contrast
- Tropics of Cancer & Capricorn marked with dashed gold lines (astronomically relevant)
- Country marker with radial-gradient gold dot and readable label pill

### Key Calibration

```javascript
// Shawwal 1447 new moon = 19 March 2026 01:26 UTC  (verified ✓)
// al-habib.info reference shows: Conjunction: Thursday, 19-03-2026 01:23 UTC
function hijriToK(hYear, hMonth) {
    return (hYear - 1) * 12 + (hMonth - 1) - 17037;
}
```

The offset `17037` was derived by computing the number of lunations between the Hijri epoch (JD 1948439.5) and the Meeus J2000 new moon reference (k=0, JD 2451550.097), then cross-validated against the al-habib.info map for Shawwal 1447.

---

## 📐 The Visibility Checker

The interactive checker takes four user-supplied values and applies the classical Odeh/Yallop criteria:

| Input | Hard Failure | Marginal | Good |
|-------|-------------|----------|------|
| Angular Separation (ARCL) | < 10° | 10°–10.5° | ≥ 10.5° |
| Hours Since Conjunction | < 15h | 15h–18h | ≥ 18h |
| Illumination % | < 0.5% | 0.5%–1% | ≥ 1% |
| Moonset Lag (min) | ≤ 0 | 0–20 min | ≥ 20 min |

Results are **bilingual** and **explanatory** — not just "pass/fail" but a specific reason for each failing criterion, telling the user exactly what is wrong and why it is scientifically impossible or marginal.

Where to get values:
- **Angular Separation** → [mooncalc.org](https://mooncalc.org)
- **Hours Since Conjunction** → [timeanddate.com](https://www.timeanddate.com/astronomy/moon/)
- **Illumination %** → [theskylive.com](https://theskylive.com/)
- **Moonset Lag** → [timeanddate.com](https://www.timeanddate.com/astronomy/moon/) (moonset time minus sunset time)

---

## 🏗️ Architecture

```
hilal-guide.html          ← The entire application. One file.
│
├── <style>               CSS — dark night theme, responsive grid, animations
├── HTML pages            Six SPA pages (Home, Basics, Science, Methods, Checker, Sources)
└── <script>
    ├── Stars engine      Animated starfield (canvas, requestAnimationFrame)
    ├── SPA router        showPage() — no URL changes, just show/hide
    ├── Lang system       EN / বাং toggle, localStorage persistence
    ├── Mobile nav        Hamburger menu, outside-click dismiss
    ├── Astronomy engine  Meeus + Odeh — all pure JavaScript, zero dependencies
    ├── Map renderer      Canvas pixel rendering + world outlines
    ├── Checker           Four-input visibility decision engine
    └── Article viewer    Expandable full article with language tabs
```

### Single-File Design Philosophy

The entire application — all HTML, CSS, JavaScript, world map data, astronomical algorithms, bilingual strings, and content — lives in **one `.html` file**. This is a deliberate choice:

- ✅ Works offline, on any device, in any browser
- ✅ Can be shared as a single attachment
- ✅ No build tools, no Node.js, no package.json
- ✅ No CDN dependencies at runtime (only Google Fonts, which degrade gracefully)
- ✅ Can be hosted on GitHub Pages, Netlify, any static host, or just a USB drive

---

## 📖 Content Pages

### 🏠 Home
The hero section with the animated crescent moon graphic and the **Generate Visibility Map** tool. Select an Islamic month, Hijri year, sighting day, and country — click Generate — and the map appears instantly, drawn from scratch using the astronomical engine.

### 📖 Basics
The foundational distinctions every observer needs to know:
- Difference between conjunction and hilal visibility
- How Saudi Arabia, Turkey, Bangladesh, Morocco and others each decide
- The Bukhari hadith (1907) on completing 30 days
- Accept / Reject decision framework with specific criteria

### 🔬 Science
The astronomy behind visibility:
- What modern astronomy can and cannot determine
- Minimum criteria table (Odeh 2006)
- Illumination percentage visual guide
- Why claims before 15 hours are scientifically impossible

### ✅ Methods
Three practical verification methods (A, B, C) with direct links to the tools needed for each. The telescope debate — why expensive instruments are unnecessary and potentially invalid under classical fiqh.

### 🔍 Checker
The interactive visibility checker with a full "How It Works" section explaining each input, where to find the values, and what the thresholds mean. Includes a quick-reference table.

### 📚 Sources
Six authoritative online sources with descriptions. Scholarly references (Odeh 2004, Yallop 1997, Jean Meeus 1991, Ṣaḥīḥ al-Bukhārī 1907). Featured article by Faiz Ahmed Taiyeb (BUET EEE, Software Architect, Vodafone Netherlands) — published in Desh Rupantor, 24 April 2020 — reproduced in full in both English and Bengali with expandable reader.

---

## 🌍 Bilingual System

Every piece of user-facing text exists in both English and Bengali. The system works via CSS attribute selectors:

```css
[data-lang="bn"] .en-text { display: none; }
[data-lang="bn"] .bn-text { display: inline; }
```

The toggle is fixed top-right on all pages. Language preference is saved to `localStorage`. The article reader has its own independent language tabs (so a site-English user can still read the Bengali article).

Bengali font: **Hind Siliguri** (Google Fonts) — chosen for its clean rendering at all sizes and good compatibility with Bangla numerals.

---

## 📱 Responsive Design

| Screen | Behaviour |
|--------|-----------|
| Desktop (> 768px) | Full horizontal nav, wide map, 3–4 column grids |
| Tablet (500–768px) | Nav compresses, grids reflow to 2 columns |
| Mobile (< 768px) | Hamburger menu, single-column layout, smaller canvas, reduced font sizes |

The map canvas dynamically resizes to its container width on every render. The hamburger menu closes automatically on outside tap and syncs active-page highlight with the desktop nav.

---

## 🔭 The Telescope Debate

A note that is central to the philosophy of this project, drawn from the featured article:
This project demonstrates that everything needed to make a scientifically sound hilal determination is available for free, runs in a browser, and requires no hardware at all.

---

## 📚 References & Sources

| Reference | Use in this project |
|-----------|-------------------|
| **Jean Meeus** — *Astronomical Algorithms*, 2nd ed. (1991) | New Moon JD formula (Chapter 49), Moon/Sun position calculations |
| **Odeh, M.S.H.** — *New Criterion for Lunar Crescent Visibility* (2004) | q-value formula, zone thresholds, visibility criterion |
| **Yallop, B.D.** — *A Method for Predicting the First Sighting of the New Crescent Moon* (1997) | V-function formula (same as Odeh basis) |
| **Schaefer, B.E.** — *Astronomical Limits for the Naked-Eye* (1991) | Theoretical background on visibility limits |
| **Ṣaḥīḥ al-Bukhārī, Hadith 1907** | Islamic textual basis for naked-eye requirement and 30-day rule |
| **al-habib.info Hilal Map** | Reference map for algorithm calibration and zone validation |
| **Faiz Ahmed Taiyeb** — *Desh Rupantor*, 24 April 2020 | Featured article on Bangladesh moon sighting reform |

---

## 🤲 Credits & Acknowledgements

**Designed and Developed by Mydul Islam Rashed**

Inspired by the work of:
- [Al-Habib.info](https://www.al-habib.info/islamic-calendar/hilalmap-crescent-moon-visibility-map.htm) — Hilal Map (Odeh 2006 criterion, Peter Hayes script 1999–2015)
- [Moonsighting.com](https://www.moonsighting.com/) — Khalid Shaukat's visibility predictions
- [Utrecht University](https://webspace.science.uu.nl/~gent0113/islam/islam_lunvis_current_year.htm) — Dr. Robert Harry van Gent's lunar visibility research
- [Faiz Taiyeb Ahmed](https://www.facebook.com/ftaiyeb) — for the original article that frames the problem so clearly

---

## 📄 Licence

This project is released for free use, sharing, and adaptation for Islamic educational and community purposes.

If you use or adapt this work, a mention of the original author (Mydul Islam Rashed) and a link back to this repository would be appreciated — though not required.

---

## 🤝 Contributing

Contributions are welcome, especially:

- **More accurate world map outlines** — the current polygons are simplified; higher-resolution Natural Earth data would improve coastline accuracy
- **Additional languages** — the bilingual system is easy to extend; Urdu, Arabic, Malay, Turkish translations would serve many more users
- **Algorithm improvements** — a full topocentric ARCV calculation (accounting for observer latitude's effect on ecliptic angle relative to horizon) would improve accuracy at high latitudes
- **More Hijri years** — the year selector currently covers 1444–1453 AH; extending it is trivial

To contribute: fork the repository, make your changes to `hilal-guide.html`, and open a pull request with a description of what you changed and why.

---

## 🐛 Known Limitations

- The world map outlines are **simplified polygons** — small islands, fjords, and complex coastlines are not detailed
- The ARCV calculation uses a **simplified geocentric formula** — a full topocentric calculation with observer latitude correction would give slightly more accurate zone boundaries, particularly at latitudes above 55°N or below 40°S
- The Hijri→Julian Day conversion uses a **calibrated arithmetic approximation** — it is accurate to within ±1 day across the range 1440–1460 AH, which is sufficient for hilal determination
- **Atmospheric refraction** is not modelled — real visibility at the horizon can be enhanced by up to ~0.5° by refraction, which can occasionally push a borderline case into visibility

---
*© 2026 Mydul Islam · All Rights Reserved*
*Developed by Mydul Islam Rashed — please keep me in your prayers.*
