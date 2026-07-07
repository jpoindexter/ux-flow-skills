---
name: userflow
description: "Use when the user invokes /userflow, asks which UX flow or layout patterns apply to what they're building, or describes an app, feature, or screen to build or review and wants the right proven flow guidance pulled in automatically. Also use at the start of any UI build when it's unclear which flow-* skills are relevant."
---

# Userflow Dispatcher

## Overview
Entry point for the `flow-*` skill set. Reads what the user is building (from their words or the project itself), selects the most relevant flow skills, loads them, and applies their combined guidance — so the user never has to know skill names.

## When to Use
- `/userflow` invoked, with or without arguments
- User describes something to build/review ("add billing", "new mobile app", "our signup is leaking users") and flow guidance would help
- Starting any app surface when the relevant `flow-*` skills aren't obvious
- **NOT for** pure visual styling (color, type, spacing) — no flow skill covers that
- **NOT when** the user already named a specific `flow-*` skill — just invoke it directly

## The Dispatch Process
1. **Determine scope.** Use the argument text if given. If none, infer from conversation; if still unclear, inspect the project (routes, screens, package manifest) — don't interrogate the user. Ask at most one question, only if the answer changes skill selection.
2. **Select skills from the routing table below.** Pick 1–4. Prefer the fewest that cover the task; a whole-app request gets the App Bundle, a single screen gets 1–2.
3. **Load them.** Invoke each selected skill (Skill tool in Claude Code; read `skills/<name>/SKILL.md` in other harnesses). Never work from memory of a skill — load it.
4. **State the selection in one line** ("Applying flow-checkout + flow-forms") and do the work under their combined rules. On conflicts, the more specific skill wins (flow-checkout beats flow-forms inside a payment step).
5. **Check the output against each loaded skill's Anti-Patterns section** before presenting it.
6. **Generate the flow report.** For any request that designs or reviews one or more flows, emit a self-contained `flow-report.html` the user can open — see Flow Report below. Skip only for trivial single-question lookups.

## Flow Report
Fill `report-template.html` (in this skill's directory) — a dependency-free HTML file, one copy per run, written to the project root (or a path the user names):
- **Header:** project, date, one-line scope, chips for each skill applied.
- **Per flow:** numbered step cards (screen title, what's on it, primary action → destination, branch conditions), a screen-inventory table (purpose / primary action / empty-loading-error states), and an anti-pattern audit table with PASS/WARN/FAIL verdicts taken from the loaded skills' Anti-Patterns sections.
- **Footer sections:** open questions / parked items.
Duplicate the marked repeat-block per flow; delete unused placeholders — never ship `{{…}}` in output. Tell the user the file path when done.

## Routing Table

| Signal in task / project | Skills |
|---|---|
| Signup, login, SSO, passkeys, 2FA, password reset, "locked out", session expiry | flow-auth |
| First-run, welcome, activation, setup wizard, "users sign up but don't stick" | flow-onboarding (+ flow-empty-states) |
| Blank screen, no data yet, loading, skeletons, spinners, "feels slow" | flow-empty-states |
| Push/notification/location/camera permission, priming, "everyone denies the prompt" | flow-permissions |
| Cart, checkout, payment, buy, donate, order confirmation, abandonment | flow-checkout (+ flow-forms) |
| Pricing page, trial, subscription, upgrade/downgrade, cancel, paywall, dunning | flow-paywall |
| Settings, preferences, profile, account, delete account, change email/password, data export | flow-settings (+ flow-forms) |
| Share, invite, collaborators, permissions on objects, "who can see this", presence | flow-sharing |
| Nav structure, menus, tab bar vs sidebar, breadcrumbs, "users get lost", cmd+K | flow-navigation |
| Overall layout, shell, panels, sidebar widths, responsive collapse, "layout feels off" | flow-app-shell (+ flow-navigation) |
| Table, list, feed, grid of records, sorting, bulk actions, pagination vs infinite scroll | flow-tables |
| Search box, filters, facets, typeahead, zero results, "can't find things" | flow-search |
| Any form, wizard, multi-step, field validation, "form abandonment" | flow-forms |
| Errors, failures, offline, retries, undo, destructive actions, 404/500, lost work | flow-errors |
| AI chat, assistant, copilot, agent UI, streaming, tool-use display, citations | flow-ai-chat |

### Bundles (multi-skill requests)
| Request shape | Bundle |
|---|---|
| "New app" / greenfield product | flow-app-shell, flow-navigation, flow-onboarding, flow-auth |
| "Add billing/monetization" | flow-paywall, flow-checkout, flow-settings |
| "Admin panel / dashboard / back office" | flow-app-shell, flow-tables, flow-search |
| "Make it collaborative / add teams" | flow-sharing, flow-permissions, flow-settings |
| "UX audit / review the whole app" | Walk the routing table against each existing surface; report per-skill anti-pattern hits |
| "Add an AI feature" | flow-ai-chat, flow-empty-states, flow-errors |

## Quick Reference
| Situation | Action |
|---|---|
| `/userflow build a pricing page` | Load flow-paywall, build under it |
| `/userflow` with no args, mid-project | Infer surface from recent work; inspect repo if needed |
| Task matches nothing in the table | Say so — don't force-fit a skill |
| More than 4 skills match | It's a bundle request — use a bundle row or split the work |

## Anti-Patterns
| Mistake | Fix |
|---|---|
| Summarizing skills from memory instead of loading them | Always load the selected SKILL.md — the value is in the specifics |
| Loading all 15 "to be safe" | Max 4; selection is the job |
| Asking the user which skills they want | They invoked the dispatcher precisely to avoid that — route, then state the pick |
| Applying flow guidance to a styling-only request | Redirect to design/styling skills; flows ≠ visuals |
