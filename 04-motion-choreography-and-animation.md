# 04. Motion Choreography & Interface Animation

> "Animation in an interface is not decoration; it is visual communication. It preserves spatial context, explains object relationships, and confirms user intent."  
> — Rachel Nabors (*Animation at Work*, 2017)

---

## 1. The Classical Animation Principles in UI

In 1993, Bay-Wei Chang and David Ungar published *Animation: From Cartoons to the User Interface* at Xerox PARC, formalizing how Frank Thomas and Ollie Johnston's 12 principles from *The Illusion of Life* (1981) govern human spatial understanding in software.

```
┌────────────────────────────────────────────────────────────────────────┐
│                   DISNEY 12 PRINCIPLES ➔ UI EQUIVALENTS                │
├────────────────────────────┬───────────────────────────────────────────┤
│ Disney Classical Principle │ User Interface Implementation             │
├────────────────────────────┼───────────────────────────────────────────┤
│ 1. Squash & Stretch        │ Active button press scale(0.97), rubber   │
│                            │ banding at scroll boundaries.             │
├────────────────────────────┼───────────────────────────────────────────┤
│ 2. Anticipation            │ Pre-activation states; pull-to-refresh    │
│                            │ tension; hover state elevating before tap.│
├────────────────────────────┼───────────────────────────────────────────┤
│ 3. Staging                 │ Dimming backdrop under modals; blurring   │
│                            │ background on sheets to isolate focus.    │
├────────────────────────────┼───────────────────────────────────────────┤
│ 4. Straight Ahead / Pose   │ FLIP animation technique; keyframe-driven │
│                            │ state transitions.                        │
├────────────────────────────┼───────────────────────────────────────────┤
│ 5. Follow Through & Overlap│ Staggered cascade entrance of list items  │
│                            │ (e.g., 20-30ms delay between items).      │
├────────────────────────────┼───────────────────────────────────────────┤
│ 6. Slow In & Slow Out      │ Asymmetric easing: cubic-bezier curves    │
│                            │ and critically damped springs.            │
├────────────────────────────┼───────────────────────────────────────────┤
│ 7. Arcs                    │ Moving elements follow curved parabolic   │
│                            │ trajectories, not rigid linear diagonals. │
├────────────────────────────┼───────────────────────────────────────────┤
│ 8. Secondary Action        │ Ripple waves, active toggle thumb squash, │
│                            │ icon morphing alongside button expand.    │
├────────────────────────────┼───────────────────────────────────────────┤
│ 9. Timing                  │ 150ms for micro-actions, 250-350ms for   │
│                            │ full-screen modal reveals.                │
├────────────────────────────┼───────────────────────────────────────────┤
│ 10. Exaggeration           │ Controlled 2-5% spring overshoot on badge │
│                            │ updates or counter increments.            │
├────────────────────────────┼───────────────────────────────────────────┤
│ 11. Solid Drawing          │ Consistent lighting, specular edges, and  │
│                            │ depth layers during 3D card tilt.         │
├────────────────────────────┼───────────────────────────────────────────┤
│ 12. Appeal                 │ Tactile fluidity, coherent physics, and   │
│                            │ zero-frame stutter.                       │
└────────────────────────────┴───────────────────────────────────────────┘
```

---

## 2. Spatial Directionality & Navigation Choreography

Motion must never lie about the underlying information architecture:

1. **Hierarchical / Depth Navigation (Parent ➔ Child):**
   * Pushing a child view into focus: Child enters from the **right** (or bottom on mobile sheet); parent recedes slightly into the background with opacity and scale reduction (`scale(0.96)`, `opacity: 0.8`).
   * Popping back: Reverse the motion exactly.
2. **Peer Navigation (Tabs / Sibling Views):**
   * Sibling views at the same architectural depth must **cross-fade** or slide laterally based on tab index order. Never use z-axis depth transitions for lateral siblings.
3. **Contextual Expansion (Element ➔ Modal / Dialog):**
   * A dialog or full-screen card must originate from the **exact bounding box** of the trigger element, expanding outward to full viewport dimensions (Shared Element Transition).

---

## 3. High-Performance Hardware Acceleration: Compositor Properties

Browsers and mobile rendering engines execute layout and paint cycles in distinct threads. Animating layout triggers causes main-thread blocking, dropped frames, and battery drain.

### 3.1 The Performance Matrix

| CSS Property | Triggers Layout (Reflow)? | Triggers Paint? | Compositor Only (GPU)? | Performance Rating |
|---|---|---|---|---|
| `transform` (`translate`, `scale`, `rotate`) | **No** | **No** | **Yes** | ⚡ **120 FPS / Instant** |
| `opacity` | **No** | **No** | **Yes** | ⚡ **120 FPS / Instant** |
| `filter` (`blur`, `brightness`) | **No** | **Yes** | Sometimes | ⚠️ Moderate (VRAM heavy) |
| `top`, `left`, `bottom`, `right` | **Yes** | **Yes** | **No** | ❌ **Jank / Layout Thrash** |
| `width`, `height`, `padding`, `margin` | **Yes** | **Yes** | **No** | ❌ **Jank / Layout Thrash** |

### 3.2 Invariant Rule
* Animate **only** `transform` and `opacity`.
* To animate size changes, morph the element using `scale()` and counteract child content distortion with inverse scaling, or use the **FLIP technique**.

---

## 4. The FLIP Animation Technique: Mathematical Code Recipe

Pioneered by Paul Lewis (Google Chrome team), FLIP stands for **First, Last, Invert, Play**. It allows animating complex layout changes (reordering lists, resizing cards, reparenting elements) entirely on the GPU compositor without layout thrashing.

```
1. FIRST:   Record initial element coordinates: rect1 = getBoundingClientRect()
2. LAST:    Apply state change, record final:  rect2 = getBoundingClientRect()
3. INVERT:  Apply inverse transform: dx = rect1.left - rect2.left
            dy = rect1.top - rect2.top, sx = rect1.width / rect2.width
            Element visually appears at 'First' position with ZERO delay.
4. PLAY:    Remove transform with transition; GPU animates to 'Last' at 60/120fps.
```

### 4.1 Production TypeScript Implementation

```typescript
export class FlipAnimator {
  private element: HTMLElement;
  private firstRect: DOMRect | null = null;

  constructor(element: HTMLElement) {
    this.element = element;
  }

  /**
   * Step 1: Snapshot the initial position
   */
  public snapshot(): void {
    this.firstRect = this.element.getBoundingClientRect();
  }

  /**
   * Steps 2, 3, 4: Compute delta and play GPU transition
   */
  public play(durationMs: number = 300, easing: string = "cubic-bezier(0.16, 1, 0.3, 1)"): void {
    if (!this.firstRect) return;

    // Step 2: Compute Last
    const lastRect = this.element.getBoundingClientRect();

    // Step 3: Compute Invert deltas
    const deltaX = this.firstRect.left - lastRect.left;
    const deltaY = this.firstRect.top - lastRect.top;
    const deltaWidth = this.firstRect.width / Math.max(lastRect.width, 1);
    const deltaHeight = this.firstRect.height / Math.max(lastRect.height, 1);

    // If no spatial movement occurred, exit
    if (deltaX === 0 && deltaY === 0 && deltaWidth === 1 && deltaHeight === 1) {
      this.firstRect = null;
      return;
    }

    // Apply Invert transform instantly (zero transition)
    this.element.style.transformOrigin = "top left";
    this.element.style.transition = "none";
    this.element.style.transform = `translate(${deltaX}px, ${deltaY}px) scale(${deltaWidth}, ${deltaHeight})`;

    // Force style flush / reflow
    void this.element.offsetHeight;

    // Step 4: Play forward to natural state
    this.element.style.transition = `transform ${durationMs}ms ${easing}`;
    this.element.style.transform = "none";

    // Clean up inline styles upon completion
    const cleanup = () => {
      this.element.style.transition = "";
      this.element.style.transform = "";
      this.element.style.transformOrigin = "";
      this.element.removeEventListener("transitionend", cleanup);
    };
    this.element.addEventListener("transitionend", cleanup);

    this.firstRect = null;
  }
}
```

---

## 5. Timing, Duration & Easing Tokens

Timing must reflect the physical scale of the interaction:

```css
:root {
  /* Durations */
  --duration-instant: 100ms;  /* Checkboxes, active buttons, micro-toggles */
  --duration-swift:   200ms;  /* Tooltips, dropdown menus, hover highlights */
  --duration-medium:  300ms;  /* Drawer panels, modal sheets, card expands */
  --duration-deliberate: 450ms; /* Full-page route transitions */

  /* Easings */
  /* Ease-Out Decelerate: Entering elements arrive fast and settle smoothly */
  --ease-decelerate: cubic-bezier(0.0, 0.0, 0.2, 1.0);

  /* Ease-In Accelerate: Exiting elements leave swiftly */
  --ease-accelerate: cubic-bezier(0.4, 0.0, 1.0, 1.0);

  /* Natural Standard (Hero transitions) */
  --ease-standard: cubic-bezier(0.2, 0.0, 0.0, 1.0);
}
```

---

## 6. Accessibility: Reduced Motion Invariants

Every motion system must respect `prefers-reduced-motion: reduce`. Users with vestibular disorders experience physical nausea, vertigo, and migraines from large spatial displacements and parallax effects.

```css
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }

  /* Retain subtle opacity fades to preserve state continuity without motion sickness */
  .fade-on-reduced {
    transition: opacity 150ms ease-in-out !important;
  }
}
```
