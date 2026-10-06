# COMMIT-SHEET — The Registry (personal-vault)

> Seven decisions before the first line of code. Grounded in the design brief
> already documented in README.md; colour/severity decisions resolved to numbers.
> Filled example answers below each field: ✗ is what slop looks like, ✓ is the bar.

## 1. Peak / Signature
<!-- build: the one element a visitor describes to a friend. -->
✓ **The privacy-by-default card**: every field and thumbnail is blurred until
the card is hovered/focused, with a single global "Reveal values" toggle and a
per-card "No. 001" rotated stamp. A visitor describes the app as a ledger you
read only when you intend to — the blur is the product, not a gimmick.
✗ "beautiful animations throughout" — the blur is the signature.

## 2. Color
<!-- primary as OKLCH + tier + why not lavender/cream + background lightness number -->
✓ **Primary: OKLCH(0.63 0.14 45)** = canvas gold `#E4B04A`; **tier: committed**
(gold carries ~35–40% of the surface: threads through the brand tag, stamps,
accent borders, focus rings, selection, the lock seal). Coral `#F0564A` is the
only other semantic (destructive).
✗ "purple→violet gradient" (hue 250–290 is banned) · "lavender/neutral panel"
✗ "warm-beige body at L 0.84–0.97" (the 2026 AI warmth reflex).
Why not cream/warm-beige: the product is a **private, discreet ledger**; the
scene is a doorway into a locked room. A lit, warm page would read as a
retail checkout and would broadcast the values on a screen-share. The palette is
deliberately cool and low-key so the sheets read as a vault, not a showroom.
✓ **Background lightness: paper = `#0A0E17` (mean L ≈ 0.02)**, the dot-grid at
0.035 alpha sits on top. The craft is held in the *shadow* (the gold reads by
fragments), not by blowing the base out — every card and the mesh are meant to
sit at this level.

## 3. Type
<!-- display + text pair on a contrast axis + why not Inter -->
✓ **Display: Fraunces** (soft-wide serif, optical sizes 9..144) / **Text:
IBM Plex Sans** (neutral grotesque) / **Numeric + field value: IBM Plex Mono**.
Contrast axis: high-contrast serif (brand warmth, the "record") × neutral
grotesque (the UI chrome) × mono (the data, always tabular-tending).
✗ "Inter for everything" (the 2024–26 AI default) — already rejected by the
existing build; keeping Fraunces is a conscious deviation, not drift.
✓ **Numbers**: field values are mono at 14.5px with tabular figures; headings
clamp to ≤6rem in prose flow (the lock title ~29px, app title ~20px — no
oversized type, since type is not the peak).

## 4. Grid break
<!-- the ONE concrete thing that breaks the symmetric grid -->
✓ **The rotated mono date-stamp** (`.stamp`, `rotate(-8deg)`, pinned top-right
inside every entry card) breaks the card's otherwise tidy 12px padding box and
disrupts the 3-column flow on wide viewports — a deliberate, named asymmetry
that keeps the "record" from reading like a uniform dashboard.
✗ "asymmetric layout" (vague) · "bento of near-identical cells" (banned) —
kept to real visual variation per cell.

## 5. Motion budget
<!-- ≤3 scroll-pattern families, named -->
✓ **UI state transitions (no scroll needed — this is an SPA)**:
(1) card field reveal — `filter: blur` in → `none` (display only, opacity not
animated; the security blur has no transform alternative), (2) card hover lift —
`translateY(-2px)` + shadow, gated behind `@media (hover: hover)` for touch,
(3) modal/overlay and lightbox enter/exit — 220ms `ease-out` (enter) / 150ms
(exit), transform origin center.
✗ "uniform fade-in on every section" · marquee (banned, none present) ·
more than one full-screen bleed pass.
✓ Ambient mesh + lock particle field are **background layers, not scroll
triggers** — they are excluded from the budget and stopped while a form card is
up (pointer over `input/textarea/select/.overlay` freezes the mesh; the lock
screen swaps it for its own field).

## 6. Reflex check
<!-- (a) generic AI's first-order reflex; (b) second-order; (c) argued deviation -->
a) "a vault page → near-black + amber-violet glow + monospace terminal labels,
unified hover, no hierarchy".
b) "an AI avoiding that → warm daylight beige + serif, or brutalist left turn".
c) **Deviation**: committed gold (not amber) on deep navy-black, so the identity
is metallike, not warm; Fraunces serif set at restrained sizes (record voice,
not display); no glow-as-light (the mesh is *additive* light — light on top of
page, never emissive bloom); no service-mono chrome (only the field values are
mono, and they are hidden by default); the lock screen's particle outline is a
purpose-built, self-ported field, not a copied UI element.

## 7. House tells broken
<!-- ≥2 items from taste.md §2.5 deliberately not done; what replaces each -->
1. **Near-black-by-default → KEPT, argued**: the scene forces it (see §2). This
   is not the reflex; it is the product. The drama lives in the gold fragments.
2. **Mono service type in the corners → KEPT, argued**: there is *no* mono
   chrome anywhere. Mono appears only in the hidden data (field values), which
   is the point of the privacy system.
3. **Glow-as-depth → KEPT, argued**: the mesh is additive light (light *on* the
   page), never emissive bloom. Real directional lighting is deliberately
   absent because the surface is a room you enter, not a product shot.
4. **Wordmark-as-hero → KEPT, argued**: the lock title is the unlock surface,
   but the hero tier never runs oversized (29px) because the signature is the
   privacy system, not letterforms.
