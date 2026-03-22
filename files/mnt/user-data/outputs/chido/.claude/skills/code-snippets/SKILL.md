# Skill: code-snippets

Reusable utility patterns for Chido projects. Reference these when building components.

Source: Rauno Freiberg — Devouring Details

---

## Grid Stacking (Overlap Without Absolute Positioning)

Stack elements on top of each other without relying on `position: absolute` + transform for centering. This keeps `transform` free for animations.

```css
.stack {
  display: grid;
  place-items: center;

  > * {
    grid-area: 1 / 1;
  }
}
```

Now you can animate `transform` without fighting the centering translate:

```css
/* Before — fighting transforms */
.item:hover {
  transform: translate(-50%, -50%) scale(0.96);
}

/* After — clean */
.item:hover {
  transform: scale(0.96);
}
```

---

## Intersection Detection

Detect whether two elements are overlapping. Useful for canvas tools, floating labels, or blur effects triggered by overlap.

```ts
export function areIntersecting(
  el1: HTMLElement,
  el2: HTMLElement,
  padding = 0
) {
  const rect1 = el1.getBoundingClientRect();
  const rect2 = el2.getBoundingClientRect();

  return !(
    rect1.right + padding < rect2.left ||
    rect1.left - padding > rect2.right ||
    rect1.bottom + padding < rect2.top ||
    rect1.top - padding > rect2.bottom
  );
}
```

---

## Custom Dashed Border (Linear Gradient)

CSS `border` can't control dash spacing. Use `background` with `linear-gradient` instead.

```css
.dashed-line {
  --color: var(--color-border);
  --size: 8px;

  &[data-direction="horizontal"] {
    width: 100%;
    height: 1px;
    background: linear-gradient(
      to right,
      var(--color),
      var(--color) 50%,
      transparent 0,
      transparent
    );
    background-size: var(--size) 1px;
  }
}
```

---

## Transition End Listener (One-Shot)

Wait for a CSS transition to finish before running the next action. Auto-cleans up after firing once.

```ts
function navigate(target: HTMLElement) {
  sidebarRef.current.addEventListener(
    "transitionend",
    () => target.scrollIntoView(),
    { once: true }
  );
  closeSidebar();
}
```

---

## Aesthetic Focus Rings

Consistent focus rings across the interface for keyboard navigation:

```css
:focus-visible {
  border-radius: 4px;
  outline: 2px solid var(--ring-color);
  outline-offset: 2px;
}
```

Apply holistically at the root level for consistency, then override per-component as needed.

---

## Direct DOM Manipulation for High-Frequency Updates

For mouse move or scroll interactions, update the DOM directly instead of triggering React re-renders. This prevents performance issues at 60fps.

```tsx
const ref = useRef<HTMLDivElement>(null);

function onMouseMove(e: MouseEvent) {
  if (!ref.current) return;
  ref.current.style.translate = `${e.clientX}px ${e.clientY}px`;
}

return (
  <div onMouseMove={onMouseMove}>
    <div ref={ref} />
  </div>
);
```

Use this for: cursor followers, parallax, any value updated on every frame.

---

## Generative Avatar Fallback

Hash a string to generate a unique gradient avatar when no profile image exists:

```tsx
const colors = ["#F6C750", "#E63525", "#050D4C", "#D4EBEE", "#74B06F"];

function generateGradient(seed: string) {
  const hash = seed.split("").reduce((acc, char) => acc + char.charCodeAt(0), 0);
  const color1 = colors[hash % colors.length];
  const color2 = colors[(hash + 2) % colors.length];
  const angle = hash % 360;
  return `linear-gradient(${angle}deg, ${color1}, ${color2})`;
}
```

---

## Rules

- Use grid stacking instead of absolute centering when you also need to animate transform
- `{ once: true }` on event listeners prevents memory leaks in transition/animation callbacks
- Direct DOM manipulation for anything updating at 60fps — skip React reconciliation
- Intersection detection is preferable to scroll position hacks for overlap detection
