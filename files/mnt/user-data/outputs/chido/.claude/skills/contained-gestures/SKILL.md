# Skill: contained-gestures

Use this skill when implementing any drag, pan, swipe, or custom pointer interaction. Contained gestures prevent accidental text selection, cursor changes, and unintended element triggers during the gesture lifecycle.

Source: Rauno Freiberg — Devouring Details

---

## Core Principle

Gestures must be isolated. Dragging should never select text, change the cursor, or trigger surrounding elements. The gesture must feel native and predictable at all times.

---

## Containing a Gesture

When a drag starts, disable pointer events and user select globally for the duration of the gesture. Restore them when the gesture ends.

```css
/* Apply to body during active gesture */
.gesture-grabbing {
  cursor: grabbing;
  user-select: none;
  -webkit-user-select: none; /* Safari */

  * {
    pointer-events: none;
    user-select: none;
    -webkit-user-select: none;
  }
}
```

```ts
export const grab = {
  start: () => document.body.classList.add("gesture-grabbing"),
  end: () => document.body.classList.remove("gesture-grabbing"),
};

function onPanStart() {
  grab.start();
}

function onPanEnd() {
  grab.end();
}
```

---

## Touch Action

Always set `touch-action` on elements with custom touch interactions. Without it, the browser will try to scroll the page and conflict with your gesture.

```css
/* Disables all browser touch gestures */
.draggable {
  touch-action: none;
}

/* Disables vertical scroll only, allows horizontal */
.horizontal-drag {
  touch-action: pan-x;
}

/* Disables horizontal scroll only, allows vertical */
.vertical-drag {
  touch-action: pan-y;
}
```

Be specific — only disable what you need to.

---

## Gesture Conflicts (Drag vs Click)

A drag gesture sends a `pointerup` at the end, which also fires `click`. This causes unintended click actions when dragging.

Fix: track gesture state and cancel the click if movement was detected.

```ts
const state = useRef<"idle" | "press" | "drag" | "drag-end">("idle");

function onPointerDown() {
  state.current = "press";
}

function onPointerMove(e: PointerEvent) {
  if (state.current === "press") {
    state.current = "drag";
  }
}

function onPointerUp() {
  if (state.current === "drag") {
    state.current = "drag-end";
  }
}

function onClick(e: MouseEvent) {
  if (state.current === "drag-end") {
    e.preventDefault();
    e.stopPropagation();
    state.current = "idle";
  }
}
```

---

## Drag Threshold

Don't register a drag immediately. Require a threshold of movement before the drag starts. This gives grace to users who accidentally move their mouse a few pixels when pressing.

```ts
function onPointerMove(e: PointerEvent) {
  const threshold = 10; // pixels

  if (state.current === "press") {
    const dx = e.clientX - origin.current.x;
    const dy = e.clientY - origin.current.y;
    const distance = Math.sqrt(dx * dx + dy * dy);

    if (distance >= threshold) {
      state.current = "drag";
    }
  }
}
```

Adjust threshold based on target size. Small targets need smaller thresholds.

---

## Pointer Capture

When dragging from the edge of an element, the pointer can leave the target area and the drag stops. Fix with `setPointerCapture`.

```ts
function onPointerDown(e: PointerEvent) {
  ref.current?.setPointerCapture(e.pointerId);
}

function onPointerUp(e: PointerEvent) {
  ref.current?.releasePointerCapture(e.pointerId);
}
```

This keeps all pointer events bound to the element even if the pointer moves outside it.

---

## Rules

- Always contain gestures with `pointer-events: none` on body during drag
- Always set `touch-action` on custom touch targets
- Always implement drag threshold — usually 10px
- Handle drag vs click conflict with state machine
- Use `setPointerCapture` so edge drags don't break
- Use `@use-gesture/react` for complex gestures — it handles these edge cases automatically
