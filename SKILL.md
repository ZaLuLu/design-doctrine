---
name: design-doctrine
description: "The Universal Design Doctrine & Frontend Engineering Canon. Codifies 12 empirical modules: cognitive laws (Fitts, Hick, Miller, Doherty), Apple fluid spring physics (WWDC 803, RK4, rubber-banding), typography invariants (Bringhurst 65ch), motion/FLIP, intrinsic CSS (Every Layout), 3-tier DTCG tokens, performance (RAIL/CWV, zero-FOUT), WCAG 2.2 accessibility, Hallmark 58 Anti-Slop Gates, 8 mandatory interactive component states, Impeccable critique workflow, feature-module frontend architecture, mobile touch ergonomics (Hoober, 100dvh), and user abandonment/information foraging science. Use whenever designing or implementing UI, UX, components, layouts, styling, animation, or mobile flows."
risk: low
source: https://github.com/ZaLuLu/design-doctrine
source_repo: ZaLuLu/design-doctrine
source_type: official
date_added: "2026-10-28"
author: ZaLuLu
---

# Universal Design Doctrine & Frontend Engineering Canon

The **Design Doctrine** is an uncompromising, mathematically grounded, and empirically validated engineering standard for digital interfaces. It synthesizes over 70 years of human-computer interaction science, Apple fluid interaction physics, typographic craft, resilient CSS algorithms, modern frontend module architecture, and mobile ergonomic rigor into an actionable, production-ready framework.

Whenever building, modifying, reviewing, or styling user interfaces, web applications, or mobile experiences, you must operate in accordance with this doctrine.

---

## 1. Canon Directory & Module Map

The complete doctrine is codified into 12 detailed reference modules located in `./references/`:

| Module | Title | Primary References & Domains |
|---|---|---|
| [**01**](./references/01-cognitive-laws-and-human-perception.md) | **Cognitive Laws & Human Perception** | Fitts' Law, Hick-Hyman, Miller 7±2, Doherty Threshold (<400ms), Sweller's Cognitive Load, Gestalt grouping, Aesthetic-Usability Effect (Kurosu & Kashimura, Tractinsky), 50ms visual judgment (Lindgaard, Reinecke). |
| [**02**](./references/02-apple-fluid-interfaces-and-interaction-physics.md) | **Apple Fluid Interfaces & Physics** | WWDC 2018 Session 803, Apple HIG, Liquid Glass, RK4 second-order differential springs, gesture continuity, velocity inheritance, interruptibility, rubber-banding. |
| [**03**](./references/03-visual-design-color-and-typographic-craft.md) | **Visual Design, Color & Typography** | Bringhurst's *Elements of Typographic Style*, Butterick, Lupton, Müller-Brockmann grids, Albers' *Interaction of Color*, APCA contrast, 65ch line measure, 3-tier DTCG tokens, surface elevation. |
| [**04**](./references/04-motion-choreography-and-animation.md) | **Motion Choreography & Animation** | Disney's 12 Animation Principles adapted to UI (Chang & Ungar), Val Head, Rachel Nabors, FLIP technique, layout projections, `prefers-reduced-motion` compliance. |
| [**05**](./references/05-intrinsic-layouts-and-css-algorithms.md) | **Intrinsic Layouts & Modern CSS** | Pickering & Bell's *Every Layout*, Ethan Marcotte responsive design, CSS subgrid, container queries (`@container`), zero-media-query intrinsic algorithms (Stack, Box, Cluster, Reel, Imposter). |
| [**06**](./references/06-design-systems-tokens-and-atomic-architecture.md) | **Design Systems, Tokens & Architecture** | Brad Frost's Atomic Design, W3C DTCG Token Specification, Material Design 3, Microsoft Fluent 2, IBM Carbon, Shopify Polaris, Atlassian Design System. |
| [**07**](./references/07-performance-engineering-and-rendering-craft.md) | **Performance Engineering & Rendering** | Google Core Web Vitals (LCP, INP, CLS), RAIL model, browser composite pipelines, zero FOUT/FOIT strategies, self-hosted WOFF2, non-blocking UI state machines. |
| [**08**](./references/08-accessibility-and-inclusive-design.md) | **Accessibility & Inclusive Design** | W3C WCAG 2.2 (Level AA/AAA), Inclusive Design Patterns, keyboard traversal traps, ARIA Authoring Practices Guide (APG), high contrast modes, screen reader heuristics. |
| [**09**](./references/09-cross-platform-production-recipes.md) | **Cross-Platform Production Recipes** | Production-grade implementations in React, React Native, CSS/Tailwind, Swift/SwiftUI, and Flutter. |
| [**10**](./references/10-anti-slop-craft-and-design-critique.md) | **Anti-Slop Craft & Design Critique** | Hallmark 58 Anti-Slop Gates, 8 mandatory interactive component states, 6-step Impeccable critique workflow, 3-radius nest rule, surface tinting vs. muddy drop shadows. |
| [**11**](./references/11-frontend-codebase-and-state-architecture.md) | **Frontend Codebase & State Architecture** | Feature-module organization, strict server-state vs UI-state separation, barrel-only encapsulation, component promotion lifecycle, zero circular dependencies. |
| [**12**](./references/12-mobile-touch-ergonomics-and-interaction-fatigue.md) | **Mobile Touch Ergonomics & Interaction Fatigue** | Steven Hoober touch telemetry, 49/36/15 thumb zone distribution, 48x48dp / 44x44pt targets with 8dp gutters, `100dvh` layout stability, virtual keyboard repositioning, Information Foraging Theory (scent of information over debunked 3-click myth), cognitive friction metrics, and rage click recovery. |

---

## 2. Core Non-Negotiable Invariants

### A. The Hallmark 58 Anti-Slop Standard
No AI-generated boilerplate, generic gray cards, or broken interactive states are permitted. Every UI element must pass the Anti-Slop Audit:
1. **Never use pure `#000000` or `#ffffff` for deep contrast surfaces**: Always use tinted neutrals (`hsl(220, 15%, 8%)` dark, `hsl(220, 20%, 98%)` light).
2. **Never apply generic single-layer drop shadows (`box-shadow: 0 4px 6px rgba(0,0,0,0.1)`)**: Layer multi-stop atmospheric shadows and tint ambient light toward surface tones.
3. **The 3-Radius Nesting Law**: Outer radius = Inner radius + Padding (`R_outer = R_inner + P`). Concentric corners prevent visual clash.
4. **Zero FOUT/FOIT Font Policy**: Self-host WOFF2 fonts with `<link rel="preload">` and `font-display: swap`. Never block the first viewport with third-party render-blocking CSS.
5. **Monospace Discipline**: Monospace fonts belong *only* on aligned tabular numbers (`tabular-nums`), timestamps, hashes, or code. Never sprinkle mono on random subheadings or captions for fake "tech aesthetic" trend-slop.

### B. The 8 Mandatory Interactive States
Every interactive element (button, link, input, card, switch) must explicitly implement all 8 states:
1. **Default**: Calibrated rest state with clear affordance.
2. **Hover**: Subdued luminance shift or elevation boost (pointer devices only; never stuck on touch).
3. **Active / Pressed**: Immediate kinetic feedback (`scale(0.98)` or inset compression).
4. **Focus-Visible**: High-contrast, non-clipped outline with 2px offset (`outline: 2px solid var(--focus-ring); outline-offset: 2px`).
5. **Loading / Pending**: Spinner or indeterminate shimmer with inline layout lock (`aria-busy="true"`). Zero width/height collapse.
6. **Disabled**: Reduced opacity (`0.5`), `cursor: not-allowed`, `aria-disabled="true"`, with explanatory tooltip or hint.
7. **Error / Invalid**: Semantic red token accent, `aria-invalid="true"`, linked error description via `aria-describedby`.
8. **Success / Complete**: Reassuring affirmative feedback state (green icon, checkmark, or subtle pulse).

### C. Typographic Hierarchy & The Bringhurst Law
- **Optimal Measure**: Continuous body prose must adhere to $45\text{--}75$ characters per line (optimal $65\text{ch}$). Set `max-inline-size: 65ch;`.
- **Display vs Body Pairing**: Pair a characterful display typeface for headings with a hyper-legible neutral grotesque/humanist typeface for UI/body text.
- **Tabular Numerals**: Every numeric column, timer, financial figure, or counter must enable `font-variant-numeric: tabular-nums` (or CSS `font-feature-settings: "tnum"`).

### D. Apple Fluid Interaction Physics (WWDC 2018 Session 803)
- **Interruptible Springs**: Interface animations must not use hard-coded linear or bezier durations when responding to user gestures. Use second-order differential spring physics (critical damping $\zeta = 1.0$, or subtle bounce $\zeta = 0.85\text{--}0.95$).
- **Velocity Inheritance**: When a user flings or releases a gesture, the target animation must adopt the gesture's initial release velocity $\mathbf{v}_0$.
- **Rubber-Banding**: Scroll boundaries and draggable sheets must resist pull with logarithmic decay:
  $$x_{\text{display}} = \frac{x_{\text{drag}} \cdot d \cdot c}{d + c \cdot x_{\text{drag}}}$$
  returning smoothly to origin on release.

### E. Mobile Ergonomics & Viewport Stability
- **Touch Targets**: Minimum $48 \times 48\text{ dp}$ (Android) or $44 \times 44\text{ pt}$ (iOS) with $\ge 8\text{ dp}$ gutter separation between interactive bounds.
- **Thumb Zone Geometry**: Primary actions and bottom sheets belong in the Natural Zone (lower $40\%\text{--}60\%$ of viewport). Destructive actions belong in the Hard-to-reach zone (top corners) to prevent catastrophic accidental taps.
- **Dynamic Viewport Height**: Always use `min-height: 100dvh` (Dynamic Viewport Height) rather than `100vh` to eliminate mobile browser navigation bar jumps.
- **Virtual Keyboard Tracking**: Use the `window.visualViewport` API or `interactive-widget=resizes-content` in viewport meta to prevent fixed buttons and bottom sheets from being occluded when the on-screen keyboard slides into view.

### F. Interaction Fatigue & The Information Foraging Law
- **Debunked 3-Click Myth**: Users do not abandon tasks because of the number of clicks; they abandon tasks when the **Scent of Information** drops or cognitive friction spikes.
- **Strong Information Scents**: Every step, link, and button must clearly telegraph what will happen next. Clear progress indicators reduce perceived duration.
- **Rage Click Prevention & Telemetry**: If a user clicks or taps $\ge 3$ times within $800\text{ms}$ on an element, treat it as an interface failure. Lock the trigger, surface an immediate loading indicator, or render an inline fallback explanation.

### G. Three-Tier DTCG Token Architecture
Maintain an unbroken three-tier token hierarchy:
```
Tier 1: Global Primitives   (--color-blue-600: #2563eb)
           │
           ▼
Tier 2: Semantic Intent     (--surface-interactive-primary: var(--color-blue-600))
           │
           ▼
Tier 3: Component Scoped    (--btn-primary-bg: var(--surface-interactive-primary))
```
Never reference Tier 1 primitives directly inside layout components. Always route through Tier 2 semantics.

### H. Feature-Module Architecture
Organize frontend projects into domain-driven feature modules:
```
src/
├── app/                  # Application root, routing, global providers
├── features/             # Self-contained domain modules
│   ├── ticketing/
│   │   ├── api/          # Server queries, mutations (TanStack / SWR)
│   │   ├── components/   # Feature-specific UI components
│   │   ├── hooks/        # Feature-specific UI state hooks
│   │   ├── types/        # Feature TypeScript definitions
│   │   └── index.ts      # Public API barrel (strictly enforce barrel imports)
├── components/ui/        # Dumb, universal design system primitives (Button, Modal)
└── lib/                  # Shared utilities, token themes, physics helpers
```
- **Strict Server vs. UI State Split**: Server state belongs in query caches (TanStack Query, SWR, Apollo); ephemeral UI state belongs in component state or lightweight stores (Zustand, Jotai). Never mirror server data into local UI stores.

---

## 3. The 6-Step Impeccable Critique Workflow

Before declaring any frontend or UI task complete, execute the critique workflow:
1. **Audit Contrast & Colors**: Check all text against APCA / WCAG 2.2 AA ($4.5:1$ text, $3:1$ graphics).
2. **Audit Typography & Measure**: Confirm `65ch` max line length, tabular figures for numbers, and crisp type scale ratios ($1.25\text{--}1.333$).
3. **Audit Touch & Click Targets**: Verify $48\times 48\text{dp}$ touch target padding (`::after` hitboxes where visual size is smaller).
4. **Audit Empty, Error & Loading States**: Zero blank screens. Shimmers match exact dimensions of final loaded content.
5. **Audit Keyboard & Screen Reader Path**: Full Tab/Shift+Tab navigation, focus trap in dialogs, visible outline focus rings.
6. **Audit Motion & Viewport Resilience**: Test with `prefers-reduced-motion: reduce`, verify `100dvh` mobile layout, check for zero horizontal overflow (`overflow-x: hidden` / robust flex wrapping).

---

## 4. Usage Instructions for Antigravity

- **Always Active**: Adhere to these principles by default on all UI, CSS, mobile, and frontend engineering tasks.
- **Deep Reference**: For specific algorithms, physics formulas, CSS recipes, or code examples, consult the files in `./references/`.
- **Authority**: The Design Doctrine supersedes ad-hoc frontend practices and ensures that every interface built is exceptionally polished, accessible, responsive, and delightful.
