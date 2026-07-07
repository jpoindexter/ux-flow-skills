---
name: flow-navigation
description: "Use when designing or reviewing an app's navigation architecture — choosing between tab bar, sidebar, top nav, or command palette; deciding menu depth vs breadth; or when users report getting lost, can't find features, or the back button behaves unexpectedly. Also applies when adding breadcrumbs, deep links, or restructuring an information hierarchy."
---

# Navigation Architecture

## Overview
Navigation is a promise about where things live. Pick the pattern that matches platform + app type, keep top-level destinations few and stable, and optimize for *scent of information* (each step visibly closer to the goal) — not raw click count.

## When to Use
- Choosing the primary nav pattern for a new app or a major new section
- Restructuring nav because features have outgrown the current menu
- Users report "I couldn't find X," dead-end back behavior, or broken shared links
- Adding secondary nav: breadcrumbs, in-page tabs, segmented views, command palette
- Deciding whether a new feature gets a top-level destination or nests under one
- Auditing an app where every team added its own entry point
- **NOT for** page-internal layout and panel arrangement (see flow-app-shell)
- **NOT for** marketing-page section anchors — that's page design, not navigation architecture

## The Proven Flow

1. **Mobile app → bottom tab bar, 3–5 items.**
   - Fixed and always visible; each tab is a top-level *mode* with independent state — switching tabs and returning preserves scroll position and navigation stack.
   - iOS HIG and Material Design both cap at 5. Exceeding 5 means the IA is wrong, not that you need a "More" tab — buried tabs are effectively deleted features.
   - Center tab may be a primary-action button (Instagram's create) only when creation is the app's core loop.
   - Tapping the active tab pops the stack to the tab root; tapping again (already at root) scrolls to top (UIKit default; Instagram/X convention).
   - Hide the tab bar only inside deep focused tasks (full-screen media, checkout); restore it on return.
   - Proven by Instagram, Spotify, iOS App Store, YouTube.
2. **Desktop productivity app → left sidebar.**
   - Persistent vertical list of workspaces/projects/views, 240–280px, collapsible to an icon rail.
   - Sidebar holds *nouns* (places to go); the header holds *verbs* (actions to take). Mixing them destroys scanability.
   - Scales to arbitrary item counts (channels, projects, pages) — the reason chat and PM tools all converge here.
   - Unread/status badges live on sidebar items, right-aligned, count capped at "99+".
   - Proven by Slack (workspace rail + channel sidebar), Linear (teams → views), Notion (page tree), Discord.
3. **Marketing / content / docs site → horizontal top nav.**
   - 4–7 items; logo left and links home; primary CTA rightmost, visually distinct.
   - Dropdowns one level deep only. Mega-menu earns its place only at ≥3 categories × ≥4 items each (Amazon, Stripe).
   - Sticky-on-scroll is fine; shrink the bar, don't hide it — disappearing nav forces a scroll-up tax.
   - Docs add a persistent left tree + right "On this page" rail (Stripe, MDN).
4. **Deep hierarchy → drill-down (hierarchical) with breadcrumbs.**
   - Each screen shows one level; back goes up exactly one level. Used by Settings apps, file browsers, e-commerce categories.
   - Breadcrumbs on web from depth ≥3: `Home / Category / Subcategory / Current` — every ancestor clickable, current item plain text.
   - Never rely on the browser back button as the only way up — a deep-linked visitor has no history.
   - Proven by Amazon category tree, macOS Finder, GitHub file browser.
5. **Hub-and-spoke → task-based apps with independent flows.**
   - A home screen fans out to self-contained tasks that return home on completion (banking apps, iOS Settings root, airline check-in).
   - Spokes never cross-link to each other — users return to the hub between tasks, keeping the mental map trivially simple.
6. **Flat structure → small apps (≤ ~8 screens).**
   - Everything one tap from the tab bar or nav. Don't add hierarchy you don't need; hierarchy is a navigation cost, not a feature.
7. **Command palette (⌘K) → power-user accelerator, never primary nav.**
   - Add when the app has >20 destinations/actions and a keyboard-heavy audience.
   - Fuzzy-matches destinations, actions, and recent items; shows keyboard-shortcut hints inline next to actions.
   - Everything reachable in the palette must also be reachable by pointer — the palette duplicates nav, never replaces it.
   - Proven by Linear, Slack, Superhuman, VS Code, GitHub, Raycast.

### Secondary navigation (within a destination)
- In-page tabs for peer views of one object (GitHub repo: Code / Issues / PRs); underline style, active tab marked.
- Segmented control for 2–5 mutually exclusive renderings of the same data (list / board / timeline — Linear views).
- Anchor "On this page" jump nav for long documents — right rail on desktop, collapsed dropdown on mobile, highlighting the section in view.
- Contextual back ("← Back to results") only when the route can't express the parent; prefer real breadcrumbs.
- Nesting limit: primary nav + one secondary layer + one tab layer. Needing a fourth visible layer means the object model should be split.

### Depth vs breadth
- The "3-click rule" is a myth. Users tolerate 5+ clicks when every click has strong scent (visible progress toward the goal); they abandon at the first *low-scent* click, not the third.
- Prefer broad-and-shallow: 8 clear categories beats 3 vague ones nested 3 deep.
- Vague labels ("Solutions," "Resources," "Tools") kill scent faster than depth ever does.
- Max practical depth: 3 levels on mobile, 4 on web (with breadcrumbs as the safety rail).
- Test with first-click: when a user's first click is wrong, overall task success collapses — fix the top level before anything deeper.

### Back behavior (get this right or nothing else matters)
- **Web:** browser Back must always work and land where the user expects. Every navigational state change updates the URL (pushState).
- Overlays that feel like pages (photo lightbox, mobile filter sheet, full-screen modal) get a history entry so Back closes them instead of leaving the page.
- Transient UI (dropdown, tooltip, small dialog) gets NO history entry — Back should never just close a menu.
- **Android:** system back = up/close within the app; back from a non-start tab returns to the start tab, then exits (Material predictive-back).
- **iOS:** each tab keeps its own navigation stack; edge-swipe back must work everywhere push navigation is used — don't block it with custom gestures.
- Never trap users: after login or checkout, redirect (replaceState) so Back doesn't resubmit a form or bounce to a dead auth page.
- A custom in-app back button must agree with system/browser back or explicitly name its different target ("Back to results").

### Deep linking
- Every screen a user can reach deserves a stable URL/route — including filtered views and selected detail panes (`/issues?assignee=me`, `/inbox/1234`).
- A deep link into the middle of a hierarchy must render working up-navigation, synthesized from the route — never from session history the visitor doesn't have.
- Mobile: register universal links (iOS) / app links (Android) so shared URLs open the app at the right screen, with a web fallback for non-installers.
- Unauthenticated hit on a deep link: send to login, then return to the original destination — never dump the user at home.

## Layout & Structure
- **Tab bar (mobile):** 3–5 items; 49pt tall + safe area (iOS; iOS 26+: floating Liquid Glass bar, height adapts — 49pt is the docked-bar baseline), 80dp (Material 3); active state via color + filled icon variant.
- Tab items are icon + short label, always — icon-only fails recognition beyond ~5 universal glyphs (home, search, settings).
- Touch targets ≥44×44pt (iOS) / 48×48dp (Android) for every nav element.
- **Sidebar (desktop):** 240–280px expanded, 56–72px collapsed to icons; active item gets a filled background pill, not just a text-color change.
- Sidebar internal order: search/quick-switcher → pinned/favorites → primary destinations → scrollable spaces/projects → settings + help pinned at bottom.
- **Drawer (mobile/tablet):** 280–320px (0–40px wider than the 240–280px docked sidebar it mirrors) over a scrim; hamburger + edge-swipe to open; scrim-tap, swipe, or Esc to close; contents mirror the desktop sidebar exactly.
- **Top nav:** 56–64px tall; logo left → primary items → search → notifications → avatar far right. Max 7 top-level items before consolidating.
- **Mega-menu:** full-width panel; one column per category with a heading that is itself a link + 4–8 child links; opens on click (never hover-only); closes on Esc and outside click.
- **Breadcrumbs:** directly under the header, above the page title; `/` or `›` separators; truncate middle segments on narrow screens (`Home / … / Current`).
- **In-page tabs:** never mix in-page tabs and a second sidebar level for the same choice — one mechanism per decision.
- **Command palette:** centered overlay, 560–640px wide; opens on ⌘K/Ctrl+K; input on top; grouped results (Recent / Actions / Navigation); ↑↓ + Enter, Esc closes; shortcut hints right-aligned per row.
- **Quick switcher:** productivity apps also bind ⌘P/⌘T for "jump to item" — same overlay, pre-scoped to documents/issues (VS Code, Slack).
- **Mobile headers:** title centered (iOS) or left-aligned (Android); back chevron top-left; max 2 action icons top-right.
- Nav labels: 1–2 words; nouns for places, verbs for actions; words users would say aloud. A label that needs a tooltip needs rewriting.
- Active-state marking is mandatory at every nav level — users answer "where am I?" from the chrome, not from memory.
- Badge counts only for actionable, user-specific items (unread, pending review); cap at "99+"; never marketing badges ("New!") on more than one item at a time.
- Persist nav state per user: sidebar collapsed/expanded, last-active tab, expanded tree nodes.
- Keyboard: web nav tabbable in visual order with visible focus rings; skip-to-content link as the first tab stop.
- **Sidebar section headers:** small-caps labels, optionally collapsible; required past ~10 items — a wall of ungrouped items kills scanning.
- **Long nav lists** (channels, projects): inline filter-as-you-type at the top of the list, never pagination inside nav.
- Wayfinding redundancy: the page title must match the nav label that got the user there — mismatched wording reads as a wrong turn.

## Quick Reference

| Situation | Pattern |
|---|---|
| Mobile app, 3–5 core modes | Bottom tab bar |
| Mobile app, >5 destinations | Fix the IA to ≤5; overflow lives inside a tab, not a "More" tab |
| Desktop productivity / SaaS tool | Left sidebar (collapsible to icon rail) |
| Marketing, docs, content site | Top nav, ≤7 items |
| E-commerce / large catalog | Top nav + mega-menu + breadcrumbs |
| Task-based app (banking, kiosk) | Hub-and-spoke |
| Small app, ≤8 screens | Flat |
| Power users, >20 destinations | ⌘K command palette as accelerator |
| Jump-to-item for power users | ⌘P/⌘T quick switcher |
| User lands mid-hierarchy via link | Breadcrumbs synthesized from route |
| Peer views of one object | In-page tabs |
| 2–5 renderings of the same data | Segmented control (list/board/timeline) |
| Long document or settings page | "On this page" anchor nav |
| Feature used by <5% of users | Nest it; no top-level slot |
| Deep focused task (checkout, player) | Hide nav chrome; restore on exit |
| "Where does this new feature go?" | Under the existing noun it belongs to — new top-level slots are a last resort |

## Anti-Patterns

| Anti-pattern | Why it breaks | Fix |
|---|---|---|
| Hamburger menu as primary mobile nav | Hides destinations; engagement on hidden items drops sharply | Bottom tab bar for top 3–5; hamburger only for rare overflow |
| >5 tabs or a "More" tab | Buried tabs are effectively deleted features | Merge or re-rank destinations; overflow inside a tab |
| Icon-only nav without labels | Users can't decode icons beyond home/search/settings | Always pair icon + label in primary nav |
| Nav items that change per page | Users lose the map; every page feels like a new app | Identical top-level nav everywhere; mark active state |
| SPA navigation without URL updates | Back breaks, links unshareable, refresh loses state | Route every navigational state; pushState on view change |
| Modal-as-page without a history entry | Back skips past it or closes the whole flow | History entry for full-screen overlays only |
| Back closing a dropdown | Back becomes unpredictable; users stop trusting it | No history entries for transient UI |
| Hover-only dropdown menus | Fail on touch; flicker on diagonal mouse paths | Open on click/tap; close on Esc + outside click |
| Vague labels ("Solutions", "More", "Stuff") | Zero information scent; users click randomly | Concrete nouns users would say aloud |
| Command palette as the only path to a feature | Excludes pointer/touch users; undiscoverable | Palette duplicates nav, never replaces it |
| Current page as a breadcrumb link | Self-link confuses and wastes a click | Current crumb is plain text |
| Cross-linking between hub spokes | Users can't rebuild their location model | Return to hub between tasks |
| Badge counts on 3+ nav items at once | Alarm fatigue; users ignore all badges | Badge only actionable, user-specific counts |
| Custom back button disagreeing with history | Two back affordances point different ways | Mirror history, or name the target ("Back to results") |
| Login redirect dropping the deep link | User re-navigates manually; shared links feel broken | Preserve destination through auth, return after login |
| Four visible layers of nav chrome | Chrome outweighs content; layers indistinguishable | Cap at primary + secondary + tabs; split the object model |
| Nav label ≠ page title | Users think they mis-clicked and back out | Same words in the nav item and the page heading |
| Ungrouped sidebar past ~10 items | Wall of items; scanning collapses | Labeled section headers + inline filter |
