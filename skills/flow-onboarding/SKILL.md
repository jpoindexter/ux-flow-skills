---
name: flow-onboarding
description: "Use when designing or reviewing onboarding, first-run, welcome, setup-wizard, or activation flows — new-user signup-to-first-value paths, personalization quizzes, product tours, or getting-started checklists. Also use when metrics show drop-off between signup and first key action, users report 'I signed up but didn't know what to do', or an app opens to a blank screen for new users."
---

# Onboarding & First-Run Flows

## Overview
Onboarding exists to get the user to their first moment of real value (activation) as fast as possible — everything that doesn't shorten time-to-value is a tax. Teach by doing inside the real product, not by telling in a tour.

## When to Use
- Building the screens between account creation and first meaningful use
- Adding a personalization quiz, setup wizard, or getting-started checklist
- Reviewing why activation rate is low or first-session churn is high
- Deciding what to ask upfront vs. defer (progressive profiling)
- Choosing between a product tour, contextual hints, or seeded content
- **NOT for**: returning-user login (see flow-auth) or feature announcements to existing users (changelog/tooltip territory)
- **NOT for**: zero-setup apps where first use IS the product (a calculator) — ship straight to the working screen

## The Proven Flow

1. **Define the activation moment first.** One sentence, before any screen: "Activated = user has ___ once."
   - Slack: sent a message. Duolingo: completed lesson 1. Linear: created and assigned an issue. Notion: edited a page.
   - Every subsequent screen either advances toward that moment or gets cut.
2. **Signup (minimal).** Email or SSO only.
   - No name, company, phone, or role yet — each extra field measurably costs conversion.
   - One field + one button (Slack: email → magic code; Notion: Google/Apple/email).
3. **Value-first inversion (use whenever feasible).** If the product can demonstrate value without an account, let users experience it BEFORE signup, then gate save/sync/continue behind account creation.
   - Duolingo runs the full placement quiz and first lesson pre-signup; signup converts because value is already proven.
   - Figma and Canva let you touch the canvas before registering.
   - This is the strongest known onboarding pattern — check feasibility before defaulting to signup-first.
4. **Value-framing screen (optional, max 1).** One sentence of what happens next.
   - Skip entirely if the next step is self-explanatory.
   - Never 3–5 swipeable marketing slides — users swipe blindly and retain nothing.
5. **Personalization quiz (only if answers change the product).**
   - 2–4 questions max, one question per screen, tap-to-advance on single-select.
   - Large tappable option cards, progress indicator, "Skip" visible on every step.
   - Each answer must visibly alter the experience: Duolingo (language + daily goal + level → lesson path), Headspace (goal → content plan), Linear (team size → workspace shape).
   - If answers only feed your CRM or segmentation, cut the quiz — users feel surveyed, not served.
   - Frame as the user's benefit ("What do you want to achieve?"), not your research. "How did you hear about us?" goes last and skippable, if at all.
6. **Templated / pre-filled first workspace.** Never land on empty.
   - Seed real, editable example content: Notion starter pages, Linear sample issues in a demo project, Trello example board, Airtable template gallery.
   - Sample content doubles as documentation — users learn structure by editing it.
   - Make cleanup one action ("Clear examples") so samples never pollute real data.
7. **Guided first key action.** One contextual prompt pointing at the single action that produces the activation moment.
   - A highlighted composer ("Send your first message"), a pulsing "+ New issue", a pre-focused input.
   - In-product, interactive, dismissible — not a modal tour of 8 features.
   - Teach ONE thing now; everything else is discoverable later or lives in the checklist.
8. **Getting-started checklist (persistent, dismissible).**
   - 3–5 items, verb-first ("Invite a teammate", "Connect your calendar").
   - First item pre-checked ("Create account ✓") — the endowed-progress effect measurably lifts completion.
   - Progress bar; collapses to a corner pill (web) or home card (mobile).
   - Each item deep-links to the exact UI location, never to a docs page.
   - Proven by Duolingo goals, Loom, Superhuman-style concierge lists, Notion's "Getting started".
9. **Deferred asks, triggered contextually after first value:**
   - Team invite → after the inviter has personally hit activation (B2B rule: never ask someone to vouch for a product they haven't used).
   - Notification permission → at a moment of demonstrated need (see flow-permissions).
   - Profile photo/name → first time it's visible to someone else.
   - Plan selection/paywall → after value, unless trial mechanics force it earlier.
10. **Persist onboarding state server-side.** Quiz answers, checklist progress, dismissed hints — a reinstall or second device must never restart onboarding.

### Which components does this product need?

Not every product needs every step. Pick by product shape:

| Product shape | Include | Skip |
|---|---|---|
| Consumer habit app (fitness, learning) | Value-first demo, quiz (goal + level), streak seed, reminder prime | Workspace templates, team invite |
| B2B collaboration tool | Seeded workspace, guided key action, checklist, deferred invite | Long quiz — infer from email domain |
| Pro creative/dev tool | Sample project + "edit this" prompt | Quiz, carousel, tour |
| Content/feed app | 1-screen interest picker (this IS the product config) | Wizard, checklist |
| Utility (single-purpose) | Nothing — open on the working screen | All of it |

### Sequencing rules

- Value before investment: every ask (fields, permissions, invites) sits AFTER the value it unlocks, never before.
- Cheapest ask first when multiple asks are unavoidable; the OS-permission ask is the most expensive (see flow-permissions).
- Personalization before workspace seeding — quiz answers should shape the seeded content.
- Checklist appears only after the first key action, not as the landing screen.

## Layout & Structure

- **One question / one decision per screen.** Multi-question quiz screens tank completion.
- **3–5 steps max** for any wizard; show a step indicator (dots or "2 of 4").
  - More than 5 required steps means the product needs redesign, not a longer wizard.
- **Primary CTA**: bottom of screen on mobile (thumb zone), full-width, ≥44pt (iOS) / 48dp (Android) tall; one primary button per screen.
- **Skip**: text link, top-right or directly below the CTA, lower visual weight.
  - Present on every quiz/personalization step; absent only on legally required steps.
  - Same tap-target size as the primary despite lighter styling.
- **Quiz options**: cards ≥56pt tall; max 6 options visible without scrolling.
  - Single-select advances on tap (no separate "Next"); multi-select gets an explicit "Continue".
- **Checklist**: docked bottom-right (web) or home-screen card (mobile).
  - Each row = checkbox state + verb label + chevron.
  - Completed rows stay visible (strikethrough/check) until full completion, then one celebration and auto-dismiss.
- **Progress & navigation**: any flow ≥3 steps shows progress; back navigation on every wizard step; answers persist through back/forward; state survives backgrounding and refresh.
- **Illustration/visual**: optional, ≤~40% of screen height; never push the CTA below the fold on a 667pt-class viewport.
- **Hints**: max 1 contextual hint visible at a time; dismiss-once, persisted.
- **Accessibility**: wizard steps announce position ("Step 2 of 4") to screen readers; quiz cards are real buttons with visible focus states; auto-advance on selection must also announce the transition; checklist progress exposed via `aria-valuenow`; skip links reachable by keyboard before the option grid; respect `prefers-reduced-motion` on step transitions and celebrations.

### Copy patterns

| Instead of | Write |
|---|---|
| "Configure notification preferences" | "Where should we send your daily reminder?" |
| "Welcome to Acme! Acme is a platform for…" | "Create your first project" |
| "Complete your profile" | "Add a photo so teammates recognize you" |
| "Step 3: Integrations" | "Connect Slack — get updates where you work" |
| "Tutorial" / "Take the tour" | "Try it: drag this card to Done" |

- Headings ≤12 words, second person, benefit-first. Body ≤2 lines per screen.

## Quick Reference

| Situation | Pattern |
|---|---|
| Can demo value without an account | Value-first: use it now, gate save/sync behind signup (Duolingo, Figma) |
| Product needs config to be useful | 2–4 question quiz, one per screen, skippable, answers alter the product |
| First screen would be empty | Seed templates/sample data (Notion, Linear, Trello) |
| Many features to teach | Teach ONE activation action now; checklist covers the rest |
| Need company/role/team-size data | Progressive profiling — ask later in context, or infer from email domain |
| Multi-step setup unavoidable | Wizard ≤5 steps, progress indicator, server-persisted state |
| User skipped onboarding | Checklist stays available; one contextual hint per area on first visit |
| B2B team product | Invite ask only after the inviter personally reaches activation |
| Consumer habit product | Daily-goal commitment question + streak seed (Duolingo) |
| Complex pro tool | Sample project + interactive "edit this" prompt, no tour |
| Returning user, new device | Restore state; zero onboarding screens |
| Measuring success | Activation rate + time-to-value; never tour completion |

## Anti-Patterns

| Anti-pattern | Why it breaks | Fix |
|---|---|---|
| 3–5 swipeable feature-tour slides before use | Users swipe blindly; retention ≈ zero | Delete; teach in-context at moment of relevance |
| Long signup form (name, company, phone, role) | Every field costs conversion before value exists | Email/SSO only; progressive profiling later |
| Modal tour with 6+ tooltips on first load | Blocks UI; dismissed unread; nobody acts on 6 things | One contextual prompt for the single key action |
| Quiz whose answers change nothing | Users feel surveyed; trust drops | Cut it, or wire every answer to a visible change |
| Landing on an empty workspace | "Now what?" → bounce | Templates, sample data, or creation-prompt empty state |
| Permission dialogs on first launch | Reflexive denial; permanent on iOS | Defer + prime contextually (see flow-permissions) |
| Mandatory video or unskippable steps | Hostage-taking; abandonment | Everything optional except legal requirements |
| Checklist with 10+ items | Reads as work, not progress | Cap at 5; reveal advanced tasks after basics |
| Celebrating account creation as "done" | User hasn't received value yet | Done = activation action completed once |
| Onboarding restarts on reinstall/second device | Forces quiz repeat; feels broken | Persist onboarding state server-side |
| "How did you hear about us?" as question 1 | Serves you, not them; sets a survey tone | Last position, skippable, or use attribution analytics |
| Invite-teammates before the inviter used the product | Asks users to vouch for unknown value | Move invite behind the inviter's own activation |
| Hints that reappear every session | Trains users to ignore all hints | Dismiss-once, persisted; one hint at a time |
| Full-screen confetti/celebration on trivial steps | Devalues real milestones | Celebrate once, at activation or checklist completion |
| Quiz answers ignored by the product | Broken promise; kills trust in future asks | Wire answers to visible defaults, or remove the question |
| Onboarding built as a separate mini-app | Diverges from real UI; teaches the wrong interface | Onboard inside the production screens with overlays/seeds |

Measure activation rate and time-to-value weekly; A/B any step removal — the winning variant is usually the shorter one.
