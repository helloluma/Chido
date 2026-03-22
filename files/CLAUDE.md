# Chido

A designer starter kit for Next.js projects. Opinionated, minimal, and built for quality.

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

## Architecture

```
chido/
├── app/
│   ├── layout.tsx        # Geist wired, antialiased set globally
│   ├── page.tsx
│   └── globals.css       # base tokens, shadow system, motion, text-wrap
├── components/
│   ├── ui/               # Button, Menu, Card, etc.
│   └── icons/            # BoxIcon wrapper
├── lib/
│   └── utils.ts
├── .claude/
│   ├── skills/
│   │   ├── ui-quality/             # Jakub's UI patterns
│   │   ├── create-journey-map/     # User journey maps
│   │   ├── affinity-mapping/       # Cluster research and feedback
│   │   ├── write-ux-copy/          # UI copy and microcopy
│   │   ├── create-component/       # Build components with Chido rules
│   │   ├── document-design-system-rule/  # Capture design decisions
│   │   ├── analyse-feedback/       # Analyze user feedback
│   │   ├── plan-workshop/          # Plan design workshops
│   │   ├── simulating-physics/     # Spring motion, weight, damping
│   │   ├── motion-choreography/    # Stagger, sequence, overlap
│   │   ├── responsive-interfaces/  # Input→response, gesture lifecycle
│   │   ├── contained-gestures/     # Drag containment, pointer capture
│   │   ├── ergonomics-interactions/ # Hit areas, focus, affordance
│   │   └── code-snippets/          # Grid stacking, intersection, DOM perf
│   └── worktrees/                  # Git worktrees (gitignored)
└── public/
```

## Core Design Rules

These are non-negotiable in every Chido project. See `.claude/skills/ui-quality/SKILL.md` for full details.

1. **Text wrapping** — `text-balance` on headings, `text-pretty` on body
2. **Concentric border radius** — outer = inner + padding, always
3. **Crispy text** — `antialiased` applied at body level in layout.tsx
4. **Interruptible animations** — CSS transitions for interactions, never keyframes
5. **Optical alignment** — icons and buttons are eyeballed, not just geometrically centered

## Typography

- **Geist Sans** — primary UI font
- **Geist Mono** — code, data, technical UI
- **IBM Plex Mono** — secondary mono, editorial use

Always set `antialiased` on the body. Always use `tabular-nums` on any numeric UI that updates dynamically.

## Shadows over Borders

Chido uses a shadow-based depth system instead of solid borders. See `globals.css` for the token definitions.

## Conventions

- Components use named exports
- No default props — use TypeScript defaults
- Tailwind utility classes only — no inline styles
- Motion animations must be interruptible (spring or CSS transition)
- Concentric radius checked on every nested component before shipping

---

## Model

Use **Opus 4.5 with thinking** for everything non-trivial. It's bigger and slower than Sonnet but requires less steering and is better at tool use — which makes it faster overall. Less back-and-forth = better results.

---

## Git Worktrees — Parallel Sessions

Run multiple Claude sessions in parallel without conflicts. Each worktree gets its own branch and working directory.

```bash
# Start a session in an isolated worktree
claude --worktree feature-new-menu
claude --worktree bugfix-button-radius

# Worktrees live in .claude/worktrees/ (gitignored)
# No changes on exit = auto cleanup
# Changes on exit = Claude prompts to keep or remove
```

Set up shell aliases to hop between worktrees in one keystroke:

```bash
# Add to ~/.zshrc or ~/.bashrc
alias za="cd .claude/worktrees/a && claude"
alias zb="cd .claude/worktrees/b && claude"
alias zc="cd .claude/worktrees/c && claude"
```

Keep a dedicated **analysis worktree** just for reading logs — never build in it.

---

## Plan Mode — Think Before You Build

Start every non-trivial task in Plan Mode (shift+tab twice). Iterate on the plan with Claude until it's solid. Then switch to auto-accept and let Claude 1-shot the implementation.

**If things go sideways mid-build — switch back to Plan Mode and re-plan. Don't keep pushing.**

For complex tasks, spin up a second Claude and say:
> "Review this plan as a staff engineer. What's wrong with it?"

Then fix the plan before writing a single line of code.

---

## Verification — The Most Important Habit

Give Claude a way to verify its own work. This 2x to 3x the quality of the final result.

Verification options by task type:
- **UI changes** — have Claude open a browser and test it
- **Logic changes** — run the test suite
- **Data changes** — run a bash command to confirm the output
- **Component changes** — run `/skill ui-quality` and check the checklist

Never ship without a verification step. If you don't have one, build one.

---

## Notes Directory

Have Claude maintain a notes directory for every project or major feature. It gets updated after every PR.

```
.claude/
└── notes/
    ├── decisions.md     # Why things are the way they are
    ├── gotchas.md       # Things that tripped us up
    └── progress.md      # What's been done, what's next
```

Point CLAUDE.md at it:
```md
See .claude/notes/ for project decisions, gotchas, and progress.
```

---

## Hooks — Auto-Format on Every Save

Add a PostToolUse hook to auto-format Claude's code after every write or edit. Prevents CI failures from formatting inconsistencies.

Add to `.claude/settings.json`:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "npm run format || true"
          }
        ]
      }
    ]
  }
}
```

---

## Slash Commands

Commands Claude runs daily. All live in `.claude/commands/` and are committed to git.

| Command | What it does |
|---------|-------------|
| `/simplify` | Reviews changed code for duplication, quality, and efficiency. Then fixes it. |
| `/techdebt` | Run at end of every session. Finds and kills duplicated code and dead weight. |
| `/batch` | Run multiple tasks in one shot without separate prompts. |
| `/btw` | Log a note mid-session without interrupting the current flow. |
| `/effort` | Control how much compute Claude throws at a task. |
| `/loop` | Schedule a recurring task for up to 3 days. e.g. "babysit my PRs" or "summarize my notifications every morning." |
| `/skill ui-quality` | Invoke Chido UI quality rules on the current component or file. |

---

## Terminal Setup

- Use **Ghostty** — synchronized rendering, 24-bit color, proper unicode support
- Run `/statusline` to always show context usage and current git branch in your status bar
- Name and color-code your terminal tabs by worktree (1=feature, 2=bugfix, 3=analysis)
- Set up system notifications so you know when any Claude session needs input

---

## Keeping CLAUDE.md Sharp

After every correction or fix, end your prompt with:

> "Update your CLAUDE.md so you don't make that mistake again."

Claude is eerily good at writing rules for itself. Use that. Ruthlessly edit this file over time. Delete rules that no longer apply. Keep it under 200 lines or split into domain-specific files in `.claude/skills/`.

---

## Prompting Patterns

These are the patterns that get the best output. Use them.

**Plan before building**
Start in Plan Mode. Iterate the plan. Then 1-shot the implementation.

**Make Claude your reviewer**
"Grill me on these changes and don't make a PR until I pass your test."
"Prove to me this works — diff the behavior between main and this branch."

**Demand the elegant solution**
After a mediocre fix: "Knowing everything you know now, scrap this and implement the elegant solution."

**Use subagents**
Say "use subagents" to throw more compute at a problem. Offloads tasks and keeps your main context clean and focused.

**Reduce ambiguity before handing off**
Write detailed specs before asking Claude to build. The more specific you are, the better the output. Vague prompts produce vague code.

**Be unbiased and unhinged**
Challenge Claude. Push back. Ask it to prove its decisions. Don't accept the first answer if it feels mediocre. Claude can handle it.
