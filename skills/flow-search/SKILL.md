---
name: flow-search
description: "Use when designing or reviewing search or filtering — placing a search box, building typeahead/autocomplete, faceted filters with chips, a results page, or scoped/command-palette search. Also applies when users report zero-result dead ends, can't find items they know exist, filters that reset unexpectedly, or a catalog/list too large to browse."
---

# Search & Filtering Flows

## Overview
Search is a conversation: the user offers an imperfect query and the system must always move them forward — suggest while typing, tolerate typos, never answer with a dead end. Filters narrow; search finds; the proven products (Amazon, Airbnb, Google) combine both and keep every applied constraint visible and removable.

## When to Use
- Adding search to an app or catalog with more items than users will browse (>~50)
- Building typeahead/autocomplete, faceted filtering, or a results page
- Fixing "no results" dead ends, typo intolerance, or filter state loss
- Deciding instant vs submit search behavior, or adding scoped/⌘K search
- Users say "I searched for X and it's not there" about content that exists
- Reviewing a filter UI where users don't understand why results are limited
- **NOT for** picking one value from a short known set — that's a select/combobox
- **NOT for** navigation structure between destinations (see flow-navigation)

## The Proven Flow

1. **Search box.**
   - Magnifying-glass icon inside the field; placeholder states the scope ("Search orders…", never bare "Search").
   - Placement: header, center or right-of-center for search-centric products (Amazon, Airbnb, GitHub); local search sits directly above the list it filters.
   - Mobile: search as a tab-bar destination or a header icon expanding to a full-width field with autofocus + keyboard raised.
   - Web apps: `/` or ⌘K focuses search from anywhere.
   - Commerce mobile adds barcode/camera and voice entry inside the field (Amazon) — only when the catalog justifies it.
2. **Instant vs submit.**
   - **Instant (filter-as-you-type):** bounded local datasets — lists, tables, settings, sidebars. Debounce 150–300ms; results update in place; no Enter needed. Linear, Notion, iOS Settings.
   - **Submit (Enter → results page):** open-ended corpus search where a query has real cost and results deserve a dedicated page. Google, Amazon. Typeahead still fires while typing; Enter commits.
   - Hybrid is the norm for big products: instant suggestions, submitted results page.
3. **Typeahead anatomy** (dropdown under the field, in this order):
   - ① Recent searches — shown on focus before typing, each with its own × and a "clear all". Respect privacy: local-first, clearable.
   - ② Query suggestions/completions — matched substring **bolded**.
   - ③ Direct entity hits — icon/thumbnail + name + type label ("Nike Air Max — Product"); Enter on one goes straight to the entity, skipping the results page.
   - ④ "Search for '⟨query⟩'" as the last row and the Enter default.
   - Max ~8 rows; keyboard ↑↓ + Enter; Esc dismisses; keyboard selection wins until the mouse moves.
   - Suggestions must respond <100ms perceived — precompute or cache; a slow typeahead is worse than none.
   - Amazon and Airbnb prove this exact stack.
4. **Results page anatomy** (top → bottom):
   - Search box stays populated with the query, editable — search is iterative, users refine.
   - Result count + sort control right-aligned: "1–24 of 312 · Sort: Relevance". Alternatives: newest, price, rating — relevance is the default for queries, recency for browsing.
   - Filter rail (desktop, left 240–280px) or filter button with count badge (mobile).
   - Applied-filter chips row directly above results.
   - Results as list rows (documents/records: title + snippet with matched terms highlighted + metadata) or card grid (visual goods).
   - Load-more or pagination at the bottom — infinite scroll only for discovery feeds.
5. **Faceted filtering.**
   - Facets derive from result attributes, each value showing its count: "Brand · Nike (43)". Hide or disable zero-count values — they are guaranteed dead ends.
   - Applied filters render as chips — each removable with ×, plus "Clear all" once ≥2 active. Chips are the single source of truth for "why am I seeing this."
   - Desktop: results update immediately on facet change. Mobile: the filter sheet batches changes behind an explicit "Show 128 results" button so results don't shift under the sheet mid-selection — Airbnb's pattern.
   - The filter trigger carries a count badge ("Filters · 3") so hidden state is never invisible.
   - Multi-select within a facet = OR; across facets = AND. Ranges (price, date) get a dual-handle slider or min/max inputs.
   - Order facets by usage, not alphabet; the top 2–3 facets do most of the narrowing (category, price, availability).
   - Persist query + filters in the URL (`?q=boots&brand=nike&size=10`) — shareable, refresh-safe, back-button-correct.
6. **Zero-results recovery — never dead-end.** In order of preference:
   - **Fix it silently:** spell-correct and show corrected results — "Showing results for *sneakers*. Search instead for *snekers*" (Google). Fuzzy matching, stemming, and synonym expansion run before zero is ever shown.
   - **Broaden automatically:** relax the least-important constraint and say so — "No exact matches. Removing *color: green* shows 14 results" — or show partial matches labeled as such (Amazon).
   - **Offer exits:** name which applied filters are killing results with one-tap removal; suggest related/popular queries; link to browse categories.
   - The zero-results page always contains at least one tappable path forward.
   - Log zero-result queries — they are the top backlog for synonyms and content.
7. **Scoped search.**
   - Scope selector attached to the field (dropdown left of the input) or scope tabs on the results page: All / Docs / People / Issues — GitHub, Slack.
   - Default to the user's current context ("this channel", "this project"); widening to "everywhere" is one visible tap.
   - Always show which scope produced the results. If the current scope has 0 but a wider scope has hits, say so: "No matches in #design — 12 in all channels".
8. **Command palette as search (⌘K).**
   - Unifies navigation + actions + entity search in one input; same typeahead anatomy with groups Recent → Actions → Results-by-type.
   - Prefix operators scope it: `>` commands, `#` channels, `@` people (Slack / VS Code convention).
   - It complements the visible search box for power users; it never replaces it.

### Ranking & relevance defaults
- Rank: exact match > prefix match > fuzzy match; title/name hits above body hits; recent and popular items boosted.
- Personal scope first in app search: the user's own items outrank global items with an equal text score (Slack, Notion).
- Detect obvious intents: an order ID, email, or URL pasted into search jumps straight to the entity, skipping results.
- Recency vs relevance: time-sensitive corpora (news, messages) default to recency; catalogs and docs default to relevance.
- App-wide search groups results by type with counts and a "See all N" per group — Spotlight, Slack.

## Layout & Structure
- **Search field:** 40–48px tall in a desktop header, ≥44pt on mobile; expanded width 480–680px for search-first products; icon left inside the field.
- Clear-× appears as soon as text is entered — mandatory on touch, where clearing by backspace is punishing.
- **Typeahead dropdown:** matches field width (min 320px); max ~8 visible rows; distinct hover and keyboard-selection states; group headers labeled.
- **Filter rail (desktop):** 240–280px left column; facets as collapsible groups, most-used first; 5–7 values visible per facet + "Show more"; checkboxes with counts.
- Long facet lists (brands, tags) get their own mini search-within-facet input once values exceed ~15.
- **Mobile filter sheet:** full-height or 90% bottom sheet; facet list with inline expanders or per-facet screens; sticky footer with "Clear all" (left, secondary) + "Show N results" (right, primary).
- Sort lives in the same sheet or its own visible control — never buried two levels deep.
- **Chips row:** directly above results; wraps to max 2 lines then "+3 more"; chip = field + value + × ("Brand: Nike ×").
- **Results list row:** title (link-styled) + 1–2 line snippet with matched terms bolded + metadata line (date, type, path).
- Card grid for visual goods: image top, 2–4 columns, price/title below; consistent image aspect ratio per grid.
- **Result count** always visible — it's how users judge whether to narrow or broaden.
- **Latency budgets:** typeahead <100ms perceived; results <1s; show skeletons past ~300ms; never blank existing results while new ones load — replace on arrival.
- **Highlighting:** bold the matched substring in both suggestions and snippets; it is the user's primary relevance check.
- Empty-query search page (search tab opened, nothing typed): recent searches + trending/popular + browse categories — never a blank screen.
- Keyboard: full flow operable without a mouse — focus search (`/`), arrow through suggestions, Enter to commit, Tab into filters.
- Announce result-count changes to screen readers (aria-live polite) when filters update results in place.
- **Min query length:** instant search fires from 1 character for local lists, 2–3 characters for remote-backed search.
- **Did-you-mean row:** directly above results, link-styled correction, one tap to re-run — never below the fold.
- **Results scroll:** preserve position when results update in place; focus stays in the field while typing.
- **Applied chips on mobile:** horizontally scrollable row under the search bar, filter button pinned at its left.
- **Voice/barcode affordances (commerce mobile):** inside the field's right edge, placed before the clear-×, each ≥44pt.
- **Back to results:** returning from a result restores query, filters, sort, and scroll position — the single most common search rage point.

## Quick Reference

| Situation | Pattern |
|---|---|
| Bounded local list/table | Instant filter-as-you-type, debounced 150–300ms |
| Open-ended corpus | Typeahead + submit → results page |
| Field focused, nothing typed | Recent searches + popular queries |
| Search tab opened, no query | Recents + trending + browse categories |
| Likely typo | Auto-correct, "showing results for…" with undo link |
| Zero results | Correct → broaden → suggest; never a dead end |
| Result set too big | Faceted filters with counts + chips |
| Facet with >15 values | Search-within-facet input |
| Mobile filtering | Bottom sheet, batch-apply "Show N results", badged trigger |
| Search within a context | Scoped search; default current scope, one tap to widen |
| Current scope empty, wider scope has hits | Say so, with a one-tap scope switch |
| User types an exact entity name | Entity hit in typeahead, Enter goes direct |
| Power users, mixed nav/actions/search | ⌘K palette with prefix operators |
| Sharing/bookmarking a filtered view | Query + filters in the URL |
| Deciding what to fix next | Zero-result query logs |
| User pastes an ID / email / URL | Detect intent; jump straight to the entity |
| News / messages corpus | Default sort: recency |
| Product / docs catalog | Default sort: relevance |
| App-wide search across types | Grouped results + "See all N" per type |
| Remote-backed instant search | Fire at 2–3 chars, debounce 150–300ms |
| Returning from a result | Restore query, filters, and scroll position |

## Anti-Patterns

| Anti-pattern | Why it breaks | Fix |
|---|---|---|
| "No results found." full stop | Dead end; the user leaves | Correct, broaden, suggest — always ≥1 tappable exit |
| Exact-match-only search | Typos and plurals return zero for content that exists | Fuzzy match + stemming + synonyms before showing zero |
| Query cleared on the results page | User can't iterate on their own query | Keep the query in the box, editable |
| Filters hidden behind an unbadged icon | Users forget filters are active and distrust results | Chips row + count badge on the trigger |
| Mobile sheet applying every tap instantly | Results shift under the sheet; disorienting | Batch behind "Show N results" |
| Facet values with zero results selectable | A guaranteed dead end one tap away | Hide or disable zero-count values |
| Search state lost on back/refresh | Users rebuild query + filters repeatedly | Everything in the URL |
| Placeholder "Search" with no scope | Users can't predict what's searchable | Name the scope: "Search 12k products…" |
| No clear-× in the field | Clearing on touch takes a dozen backspaces | Show × whenever text is present |
| Typeahead >10 rows or unranked | Choice overload defeats the accelerator | ≤8 rows, best matches first, labeled groups |
| Slow (>300ms) typeahead | Suggestions arrive after the user has typed past them | Precompute/cache; drop typeahead if it can't be fast |
| ⌘K palette replacing the visible search box | Undiscoverable for the majority of users | Palette accelerates; the box stays |
| Blanking results while the next query loads | Users lose their comparison context | Keep stale results; swap on arrival |
| Alphabetical facet ordering | The 2–3 facets that matter sit below the fold | Order by narrowing power/usage |
| Ignoring zero-result query logs | The same failures repeat forever | Review logs; add synonyms/content for top misses |
| Back from a result losing the results page state | Users re-run the whole search per result | Restore query, filters, sort, scroll |
| Global items outranking the user's own | "My doc" buried under strangers' docs | Boost personal scope in app search |
| One flat list mixing people/docs/messages | Users can't scan across types | Group by type with counts |
| Remote search firing from character 1 | Wasted queries; flickering results | 2–3 char minimum + debounce |
