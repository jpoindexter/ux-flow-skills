---
name: flow-sharing
description: "Use when designing, building, or reviewing sharing, invite, or collaboration features — share dialogs, permission levels, link sharing, team invites, access requests, or presence indicators. Also use when users report confusion about who can see what, invite recipients landing in the wrong place, permission-denied dead ends, or when reviewing growth loops for spam risk."
---

# Sharing, Invites & Collaboration

## Overview
Sharing succeeds when the sharer can answer "who can do what?" at a glance and the recipient lands in the content with the right access in one click. Every extra decision at share time, and every dead end at open time, kills the loop that grows collaborative products.

## When to Use
- Adding a Share button and dialog to any document, project, file, or workspace
- Designing permission levels and link-based access
- Building team invite flows — email, link, or both — or access-request handling
- Adding presence, live cursors, or activity indicators to collaborative surfaces
- **Not for:** social-network posting and feeds
- **Not for:** OS share sheets when you're only a data provider — those distribute a link; they can't set permissions

## The Proven Flow

1. **Share button**
   - Top-right of the content it shares, labeled with the word "Share" — not icon-only. Figma, Google Docs, Notion, and Dropbox all converge on this exact placement.
   - Opens a popover or modal scoped to *this object*, never a trip to a settings page.

2. **Share dialog — one dialog, three zones** (the Google Docs model, since converged on by Figma, Notion, and Dropbox)
   - **Invite row (top):** one combined input accepting emails and names of existing members, multi-entry chips, with a permission dropdown attached to the input. Explicit "Invite"/"Send" button — Enter adds a recipient chip, never sends. Default role: can-edit for teammates, can-view for external addresses. Optional "notify people" toggle with a message field, collapsed by default.
   - **People list (middle):** everyone with access — avatar, name/email, per-person permission dropdown that includes "Remove." Owner is marked and immovable. Inherited access shows its source: "Everyone at Acme can view (via Team folder)" — never anonymous magic access.
   - **Link zone (bottom):** general-access control plus a **Copy link** button. Copy link is the highest-traffic action in the dialog — one click, "Link copied" feedback within 100ms, never buried in a submenu.
   - Viewers and commenters can open the dialog to see who has access; only roles at-or-above their ceiling are offerable.

3. **Link sharing states** — ordered by exposure, defaulting safe:
   1. **Restricted** (default): the link works only for invited people. This must be the default — every leaked-link incident starts with a permissive default.
   2. **Anyone in [org] with the link** — role selectable: viewer / commenter / editor.
   3. **Anyone with the link** — role selectable; editor-via-public-link deserves an extra warning.
   - Changing state shows a plain-language consequence sentence: "Anyone on the internet with this link can view." That's Google Docs' literal copy — consequences, not jargon like "public link enabled."

4. **Permission levels** — four canonical roles; don't invent more until proven necessary:
   - **Viewer** — read only.
   - **Commenter** — read + comment. Critical for review workflows; when it's missing, people over-grant edit.
   - **Editor** — modify content; can invite others up to their own level (make this configurable).
   - **Owner / Full access** — everything, plus sharing control, delete, and transfer.
   - Name roles by capability ("Can edit"), never by org title ("Manager"). Notion's "Full access / Can edit / Can comment / Can view" is the reference wording.

5. **Recipient experience — the half everyone forgets**
   - Invite email: sharer's real name and avatar as sender context, the object's title, one large CTA ("Open the document"), plus the sharer's message if any.
   - Clicking lands the recipient *inside the content*. Auth happens inline if needed, and after signup they land in the same content — not on a dashboard. Deep-link persistence through the auth flow is the single most-fumbled step in sharing.
   - New-user invites: minimal signup (name + password, or SSO), and no onboarding tour standing between them and the shared thing.
   - Link unfurls respect permissions: a restricted document pasted into Slack or iMessage shows a generic card, never the content — OG previews are an access-control surface.
   - Invite emails come from a consistent, authenticated sender domain (SPF/DKIM) with the sharer in the reply-to; collaboration invites that land in spam kill the whole loop.

6. **Access request — permission-denied done right**
   - A signed-in user without access hits the link → never a bare 403 or fake 404. (Genuinely sensitive tiers may hide even the title — decide per product.)
   - Screen shows: object title, "You need access," a **Request access** button, and "You're signed in as jason@… — switch account." Wrong account is the #1 cause of false denials; Google Docs handles both cases on one screen.
   - The request notifies the owner (email + in-app) with one-click actions: Approve as viewer / Approve as editor / Deny.
   - The requester is notified on approval and lands directly in the content.

7. **Revoking access — the forgotten reverse path**
   - Per-person "Remove" in the people list, effective immediately, including live sessions.
   - Flipping a link back to Restricted cuts off everyone who arrived by link; warn with the count: "This will remove access for 12 people."
   - The removed person's next visit gets the request-access screen, not an error — they may legitimately need access again.
   - Ownership transfer is explicit and accepted by the recipient, never a silent swap; the old owner drops to editor, not to nothing.

8. **Team invite flow (workspace level)**
   - Offer both channels: email invites with a role selector, AND an invite link ("Anyone with this link joins as Member") with regenerate and disable controls.
   - Show pending invites with resend and revoke.
   - If seats are billed, show the cost consequence at invite time — "Adding 2 editors adds $24/mo" — before send, not on the next invoice (Figma's seat-confirmation model).
   - Auto-suggest teammates by verified email domain: "3 people from acme.com are already here — join them." This is Figma's and Notion's domain-capture loop — it grows teams without sending a single unsolicited email.

9. **External guests vs members**
   - Distinguish visually: external collaborators get a "Guest" badge in people lists and avatar stacks (Notion, Slack Connect) — internal users must see at a glance when outsiders are in the room.
   - Guests are scoped to invited objects only; they never see the workspace directory or member list.
   - Escalation request: when an editor tries to grant beyond their own rights, convert the attempt into a request to the owner — "Ask Maria to make this public" — instead of a dead-end error.

10. **Presence & collaboration cues** — layer by intrusiveness:
   - **Avatar stack** (top-right): overlapping avatars, "+3" overflow, max ~5 shown.
   - **Live cursors** with name labels — canvas and document surfaces only (Figma's signature).
   - **Selection/field highlights** in each collaborator's color.
   - **"X is editing"** indicators on shared fields where cursors don't apply.
   - Colors are assigned per person and stay consistent within a session. Presence decays honestly: idle state after ~1–5 minutes, gone on disconnect.
   - Add "viewed by" history only where it's socially expected (work docs), and disclose it — silent read receipts read as surveillance.
   - @mentioning someone without access prompts inline: "Maria can't see this — invite as commenter?" (Google Docs). Mentions are the highest-intent sharing signal in the product; never let them silently fail.

## Layout & Structure
- **Dialog size:** ~480–560px wide on desktop; full-height sheet on mobile. Invite input pinned top, people list scrolls in the middle (fixed max-height — the dialog doesn't grow), link controls pinned bottom.
- **One permission-dropdown component everywhere** — identical wording and options in the invite row, people list, and link zone. Divergent permission UIs are how "I thought they could only view" happens.
- **Copy link:** always visible in the dialog, plus a right-click / overflow-menu shortcut on the object itself.
- **Avatar stacks:** 24–32px avatars, ~30% overlap, deterministic order (owner first, then recency), overflow count clickable to open the full list.
- **Role dropdown order:** most restrictive first (Viewer → Commenter → Editor) so misclicks fail safe.
- **Permission checks server-side, always:** the dialog is a view of access control, not the enforcement — a hidden button is not a denied request.
- **Native mobile:** the in-app share dialog handles permissions; the OS share sheet (UIActivityViewController / Android Sharesheet) distributes an already-permissioned link. Don't rebuild the OS sheet; don't make it the only path.
- **Notification budget:** one email per invite, one per access request, one per approval. Batch comment and edit activity into digests. Every collaboration email type gets its own notification setting.
- **Keyboard and screen readers:** the share dialog traps focus while open; the permission dropdown is a real listbox/menu with the current role announced; "Link copied" fires an `aria-live` announcement, not just a visual toast.
- **Empty state:** a never-shared object's dialog shows the invite input focused and one line of reassurance ("Only you can see this") — not a blank list.
- **Audit trail (team products):** owners can see who granted what to whom and when; permission changes without history are unresolvable disputes waiting to happen.

## Viral Loops Without Spam
- A durable loop means the user shares for *their own benefit* — getting feedback, handing off work — and growth is the side effect. Figma files, Google Docs, and Calendly links work because sharing *is* the job, not a growth hack bolted on.
- Send invites **only** on explicit user action, previewing exactly who will be emailed before send. Never auto-email "suggested contacts," never pre-check bulk address-book invites, never send on the user's behalf without preview. LinkedIn's address-book import campaigns are the cautionary tale — lawsuits included.
- Incentivized referrals (Dropbox's storage-for-both) stay honest when the reward is two-sided, disclosed inside the invite ("Jason gets extra storage if you join"), capped, and the invite is visibly from the user, not the brand.
- Domain capture beats contact scraping: "people from your company are already here" converts without emailing anyone.
- Measure the loop honestly: invites sent → invites accepted → invitees who activate. A high send count with low acceptance means you're generating spam, not growth.

## Quick Reference

| Situation | Pattern |
|---|---|
| Placing the share affordance | "Share" (the word), top-right of the object |
| Default link state | Restricted — invited people only |
| Making a link public | Explicit state change + consequence sentence + role selector |
| Role set | Viewer / Commenter / Editor / Owner — capability names |
| Internal vs external invite default | Internal: can edit; external: can view |
| Recipient clicks an invite | Deep-link into content; auth inline; link survives signup |
| Visitor without access | Request-access screen + account switcher, never bare 403 |
| Owner receives a request | One-click approve-with-role / deny, from email or in-app |
| Growing a team workspace | Email invites + revocable invite link + domain suggestions |
| Showing who's present | Avatar stack ≤5 + overflow; cursors only on live surfaces |
| Inherited permissions | Show the source: "via Engineering team folder" |
| Referral incentives | Two-sided, disclosed in the invite, user-initiated only |
| Leaked or stale invite link | Regenerate/disable control on every share link |
| Removing someone's access | Immediate effect + request-access on next visit, not an error |
| Making a shared link Restricted again | Warn with the count of people losing access |
| External collaborator | "Guest" badge, scoped to invited objects only |
| Editor wants to over-grant | Convert into an escalation request to the owner |
| Ownership transfer | Explicit offer + acceptance; old owner drops to editor |
| @mention of someone without access | Inline "invite as commenter?" prompt, never a silent fail |
| Never-shared object opened for sharing | Invite input focused + "Only you can see this" |
| Invite emails landing in spam | Authenticated sender domain; sharer in reply-to |

## Anti-Patterns

| Anti-pattern | Why it breaks | Fix |
|---|---|---|
| "Anyone with the link can edit" as the default | One leak = data breach; silent overexposure | Restricted default; escalation is always explicit |
| Bare 403 or "not found" on permission denial | Dead end; users chase the sharer through other channels | Request-access flow + switch-account option |
| Invite that dumps new users on a dashboard | Recipient never reaches the shared thing; the loop dies | Persist the deep link through auth and signup |
| Auto-inviting contacts / pre-checked bulk invites | Spam; trust damage; documented dark pattern | Explicit selection + preview of exactly who gets emailed |
| Six custom roles with overlapping powers | Nobody can predict access; over-granting follows | Four canonical roles; extra roles are enterprise-only |
| Permission wording differing across surfaces | "Viewer" here, "read-only" there → wrong grants | One shared permission component and vocabulary |
| "Copy link" hidden behind a menu | Highest-frequency action gets friction | Always-visible button with instant feedback |
| Invisible inherited access | Sharer can't answer "who can see this?" | List inherited grants with their source |
| No commenter role | Reviewers receive edit rights they shouldn't have | Comment-only role between view and edit |
| Silent read-tracking ("seen by") without disclosure | Feels like surveillance; trust erosion | Disclose what presence/read data others can see |
| Invite links with no revoke or expiry | A leaked link is a permanent open door | Regenerate/disable controls; optional expiry |
| A notification per keystroke or comment | Recipients mute everything; real signal is lost | Digest batching + granular notification settings |
| OG/link previews exposing restricted content | Access control bypassed by paste-into-Slack | Generic unfurl card for non-public objects |
| Surprise seat charges after invites | Billing dispute; admin distrust | Cost consequence shown at invite time |
| Guests indistinguishable from members | Accidental disclosure to outsiders | "Guest" badge everywhere the person appears |
| Permission changes with no history | Unresolvable "who opened this up?" disputes | Audit log of grants, changes, and removals |
| Removing access with no path back | Legitimate re-access becomes a support ticket | Request-access screen for removed users |
| Presence shown as "online" long after disconnect | False expectations of live collaboration | Idle after 1–5 min; gone on disconnect |
| Share dialog reachable only from a settings page | Sharing friction kills the growth loop | "Share" button on the object itself, top-right |
