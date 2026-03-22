# Skill: motion-choreography

Use this skill when animating multiple elements, building transitions, or designing any interaction where more than one thing moves at once.

Source: Rauno Freiberg — Devouring Details

---

## Core Principle

Choreography is orchestrating when something happens in a sequence. Elements should not all move at once. Nature does not move in perfect concert — neither should your UI.

Walt Disney's principle: "Things don't come to a stop all at once. First there's one part, then another." Apply this to UI: primary elements move first, secondary elements follow.

---

## Staggering

Stagger delays the movement of elements based on their order in a group. Use it for lists, grids, and any collection of similar items entering the screen.

```tsx
{items.map((item, index) => (
  <motion.div
    key={index}
    transition={{
      delay: index * 0.05,
    }}
    initial={{ opacity: 0, y: 8, filter: "blur(4px)" }}
    animate={{ opacity: 1, y: 0, filter: "blur(0px)" }}
  />
))}
```

Staggering produces visual interest that feels almost like a hack for how little complexity it adds.

---

## Primary vs Secondary Elements

Sequence your animations so primary elements animate first, secondary elements follow with delay.

```tsx
// Primary — immediate
<motion.div animate={{ scale: 1 }} transition={{ type: "spring" }}>
  <MainContent />
</motion.div>

// Secondary — delayed, follows primary
<motion.div
  animate={{ opacity: 1 }}
  transition={{ delay: 0.4, duration: 0.3 }}
>
  <Labels />
</motion.div>
```

The delay on secondary content should not feel like lag. If the primary element's transition is snappy enough, 300–400ms delay on supplementary content feels natural.

---

## Avoiding Overlapping Layers

Layers that overlap during animation feel terrible — they create visual clutter, break rhythm, and confuse the user.

**Fix options:**
1. Speed up the exit animation — double stiffness on exit spring
2. Add `filter: blur(4px)` to the exiting element — letters/icons blend with surroundings
3. Stagger — use letter-level staggering to reduce overlap without adding lag

```tsx
// Faster exit
const exit = {
  opacity: 0,
  filter: "blur(4px)",
  transition: {
    ...transition,
    stiffness: transition.stiffness * 2,
  },
}

// Letter stagger to reduce overlap
.map((letter, index) => ({
  animate: {
    opacity: 1,
    filter: "blur(0px)",
    transition: { ...transition, delay: index * 0.01 }
  }
}))
```

---

## Morph Transitions

When morphing between two states (Dynamic Island-style), animate the inner container that holds the dynamic content — not the outer wrapper.

```tsx
// Wrong — jarringly re-centers contents
<motion.footer animate={{ width: bounds.width }}>
  <div ref={ref}>
    <Logo />
    <p>{active}</p>
  </div>
</motion.footer>

// Correct — smooth width push, contents stay put
<footer>
  <motion.div animate={{ width: bounds.width }}>
    <div ref={ref}>
      <Logo />
      <p>{active}</p>
    </div>
  </motion.div>
  <Menu>{email}</Menu>
</footer>
```

---

## Crossfading Icons

When crossfading between icons or elements with different shapes, scale + blur prevents harsh overlap.

Scale to 0.5 and blur by 7px on exit. Avoid scaling all the way to 0 or blurring by large values — too abrupt.

```tsx
// Exiting icon
exit={{ scale: 0.5, opacity: 0, filter: "blur(7px)" }}

// Entering icon  
initial={{ scale: 0.5, opacity: 0, filter: "blur(7px)" }}
animate={{ scale: 1, opacity: 1, filter: "blur(0px)" }}
```

---

## Choreographed Delay vs Immediate Response

Always respond to user input immediately with the primary animation. Use delay only for secondary content that appears after the primary transition completes.

Never delay the response to user input — only delay supplementary decoration.

---

## Rules

- Primary elements move first, secondary elements follow
- Never let overlapping layers linger — speed up exits or add blur
- Stagger produces disproportionate visual value for minimal complexity — use it freely
- Always animate the inner container in morph transitions, not the outer wrapper
- Crossfade between similarly-shaped elements when possible — round to round crossfades cleanly
- Blur is a powerful tool for cleaning up messy choreography — even 1–2px helps
