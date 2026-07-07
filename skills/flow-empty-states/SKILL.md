---
name: flow-empty-states
description: "Use when designing or reviewing any screen that can show no data or is waiting on data — first-use empty states, cleared/zero-result states, error states, loading indicators, skeleton screens, spinners, or optimistic updates. Also use when users report the app 'feels slow', a screen appears blank or janky before content arrives, or a list/search/inbox has nothing to show."
---

# Empty States & Loading

## Overview
Every list, feed, table, and search result has four non-happy states — first-use, cleared, no-results, and error — and each needs a distinct design; a blank screen is a bug. Loading UX is about perceived speed: pick the indicator by expected wait time, not by habit.

## When to Use
- Building any view backed by a collection, query, or remote fetch
- Choosing between skeleton, spinner, progress bar, or optimistic update
- Reviewing "app feels slow" feedback or layout shift during load
- Auditing a design that only shows the populated happy path
- Handling offline, failed-fetch, and stale-data presentation
- **NOT for**: static content pages that can't be empty
- **NOT for**: animation curves/durations in general (motion skills) — this covers which state to show and when

## The Proven Flow

### The four empty states (design all four, distinctly)
1. **First-use empty (user never had data).** The highest-leverage empty state — it's onboarding real estate.
   - Contents, top to bottom: small illustration or icon (optional), one benefit-oriented headline ("Capture your first idea"), one line of explanation max, **one primary CTA that creates the first item** ("New note", "Import contacts", "Create project").
   - Optional secondary link: "Use a template" / "See an example".
   - Proven: Linear seeds sample issues; Notion offers templates; Airbnb's empty wishlist explains the heart icon and links to Explore; Dropbox's empty folder shows upload targets.
   - The CTA must perform the same action as the persistent create control in the chrome — two doors, one room. Users who learn the empty-state button must find the toolbar button identical.
2. **Cleared empty — split by how it emptied.**
   - **Completed-empty** (tasks/inbox — user finished everything): celebrate. "Inbox zero" is an achievement state: Gmail's sun graphic, Todoist's rotating zero-tasks illustrations, Superhuman's rotating wallpaper.
   - **Emptied-by-deletion** (docs/projects — user deleted everything): neutral confirmation + create action — no celebration, no beginner copy.
   - Either way, no "create your first X" copy — they know how; that copy now reads as condescending or, worse, implies their data vanished.
   - Keep the create action in its normal chrome location only.
3. **No-results empty (search/filter returned nothing).** Never a dead end.
   - Echo the query: "No results for 'invoce'".
   - Offer recovery in priority order: did-you-mean/near-match → "Clear filters" button showing active-filter count ("Clear 3 filters") → broaden-search suggestion → link to browse all.
   - Distinct art/copy from first-use — a filtered veteran user must never see beginner onboarding copy.
   - If partial matches exist in other categories, show them under a "Related" divider (Amazon, App Store pattern).
4. **Error-empty (fetch failed — data may exist, you couldn't get it).** MUST look unmistakably different from true-empty.
   - Say it failed; say why when known ("You're offline", "Server error"); give a **Retry** button as the primary action.
   - Preserve cached/stale data instead of blanking the view — stale-marked data beats an empty screen.
   - Showing first-use art on a failed fetch tells users their data is gone; this is a trust-destroying bug, not a styling nit.
   - Technical details (status code, request ID) behind a disclosure for support, never as the headline.

### State detection — get this right before designing

The four states require the code to distinguish them; a common bug is one `items.length === 0` branch serving all four:

1. Request in flight, no cache → **loading** (skeleton/spinner per thresholds below).
2. Request failed → **error-empty** (regardless of whether items would exist).
3. Request succeeded, zero items, no query/filter active, user has never created one → **first-use empty**.
4. Request succeeded, zero items, no query/filter, user previously had items → **cleared empty** (requires a "has ever had data" flag — persist it).
5. Request succeeded, zero items, query or filter active → **no-results empty**.

If you can't tell first-use from cleared (no history flag), default to the cleared state's neutral tone — beginner copy shown to a veteran is worse than the reverse.

### Loading, keyed to perceived-performance thresholds

The three canonical thresholds (Nielsen; unchanged since 1993 because they're human, not hardware):

| Wait | User perception | Required response |
|---|---|---|
| < 100ms | Instantaneous | Nothing — indicator would read as jank |
| 100ms–1s | Noticeable, flow intact | Inline feedback only; no full indicator |
| 1s–10s | Attention drifting | Real indicator (skeleton or spinner) or users assume breakage |
| > 10s | Attention gone | Determinate progress + cancel; background the job |

- **< 100ms** — Show nothing. Always acknowledge input instantly (pressed state) even if work continues.
- **100ms–1s** — Subtle inline affordance suffices: button label swaps to a small spinner, row dims, cursor changes. Keep the UI in place and interactive elsewhere.
- **1s–10s** — Choose the indicator by content shape:
  - **Skeleton screens** for content-shaped loads: feeds, cards, tables, profiles, dashboards (Facebook, LinkedIn, YouTube, Slack). Must match the final layout's exact geometry — zero shift when content lands.
  - **Spinners** only for small, shape-unknown, or non-content waits: submitting a form, opening a modal, refreshing a single control.
  - Rationale: a skeleton sets a concrete expectation of WHAT is coming; a spinner only says SOMETHING is happening. Spinners also invite time-watching; skeletons redirect attention to structure.
- **> 10s or unknown-long** — Determinate progress bar with percent or step names ("Uploading 3 of 12…"), estimated time when computable, cancel affordance. Genuinely long jobs: run in background, dismissible progress toast, notify on completion — never hold the user on a blocking screen.
- **Anti-flash rule**: delay showing any indicator ~150–300ms after the request starts; once shown, keep it visible ≥300–500ms. Kills the sub-second skeleton→content flicker that makes fast apps feel broken.
- **Progressive rendering order** within a screen: app chrome/nav instantly → skeletons → cached/stale data (marked if stale) → fresh data. Above-the-fold first; below-the-fold lazily.
- **Perceived > actual**: an app that renders chrome at 200ms and content at 2s feels faster than one that renders everything at 1.5s. Optimize the order of appearance before optimizing total latency.

### Optimistic UI (when the server almost never fails)
1. On user action, update the UI immediately as if it succeeded: like, favorite, todo-check, reorder, message send (Instagram hearts, Twitter/X likes, Trello card moves, iMessage, Linear status changes).
2. Fire the request in the background; queue and retry if offline.
3. On failure (rare): revert the UI change AND surface it — "Couldn't post · Retry". Never revert silently; a like that quietly disappears reads as a ghost bug.
4. Eligibility test — use optimistic UI only when ALL hold: success rate >99%, action is low-stakes, action is easily reversible or retryable.
5. Never optimistic: payments, destructive deletes without an undo window, sends with legal/irrevocable weight. For message sends where delivery matters, use a pending affordance (single grey check → double check) instead of full optimism.
6. Pair deletes with an undo toast (5–10s) rather than a confirm dialog — faster and safer than interrogating every action (Gmail, Slack).

## Layout & Structure

- **Empty-state block**: vertically centered in the content region, max-width 400–480px, center-aligned text; order: visual (≤~160px tall) → headline (≤8 words) → body (≤2 lines) → primary CTA. Exactly one primary CTA.
- **On mobile**: keep the CTA within thumb reach; if the empty region is short (a card, not a page), drop the illustration and keep headline + CTA only.
- **Skeletons**: grey blocks matching real content geometry — text lines at 60–90% width with the last line shorter, images as correct-aspect-ratio boxes, avatars as circles. One slow shimmer or pulse (~1–1.5s cycle) across the whole group, not per-element. Render 3–6 skeleton items, never a full page of 20. Static grey (no shimmer) under `prefers-reduced-motion`.
- **Reserve space for everything async**: images via aspect-ratio boxes, embeds, banners — cumulative layout shift is an empty-state failure, not just a performance metric.
- **Error-empty layout**: headline names the failure, body gives cause + next step, Retry as the primary button, "Contact support" or details-disclosure secondary.
- **Pagination/infinite scroll**: append skeleton rows at the loading edge; never blank the loaded list behind a page spinner. Failed page-load appends an inline "Couldn't load more · Retry" row.
- **Pull-to-refresh (mobile)**: system-standard indicator; keep existing content visible during refresh.
- **Accessibility**: loading regions get `aria-busy="true"`; async status changes announce via a polite live region ("Loading results… 12 results loaded"); spinners need accessible labels; empty-state CTAs are real buttons/links, not styled divs.
- **Copy**: empty-state headlines in plain language, no blame ("No results" not "You entered a bad query"); error copy states what happened + what to do, ≤2 sentences.

### Copy patterns

| State | Instead of | Write |
|---|---|---|
| First-use | "No projects found" | "Create your first project" + CTA |
| Completed-empty | "You have 0 tasks" | "All done for today" |
| Emptied-by-deletion | "All done for today" | "No documents" + New document |
| No-results | "No matches" | "No results for 'invoce' — check spelling or clear 3 filters" |
| Error | "Something went wrong" | "Couldn't load your projects — you're offline. Retry" |
| Long job | "Processing…" | "Importing 240 of 1,200 contacts — about 2 min left" |

## Quick Reference

| Situation | Pattern |
|---|---|
| New user, zero items ever | First-use empty: benefit headline + create CTA (+ template option) |
| User completed all items | Celebration state, no instructions |
| User deleted all items | Neutral confirmation + create action, no celebration |
| Search/filter → 0 hits | Echo query; did-you-mean; clear-filters with count; broaden |
| Fetch failed | Distinct error state + Retry; keep stale data visible |
| Wait < 100ms | No indicator; pressed-state feedback only |
| Wait 100ms–1s | Inline/button-level feedback only |
| Wait 1–10s, content-shaped | Skeleton matching final geometry |
| Wait 1–10s, small or unknown shape | Spinner, localized to the affected region |
| Wait > 10s / batch job | Determinate progress + step names + cancel; background if longer |
| Reversible low-stakes action | Optimistic update; revert-with-message on failure |
| Destructive action | Undo toast (5–10s) over confirm dialog |
| Paginated list loading more | Skeleton rows at the edge; inline retry row on failure |
| Offline with cached data | Show cache, mark stale, banner "You're offline", auto-retry on reconnect |
| Dashboard with several widgets | Per-widget skeleton + per-widget error; never one global state |
| Form submit | Button spinner + disable; keep the form rendered until success |
| Can't distinguish first-use from cleared | Persist a "has ever had data" flag; default to cleared tone |
| Image-heavy grid | Aspect-ratio placeholder boxes; blur-up or dominant-color fill |

## Anti-Patterns

| Anti-pattern | Fix |
|---|---|
| Same visual for true-empty and failed-fetch | Four distinct states; error always says "error" + Retry |
| Blank white screen while loading | Skeleton or spinner per the threshold table |
| Spinner for a full content page | Skeleton screen matching layout |
| Skeleton geometry ≠ final layout (content jumps) | Match dimensions exactly; reserve image space |
| Indicator flashes on an 80ms load | 150–300ms show-delay + 300–500ms minimum display |
| Full-screen blocking spinner for one widget | Localize loading; keep the rest interactive |
| "No data" with no action | Every empty state gets a next step |
| First-use empty that only describes the feature | Lead with the CTA that creates the first item |
| Beginner onboarding copy on a filtered no-results view | Distinct no-results state that echoes the query |
| Optimistic update reverting silently | Revert + visible "failed · Retry" message |
| Optimistic UI on payments/destructive actions | Pessimistic confirm, or undo window |
| Infinite indeterminate spinner on long jobs | Determinate progress, steps, cancel, background notice |
| Shimmer ignoring reduced-motion | Static placeholder under `prefers-reduced-motion` |
| Refresh that blanks existing content | Keep content visible; swap when fresh data arrives |
| Per-element desynchronized shimmer | One synchronized shimmer across the skeleton group |
| One `items.length === 0` branch for all four states | Distinct loading/error/first-use/cleared/no-results detection |
| Empty state hidden behind the fold on mobile | Center in the visible content region; drop the illustration if short |

Test by simulating all four: throttled network, forced request failure, empty database, and a 0-result search — every one must render an intentional, distinct screen.
