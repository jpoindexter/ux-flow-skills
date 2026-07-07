# ux-flow-skills

**Proven UX flows, user journeys, and app layout patterns — packaged as agent skills.**

15 reference skills that teach coding agents (Claude Code, Codex, Gemini CLI, Copilot, Cursor, DeepSeek, or anything that reads markdown) how apps *should* be laid out and how core flows *should* work — before they generate a generic bad one. Every pattern is proven in production by products like Stripe, Linear, Slack, Figma, GOV.UK, Duolingo, and Amazon, not invented.

## Why

LLMs produce plausible-but-wrong UX by default: password field on the first signup screen, permission prompts on launch, confirm dialogs instead of undo, five-step onboarding before any value. These skills encode the battle-tested versions of each flow so the agent reaches for the proven pattern instead of the statistical average.

## The Skills

| Skill | Covers |
|---|---|
| [`userflow`](skills/userflow/SKILL.md) | **Dispatcher** — `/userflow <what you're building>` picks and loads the right skills below |
| [`flow-onboarding`](skills/flow-onboarding/SKILL.md) | First-run, activation moments, progressive onboarding, checklists |
| [`flow-auth`](skills/flow-auth/SKILL.md) | Signup/login, magic links, passkeys, SSO, 2FA, password reset |
| [`flow-empty-states`](skills/flow-empty-states/SKILL.md) | Empty states as onboarding, skeletons vs spinners, perceived speed |
| [`flow-permissions`](skills/flow-permissions/SKILL.md) | Permission priming, just-in-time requests, notification hygiene |
| [`flow-checkout`](skills/flow-checkout/SKILL.md) | Cart → payment → confirmation, guest checkout, express pay |
| [`flow-paywall`](skills/flow-paywall/SKILL.md) | Pricing pages, trials, soft/hard paywalls, honest cancellation |
| [`flow-settings`](skills/flow-settings/SKILL.md) | Settings IA, account management, destructive actions, danger zones |
| [`flow-sharing`](skills/flow-sharing/SKILL.md) | Share links, permission levels, invites, collaboration cues |
| [`flow-navigation`](skills/flow-navigation/SKILL.md) | Tab bar vs sidebar vs top nav, depth vs breadth, command palettes |
| [`flow-app-shell`](skills/flow-app-shell/SKILL.md) | Sidebar+main, three-pane, master-detail, responsive collapse order |
| [`flow-tables`](skills/flow-tables/SKILL.md) | Data tables, lists, feeds, bulk actions, pagination vs infinite scroll |
| [`flow-search`](skills/flow-search/SKILL.md) | Search behavior, faceted filters, zero-results recovery |
| [`flow-forms`](skills/flow-forms/SKILL.md) | Field discipline, validation timing, wizards, save-and-resume |
| [`flow-errors`](skills/flow-errors/SKILL.md) | Error anatomy, undo over confirm, offline, retry, work preservation |
| [`flow-ai-chat`](skills/flow-ai-chat/SKILL.md) | AI/chat UX: streaming, tool-use display, citations, human-in-the-loop |

## Format

Each skill follows the [Agent Skills specification](https://agentskills.io/specification): a directory with a `SKILL.md` containing YAML frontmatter (`name`, `description`) and a dense markdown body — The Proven Flow, Layout & Structure, Quick Reference, Anti-Patterns.

```
skills/
  flow-auth/
    SKILL.md
  flow-checkout/
    SKILL.md
  ...
```

Because it's plain markdown with standard frontmatter, any agent that can read files can use these — skill-aware harnesses load them automatically; anything else can be pointed at a `SKILL.md` as context.

## Install

### Any agent (skills CLI)

```bash
npx skills add jpoindexter/ux-flow-skills
```

### Claude Code (global, all sessions)

```bash
git clone https://github.com/jpoindexter/ux-flow-skills.git
cd ux-flow-skills
for d in skills/*/; do ln -sfn "$(pwd)/$d" ~/.claude/skills/$(basename "$d"); done
```

Skills appear in the next session; invoke by name or let Claude pick them up when the task matches.

### Codex CLI

```bash
for d in skills/*/; do ln -sfn "$(pwd)/$d" ~/.agents/skills/$(basename "$d"); done
```

### Everything else (Cursor, DeepSeek, aider, custom harnesses)

Point the agent at the relevant `SKILL.md` as context, or paste it into your system prompt / rules file. The files are self-contained — no runtime, no dependencies.

## Usage

The skills are triggering-condition driven: each `description` says *when* it applies ("Use when designing or reviewing a signup flow…"). A skill-aware agent will pull the right one in when you ask it to build a login screen, a pricing page, a data table. You can also invoke explicitly:

> "Use flow-checkout and build the payment step."

**Or let the dispatcher route for you:**

> `/userflow build a pricing page` · `/userflow new mobile app for tracking workouts` · `/userflow audit our signup`

`userflow` picks the right skills (max 4), applies them, and **generates a `flow-report.html`** — a self-contained, dependency-free page documenting each flow: numbered step cards with primary actions and branches, a screen inventory (including empty/loading/error states), and an anti-pattern audit with PASS/WARN/FAIL verdicts. Open it in a browser, share it, or drop it in the repo as living flow documentation.

## Verification

Every skill was behavior-tested by independent agents: 3 retrieval questions each (45/45 answered unambiguously after fixes), an application test (spec a real screen using only the skill, checked against its own anti-patterns), and a factual audit with sourced corrections (platform rules, regulations, product citations verified as of mid-2026).

## Contributing

PRs welcome. Rules: patterns must be proven in shipped products (name them), descriptions state triggers only, max density, no filler, anti-patterns section required.

## License

[MIT](LICENSE)
