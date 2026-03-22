# Skill: responsive-interfaces

Use this skill when building any interactive component — buttons, menus, gestures, forms, data displays. Every piece of user input must be met with a proportional, appropriate response.

Source: Rauno Freiberg — Devouring Details

---

## Core Principle

Design is an asynchronous conversation. Every input expects a response. The response must be proportional and consider the interaction context. A jarring response is worse than no response at all.

---

## Input → Response Loop

Every user interaction should produce feedback. The absence of feedback makes the interface feel broken.

- Text caret blinks when you stop typing → system is still responsive
- Loading indicators → system understood and is processing
- Hover states → element is interactive
- Press states → input was received

---

## Reduce Responsiveness for High-Frequency Actions

For interactions performed hundreds of times a day (command menus, nav items, filters), reduce or eliminate animations. The novelty wears off after the third time, and the animation starts to feel like overhead.

**Never fade in menus that the user is about to immediately interact with.**

```css
/* Command menu — no fade in, this fires hundreds of times a day */
.command-menu {
  /* Just appear */
}

/* But still fade out — confirms the action completed */
.command-menu[data-state="closed"] {
  animation: fadeOut 100ms ease;
}
```

---

## Reduce Responsiveness When Brain Processes Faster Than Animation

If the user can read the screen faster than the animation runs, remove the animation. A slowly animating number on a tooltip is annoying when you're trying to read data quickly.

Test: can you read/act before the animation finishes? If yes, the animation is unnecessary overhead.

---

## Keyboard vs Mouse Input

Keyboard interactions often feel better without animation. Keyboard input is mechanical — less visceral than touch or mouse. Command menus, keyboard shortcuts, and list navigation rarely benefit from motion.

Mouse input is fluid and dynamic — it benefits from proportional motion responses.

Touch input must always have immediate feedback. The user is physically touching the screen — any lag feels physically wrong.

---

## Gesture Lifecycle: Start, Move, End

Always use all three gesture events for continuous interactions. Never wait until gesture end to give feedback.

```tsx
// Wrong — only responds on end
function onPanEnd(_, { offset }) {
  y.set(offset.y > SNAP ? SNAPPED_Y : 0);
}

// Correct — continuous feedback during gesture
function onPan(_, { offset }) {
  y.jump(clamp(y.get() + delta.y, [0, SNAPPED_Y]));
}

function onPanEnd(_, { offset }) {
  if (offset.y > SNAP_DISTANCE) {
    y.set(SNAPPED_Y);
  } else {
    y.set(0);
  }
}
```

---

## Interpolation

Interpolate secondary properties from the primary element's movement. This makes interactions feel more dynamic and connected.

```tsx
// Interpolate backdrop from sheet position
const blur = useTransform(y, [0, max], [12, 0]);
const opacity = useTransform(y, [0, max], [0.2, 0]);

// Interpolate multiple properties from scroll
const borderRadius = useTransform(scrollY, [0, 200], [0, 20]);
const width = useTransform(scrollY, [0, 200], ["100%", "90%"]);
```

Use this pattern for: backdrop blur on sheets, dock scaling on hover, scroll-based property changes.

---

## Sound

If using sound, apply tastefully:
- Different pitch for enter vs exit states
- Disable on mobile (will interrupt music)
- Only for moments of magnitude, not every interaction

---

## Exaggeration

Reserve exaggerated responses for special moments. Most interactions should be familiar and fast. A cancelation button should not fire confetti. An engineer fixing a production error doesn't want a gooey animation on the dropdown they're trying to open.

Consider interaction context. The same component may need different behavior in different contexts.

---

## click vs mousedown

Use `click` not `mousedown` for interactive elements. `mousedown` fires before the user releases — the action can't be cancelled. Users with ADHD or motor issues regularly press and drag off to cancel. Respect that.

```tsx
// Wrong — can't be cancelled
<button onMouseDown={handleAction}>

// Correct — user can cancel by dragging off
<button onClick={handleAction}>
```

Exception: use `mousedown` only to preload data for performance — but wait for `click` before actually triggering the action.

---

## Rules

- Every input must produce proportional feedback — silence feels broken
- Reduce animation for high-frequency interactions — the 100th time it's overhead
- Always use start/move/end gesture events — never wait until end for feedback
- Interpolate secondary properties from primary movement for depth
- Use `click` not `mousedown` — preserve the user's ability to cancel
- Keyboard actions often need no animation — mechanical input, mechanical response
- Touch must always have immediate response — physical contact demands it
