# Skill: ergonomics-interactions

Use this skill when building any interactive UI element — buttons, inputs, forms, navigation, gestures, or touch targets. Ergonomics is about ease of use, not just visual appearance.

Source: Rauno Freiberg — Devouring Details

---

## Core Principle

Affordance tells you how to use something. Ergonomics tells you how easy it is to use. Both matter more than animation or visual polish. Build ergonomics first.

---

## Visual Depth for Interactivity

Lift interactive elements from the page with shadow and color. Elements that look "clickable" get clicked. Elements that blend into the background get missed.

Use a slightly lighter background for the page (`#282828`) and reserve pure black/white for interactive elements. This creates the depth contrast that signals interactivity.

```css
/* Page background — slightly lifted */
body {
  background: #f5f5f5; /* or equivalent dark: #282828 */
}

/* Interactive elements — pure, sits above */
.button {
  background: #000000;
}
```

---

## Forms: Let Users Submit and See Errors

Never disable submit buttons with tooltips. Instead, let users submit the faulty form and receive explicit field-level errors. It's far easier to find the broken field from an error state than to figure out why the button is disabled.

```tsx
// Wrong
<button disabled={!isValid} title="Fill in all required fields">
  Submit
</button>

// Correct
<button onClick={handleSubmit}>
  Submit
</button>
// Show validation errors after submit attempt
```

---

## Input Field Icons — Always Clickable

Icons inside input fields must not steal pointer events from the input. Wrap the input and icon together in a `<label>` so clicking the icon focuses the input.

```tsx
// Correct
<label className="input-wrapper focus-within:ring-2">
  <SearchIcon />
  <input className="outline-none" />
</label>
```

```css
label {
  display: flex;
  align-items: center;
  border: 1px solid var(--border);
  border-radius: var(--radius-md);
  padding: 8px 12px;

  &:focus-within {
    outline: 2px solid var(--ring);
    outline-offset: 2px;
  }

  input {
    outline: none;
    border: none;
    background: transparent;
    flex: 1;
  }
}
```

---

## Focus Rings

Never remove focus rings. They are a landmark for keyboard users.

Instead, customize them:

```css
.link {
  border-radius: 2px;
  outline: 2px solid var(--ring-color);
  outline-offset: 2px;
}
```

---

## Focus Management for Overlays

When opening a dialog, panel, or drawer, manually transfer focus to the first interactive element inside it. When closing, return focus to the trigger.

```tsx
if (open) {
  overlayRef.current?.focus();
} else {
  triggerRef.current?.focus();
}
```

Without this, keyboard users get stranded on elements that are no longer visible.

---

## Hit Area Expansion

Small interactive targets need larger invisible hit areas. Add padding via an `::after` pseudo-element so the clickable zone is bigger than the visual target.

```css
.small-button::after {
  content: "";
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 100%;
  height: 100%;
  padding: 12px;
  border-radius: 8px;
}
```

Use this for: icon buttons, close buttons, small toggles, drag handles.

---

## Gesture Discoverability on Touch

Always offer two ways to perform an action on touch screens: a visible button and a gesture shortcut. Beginners use the button, experienced users learn the gesture.

Never rely on gestures alone — they are invisible by default.

---

## Scroll Affordance

Use margins, fade edges, or visible overflow to indicate scrollable content.

```css
/* Shows that content continues */
.scroll-container {
  padding-left: 16px; /* creates visual entry point */
  overflow-x: auto;
}

/* Fade edge */
.scroll-container::after {
  content: "";
  position: sticky;
  right: 0;
  width: 40px;
  background: linear-gradient(to left, var(--bg), transparent);
}
```

---

## Rules

- Build affordance and ergonomics before animation
- Let users submit forms and receive errors — never disable with vague tooltips
- Always wrap input icons in `<label>` — clicking the icon must focus the field
- Never remove focus rings — customize them instead
- Always manage focus when opening and closing overlays
- Expand hit areas with `::after` for small targets
- Provide both visual button and gesture for touch discoverability
- Use margin or fade to indicate scrollable content
