# 05. Intrinsic Layouts & Algorithmic CSS

> "Stop thinking about screens as fixed canvas sizes with arbitrary media-query breakpoints. Build resilient layout algorithms that allow content to negotiate its own ideal geometry."  
> — Heydon Pickering & Andy Bell (*Every Layout: Relearn CSS Layout*, 2019)

---

## 1. The Paradigm Shift: Responsive vs. Intrinsic Web Design

Traditional responsive design relied on hardcoded media query breakpoints (`@media (min-width: 768px)`). When an element is placed inside a narrow sidebar or nested drawer, viewport breakpoints collapse—a card renders as if on a desktop display despite having only $300\text{px}$ of available container width.

**Intrinsic Web Design** (Jen Simmons, Heydon Pickering, Andy Bell) replaces arbitrary device breakpoints with **content-driven layout algorithms** powered by Flexbox, CSS Grid, `min()`, `max()`, `clamp()`, and Container Queries.

---

## 2. The Canonical Layout Primitives (Every Layout)

### 2.1 The Stack
Vertical flow layout that manages spacing exclusively between sibling elements, eliminating redundant top/bottom margins.

```css
.l-stack {
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
}

.l-stack > * + * {
  margin-block-start: var(--stack-space, 1.5rem);
}

/* Recursive variant: enforce space even on nested containers */
.l-stack-recursive * + * {
  margin-block-start: var(--stack-space, 1.5rem);
}
```

---

### 2.2 The Cluster
Distributes items horizontally, wrapping them onto subsequent lines with uniform spacing between all elements. Ideal for tags, chip lists, button groups, and metadata pills.

```css
.l-cluster {
  display: flex;
  flex-wrap: wrap;
  gap: var(--cluster-space, 0.75rem);
  justify-content: var(--cluster-justify, flex-start);
  align-items: var(--cluster-align, center);
}
```

---

### 2.3 The Sidebar (Media-Query-Free Responsive Split)
Splits content into a fixed-or-proportional sidebar and an expanding main content area. When available space shrinks below the threshold, the layout wraps automatically into a single column **without a media query**.

```css
.l-sidebar {
  display: flex;
  flex-wrap: wrap;
  gap: var(--sidebar-gutter, 1.5rem);
}

/* The Sidebar element */
.l-sidebar > :first-child {
  flex-basis: var(--sidebar-width, 280px);
  flex-grow: 1;
}

/* The Main Content element */
.l-sidebar > :last-child {
  flex-basis: 0;
  flex-grow: 999;
  min-inline-size: var(--main-min-width, 50%);
}
```
* **How it works:** When container width falls below $\text{sidebar-width} + \text{main-min-width} + \text{gutter}$, the `flex-grow: 999` element drops onto its own row and expands to $100\%$.

---

### 2.4 The Switcher (The $N$-Threshold Flex Switch)
Displays items in a horizontal row when space permits, but abruptly switches all children into a vertical column when the parent container drops below a specified width threshold.

```css
.l-switcher {
  display: flex;
  flex-wrap: wrap;
  gap: var(--switcher-space, 1.5rem);
}

.l-switcher > * {
  flex-grow: 1;
  /* 
   * The Switcher Formula:
   * When container width > threshold, (threshold - 100%) is negative -> basis is 0 -> items sit side-by-side.
   * When container width < threshold, (threshold - 100%) is positive -> basis is huge -> items wrap to 100%.
   */
  flex-basis: calc((var(--switcher-threshold, 40rem) - 100%) * 999);
}

/* Limit to maximum number of columns before wrapping */
.l-switcher > :nth-last-child(n + 5),
.l-switcher > :nth-last-child(n + 5) ~ * {
  flex-basis: 100%;
}
```

---

### 2.5 The Reel (Momentum-Scrolling Strip)
Enables horizontal overflow scrolling for cards or carousel items while preserving touch momentum, eliminating scrollbar obscuration, and supporting snap points.

```css
.l-reel {
  display: flex;
  block-size: auto;
  overflow-x: auto;
  overflow-y: hidden;
  overscroll-behavior-x: contain;
  scroll-snap-type: x mandatory;
  scroll-padding-inline: var(--reel-gutter, 1.5rem);
  gap: var(--reel-space, 1rem);
  padding-inline: var(--reel-gutter, 1.5rem);
  
  /* Hide scrollbar visually while preserving a11y and touch scrolling */
  scrollbar-width: none; /* Firefox */
}

.l-reel::-webkit-scrollbar {
  display: none; /* WebKit */
}

.l-reel > * {
  flex: 0 0 var(--reel-item-width, 320px);
  scroll-snap-align: start;
}
```

---

### 2.6 The Grid (Adaptive RAM Pattern: Repeat, Auto, Minmax)
Generates an intrinsically responsive grid without a single media query. Columns automatically subdivide available width and wrap gracefully down to mobile screens.

```css
.l-grid {
  display: grid;
  gap: var(--grid-gap, 1.5rem);
  /* 
   * RAM Pattern:
   * min(100%, 280px) guarantees that if the viewport is narrower than 280px,
   * the column shrinks to 100% instead of causing horizontal layout overflow.
   */
  grid-template-columns: repeat(auto-fill, minmax(min(100%, var(--grid-min-col, 280px)), 1fr));
}
```

---

### 2.7 The Cover
Ensures a hero container spans a minimum of $100\%$ viewport or parent height, centering the primary call-to-action vertically while anchoring headers and footers to the top and bottom.

```css
.l-cover {
  display: flex;
  flex-direction: column;
  min-block-size: var(--cover-min-height, 100vh);
  padding: var(--cover-padding, 2rem);
}

.l-cover > * {
  margin-block: 0;
}

/* The centered principal element consumes all available margin */
.l-cover > .l-cover-center {
  margin-block: auto;
}
```

---

## 3. CSS Subgrid: Aligning Multi-Card Grids

In traditional CSS Grid, card headers, descriptions, and CTA buttons inside adjacent cards cannot align vertically because each card's internal layout is isolated from its siblings. **CSS Subgrid** fixes this architectural flaw.

```css
.l-card-grid {
  display: grid;
  gap: 1.5rem;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
}

.l-card {
  display: grid;
  grid-row: span 3; /* Card spans 3 grid tracks */
  grid-template-rows: subgrid; /* Inherits row track sizes from parent */
  gap: 0.75rem;
  background: var(--color-neutral-raised);
  padding: 1.5rem;
  border-radius: 12px;
}

/* Row 1: Header (Title + Badge) */
/* Row 2: Description text (Takes identical height across cards) */
/* Row 3: Action Button (Locked perfectly along bottom baseline) */
```

---

## 4. Container Queries: Component-Driven Responsive Architecture

Container queries decouple components completely from the global viewport. A `<UserProfileCard />` can now adjust its layout whether rendered in a full-width dashboard or a $280\text{px}$ slide-out drawer:

```css
.card-container {
  container-type: inline-size;
  container-name: user-card;
}

.user-card {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

/* When the parent container (NOT the screen) is >= 480px */
@container user-card (min-width: 480px) {
  .user-card {
    flex-direction: row;
    align-items: center;
  }

  .user-card__avatar {
    inline-size: 80px;
    block-size: 80px;
  }
}
```
