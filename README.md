# Chido

A designer starter kit for Claude Code + Next.js projects. Opinionated, minimal, and built for quality.

Chido gives you a CLAUDE.md configuration, design-focused skills, and interaction patterns so Claude Code produces production-grade UI from the start — not generic AI output.

## What's Inside

### CLAUDE.md

The core configuration file for Claude Code. Drop it into any Next.js project root.

**Defines:**
- Stack defaults (Next.js 15, React 19, TypeScript, Tailwind v4, Framer Motion, shadcn/ui)
- Architecture and folder structure conventions
- Core design rules (text wrapping, concentric radius, crispy text, interruptible animations, optical alignment)
- Typography system (Geist Sans, Geist Mono, IBM Plex Mono)
- Shadow-based depth system (shadows over borders)
- Component conventions (named exports, no default props, Tailwind only)

**Workflow guidance:**
- Plan Mode — think before you build
- Git worktrees for parallel Claude sessions
- Verification habits (UI → browser, logic → tests, data → bash)
- Notes directory for project decisions and gotchas
- PostToolUse hooks for auto-formatting
- Slash commands (`/simplify`, `/techdebt`, `/batch`, `/btw`, `/effort`, `/loop`)
- Prompting patterns that get the best output from Claude

### Skills (`.claude/skills/`)

Seven specialized skills that teach Claude how to build interactions the way a senior designer would.

| Skill | What It Does |
|-------|-------------|
| **ui-quality** | Jakub Antalík's UI quality patterns — the design checklist Claude runs before shipping any component |
| **simulating-physics** | Spring motion, conceptual weight, damping, rubber banding, velocity projection. Based on Rauno Freiberg's work |
| **motion-choreography** | Staggering, sequencing, morph transitions, crossfading, blur cleanup for multi-element animations |
| **responsive-interfaces** | Input→response loops, gesture lifecycle (start/move/end), interpolation, when to reduce animation |
| **contained-gestures** | Drag containment, pointer capture, touch-action, drag thresholds, gesture vs click conflict resolution |
| **ergonomics-interactions** | Hit area expansion, focus management, form ergonomics, scroll affordance, visual depth for interactivity |
| **code-snippets** | Grid stacking, intersection detection, custom dashed borders, transition listeners, direct DOM manipulation, generative avatars |

**Additional skills included:**
- **create-journey-map** — User journey mapping
- **affinity-mapping** — Cluster research and feedback
- **write-ux-copy** — UI copy and microcopy
- **create-component** — Build components using Chido rules
- **document-design-system-rule** — Capture design decisions
- **analyse-feedback** — Analyze user feedback
- **plan-workshop** — Plan design workshops

## Setup

1. Clone this repo:
   ```bash
   git clone https://github.com/helloluma/Chido.git
   ```

2. Copy `CLAUDE.md` into your project root:
   ```bash
   cp Chido/files/CLAUDE.md your-project/CLAUDE.md
   ```

3. Copy the skills into your `.claude/skills/` directory:
   ```bash
   cp -r Chido/files/mnt/user-data/outputs/chido/.claude/skills/ your-project/.claude/skills/
   ```

4. Also copy the standalone physics skill:
   ```bash
   cp Chido/files/SKILL.md your-project/.claude/skills/simulating-physics/SKILL.md
   ```

5. Start Claude Code in your project — it picks up the rules automatically.

## Stack

- Next.js 15 (App Router)
- React 19
- TypeScript
- Tailwind CSS v4
- Geist Sans + Geist Mono (via `geist` npm package)
- IBM Plex Mono (via Google Fonts or npm)
- Boxicons (via `boxicons` npm package)
- Framer Motion (for interruptible spring animations)
- shadcn/ui (component primitives via Radix UI)
- Vercel (deployment target)

## Core Design Rules

These are non-negotiable in every Chido project:

1. **Text wrapping** — `text-balance` on headings, `text-pretty` on body
2. **Concentric border radius** — outer = inner + padding, always
3. **Crispy text** — `antialiased` applied at body level
4. **Interruptible animations** — CSS transitions for interactions, never keyframes
5. **Optical alignment** — icons and buttons are eyeballed, not just geometrically centered
6. **Shadows over borders** — depth system uses shadows, not solid borders

## Model Recommendation

Use **Opus** for everything non-trivial. It's bigger and slower than Sonnet but requires less steering and is better at tool use — which makes it faster overall. Less back-and-forth = better results.

## Credits

Design patterns sourced from [Rauno Freiberg](https://rauno.me) and [Jakub Antalík](https://jakub.ai).

## License

MIT
