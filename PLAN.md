# Portfolio Website Plan

## Design Direction

A single-page portfolio for Ryan Gasparini — dark, typographically rich, blending
modern web conventions with classical Italian design sensibility.

**Italian motif** = not flags and pizza. Think: Roman inscriptions, Renaissance
proportion, Bodoni and Didot typefaces, warm stone and gold accents, generous
whitespace as a luxury, classical symmetry with modern asymmetric tension.

---

## Color Palette

| Role        | Color                | Hex       |
|-------------|----------------------|-----------|
| Background  | Near-black warm      | `#121010` |
| Surface     | Dark warm gray       | `#1a1716` |
| Primary text| Warm off-white       | `#e8e0d8` |
| Muted text  | Warm mid-gray        | `#8a7e74` |
| Accent      | Antique gold         | `#c9a84c` |
| Accent alt  | Terracotta           | `#b35c3a` |
| Dividers    | Subtle warm border   | `#2a2422` |

## Typography

**Primary typeface:** A high-contrast modern serif — candidates:
- **Playfair Display** (free, Google Fonts) — Didone classification, very Italian
- **Cormorant Garamond** (free, Google Fonts) — Renaissance roots, elegant

**Secondary typeface:** A clean sans-serif for body/functional text:
- **Inter** or **DM Sans** — neutral, modern, high readability at small sizes

**Typographic details:**
- Large display type for section headings (clamp-based fluid sizing)
- Generous line-height (1.6–1.8 for body)
- Letterspacing on uppercase labels (tracking)
- Thin horizontal rules as classical dividers
- Italic serif for callouts and emphasis

## Layout & Sections

Single HTML page, top-to-bottom scroll. Sections separated by generous
whitespace and fine horizontal rules.

### 1. Hero / Introduction
- Name set large in serif display type
- A short tagline or title ("Software Engineer") in spaced uppercase sans-serif
- Subtle decorative rule below
- No image in the hero — let typography dominate

### 2. About / Philosophy
- 2–3 sentences, set in larger body serif
- Italic pull-quote style
- Centered or narrow column (max ~600px) for optimal reading measure

### 3. Experience
- Timeline or stacked cards
- Company name in serif, role in sans-serif caps, dates in muted text
- Brief description per role
- Thin rules between entries

### 4. Skills / Expertise
- Grouped by category (Languages, Frameworks, Tools, etc.)
- Typographic treatment: category label in uppercase spaced sans, items listed below
- No progress bars or skill percentages — clean text-based presentation

### 5. Contact / Footer
- Minimal: email, GitHub, LinkedIn
- Links styled with gold accent on hover
- Small copyright line
- Optional: a classical decorative flourish or thin ornamental rule

## Technical Approach

- **Pure HTML + CSS** — no build step, no framework
- Single `index.html` in `public/`
- Single `style.css` in `public/css/`
- Google Fonts loaded via `<link>` (Playfair Display + Inter)
- CSS custom properties for colors and typography scale
- Responsive via fluid type (`clamp()`) and a simple breakpoint for mobile
- Smooth scroll behavior
- Minimal, semantic HTML5 (`<header>`, `<section>`, `<footer>`)
- No JavaScript required (pure CSS interactions)

## File Structure (after implementation)

```
public/
├── index.html          # Single page, all content
├── css/
│   └── style.css       # All styles, single file
└── img/
    └── ryan.jpg        # Keep existing profile image (if needed)
```

Removed: `base.css`, `header.css`, `cwnd.html`, font files (using Google Fonts instead).

## Content Needed from User

- [ ] Work experience entries (companies, roles, dates, descriptions)
- [ ] Skills list organized by category
- [ ] Short bio / about paragraph
- [ ] Contact links (email, GitHub, LinkedIn, etc.)
- [ ] Any specific tagline preference beyond "Software Engineer"
