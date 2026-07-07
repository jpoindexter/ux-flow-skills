---
name: flow-errors
description: "Use when designing or reviewing error states, failure recovery, offline behavior, destructive-action protection, or 404/500 pages — or when users report lost work, confusing error messages, accidental deletions, or dead-end failures. Also fires when choosing between toast, banner, inline, or full-page error display, or wiring retry logic the user can see."
---

# Error Handling & Recovery Flows

## Overview
An error state is a fork where the product either keeps the user's trust and work, or loses both. Every error must answer three questions: what happened (in human terms), how to fix it, and where the user's work went — and the answer to the last one must be "nowhere, it's safe."

## When to Use
- Building any surface that can fail: forms, saves, payments, uploads, network calls, agent runs
- Adding delete, archive, cancel, overwrite, or send actions
- Designing offline or flaky-network behavior; building 404/500 pages
- **NOT for:** validation timing and error summaries inside forms — see flow-forms
- **NOT for:** logging/observability plumbing with no user-facing surface

## The Proven Flow

1. **Failure is caught; work is preserved first.** Before any message renders, the user's input and state are held: form values stay in their fields, drafts persist locally, uploads keep partial progress, unsaved edits go to local storage. Preservation precedes messaging — a perfect error message over lost work is still a catastrophe. Gmail keeps the draft through any send failure; Figma holds every offline edit.
2. **The message renders at the point of failure.** Inline next to the failed field or object if the error is local. Banner at the top of the affected region if it blocks the whole task. Toast only if the failure is non-blocking and self-explanatory. The message states human cause + concrete fix: "Couldn't save — you're offline. We'll retry automatically; your changes are stored on this device."
3. **A recovery action ships with every message.** Retry button, "Edit and resend," jump-link to the fixable field, or "Contact support" with the error reference pre-attached. An error without an affordance is a dead end, and dead ends convert to rage-refresh and churn.
4. **Retries are visible.** Automatic retry with backoff shows its state: "Retrying in 5s… · Retry now." A silent spinner during retries reads as a hang and triggers duplicate actions elsewhere. Cap visible auto-retries (~3 attempts), then hand control to the user: manual retry + persistent status.
5. **Success after failure confirms reconciliation.** "Back online — all changes saved" (Figma, Linear). The user must learn the queue flushed, not merely notice the warning banner disappeared. Brief, then gone.
6. **Destructive actions are protected before an error can exist.** Default: perform the action optimistically, then offer undo in a toast with a visible window (Gmail undo-send: 5–30s user-configurable). Confirmation dialogs only where undo is technically impossible — and then they name the consequence, never just "Are you sure?"
7. **Total failures get honest full pages.** Route missing → 404 with exits. Server down → 500 with a reference ID and a status link. Both keep global navigation so the user is lost on a page, not lost in the product.

## Layout & Structure

### Display-surface decision rules

| Surface | Use when | Rules |
|---|---|---|
| Inline (below field/object) | Error is local to one input or item | Red text + icon, 4–8px below the element; focus moves to first error on submit |
| Banner (top of page/region) | Blocks the current task; must persist | Stays until resolved or dismissed; contains the fix action; never auto-dismisses |
| Toast / snackbar | Non-blocking, informational, or undo-carrier | Auto-dismiss 5–10s; never the sole surface for an error requiring action; never for payment or permission failures |
| Full page | Nothing on this route can render (404, 500, no access) | Anatomy below; global nav intact |
| Modal | Almost never for errors | Only when the user must decide before anything else can proceed |

### Error message anatomy (every message, every surface)
1. **What went wrong**, in the user's vocabulary — name the object ("Couldn't upload invoice.pdf"), never the subsystem ("Request failed: 422").
2. **Why**, if known and useful ("The file is over the 25 MB limit").
3. **What to do next**, phrased as an action ("Compress it or choose a smaller file").
4. **Where their work is**, whenever work is at stake ("Your other 3 files uploaded fine").

Never: raw status codes as the headline; blame framing ("You entered an invalid…" → "We couldn't match that…"); ALL-CAPS or exclamation marks; "Oops!" cuteness on high-stakes failures. The error code belongs in small text for support reference, not in the headline.

### Copy examples: bad → good
| Bad | Good |
|---|---|
| "Error 422: Unprocessable entity" | "Couldn't save — that project name is already taken. Try a different name." |
| "Payment failed" | "Your card was declined by your bank. No charge was made. Try another card or contact your bank." |
| "Upload error" | "invoice.pdf is 32 MB — the limit is 25 MB. Compress it or split it into parts." |
| "Session expired" | "You were signed out after 30 minutes of inactivity. Sign in again — your entries are saved." |
| "Network error occurred" | "Can't reach the server — check your connection. We'll keep retrying; your draft is saved on this device." |
| "Permission denied" | "You need editor access to change this. Ask Maria (owner) or request access." |

### Severity → surface mapping
| Severity | Example | Surface |
|---|---|---|
| Field-level | Bad postcode | Inline below field |
| Task-blocking | Card declined, no permission | Persistent banner + fix action |
| Degraded service | One integration down, rest works | Dismissible banner, scoped to the affected feature |
| Non-blocking | One background sync retried fine | Toast, or nothing |
| Route-fatal | 404, 403 | Full page with exits |
| System-fatal | 500, outage | Full page + reference ID + status link |

### Full-page anatomy
- **404:** plain statement ("This page doesn't exist or has moved") + search box + links to the 3–5 most likely destinations + normal global nav. Never a bare joke with no exits.
- **500:** "It's a problem on our end, not yours" + auto-generated incident/reference ID + link to the status page + an explicit "your data is safe" statement when true + a retry action. GOV.UK's error pages are the canon: no branding jokes, maximum clarity, calm register.
- **403/permission:** state what's restricted and the path to access (request access button, who to ask) — not a bare "Forbidden."

### Destructive action protection (in order of preference)
1. **Undo beats confirm.** Execute immediately, show a toast with "Undo" for 5–30 seconds (Gmail undo-send; Linear's delete-issue undo). Applies to delete, archive, send, move, and bulk edits. Confirms interrupt everyone to protect against a rare mistake; undo protects only the mistaken, at the moment of mistake.
2. **Soft-delete / trash** with ~30-day recovery for containers: projects, boards, documents, files. Undo covers seconds; trash covers "realized on Tuesday."
3. **Confirm only when truly irreversible** (permanent purge, account deletion, prod deploy). The dialog names object and consequence: "Delete 'Q3 Report' and its 14 pages? This can't be undone." Generic "Are you sure?" trains blind clicking and carries zero information.
4. **Type-to-confirm** (repo name, the word DELETE) reserved for the highest tier — GitHub repo deletion. Overuse destroys the signal.
5. **Button mechanics:** destructive actions styled distinctly (red), never the default/focused action (Enter must not destroy), never placed adjacent to a frequent safe action.

### Offline handling
- **Detect and announce once:** a persistent, subtle indicator ("Offline — changes saved on this device"), not a blocking modal and not a re-toasting loop.
- **Queue mutations locally** with per-item pending state (Linear's offline queue). Persist the queue to local storage/DB — queued writes must survive tab close and app restart.
- **Keep reads alive from cache.** Never disable the whole app when cached content could render.
- **On reconnect:** sync automatically, confirm briefly ("Back online — synced"), and surface conflicts explicitly with a pick-a-version UI showing both versions — never silent last-write-wins.

### Partial failure
- When a batch half-succeeds, report per-item: "7 of 9 files uploaded · 2 failed — Retry failed items." Never collapse a batch into one merged spinner→error, and never re-run the successes on retry.
- Same rule for multi-step agent/import flows: name which steps completed; retry resumes, not restarts.

### Form data preservation
- Values survive server errors, session expiry, and navigation. Session-expired flow: keep the form rendered, re-authenticate in a modal or side flow, return to the intact form. Losing a long form to a login redirect is a top-3 rage trigger.
- On browser back / accidental close with unsaved changes: restore drafts on return (preferred) or warn before unload (fallback).

### Accessibility
- Blocking errors announce via `role="alert"` / `aria-live="assertive"`; toasts via `aria-live="polite"`.
- On failed submit, focus moves to the error summary or first errored field; never trap focus in an error state without an escape.
- Undo must be keyboard-reachable within its window; for critical undo, pair the toast with a persistent path (Edit menu, trash).
- Error state is never color-alone: icon + text alongside red.
- The offline indicator lives in the accessibility tree with a text label, not as a decorative dot.

### Metrics that prove recovery works
- Retry success rate (auto and manual) — low manual-retry success means the message promises a fix it can't deliver.
- Undo usage rate on destructive actions — near-zero may mean the window is too short to catch mistakes.
- Support tickets quoting error text — each one is a message that failed to self-serve.
- Rage-refresh / repeated-submit within 10s of an error — the signature of a dead-end error state.

## Quick Reference

| Situation | Pattern |
|---|---|
| One field failed | Inline error below field + move focus |
| Whole-task blocker (payment declined, permission) | Persistent banner with fix action |
| Non-blocking hiccup | Toast, auto-dismiss 5–10s |
| Delete / send / archive | Do it + undo toast (5–30s window) |
| Deleting a container (project, doc) | Soft-delete to trash, 30-day recovery |
| Truly irreversible destruction | Consequence-naming confirm; type-to-confirm at highest tier only |
| Network call failed | Auto-retry with visible countdown, cap ~3, then manual |
| Went offline | Persistent indicator + local queue + cached reads |
| Back online | Auto-sync + brief "synced" confirmation + explicit conflict UI |
| Batch partially failed | Per-item results + retry-failed-only |
| Server error on form submit | Preserve all values + banner with cause + retry |
| Session expired mid-form | Re-auth in place, return to intact form |
| Payment failed | Banner: bank's reason + "no charge was made" + try-another-card |
| Upload failed midway | Keep partial progress; resume, don't restart |
| Route not found | 404: statement + search + likely links + nav |
| Server down | 500: reference ID + status link + "data safe" + retry |

## Anti-Patterns

| Anti-pattern | Why it breaks | Fix |
|---|---|---|
| "Something went wrong" with no action | Dead end; only move is rage-refresh | Cause + fix + retry, always |
| Blocking error shown only in a 5s toast | Vanishes before it's read or acted on | Persistent inline/banner for anything requiring action |
| Confirm dialog on every delete | Trains blind confirmation; still loses data | Undo-first; confirms only for irreversible |
| "Are you sure?" with no object or consequence | Carries zero information | Name what's deleted and what's unrecoverable |
| Form cleared on server error | Destroys work to report a failure | Values survive every error class |
| Silent auto-retry forever | Looks frozen; user duplicates action elsewhere | Visible retry state, capped, then manual control |
| Raw exception / stack / status code shown | Meaningless and alarming | Human message; code in fine print for support |
| Offline = whole app disabled | Punishes reads cache could serve | Cached reads + queued writes + indicator |
| Queued offline writes lost on tab close | Betrayal discovered later | Persist the queue durably |
| Sync conflicts silently overwritten | Data loss found weeks later | Explicit both-versions conflict UI |
| Batch error as one blob | Can't tell what succeeded; retry duplicates | Per-item status, retry-failed-only |
| Red destructive button as default focus | Enter key destroys data | Safe action is default; destructive needs deliberate selection |
| Undo toast under 5 seconds | Gone before the mistake registers | 5–30s window; pair with trash for containers |
| Repeated failures spawn stacked toasts | Notification spam buries the actual problem | Coalesce into one updating banner with count |
| Error modal steals focus mid-typing | User's keystrokes hit the dialog's buttons | Non-modal surfaces for anything not requiring a decision |
| Same visual style for errors, warnings, promos | Users learn to dismiss everything red-ish | Reserve the error style for errors only |
| Joke-heavy 500 during an outage | Reads as mockery when work is lost | Plain, calm, reference ID, status link |

Proven by: GOV.UK Design System (error messages, error pages), Gmail (undo send, draft preservation), Figma (offline editing, reconnect sync), Linear (offline queue, optimistic updates + undo), GitHub (type-to-confirm tiering).
