---
name: flow-tables
description: "Use when designing or reviewing any data table, list, or feed — choosing table vs list vs cards, adding sorting/filtering/bulk actions/pagination, or handling empty, loading, and error states. Also applies when users report tables being unusable on mobile, losing their place on refresh, or when a screen must display more than ~10 structured records."
---

# Data Tables, Lists & Feeds

## Overview
A table is a workspace, not a printout: users scan, sort, select, and act in place. The proven pattern is dense rows + progressive disclosure of actions + persistent user control — sort, filters, and column config survive refresh.

## When to Use
- Any screen displaying >10 structured records users must scan, compare, or act on
- Adding sort, filter, column config, bulk select, or inline editing to an existing table
- Choosing pagination vs infinite scroll vs load-more
- Designing empty, loading, and error states for data-heavy views
- Adapting a wide table for mobile
- Reviewing a table users describe as "cluttered," "slow," or "I lost my place"
- **NOT for** ≤5 items with no comparison need — use a simple list or cards
- **NOT for** search-results ranking and filter chips (see flow-search)

## The Proven Flow

1. **Choose the display form first.**
   - **Table** — many attributes per record, cross-column comparison matters, dense scanning. Airtable, Stripe payments, admin panels.
   - **List** — one primary attribute + metadata, vertical scan, per-row triage. Gmail threads, Linear issues, notification centers.
   - **Cards** — the visual IS the content (images, products, boards). Never cards for records users compare field-by-field — cards put fields in different positions and kill comparison.
   - **Feed** — reverse-chronological, append-only, consumed rather than managed. Activity logs, social timelines.
2. **Toolbar above the table.**
   - Search/filter input left → applied-filter chips → density/column/view controls right.
   - One row, sticky together with the header. Proven by Linear, Airtable, Stripe Dashboard.
3. **Sorting.**
   - Click a header to sort; click again to reverse; visible arrow on the active column only (arrows on every column hide the active one).
   - Default sort must be meaningful — newest first for activity, name for directories — never insertion order.
   - Multi-column sort is a power feature behind a menu, not the default interaction.
   - Persist the user's sort choice per view.
4. **Filtering.**
   - Applied filters render as removable chips above the table ("Status: Open ×"), plus "Clear all" once ≥2 are active.
   - Filters combine AND across fields, OR within a field. Result count updates live ("128 results").
   - Persist filters in the URL (`?status=open&assignee=me`) so views are shareable and refresh-safe — Linear and GitHub Issues prove this.
   - Offer saved views once users repeat the same filter combos (Linear views, Gmail labels-as-views).
   - Power tools graduate to a filter builder (field + operator + value rows) — Airtable, Linear — but chips remain the visible summary.
5. **Row actions.**
   - Primary action = clicking the row (opens detail or peek panel).
   - Secondary actions appear on hover at row-end: icon buttons, max 3, plus an overflow `⋯` menu. On touch, the `⋯` is always visible.
   - Hover reveals must not reflow the row — reserve the space.
   - Destructive actions live only inside the overflow menu, confirmed or undoable — never bare on the row.
6. **Bulk select.**
   - Checkbox column at far left (40–48px wide); row checkbox appears on hover (desktop) or always (touch).
   - Header checkbox selects the visible page, with an explicit "Select all 1,204 matching" link — Gmail's pattern; never silently select the full result set.
   - On selection, a floating action bar appears (bottom-center, or replacing the toolbar): "12 selected — Archive · Assign · Delete · ×".
   - Shift-click selects ranges; Esc clears the selection; the selected count is always visible while any selection exists.
7. **Inline editing.**
   - Click-to-edit a cell (Airtable): the cell becomes an input in place; Enter/blur commits, Esc reverts, Tab moves to the next editable cell.
   - Save optimistically; on failure, roll back the cell and show a toast with retry.
   - Reserve inline editing for flat values (text, number, select, date). Anything multi-field opens the detail panel instead.
8. **Row → detail.**
   - Peek panel (Linear) or push navigation. Selection is reflected in the URL; Back closes the peek.
   - Keyboard ↑↓ or j/k moves selection; Enter opens; the focused row stays scrolled into view.
   - Make rows real links so cmd/ctrl-click opens detail in a new tab.

### Pagination vs infinite scroll vs load-more
- **Pagination** — users need to return to a spot, share a location, reach the end, or act on ranges. Admin tables, transactions, anything with bulk actions. Show `1 … n`, prev/next, and a page-size picker (25/50/100). Stripe Dashboard's choice.
- **Infinite scroll** — leisure consumption feeds only (social, discovery). Forbidden when there's a footer, bulk actions, or a "find it again" need. Must restore scroll position on back-navigation.
- **Load-more button** — the safe middle: user-controlled, footer stays reachable, works for search results and mobile lists. Default to this when unsure.
- Use cursor-based loading under the hood for live data — offset pagination skips or duplicates rows when the data changes between pages.
- Live-updating feeds don't shove new rows in while the user reads — show a "3 new items" pill at the top that inserts on tap (Twitter/X, Gmail).

### Empty, loading, error (all three are mandatory)
- **First-use empty:** icon/illustration + one line of value + primary CTA — "No invoices yet — Create your first invoice". Never a blank region or a bare "No data".
- **Filtered-to-empty:** a different message — "No results match these filters" + one-click "Clear filters". Never show the first-use CTA here; the user has data, just not under these filters.
- **Loading, first load:** 5–8 skeleton rows matching real row height and column layout — mismatched skeletons cause layout jump on arrival.
- **Loading, refetch:** keep stale data visible with a subtle progress indicator. Never blank a table the user is reading.
- **Error:** inline in the table region — "Couldn't load payments" + Retry button, filters preserved. Never render an empty table as if there were genuinely no data when the fetch failed.
- **Partial failure** (some rows/columns failed to hydrate): render what loaded, mark the failed cells, offer retry — don't fail the whole view.

### Feeds specifically
- Feed item anatomy: avatar/icon left; actor + action + object as the first line; timestamp right or below; optional preview block.
- Group repeated events ("Ana and 4 others commented") — ungrouped event spam drowns the signal.
- Relative timestamps ("2h ago") with absolute on hover/tap; switch to absolute dates past ~7 days.
- Unread semantics must be explicit and singular: bold + dot for unread, cleared on view or on click — pick one rule and keep it.

## Layout & Structure
- **Row heights:** compact 32–36px, default 40–44px, comfortable 48–56px. Offer a density toggle in pro tools (Gmail does). Touch rows ≥44pt.
- **Header row:** sticky on scroll; 40–44px tall; sortable columns show their affordance on hover; header labels never wrap to 3 lines — shorten the label.
- **Alignment:** text left; numbers/amounts right-aligned with tabular figures; dates left in one consistent format.
- **Status:** colored badge + text label — never color alone (accessibility), never more than ~6 distinct status colors per table.
- **Column widths:** first (identity) column widest and sticky during horizontal scroll; fixed widths for dates/status/amounts; long text truncates with ellipsis + tooltip.
- Columns user-resizable in data-heavy tools, persisted per user per view.
- **Column config:** gear/`⋯` in the toolbar → show/hide + drag-reorder; persist per user per view. Ship with ≤7 visible default columns.
- **Counts to display:** total near the toolbar ("1,204 payments"), selected count in the action bar, filtered count after filtering.
- **Row grouping** (by status, assignee, date): sticky group headers with counts, collapsible; Linear's default view proves it beats one undifferentiated list.
- **Summary/footer row** for financial tables: totals pinned at the bottom, right-aligned under their columns.
- **Zebra striping:** skip it at default density with adequate row padding; use hover highlight instead. Borders: horizontal separators only; vertical rules only in spreadsheet-like grids.
- **Mobile adaptation (<768px):** collapse each row to a card — identity field as title, 2–3 explicitly chosen priority fields as labeled lines, status badge top-right, `⋯` for actions.
- Never horizontal-scroll a 9-column table on a phone; choose the priority columns deliberately.
- Alternative for genuinely tabular data on mobile: sticky identity column + horizontal scroll with a visible affordance (edge fade or scrollbar).
- **Performance:** virtualize lists >100 rows; paginate or window anything >1k rows; debounce filter inputs 150–300ms.
- **Numeric formatting:** thousands separators, consistent decimals per column, unit in the header not in every cell ("Amount (USD)").
- **Links in cells:** one primary link per row (the identity column); other cells plain text unless genuinely navigable.
- **Timestamps:** one format per table — relative for activity tables, absolute for records and audit trails.
- **Checkbox column:** 40–48px fixed width; its count mirrors into the bulk-action bar.

## Quick Reference

| Situation | Pattern |
|---|---|
| Many attributes, field comparison | Table |
| One key attribute + metadata triage | List rows |
| Visual-first content | Card grid |
| Users must find a row again / share position | Pagination |
| Leisure consumption feed | Infinite scroll |
| Unsure / mobile / search results | Load-more button |
| New items arriving while user reads | "N new items" pill, insert on tap |
| Actions on many rows | Checkbox column + floating bulk-action bar |
| Selecting beyond the visible page | Explicit "select all N matching" link |
| Editing one flat value | Inline cell edit, optimistic save |
| Editing multiple fields | Open the detail panel |
| Repeated filter combos | Saved views |
| Long undifferentiated list | Group rows by status/date with sticky headers |
| Table on a phone | Card collapse with 2–3 priority fields |
| Fetch failed | Inline error + Retry (never fake-empty) |
| Filters applied, zero rows | "No matches" + Clear filters |
| Brand-new account, zero records | Value line + create-first CTA |
| Activity / audit trail | Feed with grouped events + relative timestamps |
| Money/quantity columns | Right-aligned, unit in header, tabular figures |
| Unread tracking in a list | Bold + dot, one clear rule for clearing |

## Anti-Patterns

| Anti-pattern | Why it breaks | Fix |
|---|---|---|
| Actions column with 5 always-visible icon buttons | Visual noise; destructive actions one mis-click away | Row click = primary; hover reveals ≤3; rest in `⋯`; delete only in `⋯` |
| Infinite scroll + bulk actions or footer | Footer unreachable; selection scope unbounded | Pagination or load-more |
| Blanking the table during refetch | User loses reading position and context | Keep stale rows; overlay a subtle loading indicator |
| "No data" shown on fetch error | User believes records are gone; support tickets follow | Distinct error state with Retry |
| Filters/sort reset on refresh or back | Users rebuild their view constantly | Persist view state in the URL |
| Header checkbox silently selecting all 10k rows | Accidental mass actions on unseen data | Select visible page + explicit "select all matching" |
| New rows shoving in mid-read | User loses their place; mis-clicks follow | "N new items" pill, insert on tap |
| Horizontal-scrolling 9 columns on mobile | One column visible at a time; unusable | Card collapse with priority fields |
| Centered or left-aligned numeric columns | Digits don't align; magnitude comparison fails | Right-align with tabular figures |
| Unsorted default order | Table appears random; trust drops | Meaningful default sort with visible arrow |
| Cards for record comparison | Fields sit in different positions per card | Use a table |
| Skeletons unlike real content | Layout jumps on load (CLS) | Skeleton matches final row height and columns |
| Row actions appearing only on hover, on touch | Touch has no hover; actions unreachable | Always-visible `⋯` on touch |
| Hover actions that reflow the row | Content shifts under the cursor; mis-clicks | Reserve the action-area space |
| Confirm dialog for every single-row action | Trains users to click through confirmations | Confirm only destructive/irreversible; prefer undo toast |
| Color-only status indicators | Fails colorblind users and grayscale scans | Badge = color + text label |
| Every cell rendered as a blue link | Row reads as link soup; primary action unclear | One primary link per row; rest plain text |
| Mixed timestamp formats in one table | Users can't compare times across rows | One format per table, applied consistently |
| Ungrouped event spam in feeds | 40 rows of "X edited Y"; signal drowns | Group repeated events by actor/object |
