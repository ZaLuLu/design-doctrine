# 08. Accessibility & Inclusive Design Architecture

> "Accessibility is not a feature or a compliance checkbox; it is the fundamental architecture of resilient, universally functional software."  
> — Heydon Pickering (*Inclusive Design Patterns*, 2016)

---

## 1. W3C WCAG 2.2 Standards & Empirical Invariants

Meeting WCAG 2.2 Level AA (and targeting AAA) guarantees that software operates under assistive technology, motor impairments, low vision, and extreme ambient sunlight.

```
┌────────────────────────────────────────────────────────────────────────┐
│                        CRITICAL WCAG 2.2 METRICS                       │
├──────────────────────────┬─────────────────────────────────────────────┤
│ Contrast (Normal Text)   │ 4.5:1 minimum against background (AA).      │
│                          │ 7.0:1 for AAA compliance.                   │
├──────────────────────────┼─────────────────────────────────────────────┤
│ Contrast (Large Text)    │ 3.0:1 minimum (>= 18pt or 14pt bold).       │
├──────────────────────────┼─────────────────────────────────────────────┤
│ UI Components & Borders  │ 3.0:1 minimum for active boundaries/icons.  │
├──────────────────────────┼─────────────────────────────────────────────┤
│ Target Size (SC 2.5.8)   │ Minimum 24x24 CSS px (AA), recommended     │
│                          │ 44x44pt (Apple) / 48x48dp (Android).        │
├──────────────────────────┼─────────────────────────────────────────────┤
│ Focus Visibility         │ Visible 2px outline with >= 3:1 contrast.   │
└──────────────────────────┴─────────────────────────────────────────────┘
```

---

## 2. Advanced Contrast: WCAG 2 vs. APCA

The legacy WCAG 2 contrast formula relies on raw relative luminance ratios ($L_1 / L_2$), which fails human perceptual reality:
* It misjudges dark mode contrast (white text on dark backgrounds often overstates readability).
* It ignores spatial frequency (thin fonts need dramatically higher contrast than bold fonts to be readable).

**APCA (Accessible Perceptual Contrast Algorithm)** calculates perceived lightness difference ($L^c$):
* **Body text ($16\text{px}$ normal):** Requires $L^c \ge 75$.
* **Headings ($32\text{px}$ bold):** Requires $L^c \ge 45$.
* **Non-text UI elements / icons:** Requires $L^c \ge 60$.

---

## 3. High-Contrast Focus Ring Architecture

Hiding focus rings via `outline: none` without replacement is a severe usability defect. Mouse clicks should not show jarring rings, but keyboard navigation must display unambiguous focus rings against any surface.

```css
/* High-Contrast Dual-Layer Focus Ring */
:focus {
  outline: none; /* Suppress legacy outline */
}

:focus-visible {
  /* 
   * Dual-layer shadow: 
   * 1. Inner white halo (2px) ensures visibility against dark surfaces.
   * 2. Outer brand/blue halo (4px) ensures visibility against light surfaces.
   * Guaranteed visible on pure black, pure white, or vibrant gradients.
   */
  outline: 2px solid transparent;
  box-shadow: 
    0 0 0 2px var(--color-surface-base, #ffffff),
    0 0 0 5px var(--color-interactive-focus, #2563eb);
  border-radius: inherit;
}
```

---

## 4. Semantic Contract: Buttons vs. Links

```
┌────────────────────────────────────────────────────────────────────────┐
│                      BUTTON OR LINK? DECISION TREE                     │
├────────────────────────────────────────────────────────────────────────┤
│ Does the user action change the URL or navigate to a new page/resource?│
│  ├─ YES ➔ Use <a href="...">                                          │
│  │         (Never use <div onclick> or <button onclick="location">)    │
│  │                                                                     │
│  └─ NO  ➔ Does it perform a state mutation, trigger a modal, or send   │
│           data to a backend?                                           │
│            └─ YES ➔ Use <button type="button"> (or type="submit")     │
│                     (Never use <a href="#"> or <a href="javascript:">) │
└────────────────────────────────────────────────────────────────────────┘
```

---

## 5. ARIA Authoring Practices Guide (APG) Production Contract: Accessible Modal Dialog

An accessible modal dialog requires:
1. `role="dialog"` and `aria-modal="true"`.
2. `aria-labelledby` referencing the title element.
3. Focus trapped within the modal while open.
4. Outside elements marked `inert`.
5. Dismissal via `Escape` key.
6. Restoring keyboard focus to the triggering element upon close.

```typescript
export class AccessibleModal {
  private dialog: HTMLElement;
  private previousActiveElement: HTMLElement | null = null;
  private focusableElements: HTMLElement[] = [];

  constructor(dialog: HTMLElement) {
    this.dialog = dialog;
    this.dialog.setAttribute("role", "dialog");
    this.dialog.setAttribute("aria-modal", "true");
  }

  public open(): void {
    // 1. Snapshot previous focus trigger
    this.previousActiveElement = document.activeElement as HTMLElement;

    // 2. Locate focusable children
    this.focusableElements = Array.from(
      this.dialog.querySelectorAll<HTMLElement>(
        'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])'
      )
    );

    // 3. Reveal and focus initial element
    this.dialog.hidden = false;
    if (this.focusableElements.length > 0) {
      this.focusableElements[0].focus();
    }

    // 4. Bind event listeners
    window.addEventListener("keydown", this.handleKeyDown);
  }

  public close(): void {
    this.dialog.hidden = true;
    window.removeEventListener("keydown", this.handleKeyDown);

    // 5. Restore focus to original trigger element
    if (this.previousActiveElement) {
      this.previousActiveElement.focus();
    }
  }

  private handleKeyDown = (event: KeyboardEvent): void => {
    // Esc dismissal
    if (event.key === "Escape") {
      event.preventDefault();
      this.close();
      return;
    }

    // Tab key focus trap
    if (event.key === "Tab") {
      if (this.focusableElements.length === 0) return;

      const firstElement = this.focusableElements[0];
      const lastElement = this.focusableElements[this.focusableElements.length - 1];

      if (event.shiftKey) {
        // Shift + Tab moving backwards
        if (document.activeElement === firstElement) {
          event.preventDefault();
          lastElement.focus();
        }
      } else {
        // Tab moving forwards
        if (document.activeElement === lastElement) {
          event.preventDefault();
          firstElement.focus();
        }
      }
    }
  };
}
```

---

## 6. Form Errors & Accessible Feedback: `aria-describedby`

Never report form errors solely through red borders or disconnected toasts. Screen readers must announce error states inline:

```html
<div class="form-field">
  <label for="user-email" class="form-label">Work Email</label>
  <input 
    id="user-email"
    type="email" 
    class="form-input form-input--error"
    aria-invalid="true"
    aria-describedby="email-error-msg email-hint"
    value="invalid-email@"
    required
  >
  <p id="email-hint" class="form-hint">Enter your company domain email.</p>
  <p id="email-error-msg" class="form-error" role="alert">
    Please enter a complete email address including domain (e.g. name@company.com).
  </p>
</div>
```
