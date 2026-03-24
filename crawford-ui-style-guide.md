# Crawford Coaching — UI Style Guide & Changelog
> Reference document for GitHub Copilot. Use this to port the static HTML design system into any Next.js/React/Tailwind build, or to keep new UI consistent with existing pages.

---

## Project Overview

Single-domain site at **crawford-coaching.ca** consolidating three sub-brands under one visual identity:

| Brand | Accent | Purpose |
|-------|--------|---------|
| Crawford Coaching | `--brand-blue` `#2d86c4` | 1:1 and executive coaching |
| Synergize Group Fitness | `--syn-orange` `#e8632b` | Group training |
| Whole | `--whole-sage` `#7a9b6d` | 16-week group coaching program |

**Target stack:** Next.js 14+ (App Router) · Tailwind CSS · TypeScript · Vercel

**Status:** Phase 1 static HTML mockups complete. Phase 2 = production build.

---

## Changelog

### Timer UI Reskin — March 2026
Rebuilt `timer.html` from a standalone dark-blue app into a page that is visually continuous with the Synergize sub-brand.

**What changed:**

#### Fonts
- **Before:** `system-ui, -apple-system, Segoe UI, Roboto...` (generic system stack)
- **After:** Cormorant Garamond (display/timer digits) + Libre Baskerville (body/exercise names) + Jost (UI labels, buttons, nav). Google Fonts imported via `<link>`.

#### Colour palette
- **Before:** Custom blue-tinted dark scheme — `--bg: #0b1020`, `--card: #151b2f`, `--border: #2a3350`, `--accent: #66a3ff`
- **After:** Site palette — `--ink: #0e0f10`, `--slate: #1c2330`, `--slate-mid: #232f3e`, `--fog: #3d4a58`. Accent colour changed from blue `#66a3ff` → Synergize orange `#e8632b`. All timer engine JS variables aliased to CSS custom properties so the engine keeps working without modification.

#### Shape language
- **Before:** `border-radius: 16px` on cards, `border-radius: 12px` on buttons, `border-radius: 10px` on chips and inputs
- **After:** `border-radius: 4px` on cards, `border-radius: 2px` on buttons/chips/inputs, `border-radius: 1px` on pills. The site uses near-square corners throughout.

#### Navigation bar
- **Before:** Compact utility strip with favicon icon, "Crawford Coaching" text, and a "Back to Site" link
- **After:** Full-width frosted-glass nav matching the site's `.nav` component exactly — logo image (`logo_white_transparent.png`), uppercase tracked links (Home / Synergize / Coaching), and the "Book Intro" CTA ghost button. Synergize link is highlighted in `--syn-orange` as the active section.

#### Typography hierarchy
- **Before:** Bold weight `font-weight: 900` for the timer countdown digit. Generic `font-weight: 700` panel headings.
- **After:** Timer digits use Cormorant Garamond `font-weight: 300` — same elegance-before-power aesthetic as the site's hero headlines. Panel headings ("Quick EMOM Workout", "My Saved Workouts") use the serif display face at `font-size: 1.2rem`. The page `<h1>` is now `"Synergize Interval Timer"` in Cormorant Garamond with the word "Synergize" in italic orange.

#### Mode selector cards
- **Before:** `font-size: 16px; font-weight: 700` single label
- **After:** Three-level hierarchy per card — emoji icon · serif display title (`.mode-title`) · uppercase sans subtitle (`.mode-sub`)

#### Buttons
- **Before:** `border-radius: 12px`, `font-weight: 600`, no text-transform, blue primary
- **After:** `border-radius: 2px`, `font-weight: 400–500`, `text-transform: uppercase`, `letter-spacing: 0.14em`. Primary buttons use `--syn-orange`. Ghost and secondary buttons use `--slate-mid` background with `--fog` border.

#### Section eyebrow labels (Quick Start header)
- **Before:** Plain `font-size: 15px; font-weight: 700` heading
- **After:** Three-layer header matching the site's section pattern — small uppercase orange tag ("Crawford Coaching") + Cormorant Garamond display heading ("Quick Start") + uppercase Jost subtitle ("Choose a workout mode")

#### Footer
- **Before:** Single inline line with `color: var(--muted)` link
- **After:** Two-column footer matching the site footer — left: brand + page name / right: copyright. Uppercase Jost, `--mist` colour, `letter-spacing: 0.1em`.

#### Dynamic background (JS `setBg()`)
- **Before:** When timer interval colour is applied, fallback FG/muted values were `#000000` / `#4b5563` (light) and `#e7ecf3` / `#d0d7e0` (dark)
- **After:** Fallback values updated to site palette — `#0e0f10` / `#3d4a58` (light) and `#f5f3ef` / `#c8d4de` (dark)

#### No JS changes
Zero modifications to any timer logic, EMOM engine, audio, PWA install, localStorage, or workout generation code.

---

## Design Tokens

### CSS Custom Properties (paste into `:root`)

```css
:root {
  /* ── Background scale ── */
  --ink:        #0e0f10;   /* page background, deepest layer */
  --slate:      #1c2330;   /* section background, cards */
  --slate-mid:  #232f3e;   /* alternate section bg, button bg */
  --fog:        #3d4a58;   /* borders, dividers, button borders */

  /* ── Text scale ── */
  --mist:       #7a8fa3;   /* secondary text, labels, metadata */
  --pale:       #c8d4de;   /* body text on dark backgrounds */
  --white:      #f5f3ef;   /* primary text (warm white, not pure) */

  /* ── Brand accents ── */
  --brand-blue:       #2d86c4;
  --brand-blue-light: #4fa3d8;

  /* ── Synergize accents ── */
  --syn-orange:       #e8632b;   /* primary CTA, active states, eyebrows */
  --syn-orange-light: #f2844e;   /* hover state */
  --syn-amber:        #f5a623;   /* status tags (waitlist) */

  /* ── Whole accents ── */
  --whole-sage:       #7a9b6d;
  --whole-sage-light: #96b48a;

  /* ── Typography ── */
  --serif-display: 'Cormorant Garamond', Georgia, serif;
  --serif-body:    'Libre Baskerville', Georgia, serif;
  --sans:          'Jost', 'Helvetica Neue', sans-serif;
}
```

### Google Fonts Import

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,300;0,400;0,500;0,600;1,300;1,400&family=Libre+Baskerville:ital,wght@0,400;0,700;1,400&family=Jost:wght@200;300;400;500&display=swap" rel="stylesheet">
```

---

## Typography

| Role | Family | Weight | Size | Usage |
|------|--------|--------|------|-------|
| Hero headline | Cormorant Garamond | 300 | `clamp(2.4rem, 5vw, 4rem)` | Page heroes, section titles |
| Display / Pullquote | Cormorant Garamond | 400 italic | `clamp(1.5rem, 2.5vw, 2rem)` | Pullquotes, modal headings |
| Section heading | Cormorant Garamond | 300 | `clamp(1.8rem, 3vw, 2.6rem)` | Section H2s |
| Body copy | Libre Baskerville | 400 | `0.95rem` | Paragraph text |
| Body italic | Libre Baskerville | 400 italic | `0.95rem` | Testimonials, descriptions |
| UI label / nav | Jost | 300–400 | `0.78rem` | Nav links |
| Button text | Jost | 400 | `0.75–0.78rem` | All buttons |
| Eyebrow tag | Jost | 400 | `0.68rem` | Section category labels |
| Metadata / caption | Jost | 300 | `0.72rem` | Timestamps, credentials |

**Key rules:**
- `line-height: 1.8` for body text
- `letter-spacing: 0.18em` for nav links and buttons
- `letter-spacing: 0.28–0.3em` for eyebrow/tag labels
- `text-transform: uppercase` on all Jost UI elements
- No `font-weight: 700` or heavier on display headings — the serif's elegance does the work
- Never use bold Cormorant for headings. Use `300` or `400` only.

---

## Layout

```
Max content width:  1200px (centered with margin: 0 auto)
Section padding:    6rem 3.5rem (desktop) / 4rem 1.8rem (mobile)
Mobile breakpoint:  768px (nav collapses) / 900px (grids stack)
Grid gap (2-col):   4rem
```

**Section alternation:**
```
--ink → --slate → --ink → --slate-mid → --ink
```
Sections alternate background to create depth without borders.

---

## Component Patterns

### Navigation Bar

Fixed/sticky, frosted glass. Always spans full width.

```css
.nav {
  position: fixed; /* or sticky for tool pages */
  top: 0; left: 0; right: 0;
  z-index: 100;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 1.6rem 3.5rem;
  background: rgba(14,15,16,0.85);
  backdrop-filter: blur(12px);
  border-bottom: 1px solid rgba(255,255,255,0.05);
}
```

**Logo:** `<img>` at `height: 36px; width: auto`. Use `logo_white_transparent.png` on dark backgrounds. No text label alongside the logo.

**Nav links:**
```css
font-family: var(--sans);
font-weight: 300;
font-size: 0.78rem;
letter-spacing: 0.18em;
text-transform: uppercase;
color: var(--pale);
opacity: 0.85;
transition: opacity 0.2s, color 0.2s;
/* Active state: */
color: var(--syn-orange); /* or --brand-blue, --whole-sage for other sub-brands */
opacity: 1;
```

**CTA ghost button (nav):**
```css
font-family: var(--sans);
font-weight: 400;
font-size: 0.75rem;
letter-spacing: 0.16em;
text-transform: uppercase;
color: var(--white);
border: 1px solid rgba(245,243,239,0.45);
padding: 0.5rem 1.3rem;
border-radius: 1px;
transition: background 0.25s, border-color 0.25s;
/* Hover: */
background: rgba(245,243,239,0.1);
border-color: rgba(245,243,239,0.8);
```

---

### Section Eyebrow Label

The eyebrow is the small uppercase label that sits above every section headline. It has a decorative rule prepended via `::before`.

```css
.section__label {
  font-family: var(--sans);
  font-weight: 400;
  font-size: 0.68rem;
  letter-spacing: 0.3em;
  text-transform: uppercase;
  color: var(--syn-orange); /* or sub-brand accent */
  margin-bottom: 1.5rem;
  display: flex;
  align-items: center;
  gap: 0.8rem;
}
.section__label::before {
  content: '';
  display: block;
  width: 28px;
  height: 1px;
  background: var(--syn-orange);
}
```

HTML:
```html
<div class="section__label">Synergize Fitness</div>
<h2 class="section__headline">Group training that compounds.</h2>
```

---

### Buttons

Three variants. All use `border-radius: 1px` (site pages) or `border-radius: 2px` (tool UI).

**Primary (filled orange):**
```css
display: inline-block;
font-family: var(--sans);
font-weight: 400;
font-size: 0.78rem;
letter-spacing: 0.18em;
text-transform: uppercase;
color: var(--white);
background: var(--syn-orange);
border: 1px solid var(--syn-orange);
padding: 0.85rem 2.2rem;
border-radius: 1px;
transition: background 0.3s, box-shadow 0.3s;
/* Hover: */
background: var(--syn-orange-light);
box-shadow: 0 4px 24px rgba(232,99,43,0.35);
```

**Ghost (outline):**
```css
background: none;
border: 1px solid rgba(200,212,222,0.25);
color: var(--pale);
/* Hover: */
border-color: var(--syn-orange);
color: var(--white);
```

**Subtle (UI / secondary):**
```css
background: var(--slate-mid);
border: 1px solid var(--fog);
color: var(--pale);
border-radius: 2px;
/* Hover: */
background: var(--fog);
color: var(--white);
```

> Rule: Never use `font-weight: 600` or `700` on buttons. Weight `400–500` with tracked uppercase does the job.

---

### Cards

Used in tool UI (timer), not on marketing pages (which use section backgrounds instead).

```css
.card {
  background: var(--slate);
  border: 1px solid rgba(61,74,88,0.6); /* --fog at 60% */
  border-radius: 4px;
  padding: 1.25rem;
}
```

No `box-shadow`. Depth comes from the background colour difference between card and page.

---

### Border-Left Accent (Blockquote / List items)

Used for pullquotes, testimonials, and "Is This You?" questions.

```css
border-left: 2px solid var(--syn-orange);
padding-left: 2rem;
```

Testimonials use a faded version:
```css
border-left: 2px solid rgba(232,99,43,0.35);
```

---

### Credential / Status Tags

Small pill-shaped tags for credentials (ISSA, ICF) and schedule status (Open, Waitlist).

```css
/* Credential tag */
.coach__cred {
  font-family: var(--sans);
  font-weight: 400;
  font-size: 0.72rem;
  letter-spacing: 0.12em;
  text-transform: uppercase;
  color: var(--mist);
  padding: 0.35rem 0.8rem;
  border: 1px solid rgba(122,143,163,0.25);
  border-radius: 1px;
}

/* Schedule: Open */
color: #5bbf6b;
border: 1px solid rgba(91,191,107,0.3);

/* Schedule: Waitlist */
color: var(--syn-amber); /* #f5a623 */
border: 1px solid rgba(245,166,35,0.3);
```

---

### Section Headline Hierarchy

```html
<!-- Eyebrow + Headline + Body pattern (used on every section) -->
<div class="section__label">Category Label</div>

<h2 class="section__headline">
  <!-- Cormorant Garamond 300, clamp(1.8rem, 3vw, 2.6rem) -->
  The section heading goes here.
</h2>

<p class="section__body">
  <!-- Libre Baskerville 400, 0.95rem, line-height 1.8, color --pale -->
  Body copy. Short paragraphs. Conversational.
</p>
```

---

### Photo Frame Decoration

An offset border frame effect used on portrait photos (coach bio, What It Is section).

```css
/* Photo sits in a relative-positioned wrapper */
.photo-wrap { position: relative; }

.photo {
  width: 100%;
  aspect-ratio: 3/4; /* or 1/1 for coach portrait */
  object-fit: cover;
  border-radius: 2px;
  display: block;
}

/* Offset frame: appears behind and offset from the photo */
.photo-frame {
  position: absolute;
  bottom: -12px; left: -12px; /* or top: -12px; right: -12px; */
  width: 100%; height: 100%;
  border: 1px solid var(--syn-orange);
  opacity: 0.25;
  border-radius: 2px;
  pointer-events: none;
}
```

---

### Numbered Phase / Step List

Used in "What to Expect" (01 / 02 / 03 phases).

```css
.phase {
  display: grid;
  grid-template-columns: 80px 1fr;
  gap: 1.5rem;
  align-items: start;
}
.phase__marker {
  font-family: var(--serif-display);
  font-weight: 300;
  font-size: 2.8rem;
  line-height: 1;
  color: rgba(232,99,43,0.2); /* ghost orange */
  text-align: center;
}
.phase__title {
  font-family: var(--sans);
  font-weight: 400;
  font-size: 1.05rem;
  color: var(--white);
  margin-bottom: 0.5rem;
}
.phase__body {
  font-family: var(--serif-body);
  font-size: 0.9rem;
  line-height: 1.8;
  color: var(--pale);
}
```

---

### Tool Callout Card

Slim inline card used to link to tools from within a content page.

```css
.tool-callout__inner {
  max-width: 700px;
  margin: 0 auto;
  display: flex;
  align-items: center;
  gap: 1.2rem;
  padding: 1rem 1.5rem;
  border: 1px solid rgba(200,212,222,0.1);
  border-radius: 2px;
  background: rgba(255,255,255,0.02);
}
/* Icon: font-size: 1.4rem */
/* Title: var(--sans) 400 0.85rem, color --white */
/* Description: var(--serif-body) 0.8rem, color --mist */
/* Link arrow: var(--sans) 400 0.75rem uppercase tracked, color --syn-orange */
```

---

### Footer

```css
.footer {
  padding: 3rem 3.5rem;
  display: flex;
  align-items: center;
  justify-content: space-between;
  border-top: 1px solid rgba(255,255,255,0.06);
  background: var(--slate);
}
/* Logo: height 28px */
/* Links: var(--sans) 300, 0.72rem, letter-spacing 0.14em, uppercase, color --mist */
/* Copyright: var(--sans) 300, 0.68rem, color --mist */
```

Mobile: `flex-direction: column; gap: 1.2rem; text-align: center;`

---

### Collapsible Section (Schedule)

```css
/* Toggle button — ghost style */
.schedule__toggle {
  font-family: var(--sans);
  font-weight: 400;
  font-size: 0.78rem;
  letter-spacing: 0.18em;
  text-transform: uppercase;
  color: var(--pale);
  background: none;
  border: 1px solid rgba(200,212,222,0.25);
  padding: 0.7rem 1.8rem;
  border-radius: 1px;
  cursor: pointer;
  transition: border-color 0.3s, color 0.3s;
}
.schedule__toggle:hover,
.schedule__toggle.open {
  border-color: var(--syn-orange);
  color: var(--syn-orange);
}

/* Panel */
.schedule__grid {
  max-height: 0;
  overflow: hidden;
  opacity: 0;
  transition: max-height 0.5s ease, opacity 0.4s ease, margin-top 0.4s ease;
}
.schedule__grid.show {
  max-height: 900px;
  opacity: 1;
  margin-top: 2rem;
}
```

---

### Segmented Control (Tab switcher)

Used in the timer tool. Sharp corners, orange active state.

```css
.segmented {
  display: inline-grid;
  grid-auto-flow: column;
  background: var(--ink);
  border: 1px solid rgba(61,74,88,0.7);
  border-radius: 2px;
  overflow: hidden;
}
.segmented button {
  font-family: var(--sans);
  font-weight: 400;
  font-size: 0.72rem;
  letter-spacing: 0.14em;
  text-transform: uppercase;
  background: transparent;
  border: 0;
  color: var(--mist);
  padding: 7px 16px;
  cursor: pointer;
  min-width: 72px;
}
.segmented button + button { border-left: 1px solid rgba(61,74,88,0.7); }
.segmented button.active { background: var(--syn-orange); color: var(--white); }
.segmented button:not(.active):hover { background: var(--slate-mid); color: var(--white); }
```

---

### Form Inputs & Labels

Used in tool UI. Matches the site's card aesthetic.

```css
label {
  display: grid;
  gap: 6px;
  font-family: var(--sans);
  font-size: 0.72rem;
  font-weight: 400;
  letter-spacing: 0.12em;
  text-transform: uppercase;
  color: var(--mist);
}

input[type="text"],
input[type="number"],
select {
  font-family: var(--sans);
  font-size: 0.88rem;
  background: var(--ink);
  border: 1px solid rgba(61,74,88,0.7);
  color: var(--white);
  border-radius: 2px;
  padding: 10px 12px;
  transition: border-color 0.2s;
}
input:focus, select:focus {
  outline: none;
  border-color: var(--syn-orange);
}
```

---

### Animation

**Fade-up on load (hero content, list items):**
```css
@keyframes fade-up {
  from { opacity: 0; transform: translateY(20px); }
  to   { opacity: 1; transform: translateY(0); }
}

/* Apply with staggered delays */
.item:nth-child(1) { animation: fade-up 0.8s ease 0.2s both; }
.item:nth-child(2) { animation: fade-up 0.8s ease 0.4s both; }
.item:nth-child(3) { animation: fade-up 0.8s ease 0.6s both; }
```

**Hover transitions:** `transition: 0.2s–0.3s` on colour/opacity. `transition: 0.15s` on transforms. Never animate `all` — be explicit.

---

## Copywriting Rules

- **No em-dashes** in generated copy. Use periods or commas instead. Em-dashes read as AI-generated.
- **First person** for Scott's voice: "I started Synergize because..."
- **Second person** for prospects: "You'll discover..."
- Short paragraphs. Conversational, not corporate.
- Testimonials are verbatim from Google Reviews — preserve original punctuation including any em-dashes already present.
- Brand signature pullquote: *"Weightlifting is a sport for enthusiasts. But lifting weights is for everyone."*

---

## Sub-brand Accent Swap Pattern

When building pages for a different sub-brand, swap the accent token and the active nav link. Everything else stays the same.

| Page | Active accent | Active nav |
|------|--------------|------------|
| Synergize | `--syn-orange` `#e8632b` | `.active { color: var(--syn-orange) }` |
| Whole | `--whole-sage` `#7a9b6d` | `.active { color: var(--whole-sage) }` |
| Coaching | `--brand-blue` `#2d86c4` | `.active { color: var(--brand-blue) }` |

Swap the `::before` rule colour on eyebrow labels, the `border-left` on blockquotes, and the primary button background.

---

## Booking Links

| Context | URL |
|---------|-----|
| Coaching / Whole / Homepage CTA | `https://calendar.app.google/R66fNg5m7w3aKPKd6` |
| Synergize gym visit / "Book Intro" | `https://calendar.app.google/cBsPtjyxJZSuz6Jo7` |

---

## Asset References

| File | Usage |
|------|-------|
| `logo_white_transparent.png` | Nav and footer on all dark-background pages |
| `logo_transparent_dark.png` | Any light-background context |
| `synergize001.png` etc. | Section photography — never hotlink, always local |

Logo sizing: `height: 36px; width: auto` in nav. `height: 28px` in footer.

---

## Do Not

- Do not use `border-radius > 4px` on any element. The site aesthetic is sharp, not rounded.
- Do not use `font-weight: 700` or bolder on Cormorant Garamond headings.
- Do not use blue (`#3b82f6` etc.) as a UI accent. Brand blue (`#2d86c4`) is reserved for the Coaching sub-brand and logo elements only.
- Do not use `system-ui` or `Inter` or `Roboto`. Only the three declared typefaces.
- Do not use `box-shadow` for card depth. Use background colour contrast instead.
- Do not use emojis in marketing copy. Emojis are acceptable in tool UI only (timer modes).
- Do not use `border-radius: 9999px` (pill shapes) for any button or tag. All elements are `border-radius: 1–4px`.
- Do not use `font-weight: 600` or `700` on buttons. Use `400–500` with uppercase and tracking instead.
