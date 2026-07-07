---
name: flow-settings
description: "Use when designing, building, or reviewing settings screens, account management, preferences, profile pages, or any destructive account action — delete account, change email/password, data export, session management. Also use when users report being unable to find a setting, accidental destructive actions, or when auditing danger-zone and confirmation patterns."
---

# Settings & Account Management

## Overview
Settings are visited rarely and under a specific goal ("turn off that email," "delete my account") — so findability beats aesthetics, and destructive actions must be impossible to trigger by accident yet never hidden from users who need them. Organize by the user's mental model, not your org chart or database schema.

## When to Use
- Building a settings/preferences area for web, iOS, or Android
- Adding account security flows: email change, password change, 2FA, active sessions
- Designing delete-account, delete-workspace, or any irreversible action
- Adding data export (GDPR/CCPA portability)
- **Not for:** in-context controls that belong next to the content they affect — a document's sharing settings live on the document (see `flow-sharing`)
- **Not for:** first-run onboarding preference pickers

## The Proven Flow

1. **Entry**
   - Avatar or gear icon, top-right on web; profile tab or gear on mobile.
   - Landing view shows the settings nav with the most-visited section (Profile/Account) already open — never an empty "pick a category" splash screen.

2. **Navigation**
   - Web: left sidebar of grouped categories, content pane on the right (GitHub, Stripe Dashboard).
   - Mobile: single scrolling list of grouped rows drilling into detail screens (iOS Settings).
   - Group by user mental model:
     - **You:** Profile, Account, Security, Notifications
     - **Preferences:** Appearance, Language, Privacy, Integrations
     - **Money:** Billing, Plan, Invoices
     - **Org (multi-user products):** Members, Teams, Permissions — visually separated from personal settings. GitHub splits personal vs organization settings entirely; Stripe separates account vs team. Users must always know which scope they're editing.
     - **Danger zone:** last, always.
   - Items needing attention carry a badge or inline alert: unverified email, expiring card, missing 2FA — settings is where users expect to resolve account health.

3. **Search**
   - Add it above the nav once you exceed ~15 settings. Users don't browse settings — they hunt.
   - Match setting *labels, descriptions, and synonyms*: "dark mode" must find "Appearance → Theme."
   - Results deep-link to the exact control, scrolled into view and briefly highlighted. iOS Settings and Chrome's settings search both do this.

4. **Individual settings**
   - Each row: label, one-line description of *consequence* (not a restatement), and the control.
   - Toggles apply instantly, confirmed by the state change itself or a brief toast.
   - Forms (profile fields) use an explicit Save button — disabled until dirty — plus an unsaved-changes guard on navigate-away.
   - Never mix auto-save and explicit-save within the same section; users can't tell what's applied.
   - Avatar upload gets crop-and-preview plus an initials fallback; username changes warn about consequences ("your profile URL changes; old links redirect for 30 days" — GitHub's rename model).
   - Feature surfaces deep-link into settings: the "Manage notifications" link in an email or a nudge lands on that exact settings row, not the settings home.

5. **Email change**
   - New email field → verification link sent to the **new** address → change applies only on verification.
   - Notification email to the **old** address with a "this wasn't me — secure my account" link.
   - The old email keeps working until verification completes. GitHub and Google both work this way. An instant swap on submit is an account-takeover vector via one stolen session.

6. **Password change**
   - Current password (or fresh re-auth) → new password with inline strength meter and visible requirements → confirm.
   - On success: confirmation screen, email notification, and an offer to sign out all other sessions.
   - Sensitive pages require *recent* authentication — GitHub's "sudo mode," Stripe's re-auth. Re-prompt when the session's last auth is older than ~15–60 minutes.

7. **Two-factor authentication setup**
   - Offer methods in security order: passkey / authenticator app first, SMS as fallback only (SIM-swap risk) — state the recommendation inline.
   - Setup: QR code + manual key → user enters a code to prove it works → **recovery codes shown once**, with download/copy buttons and a "I've saved these" confirm before finishing.
   - Disabling 2FA requires re-auth plus a second factor, and triggers a security email.

8. **Notification settings**
   - Matrix by event type × channel (email / push / in-app), not a single master toggle plus mystery.
   - Every email footer's "unsubscribe/manage" deep-links to this exact screen.
   - Security notifications are not disableable — say so: "Security alerts can't be turned off."

9. **Data export**
   - One button: "Request export." Async job → email with an expiring, auth-gated download link.
   - Show job state inline: "Export requested Jul 7 — we'll email you when it's ready."
   - State what the export contains ("all documents, comments, and account data") so users can judge completeness before deleting anything.
   - Formats: JSON/CSV plus media files. Never gate export behind a support ticket — GDPR Art. 20 makes portability a right, and export is the trust-preserving companion to deletion.

10. **Delete account — the canonical destructive flow, three gates in order**
   1. **Confirm intent:** a button in the Danger Zone opens a dedicated screen or modal — never a bare "Are you sure?"
   2. **Show consequences concretely:** "Deletes 3 workspaces, 1,240 documents, and cancels your subscription (paid through Aug 12). This cannot be undone." Enumerate real counts from real data. Offer alternatives on this same screen: export data, downgrade to free, deactivate instead.
   3. **Type-to-confirm:** the user types their username, email, or the phrase "delete my account" exactly — GitHub's repo-deletion pattern. Typing engages deliberate attention; a checkbox does not. Then re-auth if the session isn't fresh. The submit button is red, labeled with the consequence ("Delete this account"), and disabled until the phrase matches.
   - Afterward: sign out everywhere, send a confirmation email, and ideally hold a 30-day soft-delete grace window with reactivation-by-login. Whichever policy is real, *say it* — faking permanence and faking deletion both destroy trust and invite regulators.

11. **Sessions & security events**
   - List active sessions: device, approximate location, last active. Per-session revoke plus "Sign out all other sessions."
   - Mark the current session — "This device" — so users don't revoke themselves by accident.
   - Send email on: new-device sign-in, password change, email change, 2FA change. Each email carries a recovery link.

## Layout & Structure
- **Groups of 3–7 items.** More than 7 in a group → split the group. More than ~9 top-level categories → merge, or make search primary.
- **Hierarchy depth ≤ 3:** category → screen → control. Deeper is unfindable without search.
- **One column of controls.** Label left, control right (web rows / iOS cells); consequence description under the label in secondary text.
- **Danger Zone:** bottom of the Account or General page, visually distinct — GitHub's literal red-bordered "Danger Zone" card. Contents: only destructive, irreversible actions (delete account, transfer ownership, leave organization). Never place a destructive action adjacent to a routine one.
- **Destructive buttons:** red/destructive style; never the default-focused button in any dialog; never triggered by Enter; always at least one deliberate extra step away. In confirmation dialogs the *safe* action gets default focus and prominence.
- **Button copy names the action,** never "Yes"/"No"/"OK": "Delete 3 documents" / "Cancel."
- **Consequence-first descriptions** beat label restatements: "Stops all email except security alerts" — not "Controls your email settings."
- **Platform specifics:**
  - iOS — grouped inset lists, system cells, red text rows for destructive actions. Apple Guideline 5.1.1(v): if the app supports account creation, account deletion must be initiable *in-app*.
  - Android — standard preference-screen pattern; respect system back behavior between levels.
  - Web — every settings page has a stable, shareable, deep-linkable URL.
- **Disabled ≠ hidden:** settings unavailable on the current plan stay visible, disabled, with a one-line reason ("Available on Pro") — hiding them makes users doubt the feature exists.
- **Accessibility:** toggles are real switches with accessible names matching the visible label; keyboard focus order follows visual order; destructive confirms trap focus in the dialog with the safe action focused first.
- **Current value visible at rest:** rows that drill into a detail screen show the current selection inline ("Language — English") so users don't open screens just to check state — iOS Settings' core convention.
- **Confirmation emails for identity changes only:** email, password, 2FA, and payment method changes get an email; toggling dark mode does not. Over-notifying trains users to ignore the security emails that matter.

## Quick Reference

| Situation | Pattern |
|---|---|
| More than ~15 settings | Search with synonym matching + deep-link-and-highlight |
| Grouping settings | Mental-model groups of 3–7; org/team scope split from personal |
| Instant vs saved changes | Toggles auto-apply; text forms explicit Save + dirty guard |
| Change email | Verify new address first; notify old address; no instant swap |
| Password change / sensitive page | Re-auth (sudo mode) if last auth >15–60 min ago |
| Delete account | Confirm → concrete consequences + alternatives → type-to-confirm → re-auth |
| Any irreversible action | Danger Zone, red, never default-focused, named-action buttons |
| Reversible destructive action | Single confirm or undo window — don't over-gate |
| Data export | Self-serve async job + expiring emailed link; offered inside deletion flow |
| New device or credential change | Security notification email with recovery link |
| Multi-workspace destructive act | Enumerate real counts of what gets destroyed |
| Active sessions | List + per-session revoke + "sign out all" |
| 2FA setup | App/passkey first, SMS fallback; recovery codes shown once with save-confirm |
| Notification preferences | Event-type × channel matrix; email footers deep-link to it |
| Plan-gated setting | Visible but disabled, with "Available on Pro" reason |
| Drill-in row | Show current value inline ("Language — English") |
| Email footer "manage preferences" | Deep-links to the exact notification row |
| Account needs attention | Badge on the relevant settings item (unverified email, expiring card) |
| Session list | Current session marked "This device" |

## Anti-Patterns

| Anti-pattern | Why it breaks | Fix |
|---|---|---|
| Settings organized by internal team or service names | Users can't map goal → category | Group by user mental model; validate with card sorting |
| Delete account only via support email or chat | Roach motel; GDPR and App Store violations | Self-serve deletion behind the three gates |
| "Are you sure?" as the only guard on irreversible delete | Habitual click-through; guaranteed accidents | Consequences + type-to-confirm for irreversible acts |
| Type-to-confirm on every minor delete | Trains users to autopilot the ritual | Reserve heavy friction for truly irreversible actions |
| Instant email swap on submit | Account takeover via one stolen session | Verify new address + notify old address |
| Destructive button as dialog default / Enter-triggered | One keystroke destroys data | Safe action gets default focus |
| Mixed auto-save and Save-button on one page | User can't tell what's applied | One persistence model per section |
| Settings search matching labels only | "dark mode" misses "Theme" | Index synonyms and descriptions |
| Offering deletion but burying export | Data-hostage optics; legal risk | Surface export inside the deletion flow |
| No email on password/email/2FA change | Silent account takeover | Security event notifications, always |
| Danger zone rows mixed into the general list | Accidental adjacency clicks | Segregated, styled, bottom-placed zone |
| Fake deletion (account silently reactivatable forever) | Trust and legal exposure | State the real policy: grace window or permanent |
| Unsaved changes silently discarded on navigation | Lost work; users distrust every form | Dirty-state guard with save/discard choice |
| Master notification toggle only | Users kill all email to stop one type | Event-type × channel matrix |
| Plan-gated settings hidden entirely | Users conclude the feature doesn't exist | Visible, disabled, with the reason and an upgrade link |
| Username change with silent link breakage | Every old profile link 404s | Warn on change + temporary redirects |
| Recovery codes shown without a save-confirm | Locked-out users at the worst moment | Download/copy buttons + "I've saved these" gate |
| Sensitive changes without re-auth | One stolen session owns the account | Sudo mode: fresh auth for security-critical pages |
| Workspace delete confused with account delete | Users destroy the wrong thing | Separate flows; name the exact object and its counts |
| Every change triggers a confirmation email | Users tune out real security alerts | Email only for identity/payment/security changes |
