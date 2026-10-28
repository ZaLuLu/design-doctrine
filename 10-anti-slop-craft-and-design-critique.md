# 10. Anti-Slop Craft, Macrostructures & The Impeccable Critique

> "AI-generated interfaces look identical not because the models lack capability, but because they default to the median of their training set: purple gradients, centered heroes, three equal feature cards, nested card borders, and fake metrics. Great design is opinionated, deliberate, and structurally distinct."  
> — Synthesized from Hallmark (`nutlope/hallmark`) and Impeccable (`pbakaus/impeccable`)

---

## 1. The Pre-Emit Self-Critique (The 6 Axes)

Before any page or component is marked complete or delivered to production, evaluate it across six qualitative axes (scored 1 to 5). Any score below **3 on any axis triggers an immediate revision pass**:

| Axis | Metric Focus | Production Failure Tell | Impeccable Standard |
|---|---|---|---|
| **A. Philosophy** | Conceptual point of view | Just a generic layout without a clear opinion or attitude. | Clear stance, intentional tonal direction (e.g. brutalist, modern-minimal, editorial). |
| **B. Hierarchy** | 2-second visual scan | Everything has identical visual weight; no clear primary focus. | Instant clarity: primary headline, secondary anchor, tertiary detail. |
| **C. Execution** | Mechanical precision | Unequal padding, mismatched radii, sloppy borders, jarring contrast. | Hairline 1px rules, strict 8pt grid cadence, verified APCA contrast. |
| **D. Specificity** | Tailored to the exact domain | Looks like an interchangeable SaaS landing page. | Bespoke domain macrostructure, real domain copy, customized density. |
| **E. Restraint** | Elimination of fluff | Decorative blobs, arbitrary borders, redundant pills, gradient text. | Zero unearned decoration; 90%+ viewport allocated to primary content. |
| **F. Variety** | Structural differentiation | Standard "Hero ➔ 3 Cards ➔ CTA ➔ Footer" rhythm. | Varied column grids, asymmetric layouts, distinct section rhythms. |

---

## 2. The Impeccable 6-Step Refinement Workflow

To transform a functional prototype into an artisanal, world-class interface, run the **Impeccable Workflow**:

```
┌───────────┐      ┌─────────┐      ┌───────────┐      ┌───────────┐      ┌──────────┐      ┌───────────┐
│ CRITIQUE  │ ───► │  SHAPE  │ ───► │  DISTILL  │ ───► │ NORMALIZE │ ───► │  POLISH  │ ───► │  ANIMATE  │
└───────────┘      └─────────┘      └───────────┘      └───────────┘      └──────────┘      └───────────┘
 Audit visual       Macro-grid &      Strip noise &      Lock 3 radii       Optical gaps,     Tactile fluid
 noise & tells      silhouette        fake metrics       & 1.5px icons      ambient depth     spring curves
```

1. **`critique`**: Ruthlessly audit visual hierarchy, alignment, contrast, and layout tells. Identify elements competing for attention.
2. **`shape`**: Establish content-to-chrome ratios. Ensure 85–90% of the viewport displays primary content; push navigation and tools into slender, unobtrusive utility zones.
3. **`distill`**: Strip away decorative fluff. Delete nested card containers, eliminate multi-line borders, remove unearned badges, and erase fabricated marketing statistics.
4. **`normalize`**: Enforce systematic invariants:
   * **The 3-Radius Rule:** Lock corner radii to strictly 3 values across the entire product:
     * Small controls (inputs, dropdowns, buttons): `8px` (`--radius-sm`)
     * Surface cards, dialogs, drawers: `14px` (`--radius-md`)
     * Contextual badges, pills: `9999px` (`--radius-pill`)
   * **Iconography Standard:** All SVG glyphs must share a uniform **`1.5px` stroke width** and map strictly to `--icon-sm` (16px), `--icon-md` (20px), or `--icon-lg` (24px).
5. **`polish`**: Optical alignment, hairline dividers (`1px solid var(--color-border)`), sub-pixel rendering, and multi-stage ambient contact shadows.
6. **`animate`**: Replace arbitrary duration transitions (`cubic-bezier`, `linear-ease`) with interactive spring physics (`linear(...)` curves), direct drag interruptibility, and velocity conservation.

---

## 3. Hallmark’s 58 Anti-Slop Gates

Every production interface must pass all 58 gates. An answer of "Yes" to any gate indicates an AI default failure that must be corrected.

### Visual & Stylistic Gates
1. **Generic Display Fonts:** Is the display font Inter, Roboto, Open Sans, Poppins, or Lato without domain justification? (Use characterful display typography or system defaults paired with editorial grotesques).
2. **Gradient Text Headline:** Is there a purple-to-blue or cyan-to-magenta gradient text headline (`background-clip: text`)? (Strictly forbidden).
3. **The 3-Equal-Card Cliché:** Is there a generic 3-equal-column grid with an icon hovering above a centered heading?
4. **Nested Card Containers:** Is a card nested inside another card? (Enforce single-surface hierarchy).
5. **Coloured Side-Stripe:** Does any card use a thick colored left/right border accent?
6. **Centered-Everything Hero:** Are the eyebrow, title, lede, and CTA all stacked along a single centered vertical axis? (Break alignment: anchor eyebrow off-axis or align CTA to margin).
7. **Dead Base Fills:** Is pure `#000` or pure `#fff` used as an un-tinted flat canvas? (Tint neutrals with $\ge 0.005$ chroma).

### Structural & Layout Gates
8. **Structural Fingerprint Reuse:** Does the page copy the default AI template (Hero $\to$ 3 features $\to$ CTA $\to$ footer)?
9. **Whitespace-Only Separation:** Are sections distinguished only by whitespace without changes in rhythm, rule dividers, or surface shifts?
10. **`transition: all`:** Is `transition: all` declared anywhere? (Explicitly list `transform`, `opacity`, `background-color`).
11. **Uniform `hover:scale-105`:** Is a uniform scale transform applied indiscriminately to all cards?
12. **Bouncy UI Transitions:** Are overshoot springs applied to standard menus or dialogs? (Reserve bounce for physical gestures only; use critically damped transitions for UI states).
13. **Stacked Hover Effects:** Does an element trigger simultaneous translate + scale + shadow + rotation + color shifts on hover? (Pick at most one or two complementary shifts).
14. **Layout Property Animation:** Are `width`, `height`, `top`, `left`, `margin`, or `padding` animated? (Compositor `transform` only).
15. **Focus Ring Fade-In:** Does the focus ring transition into existence? (Focus rings must render immediately upon keyboard focus).
16. **Triumphant Success Toasts:** Is a celebratory toast shown for an action whose outcome is already visible in the DOM? (Silent success is superior).
17. **Symmetric Tooltip Delays:** Do tooltips use the same delay for hover and keyboard focus? (Hover: 800–1000ms delay; Focus: 0ms instant).
18. **Un-pausable Carousels:** Does rotating content lack pause-on-hover and pause-on-focus? (WCAG 2.2.2 violation).
19. **Cliché Placeholders:** Are "Jane Doe", "John Smith", "Acme Corp", or "Unleash your potential" used? (Use realistic domain data).

### Implementation & Token Disciplines
20. **Missing Macrostructure Intent:** Is the architectural layout rationale undocumented?
21. **Specimen Fall-through:** Did the interface default to an editorial serif specimen when the domain required SaaS utility?
22. **Zero-Chroma Neutrals:** Are greys mathematically dead (`oklch(L 0 0)`) rather than subtly tinted with brand hue?
23. **Accent Flooding:** Does the primary accent color cover $> 5\%$ of any viewport area? (Accent is for focal emphasis, not wallpaper).
24. **Off-Scale Spacing:** Is any margin/padding an arbitrary value (e.g. `17px`) outside the 4pt/8pt scale?
25. **Unconstrained Measure:** Is body text allowed to stretch beyond $75\text{ch}$?
26. **Incomplete Component States:** Does an interactive element lack explicit styling for `:focus-visible`, `:active`, or `:disabled`?
27. **Missing Reduced Motion:** Are keyframes lacking `@media (prefers-reduced-motion: reduce)` fallbacks?

### Hero Enrichment & Iconography
28. **LCP-Killing Hero Video:** Does background video autoplay with sound, lack a poster, or use `loading="lazy"`?
29. **Mesh-Gradient Blobs:** Is the page blanketed in multicolored animated mesh blobs?
30. **Mixed Iconography / Emojis:** Does the UI mix multiple icon families or use emoji glyphs (✨, 🚀, ⚡) as feature icons? (Stick to one consistent SVG family).
31. **Gratuitous Lottie:** Is heavy Lottie JSON loaded where crisp CSS/SVG suffices?
32. **Un-differentiated Archetypes:** Are component variants identical copies without contextual knobs?
33. **Inaccessible Decorative Graphics:** Do SVGs lack `aria-hidden="true"` or meaningful `aria-label`?

### Layout Safety & Mobile Responsiveness
34. **Horizontal Viewport Overflow:** Does the page horizontally scroll between 320px and 1920px? (Enforce `overflow-x: clip` on `html` and `body`).
35. **Misaligned Text Highlights:** Do highlighter marks sit on the baseline instead of behind the x-height?
36. **Un-centered Flex Bars:** Do interactive headers inherit `align-items: stretch`, misaligning buttons and text baselines?
37. **Font Family Proliferation:** Are more than three font families declared on a single page? (Display + Body + optional single Outlier).
38. **Outlier Overuse:** Is an outlier font used in more than two distinct locations?
38a. **Italic Headers Tell:** Is any display heading or title rendered in italic (`font-style: italic` or `<em>` inside `h1`)? (Headings are strictly roman; emphasis is conveyed via weight or accent).

### Input States & Contrast Invariants
39. **Defective Input States:**
    * Border width shifts between states (e.g., 1px default $\to$ 2px focus shifts layout; keep 1px and use `outline`).
    * Focus ring constructed from `border` rather than `outline`.
    * Form input height differs from adjacent submit button height (both must share identical 44px/48px floor).
    * Helper text container collapses when empty, causing layout jumps when errors appear.
    * Disabled state indicated solely by opacity (requires `opacity: 0.55`, `cursor: not-allowed`, and native `disabled` attribute).
40. **Contrast Violations:** Does any text fail WCAG 4.5:1 or APCA $L^c \ge 60$ against computed backgrounds?
41. **Ink-on-Ink Clashing:** Does dark-surface text inherit dark body colors, resulting in unreadable black-on-black or dark-on-dark buttons?

### Chrome & Honest Copy
42. **Nav AI Fingerprint:** Is navigation a generic wordmark-left + 5 links + button-right full-bleed bar?
43. **Footer AI Fingerprint:** Is footer a generic 4-column link stack + social icons on flat grey?
44. **Unfitted Hero Fold:** Does the hero exceed the fold on a 1280×800 laptop screen, pushing the primary CTA below the fold?
45. **Unmotivated Decoration:** Are decorative floating cursors, fake stamps, or non-functional badges cluttering the hero?
46. **Fabricated Metrics & Telemetry:** Does the UI claim "10x faster", "99.9% uptime", or "50,000+ teams" without customer verification?
47. **Re-Drawn OS/Browser Chrome:** Did you draw fake browser frames (traffic-light dots) or fake IDE windows instead of real screenshots wrapped in clean figures?
48. **Mid-Render Token Improvisation:** Are inline hex or arbitrary OKLCH values sprinkled into CSS instead of using defined tokens?
49. **Two-Line Clickable Affordances:** Does any button, tab, or primary nav link wrap onto two lines on mobile? (Must stay strictly single-line).

### Mobile Non-Negotiables
50. **Grid Tracks Without `minmax(0, 1fr)`:** Does an image-bearing grid use bare `1fr`? (Resolves to `minmax(auto, 1fr)` and blows out viewport on mobile; change to `minmax(0, 1fr)`).
51. **Missing Long-Word Break:** Do display headings lack `overflow-wrap: anywhere; min-width: 0`?
52. **Un-collapsed Section Headers:** Do multi-column section titles fail to collapse to a single column on mobile?
53. **Radio-Tab Scroll Jumping:** Do CSS tab toggles jump the viewport on click due to un-handled focus states?
54. **Eyebrow Beside Heading:** Is a section kicker or tag placed horizontally adjacent to a heading? (Always stack vertically: eyebrow directly above heading).
55. **All-Caps Display Collision:** Does an all-caps heading have `line-height < 1.0`? (Floor for uppercase display heads is `1.02–1.08` to prevent glyph collisions).
56. **Double Sticky Top Bleed:** Are two sticky elements set to `top: 0` without height offset, causing section headers to bleed over the sticky navbar?
57. **Discarded Studied DNA:** Did you extract a client's design DNA only to revert to a generic template theme?
58. **Touch Target Clipping:** Does any mobile interactive element have a hit target below $44 \times 44\text{ pt}$?

---

## 4. The Mandatory 8-State Interactive Component Discipline

Every interactive UI primitive (button, input, select, card, tab, menu item) must implement **all 8 states** in code:

```
1. DEFAULT          ── Baseline rest state
2. HOVER            ── Pointer presence indicator (cursor: pointer, subtle tint)
3. :FOCUS-VISIBLE   ── Instant dual-layer high-contrast keyboard ring
4. :ACTIVE          ── Tactile depress (scale: 0.98 or translateY: 1px)
5. DISABLED         ── opacity: 0.55, cursor: not-allowed, aria-disabled="true"
6. LOADING          ── Spinner/pulse replacing icon, preserved width, pointer-events: none
7. ERROR            ── Semantic border/halo, aria-invalid="true", linked error message
8. SUCCESS          ── Brief affirmative state confirmation
```

### Production CSS Implementation Template

```css
.c-button {
  /* 1. DEFAULT */
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: var(--space-2);
  min-block-size: 44px;
  padding-inline: var(--space-4);
  border-radius: var(--radius-sm);
  background-color: var(--color-interactive-default);
  color: var(--color-interactive-on);
  font-family: var(--font-body);
  font-size: var(--text-sm);
  font-weight: 600;
  line-height: 1;
  white-space: nowrap;
  border: 1px solid transparent;
  outline: 2px solid transparent;
  outline-offset: 2px;
  cursor: pointer;
  transition: 
    background-color 150ms ease,
    border-color 150ms ease,
    box-shadow 150ms ease,
    transform 100ms ease;
}

/* 2. HOVER */
.c-button:hover:not(:disabled) {
  background-color: var(--color-interactive-hover);
}

/* 3. FOCUS-VISIBLE (Instant, never animated) */
.c-button:focus-visible {
  outline: 2px solid var(--color-surface-base);
  box-shadow: 0 0 0 4px var(--color-focus-ring);
}

/* 4. ACTIVE (Tactile Press) */
.c-button:active:not(:disabled) {
  transform: scale(0.98);
  background-color: var(--color-interactive-active);
}

/* 5. DISABLED */
.c-button:disabled,
.c-button[aria-disabled="true"] {
  opacity: 0.55;
  cursor: not-allowed;
  pointer-events: none;
  box-shadow: none;
}

/* 6. LOADING */
.c-button--loading {
  pointer-events: none;
  position: relative;
  color: transparent !important; /* Hide text while reserving footprint */
}

.c-button--loading::after {
  content: "";
  position: absolute;
  inline-size: 18px;
  block-size: 18px;
  border: 2px solid var(--color-interactive-on);
  border-block-start-color: transparent;
  border-radius: 50%;
  animation: button-spin 600ms linear infinite;
}

@keyframes button-spin {
  to { transform: rotate(360deg); }
}

/* 7. ERROR */
.c-button--error {
  background-color: var(--color-danger);
  color: var(--color-danger-on);
}

/* 8. SUCCESS */
.c-button--success {
  background-color: var(--color-success);
  color: var(--color-success-on);
}
```
