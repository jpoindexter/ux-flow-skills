---
name: flow-checkout
description: "Use when designing, building, or reviewing any checkout, cart, payment, or purchase flow — e-commerce carts, one-off purchases, donation forms, in-app buying. Also use when users report cart abandonment, payment errors losing form state, forced account creation complaints, or confusion about order status after paying."
---

# Checkout & Payment Flows

## Overview
Checkout is a funnel where every field, click, and surprise costs conversion — average cart abandonment sits near 70%, and roughly 1 in 4 abandoners cite forced account creation or a too-long checkout. The proven flow minimizes steps, defers everything deferrable, and never loses user state.

## When to Use
- Building any cart → payment → confirmation sequence, web or native
- Adding express payment: Apple Pay, Google Pay, Shop Pay, PayPal
- Reviewing an existing checkout for abandonment causes
- Designing error handling for declined or failed payments
- Adding saved payment methods or one-click reorder for returning customers
- **Not for:** subscription pricing and paywall flows (see `flow-paywall`)
- **Not for:** OS-controlled in-app purchase sheets — StoreKit/Play Billing render their own UI; you only choose the trigger point and the screen before it

## The Proven Flow

1. **Cart**
   - Line items: thumbnail, name, variant, quantity stepper, per-item remove, line price.
   - Removing an item offers an inline undo instead of a confirm dialog.
   - Order summary — subtotal, estimated shipping, estimated tax, total — pinned visible.
   - Surprise costs at the last step are the #1 abandonment cause (~48% in Baymard's data), so estimate shipping and tax from the cart onward, from geolocation or a zip prompt.
   - Primary action: **"Checkout"** — one button, high contrast, above the fold on mobile.
   - Express pay buttons (Apple Pay / Shop Pay / PayPal) directly above or beside it, so ready buyers skip the entire form. Shopify carts and Amazon's "Buy Now" both prove this placement.
   - Promo code field collapsed behind a "Have a code?" link. An open field invites users to leave the flow and hunt for coupons.
   - Free-shipping threshold nudge, if you have one: "Add $12 for free shipping" with a progress bar — honest and proven to raise order value.

2. **Guest gate (only if you must have one at all)**
   - Never a wall. The default path is guest checkout with an email field only.
   - "Sign in" is the secondary link, not the primary button.
   - Stripe Checkout collects only email; Shopify's Shop detects returning users by email and offers one-tap sign-in with saved details.
   - Account creation is offered *after* purchase, on the confirmation screen ("Save your info for next time — add a password"). It converts far better there because the user has nothing left to lose.

3. **Shipping**
   - Email first — this enables abandonment-recovery email even if the user quits on the very next screen.
   - Name as a single full-name field, not first/last.
   - Address with **autocomplete** (Google Places or equivalent): user types 3–5 characters of street, picks from a dropdown, and city/state/zip/country auto-fill.
   - Manual entry always remains available — autocomplete fails on new buildings and rural addresses.
   - Shipping method as a radio list with price and a concrete date range ("Arrives Thu, Jul 16 — $4.99"), cheapest or free option preselected.
   - Primary action: "Continue to payment."

4. **Payment**
   - Express pay buttons repeated at the top, with an "or pay with card" divider below — Stripe Checkout's exact layout.
   - Card form: one card-number field with inline brand detection, expiry as a single MM/YY field, CVC, and postal code only if the processor requires AVS. Four fields max.
   - Returning signed-in users see saved cards (brand + last 4) as the default, with "Use a different card" below — Amazon's core one-click mechanic.
   - Billing address defaults to "Same as shipping" (checked); the billing form appears only when unchecked.
   - Trust signals near the pay button — lock icon, "Secured by Stripe" — not banner-sized badge walls.
   - Primary action is the money button: **"Pay $48.20"** — the exact amount on the button, never a bare "Submit" or "Continue."

5. **Review**
   - A distinct review screen only for high-consideration orders: travel, B2B, roughly >$500.
   - Otherwise fold review into the payment screen as an editable summary — Stripe and Shopify both do this, because an extra screen is an extra abandonment point.
   - Every summary section gets an "Edit" link that returns the user *without wiping other entered data*.

6. **Processing**
   - Disable the pay button on tap; swap the label to a spinner + "Processing…".
   - Never allow double-submit; pair the disabled button with server-side idempotency keys.
   - Never navigate away until the processor responds.
   - 3-D Secure / SCA challenges open in a modal or inline frame — avoid full redirects that lose context.

7. **Confirmation** — see the anatomy section below. The order is confirmed on screen *and* by email within a minute.

## Express Pay Placement Rules
- Show express buttons in **two places**: the cart (skip everything) and the top of the payment screen (skip the card form). Detect availability first — render Apple Pay only when `canMakePayments` is true; a dead button erodes trust.
- Order the buttons by your audience's actual wallet share; 2–3 wallets max. Five wallet buttons is a choice-paralysis wall.
- Express pay returns shipping address and contact from the wallet sheet — don't re-ask for anything the sheet already provided.
- The wallet sheet shows the final total; make sure shipping and tax are computed *before* invoking it, or the user approves a number that then changes.
- Keep the card form visible below a divider ("or pay with card") — express-only checkouts strand users whose wallet fails.

## Layout & Structure
- **Single-column form.** Multi-column checkout forms consistently test worse (Baymard Institute).
- **Order summary:** sticky right sidebar on desktop; collapsible accordion at the top on mobile — collapsed by default, always showing the total.
- **One primary action per screen.** Back, edit, and sign-in are links or ghost buttons, never competing filled buttons.
- **Field budget:** guest checkout in ≤8 interactions is achievable — email, name, address autocomplete, card ×3. Every added field must justify itself.
- **Optional fields say why:** phone number labeled "for delivery updates only," marked optional.
- **Progress indicator** for multi-step checkouts: 3–4 labeled steps ("Shipping → Payment → Review"), current step highlighted, completed steps clickable to go back.
- **Persist state through everything:** back button, refresh, validation error, card decline, 3DS failure. Losing a filled form after a decline is the single most rage-inducing checkout bug.
- **Inline validation on blur,** not on submit. Errors sit next to the field, red text + icon (never color alone), and state the fix: "Card number is 15 digits — Amex has 15, most cards have 16."
- **Tax display is regional:** US audiences expect tax added at checkout; EU/UK audiences expect VAT-inclusive prices throughout. Showing exclusive prices to EU users reads as a bait-and-switch and can be unlawful.
- **Mobile keyboards:** `inputmode="numeric"` for card, zip, and phone fields.
- **Autofill:** correct `autocomplete` attributes on every field (`cc-number`, `cc-exp`, `postal-code`, `shipping address-line1`) so browser and OS autofill work — this is free conversion.
- **Accessibility:** every field has a real `<label>`; errors are announced via `aria-live` and linked with `aria-describedby`; the pay button's accessible name includes the amount; touch targets ≥44px.
- **Native apps:** Apple Pay via `PKPaymentButton` at Apple's mandated sizes and styles — don't restyle it. Same rule for the Google Pay brand button.
- **Stability:** the payment screen must never reflow under the user's finger — a layout shift that moves the pay button is a misclick generator.
- **Currency:** detect and display the buyer's local currency with the code where ambiguous ("$" means five different currencies); never switch currency between cart and charge.
- **Order notes / gift options:** collapsed behind links, like promo codes — present for those who need them, invisible friction for everyone else.
- **Instrument the funnel:** fire events at cart-view, checkout-start, shipping-complete, payment-attempt, payment-error (with reason), and purchase. Without per-step drop-off data you cannot diagnose abandonment.

## Error Recovery Mid-Payment
- **Card declined:** stay on the payment screen, all fields intact, card fields highlighted. Say what to do, not processor jargon: "Your card was declined. Try another card or contact your bank." Offer express-pay alternatives right there.
- **3DS/SCA abandoned or failed:** return to the payment screen with state intact, a one-line explanation, and a retry button.
- **Network timeout after submit:** show "Checking your payment…" and poll. Never report "failed" when the charge may have succeeded. If genuinely unknown, say so: "We're confirming your payment — check your email in a few minutes before retrying." This prevents double-charges.
- **Item out of stock at payment time:** name the item, remove or adjust it, recalculate the total, and let the user proceed with the rest. Never dump them back to an emptied cart.
- **Expired session:** re-authenticate inline; restore the cart and form exactly.
- **Address validation failure:** show the suggested correction side-by-side with what they typed — "Use suggested / Keep what I entered." Never silently rewrite the address.
- **Price or tax changed since cart:** flag the change explicitly before charging; never charge a different amount than the button showed.

## Order Confirmation Anatomy
1. Success signal first: checkmark, "Order confirmed," order number (copyable).
2. What happens next: delivery estimate, "Confirmation sent to jane@…".
3. Order recap: items, shipping address, payment method (last 4 digits), total.
4. Actions in priority order: track order → create account (for guests) → continue shopping.
5. Support escape hatch: "Problem with your order?" link.

Amazon and Shopify both follow this hierarchy. A bare "Thank you" with no order number reads as a failed transaction and generates support tickets and duplicate orders.

## Quick Reference

| Situation | Pattern |
|---|---|
| User has Apple Pay / Shop Pay | Express buttons above the card form, on cart AND payment screens |
| Returning customer | Email-first detection → offer sign-in + saved card, never require it |
| Guest user | Guest checkout default; account offer on the confirmation screen |
| Address entry | Autocomplete after 3–5 chars; manual fallback always visible |
| Card entry | 4 fields max; inline brand detection; exact amount on the pay button |
| Payment declined | Stay on screen, keep state, plain-language fix, offer alternatives |
| High-value / B2B order | Distinct review step; otherwise an inline editable summary |
| Mobile order summary | Collapsed accordion at top, total always visible |
| Promo codes | Collapsed behind a link, never an open field |
| Charge state unknown | "Confirming…" + poll; never a premature "failed" |
| Out-of-stock at payment | Adjust the line item inline, recalculate, proceed |
| Double-click on pay | Button disabled on first tap + idempotency key server-side |
| EU/UK audience | VAT-inclusive prices end to end |
| Removing a cart item | Inline undo, not a confirm dialog |

## Anti-Patterns

| Anti-pattern | Why it breaks | Fix |
|---|---|---|
| Account-creation wall before checkout | ~25% of abandoners cite it directly | Guest default; offer an account post-purchase |
| Coupon field wide open | Users leave to hunt codes; many never return | Collapse behind a "Have a code?" link |
| Surprise shipping/tax at the final step | Top cited abandonment reason (~48%) | Show the estimated total from the cart onward |
| "Submit" / "Continue" on the money button | Ambiguity at the moment of maximum anxiety | "Pay $48.20" with the real amount |
| Wiping form state on error or decline | User must re-type card + address; most won't | Persist all fields through every error path |
| Express pay below the card form | Ready buyers forced through 8 fields first | Express pay first, "or pay with card" divider below |
| First/last/street/apt/city/state/zip all separate and required | 10+ fields of friction | Address autocomplete; a single full-name field |
| Redirect to a third-party page that looks unrelated | Trust collapse mid-payment | Embedded, branded payment (Stripe Elements/Checkout) |
| Double-charge on double-click | Support tickets and chargebacks | Disable on first tap; idempotency keys |
| Generic error: "Payment error (code 402)" | User can't act on it | State the cause and the next step in plain words |
| Pre-checked upsells or insurance in the cart | "Sneak into basket" dark pattern; illegal in the EU | All add-ons opt-in, clearly priced |
| Confirmation page without an order number | Reads as failure; triggers re-orders and support load | Full confirmation anatomy above |
| Validation only on submit, errors listed at the top | User hunts for which field is wrong | Inline on-blur validation beside each field |
| Charging a different amount than the button showed | Chargebacks and lost trust | Re-confirm any price change before charging |
| Required phone number with no stated reason | Perceived data grab; field-level abandonment | Optional, labeled "for delivery updates only" |
| Cart emptied on session expiry | Return visitors start from zero | Persist carts server-side (signed-in) or in local storage |
| Shipping options without delivery dates | User can't trade cost against time | "Arrives Thu, Jul 16" on every option |
| Express-pay-only checkout | Wallet failure strands the purchase | Card form always available below a divider |
