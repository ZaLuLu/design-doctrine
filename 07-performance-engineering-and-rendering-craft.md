# 07. Performance Engineering & Rendering Craft

> "Performance is design. An interface that drops frames, shifts layout during load, or takes 800ms to acknowledge a click destroys user trust regardless of visual styling."  
> — Google Chrome Team (*RAIL Performance Model*, web.dev)

---

## 1. The RAIL Performance Model & Core Web Vitals

```
┌────────────────────────────────────────────────────────────────────────┐
│                        THE RAIL METRIC TARGETS                         │
├───────────────┬────────────────────────────────────────────────────────┤
│ R - Response  │ < 50ms to acknowledge user input. Total INP < 200ms.   │
├───────────────┼────────────────────────────────────────────────────────┤
│ A - Animation │ 16.6ms per frame (60fps) or 8.3ms (120fps ProMotion).  │
├───────────────┼────────────────────────────────────────────────────────┤
│ I - Idle      │ Maximize idle time; chunk heavy work into 50ms blocks. │
├───────────────┼────────────────────────────────────────────────────────┤
│ L - Load      │ LCP < 2.5s on mobile networks. Deliver first screen.   │
└───────────────┴────────────────────────────────────────────────────────┘
```

### 1.1 Core Web Vitals (CWV) Invariants

| Metric | Target | Measurement Focus | Primary Root Cause & Fix |
|---|---|---|---|
| **LCP** (Largest Contentful Paint) | **$< 2.5\text{s}$** | Render time of largest above-the-fold image/text. | Render-blocking CSS/fonts, lazy-loaded hero images. **Fix:** Preload WOFF2 fonts and `fetchpriority="high"` on hero. |
| **INP** (Interaction to Next Paint) | **$< 200\text{ms}$** | Latency of worst interaction throughout page session. | Long JavaScript execution on main thread. **Fix:** `requestIdleCallback()`, web workers, optimistic UI updates. |
| **CLS** (Cumulative Layout Shift) | **$< 0.1$** | Unexpected visual shifting of page layout. | Images/ads/videos without dimensions, dynamic font swaps. **Fix:** Strict `aspect-ratio` reserving space. |

---

## 2. Zero-FOUT Font Architecture: Eliminating Typographic Flash

`font-display: swap` displays a fallback font and abruptly flashes/reflows when the custom webfont loads. This breaks visual polish.

### 2.1 The Invariant Font Pipeline
1. **Self-Host WOFF2:** Never use third-party render-blocking stylesheets (e.g., Google Fonts `<link>` adds 2 network roundtrips). Convert typefaces to WOFF2 and host locally.
2. **Preload Critical First-Viewport Faces:**

```html
<link 
  rel="preload" 
  href="/fonts/inter-display-variable.woff2" 
  as="font" 
  type="font/woff2" 
  crossorigin
>
```

3. **Gate Visibility with Graceful Fade-In:**
   Prevent display text from flashing a system fallback font using an inline `<head>` execution script with a failsafe timeout:

```html
<!-- Put immediately in <head> before stylesheets -->
<script>
  (function() {
    document.documentElement.classList.add('fonts-pending');
    
    // Fallback safety timeout: never leave text hidden if network stalls
    var timeout = setTimeout(function() {
      document.documentElement.classList.replace('fonts-pending', 'fonts-ready');
    }, 2000);

    if ('fonts' in document) {
      document.fonts.ready.then(function() {
        clearTimeout(timeout);
        document.documentElement.classList.replace('fonts-pending', 'fonts-ready');
      });
    }
  })();
</script>

<style>
  /* Hold display headings invisible during font transfer */
  .fonts-pending .hero-display-heading {
    opacity: 0;
  }
  
  /* Fade in gracefully once the true vector glyphs are parsed */
  .fonts-ready .hero-display-heading {
    opacity: 1;
    transition: opacity 280ms ease-out;
  }
</style>
```

4. **Metric-Matched Fallbacks for Body Copy:**
   Tune the system fallback font using CSS font metrics so its x-height and width match the custom font, eliminating layout shifts:

```css
@font-face {
  font-family: 'Fallback-Body';
  src: local('Arial');
  ascent-override: 90%;
  descent-override: 22%;
  line-gap-override: 0%;
  size-adjust: 104.5%;
}
```

---

## 3. Zero-CLS Media Invariants

Deferred content must never cause surrounding elements to jump.

### 3.1 Strict Media Sizing
Always reserve the box dimensions before media arrives:

```html
<!-- Direct dimensions in HTML -->
<img 
  src="/hero-cover.webp" 
  width="1200" 
  height="675" 
  alt="Dashboard Preview"
  fetchpriority="high"
  decoding="async"
  style="inline-size: 100%; block-size: auto; aspect-ratio: 16 / 9;"
>
```

### 3.2 Dynamic Media Placeholders
Every image and canvas must paint a coherent placeholder:
1. Low-Resolution Image Placeholder (LRIP / BlurHash) rendered as a blurred inline SVG/Canvas background.
2. Solid background surface matching the dominant image hue.
3. Fade the real asset in once loaded:

```css
.media-container {
  background-color: var(--color-neutral-raised);
  position: relative;
  overflow: hidden;
  aspect-ratio: 16 / 9;
}

.media-container img {
  opacity: 0;
  transition: opacity 300ms ease-out;
}

.media-container img.loaded {
  opacity: 1;
}
```

---

## 4. Chrome Stability: The Constant-Height Invariant

Persistent navigation bars, search inputs, toolbars, and status banners must maintain **strictly fixed height** regardless of dynamic content.

### 4.1 Layout Shift Bug Pattern
A status message like *"Synchronizing 4,120 records with remote cluster..."* wraps from one line to two lines, pushing down the main workspace or action buttons by $24\text{px}$. This is a layout-jank bug.

### 4.2 The Fix
1. Constrain status text to a single line:
   ```css
   .status-line {
     white-space: nowrap;
     overflow: hidden;
     text-overflow: ellipsis;
   }
   ```
2. Lock the parent container footprint using `block-size` / `min-block-size`:
   ```css
   .app-toolbar {
     block-size: 48px;
     min-block-size: 48px;
     display: flex;
     align-items: center;
     contain: layout size;
   }
   ```

---

## 5. Main-Thread Hygiene & GPU Compositing

### 5.1 Eliminating Layout Thrashing
Layout thrashing occurs when JavaScript writes to the DOM and reads geometric layout properties in an interleaved loop, forcing repeated synchronous layout recalcs.

```typescript
// ❌ HORRIBLE: Forces N synchronous layouts
items.forEach(item => {
  const height = item.offsetHeight; // Read (invalidates style)
  item.style.height = `${height + 10}px`; // Write (triggers reflow)
});

// ✅ CORRECT: Batch reads, then batch writes
const heights = items.map(item => item.offsetHeight); // Batch Reads
items.forEach((item, index) => {
  item.style.height = `${heights[index] + 10}px`; // Batch Writes
});
```

### 5.2 Prudent Use of `will-change`
Applying `will-change: transform` to thousands of elements allocates GPU memory (VRAM) indiscriminately, crashing low-memory mobile devices.
* **Invariant:** Apply `will-change` dynamically upon pointer hover (`pointerenter`) or touch gesture initiation (`touchstart`), and remove it once the transition ends.
