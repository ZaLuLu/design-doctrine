# 11. Frontend Codebase & State Architecture

> "Architecture is the structure that allows an interface to maintain its design integrity as teams scale from 1 engineer to 100. If code organization is ambiguous, visual polish inevitably degrades into ad-hoc hacks and UI entropy."  
> — Synthesized from Frontend Architecture (`stareezy-1/frontend-architecture-skill`) and UI/UX Pro Max

---

## 1. The 5 Foundational Architectural Invariants

A world-class design system must be supported by an unambiguous codebase architecture. Every contributor must instinctively know the answer to three questions:
1. *Where does this component live?*
2. *What is allowed to import what?*
3. *Is this server state or UI state?*

```
┌────────────────────────────────────────────────────────────────────────┐
│                   THE 5 CODEBASE INVARIANTS                            │
├──────────────────────────┬─────────────────────────────────────────────┤
│ 1. Feature Modules       │ Features own their world in /modules/{name} │
│                          │ with their own pages, stores, and hooks.    │
├──────────────────────────┼─────────────────────────────────────────────┤
│ 2. Pages are Directories │ Route components are directories that       │
│                          │ co-locate page-local styles and components. │
├──────────────────────────┼─────────────────────────────────────────────┤
│ 3. State Split by Origin │ Server data (query cache) and client UI     │
│                          │ state (stores) NEVER duplicate or overlap.  │
├──────────────────────────┼─────────────────────────────────────────────┤
│ 4. Barrel Boundary       │ Cross-module imports enter ONLY through the │
│                          │ feature's top-level index.ts barrel.        │
├──────────────────────────┼─────────────────────────────────────────────┤
│ 5. Code is Promoted      │ Components start page-local; they promote   │
│                          │ outward ONLY when a second consumer exists. │
└──────────────────────────┴─────────────────────────────────────────────┘
```

---

## 2. Directory Schema & Module Boundaries

This framework-agnostic structure scales across Next.js (App Router), React + Vite (SPA), Remix, and React Native:

```
src/
├── app/ or routes/                  ← Framework routing layer (thin wrappers only)
├── modules/                         ← Feature modules (the product domain)
│   └── {feature}/                   ← e.g. auth, billing, events, ticketing
│       ├── index.ts                 ← PUBLIC BARREL: The only cross-module entry point
│       ├── README.md                ← Module ownership, route definitions, data deps
│       ├── components/              ← Reused by 2+ pages within this module
│       ├── pages/                   ← Page directories (one per route)
│       │   └── {page}/
│       │       ├── {page}.tsx       ← The page view component
│       │       ├── {page}.styles.ts ← Co-located CSS/Tailwind definitions
│       │       ├── index.ts         ← Re-exports page component
│       │       ├── components/      ← Components used ONLY by this page
│       │       └── hooks/           ← Hooks used ONLY by this page
│       ├── hooks/                   ← Data queries (TanStack) + module business hooks
│       ├── stores/                  ← UI/client state store (Zustand) — zero server data
│       ├── services/                ← Typed API clients and network calls
│       ├── utils/                   ← Pure module utility functions (*.test.ts)
│       └── types/                   ← Request/response DTOs & view models
│ └── shared/                          ← Universal Design System & Cross-Module Primitives
    ├── components/                  ← Design system atoms/molecules used across modules
    ├── hooks/                       ← Universal hooks (useMediaQuery, useSpringGesture)
    ├── api-client/                  ← Single typed HTTP / GraphQL client instance
    ├── store/                       ← Root global store wiring (auth session, theme)
    ├── tokens/                      ← Design token constants & CSS variable bindings
    ├── utils/                       ← formatters, cn(), assertion guards
    └── types/                       ← Universal base types (PaginatedResponse, Result)
```

---

## 3. Strict State Separation: Server Data vs. UI State

The most common source of UI bugs and performance regressions is storing server responses in client UI stores.

```
┌───────────────────────────────────────┐      ┌───────────────────────────────────────┐
│       SERVER STATE (Query Layer)      │      │        UI STATE (Client Store)        │
├───────────────────────────────────────┤      ├───────────────────────────────────────┤
│ • TanStack Query, SWR, Apollo Client  │      │ • Zustand, Jotai, Pinia, React State  │
│ • Origin: Remote database / backend   │      │ • Origin: User interactions in browser│
│ • Traits: Asynchronous, shared, cache │      │ • Traits: Synchronous, local to tab   │
│ • Examples: User profile, ticket list,│      │ • Examples: Modal open/closed, active │
│   invoice totals, order history       │      │   filter selections, command palette  │
└───────────────────────────────────────┘      └───────────────────────────────────────┘
```

### The Invariant Rule:
Never copy query data into a Zustand store or React `useState` to "edit" or "filter" it. 
* Keep the query cache authoritative.
* UI stores hold only active filter parameters (e.g. `selectedCategoryId`, `searchQuery`).
* Derive final displayed data via computed selectors: `const visibleEvents = events.filter(...)`.

---

## 4. The Component Promotion Pipeline

Design systems fail when engineers prematurely abstract components into global shared folders before understanding their actual usage patterns.

```
Level 1: PAGE-LOCAL COMPONENT
  └─ src/modules/{feature}/pages/{page}/components/TicketHeader.tsx
     Used only on this screen. Lives next to its route.

Level 2: MODULE-REUSED COMPONENT
  └─ Promoted to src/modules/{feature}/components/TicketCard.tsx
     Condition: A second page within the SAME feature module requires it.

Level 3: GLOBAL DESIGN SYSTEM PRIMITIVE
  └─ Promoted to src/shared/components/Card/Card.tsx
     Condition: A second FEATURE MODULE imports it. Abstracted into a pure,
     domain-agnostic atom/molecule with comprehensive 8-state coverage.
```

---

## 5. UI/UX Pro Max Priority Matrix

When executing features, resolving engineering tradeoffs, or triaging QA issues, adhere strictly to this priority ranking:

```
Priority 1: ACCESSIBILITY (CRITICAL)
  └─ Keyboard traps, screen reader ARIA contracts, APCA contrast, focus visibility.

Priority 2: TOUCH & INTERACTION (CRITICAL)
  └─ 44pt tap targets, Doherty < 100ms feedback, loading button disabling, cursor-pointer.

Priority 3: PERFORMANCE (HIGH)
  └─ Core Web Vitals (LCP < 2.5s, INP < 200ms, CLS < 0.1), zero-FOUT font pipelines.

Priority 4: LAYOUT & RESPONSIVENESS (HIGH)
  └─ Single-line affordances, overflow-x: clip, intrinsic RAM grid algorithms.

Priority 5: TYPOGRAPHY & COLOR (MEDIUM)
  └─ Bringhurst 65ch measure, OKLCH perceptual palettes, 8pt spatial cadence.

Priority 6: ANIMATION & MOTION (MEDIUM)
  └─ Compositor-only transforms, spring physics, reduced-motion fallbacks.

Priority 7: STYLE & FINISH (MEDIUM)
  └─ Single icon system (1.5px stroke), 3-radius rule, no re-drawn fake chrome.

Priority 8: CHARTS & DATA VISUALIZATION (LOW / SPECIALIZED)
  └─ Dimensionally matched charts, tabular-nums alignment, accessible data tables.
```

---

## 6. Data Visualization & Chart Selection Guide

Never select charts based on decorative aesthetic appeal. Match chart anatomy directly to the data dimension:

| Data Relationship | Data Variables | Recommended Chart | Implementation Invariant |
|---|---|---|---|
| **Trend over Time** | Continuous Time + 1 Metric | **Line Chart / Area** | Zero baseline not required for indexes; maintain $\ge 3 : 1$ line contrast. |
| **Discrete Comparison** | Categorical + 1 Metric | **Bar Chart (Column/Row)** | Always start baseline at 0; sort categories by descending value. |
| **Part-to-Whole** | Categorical shares (sum = 100%) | **Donut Chart** | Cap at $\le 5$ slices; order clockwise largest to smallest. |
| **Correlation** | 2 Continuous Metrics | **Scatter Plot** | Use semi-transparent marks to reveal cluster density. |
| **Distribution** | 1 Continuous Metric (many samples) | **Histogram / Boxplot** | Use uniform bin widths; label quantile medians clearly. |
| **Hierarchical Share** | Nested Categories | **Treemap** | Enforce legible label contrast on smallest child rects. |

---

## 7. Systematic Z-Index Layering Token Scale

Arbitrary z-index assignments (`z-index: 99999`) cause layered overlay collisions. Enforce a strict 6-tier token scale:

```css
:root {
  --z-base: 0;             /* Standard document flow content */
  --z-dropdown: 100;       /* Contextual menus, select popovers */
  --z-sticky-content: 200; /* Sticky section headers, table headers */
  --z-sticky-nav: 300;     /* Primary global navigation bar */
  --z-backdrop: 400;       /* Modal and drawer dimming overlays */
  --z-dialog: 500;         /* Accessible modal dialogs, slide sheets */
  --z-popover: 600;        /* Floating tooltips, contextual callouts */
  --z-toast: 700;          /* System notification toasts */
}
```

---

## 8. Form Field & Interactive Cursor Contracts

1. **Explicit Label Binding:** Every `<input>`, `<select>`, and `<textarea>` must be linked to an explicit `<label>` via matching `id` and `for` (`htmlFor` in React):
   ```tsx
   <label htmlFor="user-handle" className="text-sm font-medium text-ink">Handle</label>
   <input id="user-handle" type="text" className="c-input" />
   ```
2. **The `cursor: pointer` Invariant:**
   * All clickable elements (buttons, links, custom select triggers, accordion headers, dismiss icons) must explicitly declare `cursor: pointer`.
   * Disabled elements must declare `cursor: not-allowed`.
