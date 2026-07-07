---
name: flow-permissions
description: "Use when designing or reviewing OS permission requests (notifications, location, camera, contacts, photos, tracking) or notification strategy — priming screens, request timing, denial recovery, frequency controls, digests, or re-engagement pushes. Also use when analytics show high permission-denial rates, users report notification spam or are disabling notifications, or an app requests permissions on first launch."
---

# Notification & Permission Priming

## Overview
The OS permission dialog is a one-shot, high-stakes ask — on iOS a notification denial is permanent until the user digs through Settings — so never spend it before the user understands the value. Prime first with your own UI, ask at the moment of obvious benefit, and treat every notification sent as spending trust you must earn back.

## When to Use
- Adding any OS-level permission request: push, location, camera, mic, contacts, photos, Bluetooth, ATT/tracking
- Designing notification types, frequency rules, digests, or a preference center
- Reviewing denial rates, opt-out rates, uninstalls after pushes, or "too many notifications" feedback
- Building re-engagement / win-back messaging
- **NOT for**: in-app authorization/roles (access control), or transactional email deliverability
- **NOT for**: capabilities with no OS dialog on the target platform — when a dialog exists anywhere in scope, this skill applies

## The Proven Flow

### Pre-permission priming (the core pattern)
1. **Trigger: a user action that needs the permission.** User enables order updates, follows a topic, sets a reminder, taps "find stores near me", starts a video call (camera/mic). Never app launch, never a timer, never "day 3 of usage".
2. **Priming screen (your UI, not the OS dialog).** One screen or modal containing:
   - Icon or small visual tied to the benefit.
   - Benefit headline framed as the user's outcome: "Know the moment your order ships" — not "Enable push notifications".
   - 1–2 lines of specifics: what you'll send and roughly how often ("Order updates only — usually 2–3 per order").
   - Primary button: "Turn on notifications" / "Enable".
   - Secondary: **"Not now"** — never "No" or "Don't allow"; keep refusal soft and honestly re-askable.
   - Proven: Duolingo primes off the streak ("Don't lose your 7-day streak — remind me"); Airbnb primes location with "find homes near you"; Headspace ties the ask to the user's own chosen reminder time; Hopper primes with price-drop watch value.
3. **Branch on the soft answer:**
   - **"Not now"** → dismiss; the OS dialog is never shown, so your one-shot is preserved. Re-prime later at the next high-intent moment. Cap: 2–3 primes total per permission, spaced days apart, each tied to a *different* concrete benefit. After the cap, stop — the answer is no.
   - **"Enable"** → fire the OS dialog immediately, same interaction. Acceptance here runs far above cold asks because the user already said yes once, and a rare OS-level denial no longer surprises you.
4. **OS dialog result:**
   - **Granted** → deliver value instantly if possible (send the confirmation they enabled, show the nearby results) and continue the flow where they left off.
   - **Denied despite priming** → respect it, record it client- and server-side, don't loop. Route future feature touchpoints to the denial-recovery path.
5. **Provisional path (iOS, content notifications).** Provisional authorization delivers quietly to Notification Center with no dialog at all; the user upgrades to full alerts or kills them from the notification itself. Use it to prove value before ever spending the formal ask.

### Priming copy patterns

| Instead of | Write |
|---|---|
| "Enable push notifications" | "Know the moment your order ships" |
| "Allow location access to enhance your experience" | "Find pharmacies open near you right now" |
| "Acme would like to send you notifications" (raw dialog) | Prime first: "Get a heads-up when prices drop on flights you're watching" |
| "Turn on notifications?" primary + "No" secondary | "Remind me daily" primary + "Not now" secondary |
| "We need camera access" | "Scan the barcode to log this food" (ask on the scan tap) |

### Platform specifics
- **iOS**: one shot at the notification dialog per install; denial is fixable only in Settings. Priming is mandatory practice. Location offers "While Using" vs "Always" — request "While Using" first; "Always" only later, in context, when a feature truly needs background location (the OS will re-confirm anyway).
- **Android 13+**: notifications are runtime-requested (`POST_NOTIFICATIONS`); the system tolerates limited re-asks but two "deny" responses ≈ permanent ("don't ask again" behavior). Same priming pattern. Register notification **channels** per category from day one so users can kill one category without killing all.
- **Web**: browsers punish premature `Notification.requestPermission()` — Chrome and Firefox quiet or auto-block sites that ask on load, and a dismissed prompt can trigger a temporary or permanent block. Soft-prompt first, always; request only inside a user-gesture handler.
- **ATT (iOS tracking)**: priming screens are allowed; incentivizing the grant or gating the app on it is forbidden by App Review. Expect majority denial; design the product to work without it.
- **Camera/mic/photos**: lowest-friction permissions when asked in-context (user tapped the camera button — intent is self-evident); a full priming screen is often unnecessary, but never ask before the triggering tap. Photos on iOS: design for "Selected Photos" limited access as the normal case.

### Timing decision guide

| Permission | Ask at | Never at |
|---|---|---|
| Notifications | User enables a specific alert/reminder/watch | Launch, signup, day-N timer |
| Location | User taps a "near me" / map feature | Launch, background upsell |
| Camera/mic | The capture/call tap itself | Feature discovery, launch |
| Contacts | User taps "invite friends" / "find friends" | Signup, onboarding wizard |
| Photos | User taps upload/attach | Profile-setup step 1 |
| ATT/tracking | After first value, with honest priming | First launch cold |

### Denial recovery
1. Check permission state before promising permission-dependent features anywhere in the UI.
2. When a denied permission blocks something the user just tried to do ("Notify me about this auction"), show an inline explainer: what's off, why it matters *right now*, and a button deep-linking to the app's OS settings page (iOS `app-settings:` URL; Android app-notification-settings intent; web: instructions for the lock-icon site settings). Add a one-line pointer to the exact toggle they'll see.
3. Never nag on a schedule. Recover only at moments of demonstrated intent.
4. Always offer fallback channels: in-app inbox/activity feed (notifications' value without the permission), email or SMS opt-in, calendar export, badge-only updates.

### Notification strategy: frequency, digests, re-engagement
- **Categorize every notification type** before building:
  - *Transactional* (order shipped, security alert, direct payment) — always send; not user-disable-able except by law.
  - *Social* (mention, reply, follow) — send by default; per-category user control.
  - *Promotional/engagement* (digest, streak, tips, win-back) — strictly opt-in behavior, hard-capped. Never smuggle promo content through the transactional channel; it burns the channel and violates platform policy.
- **Digest pattern**: batch low-urgency items into one daily or weekly summary at a user-chosen or engagement-inferred send time (LinkedIn, Substack, GitHub notifications). Default new non-urgent categories to digest, not instant — let users upgrade, don't make them defend their attention.
- **Frequency caps**: promotional ≤1/day hard cap, fewer by default; respect quiet hours (default ~22:00–08:00 device-local, overridable); collapse bursts server-side ("3 new replies on your post" via collapse keys/thread IDs, not 3 pushes).
- **The three-question send test** — every notification must pass all: Is it about the user's own activity or explicit interest? Can they act on it? Would they miss it if it never arrived? Any "no" → digest or drop.
- **Re-engagement without spam**:
  - Trigger from user-created stakes: Duolingo's streak works because "your 12-day streak" is the user's own artifact, not the brand's neediness.
  - Reference specific abandoned state: "Your draft from Tuesday is still here", "3 people applied to your posting" — never content-free "We miss you 😢".
  - Cap win-backs at 1–2 attempts per lapse, then go silent; an uninstall or OS-level notification kill costs more than a lapsed user.
  - **Auto-downgrade rule**: a category with zero opens across N sends (5–10) drops itself to digest or off, with one in-app note saying so and how to resume.
- **Preference center (in-app, mandatory)**: per-category toggles with a one-line description + example under each, digest-frequency choice, "Pause all for 1 week", honored server-side immediately. Mirror the reachable-from-notification affordance (long-press → settings on mobile; unsubscribe-parity for email digests).

**Re-engagement copy — user-stakes vs. brand-neediness:**

| Spam (brand's need) | Earns the open (user's stake) |
|---|---|
| "We miss you! Come back 😢" | "Your 12-day streak ends at midnight" (Duolingo) |
| "Check out what's new in Acme" | "3 people applied to the job you posted" |
| "Don't forget about us!" | "Your draft 'Q3 plan' has been waiting since Tuesday" |
| "Big sale! Open now!" | "The flight you're watching dropped $84" (Hopper) |

## Layout & Structure

- **Priming screen**: one benefit only; headline ≤8 words; body ≤2 lines; optional visual ≤~40% of screen height; primary CTA full-width at bottom (≥44pt iOS / 48dp Android); "Not now" as a text button directly below — equal tap-target size, lower visual weight, never an X-only escape.
- **One permission per moment.** Never stack primes. Camera+mic may pair only when one feature needs both simultaneously (video call); notifications pair with nothing.
- **Copy rule**: name the concrete trigger ("when your order ships", "when someone replies"), never the mechanism ("to send you push notifications") and never vague value ("to enhance your experience").
- **Denial-recovery banner**: one line + "Open Settings" button, dismissible, reappears only on the next blocked intent — not on every screen view.
- **Preference center**: grouped by category; each row = toggle + name + one-line example ("Weekly digest — top posts from people you follow"); show OS-level state when it overrides ("Notifications are off for this app in Settings — Open Settings"); place it ≤2 taps from any received notification and inside app settings.
- **Priming placement in flows**: insert as an interstitial inside the triggering flow, then return the user exactly where they were — never eject to a home screen after the dialog.
- **Badging**: app-icon badges count only actionable items; a badge that never clears trains users to ignore it — clear on view, not on tap-through of one item, unless items are discrete tasks.
- **Notification anatomy**: title = the event in the user's terms ("Sara replied"), body = the specific content preview, deep link = the exact item (never the app home screen); rich attachments (image/actions) only when they let the user finish without opening the app (reply inline, mark done, snooze).
- **Actionable notifications**: 1–2 quick actions max (Reply, Mark done, Snooze); every action must be idempotent and safe from a lock screen.
- **Accessibility**: priming screens are fully keyboard/screen-reader operable; "Not now" is a real button in the accessibility tree, not a dismiss gesture only; notification copy makes sense read aloud without the visual context.

## Quick Reference

| Situation | Pattern |
|---|---|
| App first launch | Ask for NOTHING; defer every permission |
| Feature needs permission after a user tap | Just-in-time: prime → soft yes → OS dialog → continue flow |
| User tapped the camera/mic feature itself | In-context ask, no priming screen needed; never earlier |
| User said "Not now" | One-shot preserved; re-prime at next high-intent moment, ≤3 total |
| OS-level denial | Inline recovery at moment of need + Settings deep link + fallback channel |
| iOS location | "While Using" first; "Always" only later, in context |
| iOS content app unsure users want pushes | Provisional (quiet) delivery, upgrade from the notification |
| Android | Channels per category; treat second deny as permanent |
| Web | Soft prompt inside a user gesture; never on load |
| Low-urgency updates | Default to digest; user upgrades to instant |
| Event burst | One collapsed summary notification |
| Lapsed user | Reference their own stakes/state; 1–2 attempts, then silence |
| Category repeatedly ignored | Auto-downgrade to digest/off; say so in-app |
| ATT/tracking | Prime honestly, no incentives, product works on denial |

## Anti-Patterns

| Anti-pattern | Fix |
|---|---|
| OS permission dialog on first launch | Just-in-time asks tied to user actions |
| Firing the OS dialog with no priming (push, location) | Soft-ask screen first; escalate only on "yes" |
| Soft-prompt refusal labeled "No" / "Don't allow" | "Not now" — soft, honest, re-askable |
| Re-priming every session after "Not now" | Cap 2–3, spaced days apart, new benefit each time |
| "Enable notifications to continue" wall | Never gate core features on non-essential permissions |
| Promo pushes through the transactional channel | Strict category separation; promo is opt-in |
| "We miss you 😢" content-free win-backs | Reference the user's own state (streak, draft, activity) or send nothing |
| One master notification toggle | Per-category preference center + digest options |
| Ignoring quiet hours / timezones | Local quiet hours on by default for non-urgent sends |
| Denial → silently broken feature | Inline explainer + Settings deep link + fallback channel |
| Asking for contacts/location "for later use" | Ask when the invite/map screen is actually open |
| Requesting "Always" location upfront | "While Using" first; upgrade contextually |
| Badge that never clears | Badge counts actionable items only; clears on view |
| Measuring dialog-grant rate as success | Optimize retained opt-in (still enabled at day 30) |
| Same push to all timezones at HQ time | Send-time per user's local time / inferred active hours |
| Stacking two OS dialogs back-to-back | One permission per moment; the second waits for its own trigger |
| Notification deep-links to the app home screen | Deep-link to the exact item the notification names |
| Preference changes applied client-side only | Honor server-side immediately; a re-sent muted category is spam |

Success metrics: opt-in still enabled at day 30, notification-driven opens, and uninstall rate within 24h of a push — not day-0 grant rate.
