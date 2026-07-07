---
name: flow-paywall
description: "Use when designing, building, or reviewing pricing pages, paywalls, free trials, subscription upgrade/downgrade flows, cancellation flows, or win-back campaigns. Also use when users report trial-to-paid conversion problems, surprise-charge complaints, App Store subscription rejections, or when auditing for dark patterns and FTC click-to-cancel compliance."
---

# Subscription, Pricing & Paywall Flows

## Overview
A paywall converts when the user hits it at a moment of felt value, understands exactly what they pay and when, and trusts they can leave. Extraction tricks — hidden toggles, roach-motel cancellation — raise short-term conversion while destroying LTV, app-store standing, and, post FTC click-to-cancel, legality.

## When to Use
- Building a pricing page, in-app paywall screen, trial start, or plan-change flow
- Placing upgrade prompts inside a freemium product
- Designing cancellation, pause, or win-back flows
- Reviewing subscription UX for dark patterns or App Store Guideline 3.1 compliance
- **Not for:** one-off purchase checkout mechanics (see `flow-checkout`)
- **Not for:** usage-based enterprise billing negotiated by sales

## The Proven Flow

1. **Pricing page**
   - 3 tiers (2–4 acceptable; 5+ paralyzes). Middle tier visually highlighted with a "Most popular" badge — it anchors choice and is where most buyers land (Notion, Spotify, essentially every major SaaS).
   - Annual/monthly toggle above the cards, **annual preselected**. Savings stated as a concrete number ("Save $24/yr" or "2 months free"), and the annual card shows both figures: "$8/mo, billed $96 annually."
   - Each card: plan name, price, one-line who-it's-for, 5–8 *differentiating* features (not the full matrix), one CTA. Free tier CTA: "Get started." Paid CTAs: "Start free trial" or "Upgrade."
   - Per-seat pricing says so on the card ("$12 per editor / month") — a flat-looking price that multiplies at checkout reads as a bait-and-switch.
   - Signed-in existing subscribers see their current plan marked ("Current plan") with only upgrade/downgrade CTAs active.
   - Full comparison table below the fold, with the tier columns sticky on scroll so long tables stay comparable.
   - FAQ answers the three fear questions: can I cancel anytime, what happens when the trial ends, do you prorate.
   - An enterprise tier uses "Contact sales" — never a fake price. Mixing self-serve numbers with one "Custom" column is fine; a page of all-"Contact us" is not a pricing page.
   - Localize currency where you sell; a $-only page in the EU depresses conversion and VAT-inclusive display is expected there.

2. **Trial start — pick the model deliberately**
   - **Card-upfront** (Superhuman, most B2B SaaS): fewer trial starts but roughly 3–5× higher trial→paid conversion; filters for intent. Requires: exact charge date on the consent screen ("Your trial ends July 21 — we'll email you 3 days before"), a reminder email that actually sends, and one-tap cancel.
   - **No-card** (Spotify's freemium ramp, most PLG tools): maximal top-of-funnel; conversion happens later via in-product upgrade prompts. Requires a real free tier or a graceful trial-end state — never a data hostage.
   - Rule of thumb: no-card when the product proves value inside one session; card-upfront when value takes days and you can afford a smaller, warmer funnel.
   - Design the trial-end state before launch: what the user sees on day 15 (locked features grayed with upgrade CTA, data intact and exportable) determines whether lapsed trials ever convert.
   - During the trial, show remaining time passively — "9 days left in trial" in the nav — not as recurring interrupts.

3. **In-app paywall screen (mobile)**
   - One screen: value proposition as 3–4 benefit bullets (outcomes, not feature names), plan selector with the preferred option preselected, price + renewal terms in plain sight, primary CTA.
   - **Restore Purchases** and Terms/Privacy links visible. Apple requires price, duration, and auto-renewal terms visible before purchase — hiding them is a Guideline 3.1 rejection.
   - Soft paywalls must have a dismiss "X" findable within ~2 seconds. Delayed or camouflaged close buttons are a documented dark pattern and a review-team flag.
   - Timing: show the paywall after onboarding demonstrates value (post-aha), and again at feature-gate moments — not as the app's first screen unless the model is deliberately hard-paywall.
   - Use the platform's native purchase sheet (StoreKit / Play Billing) for the transaction itself; the paywall screen is yours, the payment UI is theirs.

4. **Soft vs hard paywall**
   - **Soft** (metered or feature-gated): the free tier persists; the wall appears on premium actions. Use when network effects or habit formation need free users — Notion, Figma, Spotify.
   - **Hard** (nothing without paying or trialing): use when serving free users costs real money or the audience is professional — Superhuman, Bloomberg. Hard paywall + trial beats hard paywall alone.
   - **Metered hybrid** (N free uses/month) fits content and AI products. Show the meter *before* it runs out: "2 of 3 free reports used this month."

5. **Upgrade prompts at value moments**
   - Trigger on the action that proves need, never on a timer: hitting the block/file/seat limit, previewing a premium feature, exporting, inviting a third collaborator. Notion's block limit, Figma's editor seats, and Spotify's skip limit all monetize exactly this way.
   - Anatomy: name what just happened ("You've reached 3 boards"), one-line benefit of upgrading, CTA + neutral "Maybe later."
   - Frequency cap: the same prompt at most ~once per session. Never modal-interrupt mid-typing or mid-playback.
   - Instrument each trigger separately — prompt-shown → upgrade-started → paid per trigger tells you which value moments actually convert.

6. **Upgrade / downgrade**
   - Upgrades apply instantly, prorated, with the prorated charge shown *before* confirm.
   - Downgrades apply at period end, with an explicit statement of what's lost: "You'll lose version history over 30 days on Aug 1."
   - Never silently delete data on downgrade — a read-only lock beats deletion every time.

7. **Cancellation**
   - Reachable in ≤3 clicks/taps from account settings, fully self-serve, in the same medium as signup. The FTC click-to-cancel rule requires canceling to be as easy as subscribing — no mandatory chat, no phone call, no "contact us."
   - Flow: visible Cancel button (not gray-on-gray, not buried) → optional one-screen reason survey (skippable) → **one** save offer max → confirm → confirmation screen + email stating the access-until date.
   - Save offers ranked by performance and user goodwill: pause 1–3 months > temporary discount > downgrade to free. Pause converts best and is least resented.
   - Accepted pause states the auto-resume date on confirm and sends a reminder email ~3 days before billing resumes — a silent resume is a hidden-charge complaint waiting to happen.
   - State the refund policy at the confirm step ("No further charges; no refund for the current period" or whatever is true) so nobody has to ask support.
   - iOS subscriptions: deep-link straight to the App Store manage-subscriptions sheet. Never build a fake in-app cancel that dead-ends.

8. **Dunning — failed renewal payments (involuntary churn)**
   - Involuntary churn is typically 20–40% of all churn; treat it as a flow, not an edge case.
   - Retry the charge on a decaying schedule (e.g., day 1, 3, 7) while access continues in a grace period.
   - Notify in two channels: email ("Your payment failed — update your card") plus a persistent in-app banner deep-linking to the payment method form.
   - Only after retries exhaust does the account downgrade — to read-only or free, never to deleted.
   - Never treat a failed card as a cancellation intent; no save offers, just the fastest possible path to updating the card.

9. **Win-back**
   - At cancellation confirm: "You'll have access until Aug 12" — and nothing pushy.
   - Post-expiry: email at ~1 week and ~1 month with a concrete offer (discount, or "here's what's new since you left"). Max 2–3 touches; honor unsubscribes immediately.
   - Reactivation is one click back into the old plan with prior data intact — never force re-onboarding.
   - Win-back offers must not undercut loyal subscribers visibly — "new customers only" pricing shown to an existing subscriber breeds cancel-to-rebuy gaming and resentment.

## Layout & Structure
- **Pricing cards:** equal width, feature rows aligned across cards, highlighted tier ~5–10% larger or elevated with border/shadow + badge. Price is the largest text on the card. One full-width CTA per card.
- **Good–Better–Best structure:** the middle tier's features must make it obviously best-value. If analytics show everyone buys the cheapest tier, the middle tier's differentiators are wrong — fix the packaging, not the buttons.
- **Paywall screen (mobile):** value bullets top, plan selector middle, CTA fixed at bottom above the home indicator. Renewal terms ("$79.99/yr, renews annually, cancel anytime") within one line of the CTA — never footnote-buried.
- **Numbers everywhere:** "Save $24" beats "Save 20%." Show annual total AND monthly equivalent. Show the exact trial-end date, not "after 14 days."
- **Trust block near the CTA:** cancel-anytime line, money-back guarantee if real, payment logos. One line, not a badge wall.
- **Price presentation:** charm pricing ($9/$29/$99) is the SaaS default; round pricing signals premium (Superhuman's flat $30). Pick one system. Typical inter-tier ratio is ~2.5–4×.
- **Plan-change screens:** always show current plan, target plan, effective date, and the exact next charge amount before the confirm button.
- **Accessibility:** the highlighted tier must not rely on color alone — badge text plus elevation; the annual/monthly toggle is a real switch/tab control with visible state, not a styled div.
- **Billing surface:** current plan, renewal date, next charge amount, payment method, and invoice history live on one billing page — users judging "should I cancel?" need all five in one glance.
- **Social proof placement:** logos or a single customer quote *below* the pricing cards, never between the toggle and the cards — proof supports the decision; it must not interrupt price comparison.

## Compliance Floor (non-negotiable)
- **FTC click-to-cancel (US):** cancel as easy as signup, same medium, no forced retention calls or chats.
- **Apple Guideline 3.1:** price, duration, and auto-renewal terms visible pre-purchase; Restore Purchases present; external purchase links only where regionally entitled.
- **EU consumer law / DSA:** no fake countdowns, no confirmshaming, no pre-ticked add-ons; VAT-inclusive pricing displayed.
- **Google Play:** parallel requirements — clear pricing and renewal terms pre-purchase, cancellation via Play's subscription center, no misleading trial framing.
- **Auto-renewal disclosure:** the exact renewal price and date appear at consent and in the receipt email — not only in the ToS.
- Log consent: store what price/terms the user saw at subscribe time; it settles disputes and refund requests.

## Quick Reference

| Situation | Pattern |
|---|---|
| Choosing tier count | 3 tiers, middle highlighted "Most popular" |
| Annual vs monthly | Toggle, annual default, dollar savings + both prices shown |
| Product proves value in one session | No-card trial, or freemium + soft paywall |
| Value takes days; warm audience | Card-upfront trial + end-date consent + reminder email |
| Free user hits a limit | Contextual upgrade prompt naming the limit, ≤1×/session |
| AI/content product | Metered free tier with a visible usage meter |
| User clicks Cancel | ≤3 clicks, self-serve, one save offer (pause first), access-until date |
| User downgrades | Apply at period end; state losses; never delete data |
| User upgrades | Instant, prorated, prorated amount shown pre-confirm |
| iOS paywall | Price + renewal terms + Restore Purchases + ToS visible; native manage-subs for cancel |
| Trial ending, card on file | Reminder email 3+ days out, with a cancel link in it |
| Churned user | 2–3 win-back emails; one-click reactivation to old plan |
| Enterprise tier | "Contact sales" — never a fake price |
| Trial in progress | Passive "9 days left" indicator, no interrupts |
| Trial ended, no conversion | Features locked + grayed, data intact and exportable |
| Paywall timing (mobile) | Post-aha in onboarding + at feature gates, not app-open |
| Renewal payment fails | Dunning: decaying retries + grace period + update-card banner |
| Pause offer accepted | Auto-resume date on confirm + reminder email before resume |
| Existing subscriber views pricing | Current plan marked; only change-plan CTAs active |

## Anti-Patterns

| Anti-pattern | Why it breaks | Fix |
|---|---|---|
| Roach motel: sign up online, cancel by phone | FTC click-to-cancel violation; chargebacks; rage | Self-serve cancel, same medium, ≤3 clicks |
| Hidden auto-renewal / silent trial→paid charge | Refund storms; App Store rejection; legal exposure | Exact charge date at consent + reminder email |
| Annual price shown as monthly with tiny "billed annually" | Perceived bait-and-switch at checkout | Both numbers adjacent, equal visual weight |
| Confirmshaming decline copy ("No thanks, I hate saving money") | Documented dark pattern; erodes trust | Neutral decline: "Maybe later" / "No thanks" |
| 4+ sequential save-offer screens on cancel | Roach-motel variant; FTC scrutiny | One save offer, then confirm |
| Hard paywall before any value shown, no trial | Nothing to want yet; near-zero conversion | Trial, feature preview, or metered free tier first |
| Upgrade nag on a timer, context-free | Trains banner blindness toward all prompts | Trigger only at limit and feature moments |
| Deleting data when a plan lapses | Data hostage; reputational damage | Read-only lock; export always available |
| Fake urgency (looping countdown timers) | Deceptive countdown = FTC/EU DSA dark pattern | Real deadlines only, or none |
| Pre-selecting the most expensive plan | Sneaking; refunds and distrust | Preselect the honest recommendation (middle tier) |
| Burying Restore Purchases | App Store rejection; locked-out paying users | Visible on every paywall screen |
| 40-row feature matrix as the only pricing info | Choice paralysis | 5–8 differentiators per card; matrix below fold |
| Paywall "X" that appears after a delay | Manipulative friction; review-team flag | Dismiss visible immediately on soft paywalls |
| Silent price increase on renewal | Refunds, chargebacks, regulator attention | Advance notice email with the new price and a cancel link |
| Treating failed payments as churn | 20–40% of churn is involuntary | Dunning flow: retries + grace period + update-card path |
| "New customers only" deals shown to subscribers | Cancel-to-rebuy gaming; loyalty resentment | Match or hide promotional pricing for existing users |
| Cancel button styled to be invisible (gray-on-gray) | Manipulative friction; FTC exposure | Cancel styled like any other secondary action |
| Grandfathered pricing revoked without notice | Highest-loyalty users churn angriest | Honor it, or migrate with long notice and a better offer |
| Trial countdown via daily push notifications | Anxiety marketing; notification-permission revokes | Passive in-app indicator + one email near the end |
| Proration math hidden until the invoice | Perceived overcharge; support load | Exact prorated amount on the confirm screen |
