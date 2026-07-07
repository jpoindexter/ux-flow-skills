---
name: flow-app-shell
description: "Use when designing or reviewing an application's overall layout shell — deciding between sidebar+main, top-nav, three-pane, master-detail, dashboard grid, or mobile tab shell; sizing panels and content widths; or choosing what stays fixed vs scrolls. Also applies when a layout breaks on resize, content lines run too long, or the responsive collapse order is unclear."
---

# Application Layout Shells

## Overview
Almost every successful app uses one of ~6 proven shells; invention here is nearly always a regression. Pick the shell that matches the app's object model, then apply the standard sizes — users navigate on muscle memory built from Slack, GitHub, and Gmail.

## When to Use
- Scaffolding a new app's top-level layout before any feature screens
- Reviewing a layout that feels "off," breaks on resize, or scrolls the wrong regions
- Deciding panel widths, content max-widths, and fixed vs scrolling regions
- Planning how a desktop layout collapses to tablet and mobile
- Adding a detail/inspector panel to an existing list or canvas view
- Choosing between a modal and a routed page for a workflow
- **NOT for** choosing which nav pattern carries the destinations (see flow-navigation)
- **NOT for** single-purpose pages (login, marketing landing) — those are page layouts, not shells

## The Proven Patterns

1. **Sidebar + main** — the default SaaS/productivity shell.
   - Fixed left sidebar (nav, spaces, filters) + main content area holding one primary object or list at a time.
   - Wins when users switch contexts constantly: channels, projects, documents, views.
   - Optional right inspector panel appears on selection without displacing the main content's meaning.
   - Proven by Slack, Linear, Notion, Figma, Discord.
2. **Top-nav + content** — full-width header, content below, no persistent sidebar.
   - Wins when each page is self-contained and deep (a repo, a deployment, an article) and horizontal space matters more than persistent context switching.
   - Secondary nav becomes in-page tabs directly under the header (GitHub: Code / Issues / PRs).
   - Page content typically centers in a max-width column; the header stays full-bleed.
   - Proven by GitHub, Vercel, most docs and content sites.
3. **Three-pane** — nav rail/folder list → item list → detail.
   - The triage shell: high item volume, quick scanning, read-one-act-move-on.
   - The middle list is the workhorse; the detail pane updates in place on selection without navigation.
   - Keyboard ↑↓ (or j/k) moves the selection through the list while the detail follows.
   - Proven by Gmail/Outlook (folders → threads → message), Slack (channels → messages → thread panel), Apple Mail, Discord.
4. **Master-detail** — list + detail as one selection-driven unit.
   - Same DNA as three-pane minus the first rail. Selection in the list drives the detail pane.
   - On mobile this becomes push navigation: list screen → detail screen — never side-by-side squeezing.
   - Proven by iOS Settings split view, Linear issue list → peek, Intercom inboxes.
5. **Dashboard grid** — card grid of independent modules (metrics, charts, feeds).
   - Wins only for *monitoring*: glance, don't act. The moment users need to act on items, route them to a real list/table view.
   - Each card answers one question and links out; cards never scroll internally.
   - Proven by Stripe Dashboard home, Grafana, Mixpanel, Vercel analytics.
6. **Mobile tab-bar shell** — bottom tab bar + per-tab navigation stack, header per screen.
   - Each tab preserves its own stack and scroll position; modals slide up as sheets over the shell.
   - Proven by effectively every major iOS/Android app.

### Choosing a shell (decision sequence)
- Users triage many incoming items → three-pane.
- Users switch between named spaces/projects all day → sidebar + main.
- Each page is a deep self-contained object → top-nav + content.
- Users browse a list and inspect one item at a time → master-detail.
- Users glance at status without acting → dashboard grid.
- Mobile is the primary platform → tab-bar shell, and derive desktop from it, not vice versa.

### Responsive collapse order (desktop → mobile)
1. **≥1280px:** full shell — sidebar expanded, detail panel docked, comfortable density.
2. **1024–1279px:** sidebar collapses to icon rail (56–72px) or auto-hides; detail panel overlays instead of docking.
3. **768–1023px (tablet):** sidebar becomes a drawer (hamburger / edge swipe); three-pane drops to two panes (list + detail).
4. **<768px (mobile):** drawer → bottom tab bar for the 3–5 primary destinations; panes become pushed screens; detail is a full screen, not a panel.

The order is always sidebar → rail → drawer → tab bar. Never skip straight to hamburger-only on mobile when the app has clear primary modes.

### Fixed vs scrolling regions
- Header: fixed. Sidebar: fixed, scrolling internally if its content overflows. Main content: the only region that scrolls with the page by default.
- Each pane in a three-pane shell scrolls independently — list scroll must never move the detail pane.
- Never two sibling vertical scrollbars fighting inside the main area; one scroll container per visual pane.
- Fixed footers only for action bars tied to the current task (bulk actions, wizard next/back) — not for nav on desktop.
- Keyboard-driven lists keep the focused row in view; sticky headers must not cover the focused element (set scroll-margin-top to header height).
- Page background scroll is locked while a modal or sheet is open.
- Chat-style panes pin to the bottom and stay pinned on new content unless the user has scrolled up — then show a "jump to latest" affordance.

### Platform notes
- **iPad/tablet:** use the system split view (sidebar + content), not a scaled phone layout; sidebar overlays in portrait, docks in landscape.
- **Desktop (native/Electron):** windows resize to extremes — test 1024×640 and ultrawide; panels clamp to max widths, content column centers.
- **Web:** paint the shell before data — chrome renders immediately, content regions skeleton; never a full-page spinner.
- **Android:** respect top app bar + system navigation insets; drawers and bottom sheets follow Material 3 metrics.
- **Split-screen/multi-window:** the shell must survive 50% width on desktop OSes — that is the tablet breakpoint; reuse it.

## Layout & Structure
- **Header anatomy, left → right:** logo/app switcher → context (breadcrumb or workspace name) → global search (center or right-of-center) → create/primary action → notifications → help → avatar (always far right).
- Header height: 56–64px web/desktop; 44pt + safe area iOS; 64dp Android top app bar.
- **Sidebar:** 240–280px expanded; 56–72px icon rail; user-resizable in document tools (Notion, Figma) with min 200px / max ~400px, persisted per user.
- Sidebar internal order: search/quick-switch → pinned → primary nav → scrollable spaces list → settings pinned at bottom.
- **List pane (three-pane middle):** 320–400px. Row = title + 1–2 line preview + timestamp + unread/status indicator.
- List row heights: 40–56px — denser for pro tools, taller when rows carry previews or avatars.
- **Detail/inspector panel (right):** 320–400px docked; up to 480px when hosting forms. Below ~1280px viewport, switch it to an overlay so main content keeps a usable min-width.
- Main content min-width: ~480px for text views, ~640px for tables — below that, panels must overlay rather than dock.
- **Content max-widths:** prose 65–75ch (~640–720px) — wider measurably hurts reading; forms 480–640px; app content (tables, boards, canvases) 1200–1440px max or full-bleed with 24–32px gutters.
- Center the constrained column; never let a paragraph span a 1920px window.
- **Dashboard grid:** 12-column grid, 16–24px gutters; cards span 3/4/6/12 columns; min card width ~280px.
- Every dashboard card = title + one primary stat/visual + optional "View all" link to the full view.
- **Modals/sheets:** small dialog 400–480px; standard 560–640px; multi-step workflows get a routed page or full-screen takeover, never a mega-modal.
- Mobile: bottom sheets with detents (half/full) instead of centered dialogs; drag handle at top, swipe-down to dismiss.
- **Z-layers, bottom → top:** content → sticky headers → docked panels → dropdowns/popovers → modals/sheets → toasts. Toasts never sit under modals.
- Spacing: 8px base grid; region padding 16–24px; section gaps 24–32px.
- Total fixed chrome (all stacked bars) ≤ ~128px on desktop before content starts; on mobile, header + tab bar should leave ≥70% of the viewport for content.
- Empty main region (nothing selected in master-detail): show a neutral prompt ("Select a conversation") — never a blank pane.
- Resizable split panes: drag handles ≥8px hit area, double-click to reset, sizes persisted per user.
- Safe areas: respect notch/home-indicator insets on mobile; fixed bottom bars pad for the home indicator.
- **Full-screen takeover:** header reduces to title + close/back + primary action; used for composers and multi-step flows (Gmail mobile compose, Linear new-issue full view).
- **Banner slot** (offline, trial, incident): above the header, pushes content down, dismissible — never overlays nav or content.
- Print/export views drop all chrome — main content only.

## Quick Reference

| Situation | Shell |
|---|---|
| SaaS / productivity, frequent context switching | Sidebar + main (Slack, Linear, Notion) |
| Self-contained deep pages, wide content | Top-nav + content (GitHub) |
| High-volume triage (mail, messages, tickets) | Three-pane (Gmail, Slack) |
| Browse a list, inspect one item | Master-detail |
| Passive monitoring / metrics glance | Dashboard grid (Stripe home) |
| Mobile, 3–5 core modes | Tab-bar shell with per-tab stacks |
| Long-form reading surface | Centered column, 65–75ch |
| Sidebar at 1024px | Icon rail or auto-hide |
| Sidebar at <768px | Drawer, then tab bar for primaries |
| Edit-item-properties panel | Right inspector 320–400px; overlay below 1280px |
| Multi-step workflow UI | Routed page / full-screen takeover, not a modal |
| Quick confirm or single field | Small dialog 400–480px |
| Nothing selected in detail pane | Neutral "select an item" prompt |
| Chat/log pane | Bottom-pinned scroll + "jump to latest" |
| System-wide alert (offline, trial) | Banner above the header, pushes content down |
| Composer / create flow on mobile | Full-screen takeover: close + title + submit |
| App shell first paint | Chrome immediately; skeleton the content regions |
| iPad / 50%-width split screen | Tablet breakpoint layout: two panes, drawer sidebar |

## Anti-Patterns

| Anti-pattern | Why it breaks | Fix |
|---|---|---|
| Full-width text on wide monitors | 200+ char lines are unreadable; the eye loses the return sweep | Max 65–75ch for prose, centered |
| Whole page scrolls, including nav | Users lose orientation and pay a scroll tax to navigate | Fix header + sidebar; scroll main only |
| Nested scrollbars in one region | Scroll events land unpredictably; content gets trapped | One scroll container per visual pane |
| Novel shell "for differentiation" | Users spend their first week lost; muscle memory is a feature | Pick a proven shell; differentiate in content, not chrome |
| Dashboard cards containing full tables | Cards are for glancing; tables need width, sorting, density | Card shows the stat; "View all" links to a real table view |
| Internally scrolling dashboard cards | Hidden content + trapped scroll inside a glance surface | Card fits its content or links out |
| Detail panel squeezing main below usable width | Table/canvas becomes unusable while inspecting | Overlay the panel below 1280px; enforce a main min-width |
| Modal hosting a multi-step workflow | No URL, no back, lost work on accidental dismiss | Promote to a routed page or full-screen takeover |
| Desktop layout naively shrunk to mobile | 240px sidebar + panes at 375px = nothing usable | Follow the collapse order: rail → drawer → tabs; panes → pushed screens |
| Avatar/menu on the left, logo on the right | Fights every app users already know | Logo left, avatar far right — always |
| Two+ toolbars stacked above the content | Chrome eats the viewport; content starts below the fold | Merge bars; fixed chrome ≤ ~128px desktop |
| Sidebar with no collapsed state | Small-laptop users lose 280px permanently | Provide icon rail + remember the user's choice |
| Toast/notification under an open modal | Critical feedback invisible at the moment it matters | Toasts render at the top z-layer |
| Blank pane when nothing is selected | Looks broken; users don't know the pane exists for detail | Neutral prompt naming the action ("Select a message") |
| Full-page spinner on load | The whole app feels down while one query runs | Paint the shell; skeleton content regions |
| Alert banner overlaying the header | Hides nav exactly when users need it | Banner pushes content down instead |
| Ultrawide stretching every pane | Panels balloon; eye travel explodes | Clamp panel widths; center the content column |
