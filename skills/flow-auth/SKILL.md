---
name: flow-auth
description: "Use when designing, building, or reviewing any authentication surface — signup, login, logout, magic links, passkeys, OAuth/SSO buttons, password reset, 2FA/MFA, email verification, or session-expiry handling. Also use when users report being locked out, duplicate accounts from mixed auth methods, confusing reset emails, or drop-off on the signup/login screen."
---

# Authentication Flows

## Overview
Auth is a toll booth, not a destination: minimize fields, decisions, and round-trips, and never lose the user's work or intent across the auth boundary. Ask for identity (email) first and defer the credential decision — the server, not the user, should figure out what happens next.

## When to Use
- Building signup, login, reset, verification, 2FA, or session-management screens
- Choosing among passwords, magic links, OTP codes, passkeys, OAuth/SSO
- Reviewing auth drop-off, lockout complaints, or duplicate-account bugs
- Handling session expiry, re-authentication, step-up auth, or logout UX
- **NOT for**: authorization/roles/permissions logic (backend access control)
- **NOT for**: machine-to-machine/API-key auth (no human in the loop)

## The Proven Flow

### Combined entry (preferred over separate signup/login screens)
1. **Screen 1 — Identity.** Single email field + "Continue" button.
   - SSO buttons (Google/Apple + at most 1–2 more) placed **above** the email field, above the fold.
   - No password field yet. No "Sign up vs Log in" choice — there are no tabs to pick wrong.
   - Proven: Google, Slack, Notion, Medium, Vercel all use progressive single-field entry.
   - If legacy constraints force separate pages, cross-link both prominently ("Already have an account? Log in").
2. **Server routing.** Existing account → credential step for that account's method (passkey, password, or code). New email → signup step, silently — the same send-a-code screen as login; the user never learns they took a different path.
3. **Screen 2 — Credential.** Exactly one of:
   - **Passkey** (offer first when the account has one): conditional UI (autofill-style prompt on the email field), one biometric tap, done. Always render a visible fallback ("Use a code instead") — passkeys fail on borrowed/shared devices.
   - **Magic link + OTP code together**: "We sent a code to j***@x.com" with a 6-digit input. Send BOTH a tappable link and a code in the same email (Slack, Notion, Medium) — links break when email opens on a different device; the code works anywhere. Auto-advance digits, auto-submit on the 6th, `autocomplete=one-time-code`. Code/link valid 5–10 minutes, single-use, with the expiry shown ("Code expires in 9:59"). "Resend" disabled 30s with visible countdown; "Use a different email" link present.
   - **Password** (if that's the account's method): single field, show/hide toggle, "Forgot password?" adjacent to the field (not page bottom), submit on Enter.
4. **New accounts: nothing else required.** No name, no confirm-password (show/hide replaces it), no CAPTCHA unless abuse signals fire. Profile data belongs to onboarding (see flow-onboarding).
5. **Preserve intent across the boundary.** The deep link / cart / draft the user was heading to must survive the whole flow — including OAuth redirects and the verification round-trip. Auth that dumps users on a generic dashboard loses the task.

### Choosing the credential stack

| Product type | Primary | Secondary | Password? |
|---|---|---|---|
| Consumer mobile | Apple + Google SSO | Email OTP code | No — skip entirely |
| Consumer web | Google SSO + passkeys | Email code + link | Optional, never required |
| B2B SaaS | Google SSO / domain-routed SAML | Email code | Legacy support only |
| Security-sensitive (fintech) | Passkeys | Password + mandatory 2FA | Yes, with step-up |

- New products in 2026 should treat passwords as the legacy fallback, not the default: passkey-first with email-code fallback covers every device case with less support load.
- New accounts enroll a passkey via a one-tap prompt immediately after first successful login (never mid-signup); decline is silent, re-offer from settings only.
- Whatever the stack, one account per email — methods are doors into the same account, never separate accounts.

### OAuth / SSO specifics
- 1–3 providers max; each extra button lowers completion and pushes email entry below the fold.
- "Continue with Google" wording on a combined screen (serves both signup and login). Official brand buttons, full-width, uniform heights.
- **Apple rule (iOS)**: offering third-party/social login requires also offering a privacy-preserving option (App Review 4.8, relaxed Jan 2024) — Sign in with Apple satisfies it and is the pragmatic default.
- **Account collision**: OAuth email matches an existing password account → auto-link only when the provider verifies the email; otherwise prompt "This email has a password account — log in to connect Google."
  - Never create a silent duplicate account — duplicates are the top source of "my data disappeared" tickets.
- **B2B**: detect corporate domains on the email step and auto-route to the org's SAML/SSO (Slack, Notion, Figma pattern).

### Email verification
- Do NOT block product use on verification unless legally or abuse-required.
  - Let the user in; show a dismissible banner; hard-gate only sensitive actions (payouts, inviting others, publishing publicly).
- Verification email: one button, valid ≥24h, single-use.
- Clicking lands the user **logged in at their prior destination** — never a "Verified! Please log in." dead end.
- Waiting screen offers: "Check spam · Resend · Change email address."

### Password reset (when passwords exist)
1. "Forgot password?" → email field pre-filled from the login attempt → send.
2. Confirmation copy identical whether or not the account exists: "If an account exists for X, we've sent a reset link." Never leak account existence.
3. Reset link: expires ≤1h, single-use → new-password screen with one field, show/hide, and a live requirements checklist that ticks while typing.
4. On success: log the user in immediately, invalidate other sessions, email a "your password was changed" notice with a support link.

### 2FA / MFA
- Prompt at login only, after the primary credential succeeds.
- Method order in UI: passkey/security key → TOTP authenticator app → SMS last (SIM-swap risk — express via ordering, not scare copy).
- "Trust this device for 30 days" checkbox on the challenge screen.
- Enrollment must issue **backup codes**: 10 one-time codes, forced download/copy confirmation before setup completes.
- "Lost your device?" recovery link on the challenge screen itself — invisible recovery paths are the #1 auth support ticket.
- Step-up auth: re-challenge for sensitive actions (change email, view API keys, delete account) even within a valid session.

### Session expiry
- Expiry mid-session: keep the page rendered, overlay a re-auth modal, **preserve all unsaved work and the in-flight action**, replay it after re-auth.
- Never hard-redirect a mid-form user to a login page — that destroys drafts.
- Sensitive apps (banking pattern): warn before timeout — "Session expires in 2:00 — Stay signed in."
- Remember-me default ON for consumer products, OFF for shared-device contexts.

### Logout
- One action from the account menu, no confirmation dialog (cheaply reversible), land on login/marketing page, clear all client state and tokens.
- "Log out of all devices" lives in security settings alongside a session list (device, location, last active, revoke) — not inside the logout action.

## Layout & Structure

- **One field per screen** in progressive flows; never email + password + confirm + name stacked at signup.
- **Vertical order**: SSO block → "or" divider → email field → Continue. All above the fold on a 667pt-class viewport.
- **Buttons/fields ≥44pt (iOS) / 48dp (Android) tall**, full-width on mobile, single column always.
- **Autofill attributes are mandatory**:
  - `type=email` + `autocomplete=email` (or `username`)
  - `autocomplete=current-password` / `new-password`
  - `autocomplete=one-time-code` + `inputmode=numeric` on OTP fields (enables SMS/email code autofill on iOS and Android)
- **Passwords**: never disable paste; show/hide toggle; live requirements checklist, not a post-submit error dump; accept length ≥64; no forced periodic rotation.
- **Errors**: inline under the offending field, specific and safe.
  - Two-step flow may say "Incorrect password" (email already validated).
  - Single-screen login says "Email or password is incorrect."
  - Escalation under attack: rate-limit → CAPTCHA → temporary lockout with reset prompt.
- **Identity persistence**: show it on every step — "Signing in as j***@x.com · Not you?"
- **Loading**: submit buttons show an inline spinner and disable on tap; never allow double-submit of OTP or signup.
- **Legal**: "By continuing you agree…" small text below the CTA; no checkbox unless a jurisdiction requires it.

### Error copy patterns

| Instead of | Write |
|---|---|
| "Authentication failed" | "Email or password is incorrect. Forgot password?" |
| "Invalid token" | "This link has expired. We can send a new one." |
| "User not found" | "If an account exists for X, we've sent a link." |
| "Too many attempts" | "Too many tries. Wait 5 minutes or reset your password." |

## Quick Reference

| Situation | Pattern |
|---|---|
| Greenfield auth surface | Combined email-first flow; passkeys with code fallback; SSO on top |
| Consumer mobile app | Apple + Google SSO first, email code second, password optional/absent |
| B2B SaaS | Google SSO + email; corporate domain → auto-route to SAML |
| Unknown email typed at "login" | Route silently into signup — no "no account found" error |
| Magic link opened on another device | The paired 6-digit code covers it |
| Password reset request | Neutral confirmation; never reveal account existence |
| 2FA enrollment | Backup codes mandatory; TOTP over SMS; trust-device option |
| Session died mid-form | Re-auth modal over the page; preserve and replay the action |
| OAuth email collides with password account | Verify then link, or prompt to log in and connect — never duplicate |
| Sensitive action inside a session | Step-up re-authentication |
| Logout | Immediate, no confirm; all-device revoke lives in settings |
| Verification email | Lands the user logged-in at their original destination |

## Anti-Patterns

| Anti-pattern | Fix |
|---|---|
| Separate Sign up / Log in tabs users pick wrong | One email field; server routes to the right flow |
| Password + confirm + name + phone at signup | Email (or SSO) only; show/hide replaces confirm |
| Blocking all product use until email verified | Let them in; gate only sensitive actions |
| "No account found with that email" | Neutral copy or silent route-to-signup |
| Magic link as the ONLY credential | Send a 6-digit code too; links strand cross-device users |
| SMS as default 2FA | Passkey/TOTP first; SMS as last-resort fallback |
| Disabling paste in password/OTP fields | Never — it breaks password managers and autofill |
| Session expiry → hard redirect, draft lost | Overlay re-auth, preserve state, replay the action |
| Logout confirmation dialog | Just log out |
| Verification link → "Success, please log in" | Land logged-in at the original destination |
| 5+ social login buttons | 1–3; more measurably lowers completion |
| Password rules revealed only via rejection | Live checklist while typing |
| Forced periodic password rotation | Drop it; push passkeys instead |
| CAPTCHA on every login by default | Only after rate-limit signals fire |
| Auth dumps user on a generic dashboard | Carry the deep link through every redirect |
| Missing autocomplete attributes | Full autofill markup; test with a password manager |

Success metrics: entry-flow completion rate and access-related support-ticket rate — not the count of security features shipped.
