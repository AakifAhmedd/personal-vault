# DESIGN — The Registry

> The style contract. Written once after the build passes QA; read at the START of every subsequent edit. New work that contradicts this file is wrong even if it looks good in isolation — consistency IS the design. Update the file deliberately when the system itself evolves; never drift it silently.

## Identity
- **The one feeling:** quiet, precise, metallike — a set of sheets you step behind when you want them. (From the design brief in README.md.)
- **Signature / peak:** the privacy-by-default card — every value and thumbnail blurred until hovered/focused, a single global "Reveal values" toggle, and a rotated mono "No. 001" stamp per record. The blur is the product.
- **Register:** build

## Tokens (verbatim from the shipped CSS)
```css
:root{
  --ink:#E8EEF9;           /* primary text on the dark base */
  --ink-soft:#95A3BE;       /* secondary text */
  --paper:#0A0E17;          /* page base */
  --paper-deep:#060A11;     /* deepest wells (thumbnails) */
  --card:#121B2C;           /* glass tint base */
  --brass:#E4B04A;          /* vault gold */
  --brass-deep:#C79238;
  --rust:#F0564A;           /* destructive */
  --line: rgba(232,238,249,0.10);

  --glass-bg: color-mix(in srgb, var(--card) 62%, transparent);
  --glass-strong: color-mix(in srgb, var(--card) 90%, transparent);
  --glass-border: rgba(232,238,249,0.12);
  --glass-shadow:
    0 12px 34px rgba(0,0,0,0.45),
    inset 0 1px 0 rgba(255,255,255,0.06);
  --glass-blur: blur(16px) saturate(150%);

  --radius-s:10px;
  --radius-m:16px;
}
```
Full `@supports not (backdrop-filter)` opaque fallbacks and the dot-grid
`radial-gradient` dot base are also part of the token system.

## Typography
- **Display:** Fraunces (optical sizing 9..144), weights 400/600/700 + italic 500.
- **Text:** IBM Plex Sans (neutral grotesque), 400/500/600.
- **Numerals/mono:** IBM Plex Mono (field values, stamps, counts, versioning).
- **Pairing axis:** high-contrast serif (brand warmth, the "record") × neutral
  grotesque (UI chrome) × mono (the data). Display letter-spacing ≥ −0.04em;
  headings clamp to ≤6rem in prose flow (this app runs a restrained 29px/20px
  because type is not the peak).

## Color rules
- **Commitment tier:** committed. The single gold (#E4B04A) carries ~35–40% of
  the surface: brand tag, stamps, accent borders, focus rings, text selection,
  the lock seal, and the destructive coral (#F0564A) is the only other semantic.
- **Background lightness: paper `#0A0E17` (mean L ≈ 0.02)** — the craft lives in
  the shadow; the gold reads by fragments, never by blowing the base out.
- **Forbidden:** purple→blue gradient stops (hue 250–290); warm-beige body at
  L 0.84–0.97; amber or acid as the one accent.

## Motion vocabulary
UI state transitions only (≤3 families; no scroll families exist on this SPAA —
it is not a scroll page):

1. **Card field reveal** — `filter: blur(5px)` → `none` on value & thumbnail.
   Duration 160ms ease. Reduced-motion → zero.
2. **Card hover lift** — `translateY(-2px)` + shadow. Duration 180ms (shared on
   `.card`), gated behind `@media (hover: hover) and (pointer: fine)` for touch.
3. **Modal / drawer / lightbox** — enter 220ms `ease-out`, exit 150ms
   (`ease-out`); transform-origin center.

Locks & the lock screen run their own independent field (the particle outline,
not the mesh). The ambient mesh is a background layer: it freezes while a form
card or overlay is up, and is fully disabled under `prefers-reduced-motion`.

## Layout patterns
- **Grid break:** the rotated mono date-stamp (`rotate(-8deg)`, top-right,
  inside every entry card) breaks the card's tidy padding box and the 3-column
  flow on wide viewports.
- **Section openings:** not a scrolling page — the "sections" that do appear
  (overlay modals, lightbox, cover card) open with a background change + type
  scale + consistent token spacing; do not invent an eyebrow kicker.
- **Components that exist:** vault glass card (reuse rather than invent a
  substitute), entry card, modal, overlay, lock cover, stamped list item —
  reuse these before writing a new one.

## Copy voice
- **Voice:** discreet, precise, metallike — two adjectives; never a sales
  superlative. Microcopy is a butler.
- **Buttons say what happens** ("Reveal values", "Save vault file", "Lock").
- **Banned words stay banned:** Revolutionize / Seamless / Effortless / Unleash /
  Elevate; em-dash chains; "BRAND. MOTION. SPATIAL." strips.

## Project ban additions
- **auteur-allow suppressions in force:** none — slopscan is clean.
- **Glassmorphism is purposeful here** (persistent surfaces over real imagery),
  not a decorative default; where `backdrop-filter` is unsupported the
  `@supports` block falls back to opaque surfaces rather than a degraded blend.
- **Blur is a security display pattern** (privacy-by-default), not a privacy
  boundary — values are in page memory while unlocked regardless of the blur.
- **The dark base is argued by scene** (a discreet ledger you step behind), not
  the "premium=dark" reflex — the drama lives in the gold fragments.

## Rejected — decisions that already have a history
| What was tried | Why it lost | What is there instead |
|---|---|---|
| Full ~290° hue rotation of the lock-field particles | At that density it reads as confetti; the field must sit with the brass rather than sweep the whole wheel | A narrow analogous band (red→amber→yellow, ~60°) with lightness variety 10–88%; each hue breathes only ±8° |
| Field-only tuning of the lock field (no ring-pull) | Measured gradient was 13.4/step near the crest and 4.0 far out — both below the original CHAOS of 30, so the walk was really a random walk; a flat field could not fix it | Each particle additionally pulled toward the nearest point of the ring, making the outline deterministic; spring, jitter and trails kept as before |

## Editing protocol
1. Read this file fully before touching anything.
2. New section/overlay → pick an existing opening + an existing motion family +
   existing tokens.
3. After any edit: `node scripts/slopscan.mjs <src>` and re-shoot the changed
   viewport(s); compare neighbouring sections for family consistency.
4. If a new pattern is genuinely needed → update THIS file first, then the page.
5. An edit that "fixes" a row in the Rejected table is a regression, however
   reasonable it looks; argue the row in the file first.
