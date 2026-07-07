---
name: flow-forms
description: "Use when designing, building, or reviewing any form, signup, checkout, settings page, or multi-step wizard — or when users abandon forms, report confusing errors, or lose data navigating back. Also fires when deciding field count, label placement, validation timing, required-field marking, or whether to split a form into steps."
---

# Forms & Multi-Step Wizards

## Overview
Every field costs conversion; every ambiguity costs trust. A form is a conversation with one question at a time, not a database schema rendered to screen.

## When to Use
- Any data-entry surface: signup, checkout, onboarding, settings, application forms, filters-as-forms
- Splitting a long form into a wizard, or deciding whether to
- Reviewing a form with drop-off, error-rate, or support-ticket problems
- **NOT for:** search boxes and single-input inline edits — no form chrome needed
- **NOT for:** command palettes and query builders — interaction patterns, not form patterns

## The Proven Flow

1. **Entry screen states the deal.** Before field one appears: what completing this gets the user, how long it takes, what they'll need (documents, card number, ID). GOV.UK "start pages" list exactly this. For wizards, set scope upfront — "About 5 minutes" beats "Step 1 of 9" for perceived effort. Primary action: a single prominent "Start" button.
2. **One topic per step.** Each wizard screen asks about one thing: "Your address," "Payment method," "Delivery options." GOV.UK's "One thing per page" pattern measurably cuts error rates and support calls on citizen-facing services. Never mix identity fields with payment fields on one step. A step can be a single question — that's fine (Typeform's entire model).
3. **Fields render single-column with top-aligned labels.** The user completes the step in one vertical scan line, top to bottom. Primary action sits at the bottom of the column, full-width or wide, labeled with the verb and outcome ("Continue," "Pay €49") — never "Submit."
4. **Validation fires on blur.** User leaves a field → validate that field → show the inline error directly below it. Once a field has errored, revalidate on every keystroke so the error clears the instant it's fixed. Never validate while the user is still typing into a fresh field — premature "invalid email" after two characters is the most common validation bug. Stripe Checkout's card fields are the reference implementation: silent while typing, error on blur, live-clear on correction.
5. **Submit surfaces all errors at once.** On failed submit: preserve every entered value, move focus to the first errored field, and for forms longer than one viewport show an error summary at the top linking to each bad field (GOV.UK error summary — also what screen readers announce). Never reveal errors one-per-submit; users must see the full repair job.
6. **Wizard back is free.** Back returns to the previous step with all of its data intact. Data loss on back-navigation is the #1 wizard killer. Persist step data on every step transition — not only at final submit. Browser back should behave like UI back, not eject the user.
7. **Review step before commit.** Any wizard with consequences (payment, application, publish) ends with a read-only summary of all answers, each row with a "Change" link that jumps to that step and returns to review afterward. GOV.UK "Check your answers"; every serious checkout's order-review step.
8. **Confirmation screen closes the loop.** What just happened, a reference number, what happens next and when, and the single most likely next action. Never dead-end on a bare "Success!" — the moment after completion is peak engagement.

## Layout & Structure

### Column and label rules
- **Single column, always.** Baymard's checkout eye-tracking: multi-column forms cause skipped fields, wrong completion order, and misinterpreted relationships. Only exception: tightly-coupled short fields inline (City / Postcode, Expiry / CVC, First / Last if you must split names).
- **Labels above fields**, left-aligned, 4–8px above the input. Not left-of-field (breaks the vertical scan line and wraps badly on mobile). Never placeholder-as-label — it vanishes on focus, fails recall mid-entry, fails WCAG, and breaks autofill review.
- **Placeholders are for format hints only** ("DD/MM/YYYY") — and visible hint text below the label beats even that, since it survives focus.
- **Field width signals expected answer length:** postcode short, address line long, CVC tiny. Uniform full-width inputs delete a useful cue.
- **One primary button per screen.** Secondary actions (Back, Save for later) visually quieter, placed left of or below the primary. Back is a link or ghost button, never a twin of Continue.

### Field discipline
- **Every field must justify itself against conversion cost.** Baymard: the average checkout can cut roughly half its fields. Defaults to cut: "Confirm email" (echo the typed value prominently instead), "Confirm password" (show-password toggle instead), separate First/Last name where a single "Full name" is legally sufficient, phone number unless you will actually call, company/title fields on consumer products.
- **Mark optional, not required.** When most fields are required — which should be true — append "(optional)" to the label of the exceptions. Asterisks force users to learn a legend and add noise to every label. If most of your fields are optional, the form is too long.
- **Progressive profiling:** anything not needed to complete *this* transaction moves to a later, contextual ask.
- **Never pre-check** consent, marketing, or add-on checkboxes. Pre-select only genuinely most-common neutral options (country from locale, standard shipping).

### Input mechanics (platform-specific where noted)
- **Web:** correct `type` (`email`, `tel`, `url`), `inputmode="numeric"` for codes, and `autocomplete` tokens on every field (`given-name`, `postal-code`, `cc-number`, `one-time-code`). Browser autofill is the single biggest form-speed win available; breaking it with custom inputs is self-sabotage.
- **iOS/Android:** matching keyboard type per field; iOS inputs ≥16px font size or Safari zooms on focus.
- **Touch targets ≥44px tall** on mobile (Apple HIG floor; Android 48dp).
- **Radio group / segmented control for 2–4 options** — a dropdown hides options that would fit on screen and costs an extra interaction.
- **Autocomplete combo box for huge lists** (country, bank) — never a 200-item dropdown.
- **Date of birth:** three plain inputs (DD / MM / YYYY) or one masked field. Never a calendar picker — nobody scrolls to 1987 — and never scroll wheels on web.
- **Phone/card numbers:** auto-format as typed (spaces in card numbers), accept pasted input with any formatting, never reject a value you could normalize (Postel's law at the field level).

### When to split into a wizard
- Split when: >1 topic, >~7 fields, later questions depend on earlier answers (branching), or the form needs a save-and-resume lifecycle.
- Stay single-page when: ≤7 fields on one topic (login, simple contact), or expert users repeat the form daily (data-entry staff want one dense page, not steps).
- Branching earns wizards their keep: ask the discriminating question early ("Business or personal?") and skip irrelevant steps entirely — never show fields the user's earlier answers made moot.
- Never split to make each screen "feel simple" while tripling total taps — steps must map to real topic boundaries.

### Wizard mechanics
- **Progress indicator for ≥3 steps:** labeled steps ("2 of 4 — Payment"), current step visually distinct, completed steps clickable to revisit where safe. Unlabeled numbered dots help nobody.
- **Step count honesty:** conditional steps shouldn't make the total jump around; show ranges or recount silently, never "step 3 of 7" becoming "3 of 11."
- **Save-and-resume for any form >5 minutes** or requiring documents: autosave per step, an explicit "Save and finish later" exit, and a return path (emailed link or account draft). Government applications and Typeform respondent resume prove the pattern.
- **Session expiry mid-form:** re-authenticate in place and return the user to the intact form. A login redirect that discards 20 minutes of entry is a top rage trigger.

### Error message anatomy (see also flow-errors)
- State what's wrong in the user's terms + how to fix it: "Card number must be 16 digits — you entered 15." Never "Invalid input," never the validation rule name, never blame framing.
- Error text sits directly below its field, colored + icon-marked (never color alone), and is programmatically linked to the input.

### Per-field playbook
| Field | Rules |
|---|---|
| Email | `type=email`, `autocomplete=email`; no confirm field; trim whitespace before validating; don't reject `+` aliases |
| Password (create) | Show-password toggle; live requirements checklist that ticks off as met; min 8, max ≥64; always allow paste |
| Password (login) | Just the field + "Forgot password?" link; never list requirements at login |
| One-time code | `inputmode=numeric`, `autocomplete=one-time-code`; accept full-code paste; auto-advance/auto-submit on last digit |
| Name | Single "Full name" where legally possible; no character restrictions — apostrophes, hyphens, unicode all valid |
| Address | Autocomplete/lookup service with manual-entry fallback always available; correct `autocomplete` tokens per line |
| Card number | Auto-space in groups of 4; detect brand from first digits and show its logo; `autocomplete=cc-number` |
| Country | Autocomplete combo box, defaulted from locale |
| Date, memorable (DOB) | Three plain inputs or masked field — never a calendar |
| Date, scheduling (delivery day) | Calendar picker is correct here — near-future picking is spatial |
| Quantity / small number | Stepper or plain numeric input, not a dropdown of 1–10 |

### Accessibility (non-negotiable)
- Every input has a programmatically associated label (`<label for>` or platform equivalent); hint text linked via `aria-describedby`.
- On failed submit, the error summary receives focus and announces (`role="alert"`); each inline error is linked to its field via `aria-describedby`.
- Related radios/checkboxes grouped in `<fieldset>` + `<legend>` ("Delivery method") so the question is announced with each option.
- Focus order matches visual order; focus visible on every control; never remove outline without replacement.
- 4.5:1 contrast on labels, values, and error text; error state never conveyed by color alone.
- Session time limits: warn before expiry and allow extension (WCAG 2.2.1) — silent expiry that eats a form fails both accessibility and trust.

### Measuring form health
- Instrument per-field: drop-off (last field touched before abandon), error rate, correction time. The worst field is usually removable.
- Track resubmission count (validation churn), total time-to-complete, and back-navigation frequency per wizard step.
- A step where >20% of users go back is asking its question too early or unclearly.

## Quick Reference

| Situation | Pattern |
|---|---|
| Form >7 fields or >1 topic | Split into wizard, one topic per step |
| When to validate | On blur; live-revalidate once errored; never mid-first-typing |
| Error placement | Inline below field + top error summary on submit (long forms) |
| Required fields | Mark the optional ones "(optional)"; no asterisks |
| Label position | Above the field, always visible |
| Confirm-email / confirm-password | Delete; echo value / show-password toggle |
| Wizard back navigation | Restores step with data intact, always |
| Consequential wizard end | Read-only review step, per-answer Change links |
| Long application (>5 min) | Autosave + save-and-resume link |
| Select with 2–4 options | Radio group / segmented control |
| Select with huge list | Autocomplete combo box |
| Date of birth | Three text inputs or masked field, never a calendar |
| Card/phone entry | Auto-format, accept any paste format |
| Password creation | Show-password toggle + live requirements checklist |
| One-time code entry | Numeric keyboard, full-code paste, auto-submit |
| Multi-select from many options | Searchable checklist with selected-items summary |
| Session nearing expiry mid-form | Warn + extend option; entries preserved regardless |
| Currency/unit input | Symbol as fixed affix inside the field, locale-aware format |
| Every text field, always | Correct `autocomplete` token — autofill is the biggest speed win |
| Primary button label | Verb + outcome ("Create account", "Pay €49") |
| After success | Confirmation + reference + one next action |

## Anti-Patterns

| Anti-pattern | Why it breaks | Fix |
|---|---|---|
| Placeholder text as the only label | Vanishes on focus; fails WCAG and recall | Persistent label above field |
| Validating on first keystroke | "Invalid email" after one typed character | Validate on blur; live-revalidate only after first error |
| Error: "Invalid input" | Describes the machine's problem, not the fix | Problem + fix: "Postcode must be 5 digits" |
| Clearing the form on error | Punishes the user for the rejection | Every value survives every error, including 500s |
| Back button loses wizard data | Forced re-entry; most abandon instead | Persist per-step on every transition |
| Asterisk on every required field | Legend tax, visual noise | Mark optional fields only |
| Two-column field layout | Skipped fields, broken order (Baymard) | Single column; inline-pair only coupled shorts |
| Disabled submit with no explanation | User can't discover what's missing | Enabled submit → validate → error summary |
| "Step 3 of 12," unlabeled dots | Feels endless; no sense of what's left | Fewer, labeled steps + upfront time estimate |
| Dropdown for 2–4 options | Hides options that fit on screen | Radio group / segmented control |
| Rejecting paste in password/OTP fields | Blocks password managers and autofill | Always allow paste |
| Strict input formats ("no spaces") | Rejects values you could normalize | Normalize on your side |
| Asking for data you don't use | Every field costs conversion | Cut it or defer to progressive profiling |
| Reset/Clear button beside Submit | Catastrophic misclick, no legitimate use | Delete it |
| CAPTCHA before any typing | Blocks the user at peak intent | Invisible/risk-based challenge, on suspicion only |
| Auto-jumping focus between split inputs | Backspace breaks; corrections become a fight | Single masked field, or split inputs with back-delete handled |
| Password max length <64 or paste blocked | Defeats password managers, weakens security | Long max, paste always allowed |
| Home-country validation applied globally | Rejects valid foreign postcodes/phones | Validate per selected country, or loosely |
| Critical instructions hidden in tooltips | Mobile users never see them | Visible hint text below the label |
| "Please select" default that submits | Slips through as a value | Validate selection; or pre-select the true common case |
| Unsaved-changes prompt on an unchanged form | Cried-wolf warnings get dismissed | Dirty-check before warning |
| Success page with no next step | Dead end at peak engagement | Confirmation + reference + single next action |

Proven by: GOV.UK Design System (one-thing-per-page, error summary, check-your-answers, start pages), Stripe Checkout (validation timing, autofill, field minimalism), Typeform (one-question flow, resume), Baymard Institute checkout research (single column, field-count reduction, label placement).
