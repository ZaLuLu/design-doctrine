# 09. Cross-Platform Production Engineering Recipes

> "Design principles are useless if they cannot survive the transition into production code. These reference recipes provide drop-in, enterprise-grade implementations across Modern Web and Apple Native platforms."

---

## 1. Web / React Production Recipe: The Universal Command Palette (`Cmd + K`)

This production component integrates Hick’s Law, Miller’s chunking, Doherty’s 400ms threshold, APG accessibility contracts, and fluid keyboard acceleration.

```tsx
import React, { useState, useEffect, useRef } from 'react';

interface CommandItem {
  id: string;
  title: string;
  category: string;
  shortcut?: string;
  onSelect: () => void;
}

interface CommandPaletteProps {
  items: CommandItem[];
  isOpen: boolean;
  onClose: () => void;
}

export const CommandPalette: React.FC<CommandPaletteProps> = ({ items, isOpen, onClose }) => {
  const [query, setQuery] = useState('');
  const [selectedIndex, setSelectedIndex] = useState(0);
  const inputRef = useRef<HTMLInputElement>(null);
  const listRef = useRef<HTMLUListElement>(null);

  // Filter items dynamically based on search query
  const filtered = items.filter(item =>
    item.title.toLowerCase().includes(query.toLowerCase()) ||
    item.category.toLowerCase().includes(query.toLowerCase())
  );

  // Reset selected index on query change
  useEffect(() => {
    setSelectedIndex(0);
  }, [query]);

  // Focus input automatically on open
  useEffect(() => {
    if (isOpen) {
      setTimeout(() => inputRef.current?.focus(), 50);
    } else {
      setQuery('');
    }
  }, [isOpen]);

  // Handle global keyboard navigation (Up, Down, Enter, Esc)
  const handleKeyDown = (e: React.KeyboardEvent) => {
    if (e.key === 'ArrowDown') {
      e.preventDefault();
      setSelectedIndex(prev => (prev + 1 < filtered.length ? prev + 1 : 0));
    } else if (e.key === 'ArrowUp') {
      e.preventDefault();
      setSelectedIndex(prev => (prev - 1 >= 0 ? prev - 1 : filtered.length - 1));
    } else if (e.key === 'Enter') {
      e.preventDefault();
      if (filtered[selectedIndex]) {
        filtered[selectedIndex].onSelect();
        onClose();
      }
    } else if (e.key === 'Escape') {
      e.preventDefault();
      onClose();
    }
  };

  if (!isOpen) return null;

  return (
    <div 
      className="fixed inset-0 z-50 flex items-start justify-center pt-24 bg-black/40 backdrop-blur-md animate-fade-in"
      onClick={onClose}
    >
      <div 
        role="dialog"
        aria-modal="true"
        aria-label="Command Palette"
        className="w-full max-w-xl overflow-hidden rounded-2xl bg-neutral-900 border border-neutral-800 shadow-2xl animate-scale-up"
        onClick={e => e.stopPropagation()}
        onKeyDown={handleKeyDown}
      >
        <div className="flex items-center px-4 py-3 border-b border-neutral-800">
          <svg className="w-5 h-5 text-neutral-400 mr-3" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
          </svg>
          <input
            ref={inputRef}
            type="text"
            role="combobox"
            aria-expanded={filtered.length > 0}
            aria-autocomplete="list"
            aria-controls="command-results"
            placeholder="Type a command or search..."
            value={query}
            onChange={e => setQuery(e.target.value)}
            className="w-full bg-transparent text-white text-base focus:outline-none placeholder:text-neutral-500"
          />
          <kbd className="px-2 py-0.5 text-xs text-neutral-400 bg-neutral-800 rounded font-mono">ESC</kbd>
        </div>

        <ul 
          id="command-results" 
          ref={listRef}
          role="listbox" 
          className="max-h-80 overflow-y-auto p-2 space-y-1"
        >
          {filtered.length === 0 ? (
            <li className="px-4 py-6 text-sm text-center text-neutral-500">No matching commands found.</li>
          ) : (
            filtered.map((item, index) => {
              const isSelected = index === selectedIndex;
              return (
                <li
                  key={item.id}
                  id={`command-item-${item.id}`}
                  role="option"
                  aria-selected={isSelected}
                  onClick={() => {
                    item.onSelect();
                    onClose();
                  }}
                  onMouseEnter={() => setSelectedIndex(index)}
                  className={`flex items-center justify-between px-3 py-2.5 rounded-xl cursor-pointer text-sm transition-colors ${
                    isSelected ? 'bg-blue-600 text-white' : 'text-neutral-300 hover:bg-neutral-800/60'
                  }`}
                >
                  <div className="flex items-center gap-3">
                    <span className="text-xs uppercase tracking-wider text-neutral-400 font-semibold">{item.category}</span>
                    <span className="font-medium">{item.title}</span>
                  </div>
                  {item.shortcut && (
                    <kbd className={`text-xs px-2 py-0.5 rounded font-mono ${isSelected ? 'bg-blue-700 text-blue-100' : 'bg-neutral-800 text-neutral-400'}`}>
                      {item.shortcut}
                    </kbd>
                  )}
                </li>
              );
            })
          )}
        </ul>
      </div>
    </div>
  );
};
```

---

## 2. Apple Native SwiftUI Recipe: Interactive Spring Bottom Sheet

This native SwiftUI component implements Apple HIG fluid gesture interaction:
* Interruptible pan gestures.
* Momentum preservation on release with initial velocity.
* Asymptotic rubber-banding when pulled past top boundaries.
* Native system haptic feedback.

```swift
import SwiftUI

public struct FluidBottomSheet<Content: View>: View {
    @Binding var isPresented: Bool
    let content: Content
    
    @GestureState private var translation: CGFloat = 0
    @State private var sheetOffset: CGFloat = 0
    
    private let sheetHeight: CGFloat = 520
    private let threshold: CGFloat = 140
    
    public init(isPresented: Binding<Bool>, @ViewBuilder content: () -> Content) {
        self._isPresented = isPresented
        self.content = content()
    }
    
    public var body: some View {
        GeometryReader { proxy in
            ZStack(alignment: .bottom) {
                if isPresented {
                    // Dimmed backdrop with smooth fade
                    Color.black.opacity(0.4)
                        .ignoresSafeArea()
                        .transition(.opacity)
                        .onTapGesture {
                            dismissWithSpring(velocity: 0)
                        }
                    
                    // The Fluid Sheet Surface
                    VStack(spacing: 0) {
                        // Grab Handle
                        Capsule()
                            .fill(Color.secondary.opacity(0.35))
                            .frame(width: 38, height: 5)
                            .padding(.top, 10)
                            .padding(.bottom, 16)
                        
                        content
                            .frame(maxWidth: .infinity, maxHeight: .infinity)
                    }
                    .frame(height: sheetHeight)
                    .background(
                        RoundedRectangle(cornerRadius: 28, style: .continuous)
                            .fill(.ultraThinMaterial)
                            .shadow(color: .black.opacity(0.12), radius: 24, x: 0, y: -4)
                    )
                    .offset(y: max(0, sheetOffset + translation))
                    .gesture(
                        DragGesture()
                            .updating($translation) { value, state, _ in
                                let dragY = value.translation.height
                                if dragY < 0 {
                                    // Rubber band formula for upward pull
                                    let c: CGFloat = 0.55
                                    state = (dragY * sheetHeight * c) / (sheetHeight - c * dragY)
                                } else {
                                    state = dragY
                                }
                            }
                            .onEnded { value in
                                let dragY = value.translation.height
                                let velocityY = value.velocity.height
                                
                                // Tactile haptic tick on drag threshold cross
                                UIImpactFeedbackGenerator(style: .light).impactOccurred()
                                
                                // Determine dismiss intent via position + predicted momentum
                                if dragY + (velocityY * 0.2) > threshold {
                                    dismissWithSpring(velocity: velocityY)
                                } else {
                                    snapBackWithSpring(velocity: velocityY)
                                }
                            }
                    )
                    .transition(.move(edge: .bottom))
                }
            }
            .animation(.interpolatingSpring(mass: 1.0, stiffness: 280, damping: 26), value: isPresented)
        }
    }
    
    private func dismissWithSpring(velocity: CGFloat) {
        withAnimation(.interpolatingSpring(mass: 1.0, stiffness: 260, damping: 28, initialVelocity: velocity / 100)) {
            sheetOffset = sheetHeight
            isPresented = false
        }
        sheetOffset = 0
    }
    
    private func snapBackWithSpring(velocity: CGFloat) {
        withAnimation(.interpolatingSpring(mass: 1.0, stiffness: 320, damping: 24, initialVelocity: -velocity / 100)) {
            sheetOffset = 0
        }
    }
}
```

---

## 3. Web React Hook: `useSpringGesture` (Velocity & Inertia Conservation)

This hook allows any HTML element on the web to mimic Apple's native gesture fluidity using Pointer Events and `requestAnimationFrame`.

```typescript
import { useRef, useEffect } from 'react';

interface SpringGestureOptions {
  onDismiss?: () => void;
  dismissThreshold?: number;
}

export function useSpringGesture<T extends HTMLElement>(options: SpringGestureOptions = {}) {
  const ref = useRef<T>(null);
  const { onDismiss, dismissThreshold = 150 } = options;

  useEffect(() => {
    const el = ref.current;
    if (!el) return;

    let startY = 0;
    let currentY = 0;
    let lastTime = 0;
    let velocityY = 0;
    let isDragging = false;

    const onPointerDown = (e: PointerEvent) => {
      isDragging = true;
      startY = e.clientY;
      currentY = 0;
      lastTime = performance.now();
      velocityY = 0;
      el.setPointerCapture(e.pointerId);
      el.style.transition = 'none';
    };

    const onPointerMove = (e: PointerEvent) => {
      if (!isDragging) return;
      const now = performance.now();
      const dt = Math.max(now - lastTime, 1);
      const deltaY = e.clientY - startY;

      // Calculate instantaneous velocity in px/ms
      velocityY = (deltaY - currentY) / dt;
      currentY = deltaY;
      lastTime = now;

      if (currentY < 0) {
        // Rubber-band resistance
        const c = 0.55;
        const rubber = (currentY * 400 * c) / (400 - c * currentY);
        el.style.transform = `translate3d(0, ${rubber}px, 0)`;
      } else {
        el.style.transform = `translate3d(0, ${currentY}px, 0)`;
      }
    };

    const onPointerUp = (e: PointerEvent) => {
      if (!isDragging) return;
      isDragging = false;
      el.releasePointerCapture(e.pointerId);

      const predictedY = currentY + velocityY * 180;

      if (predictedY > dismissThreshold && onDismiss) {
        // Dismiss downwards
        el.style.transition = 'transform 320ms cubic-bezier(0.32, 0.72, 0, 1)';
        el.style.transform = 'translate3d(0, 100vh, 0)';
        setTimeout(onDismiss, 320);
      } else {
        // Snap back to origin with spring curve
        el.style.transition = 'transform 450ms cubic-bezier(0.175, 0.885, 0.32, 1.15)';
        el.style.transform = 'translate3d(0, 0px, 0)';
      }
    };

    el.addEventListener('pointerdown', onPointerDown);
    el.addEventListener('pointermove', onPointerMove);
    el.addEventListener('pointerup', onPointerUp);
    el.addEventListener('pointercancel', onPointerUp);

    return () => {
      el.removeEventListener('pointerdown', onPointerDown);
      el.removeEventListener('pointermove', onPointerMove);
      el.removeEventListener('pointerup', onPointerUp);
      el.removeEventListener('pointercancel', onPointerUp);
    };
  }, [onDismiss, dismissThreshold]);

  return ref;
}
```
