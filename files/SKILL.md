# Skill: simulating-physics

Use this skill when building any animated or interactive component. Apply physics-based principles to make interactions feel alive, natural, and grounded in the real world.

Source: Rauno Freiberg — Devouring Details

---

## Core Principle

Great interactions are inspired by physics and modeled after the natural world. The goal is not to animate everything with springs — it's to find the right physical metaphor for each interaction.

---

## Spring Motion

Use spring animations (via Framer Motion) for any rotation, translation, or scaling. Springs have physical properties that create more natural motion than fixed durations.

```tsx
// Base spring config — always tweak per interaction
const spring = {
  type: "spring",
  stiffness: 200,
  damping: 20,
}

// No bounce — for UI transitions, menus, panels
{ type: "spring", stiffness: 250, damping: 25 }

// Slight bounce — for swipe gestures with velocity
{ type: "spring", stiffness: 250, damping: 10 }

// Heavy/weighted — for elements with conceptual mass
{ type: "spring", stiffness: 100, damping: 20, mass: 1.5 }
```

**Never reuse the same spring config across different interactions.** Tune each one based on the size of the target and the nature of the interaction.

**Mental model:**
- Higher stiffness = faster movement
- Lower damping = more bounce/overshoot
- Mass = only use when the element should feel heavy (clipboard contents, large panels)

---

## When to Use Bounce

Only use bounce when the movement carries momentum. Ask: does this interaction have velocity?

- **Press/hover** — no bounce. These are mechanical. Use damping: 20+
- **Swipe/drag** — bounce feels right. Movement has velocity.
- **Drag-to-snap** — subtle bounce at the snap point
- **Playful UI** — bounce acceptable when the audience expects it

---

## Conceptual Weight

Pixels are weightless, but we can assign conceptual weight to control how an element moves.

Heavy elements (files, panels, drawers) should move with more friction than lighter ones (tooltips, badges). Apply this by increasing damping or adding mass.

Destructive actions should also feel heavy — apply more friction to prevent accidents. Use hold-to-confirm or slow-release patterns.

---

## Damping (Rubber Banding)

Use damping to communicate content bounds without hard stops. This keeps the interface responsive while indicating limits.

```ts
function dampen(
  val: number,
  [min, max]: [number, number],
  factor: number = 2
): number {
  if (val > max) {
    const extra = val - max;
    const dampenedExtra = extra > 0 ? Math.sqrt(extra) : -Math.sqrt(-extra);
    return max + dampenedExtra * factor;
  } else if (val < min) {
    const extra = val - min;
    const dampenedExtra = extra > 0 ? Math.sqrt(extra) : -Math.sqrt(-extra);
    return min + dampenedExtra * factor;
  } else {
    return val;
  }
}

// Usage in a drag handler
function onPan(_, { offset }) {
  const dampenedY = dampen(offset.y, [-max, max]);
  y.set(dampenedY);
}
```

---

## Projected Position (Swipe Velocity)

Swipe gestures should use velocity to determine where the element lands, not just the dragged distance.

```ts
// Deceleration rate from iOS UIScrollView
function project(initialVelocity: number, decelerationRate = 0.998) {
  return (initialVelocity / 1000) * decelerationRate / (1 - decelerationRate);
}

function onPanEnd(_, { offset, velocity }) {
  const projection = y.get() + project(velocity.y);
  const overshot = projection >= SNAP_DISTANCE;
  const targetY = overshot ? SNAPPED_Y : 0;
  y.set(targetY);
}
```

---

## Collision and Rebounding

When elements reach screen edges, rebound them instead of letting them fall off. This creates a sense of physicality and solves usability issues at the same time.

Apply the same pattern to floating UI elements, tooltips, and indicators that move with user input.

---

## Rules

- Never use `mass` in spring configs unless the element should feel conceptually heavy
- Always test springs by interrupting animations mid-way — they should retarget smoothly
- CSS `linear()` springs are not interruptible — always use Motion for interactive springs
- Scale elements down on press (not hover) to simulate physical pressure
- Bounce on press feels jarring — reserve bounce for velocity-based interactions
