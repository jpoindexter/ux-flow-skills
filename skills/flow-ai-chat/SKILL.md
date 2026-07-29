---
name: flow-ai-chat
description: "Use when designing, building, or reviewing any AI chat, assistant, copilot, or agent interface — composer, streaming responses, tool-use display, citations, regenerate/edit, or AI empty states. Also fires when users report the AI feeling frozen or opaque about what it's doing or can see, or when wiring confirmations before an agent takes consequential actions."
---

# AI & Chat Interface Flows

## Overview
An AI interface's core job is managing uncertainty: latency you can't predict, output you can't guarantee, and actions the user must stay in control of. Streaming, visible working state, and human-in-the-loop gates are the load-bearing patterns — not decoration.

## When to Use
- Chat/assistant surfaces, copilots embedded in tools, agent dashboards, AI generation panels
- Adding tool use, citations, multi-step agent runs, or consequential actions to an AI feature
- **NOT for:** classic autocomplete/typeahead — that's an input pattern, not a conversation
- **NOT for:** one-shot ML features with instant deterministic results (spam filter, image tagger) — no chat chrome needed

## The Proven Flow

1. **Empty state teaches by example.** First screen: a brief capability statement + 3–6 clickable prompt suggestions spanning distinct use cases (ChatGPT, Claude, and Perplexity all converged here). Suggestions are real, specific prompts ("Summarize this PDF's key risks"), not categories ("✍️ Writing"). The composer is already focused. Never ship an empty text box with only "Ask me anything" — blank-page paralysis produces weak first prompts and weak first impressions.
2. **User sends; the message appears instantly.** The user message renders optimistically, the composer clears, the view anchors to the new exchange. Multi-line input via Shift+Enter; Enter sends (web/desktop convention). On mobile, the send button sends and return key makes a newline.
3. **The response streams token-by-token.** Target under ~1 second to first visible activity — show a lightweight shimmer/typing indicator until the first token. Render Markdown progressively: headings, lists, and code blocks format as they arrive, not in a raw-text pass that reflows at the end. Blocking on the complete response is the single worst AI-UX mistake; streaming *is* the interaction, converting a 20-second wait into 20 seconds of reading.
4. **A stop button replaces send during generation.** Same position, swapped icon (ChatGPT/Claude convention). Stopping keeps the partial output in the thread and returns control immediately. Never force the user to wait out a generation they can already see is wrong.
5. **Tool use and agent steps render as visible working state.** Each step is a compact live status line ("Searching the web…", "Reading main.ts", "Running tests…") that resolves into a collapsible completed entry with a one-line result summary. Steps are collapsed by default, expandable to full input/output detail (Cursor's agent steps, Claude's tool-use blocks, v0's build steps). For long runs, show step counts or progress so visible silence never exceeds a few seconds.
6. **Consequential actions gate on the human.** Before the agent sends an email, deletes files, spends money, or pushes code: show exactly what will happen — the draft, the diff, the literal command — with Approve / Edit / Reject. Approval is per-action or narrowly scoped ("allow file reads this session"), never a blanket "always allow everything" default. Cursor's command approval and agent-framework HITL checkpoints prove the pattern; one unapproved destructive action ends trust permanently.
7. **The response tail carries recovery affordances.** After each assistant message: copy, regenerate, feedback. Regenerate versions the response with a pager ("2/3" — ChatGPT) rather than silently discarding the previous attempt. Edit on the *user's* message forks the conversation from that point — make the fork explicit so users understand what happened to the downstream messages.
8. **Errors and refusals stay in the conversation.** Failed generation → inline error bubble with Retry; partial output preserved. Refusal → one-line reason + what the AI *can* do instead — never a dead-end lecture. Rate limit → what the limit is, when it resets, and what tier removes it. Context-window exhaustion → offer to summarize-and-continue or start a linked new chat, don't just degrade silently.

## Layout & Structure

### Composer
- **Pinned to the bottom**, full conversation width, auto-growing to ~8–10 lines then scrolling internally.
- Attachment/context controls on the left or inside the field; send/stop on the right.
- Keep the composer usable while a response streams — queue the next message or allow interjection per your product model; a dead composer during generation feels broken.
- Attached context renders as removable chips above or inside the composer, not as invisible state.
- Power features that pay for themselves: drag-and-drop + paste for attachments, `@`-mentions for context objects (files, docs, people), `/`-commands for actions, up-arrow to recall/edit the last sent message.
- If input length is limited, show remaining capacity as the user approaches it — never reject a long prompt only after send.

### When chat is the wrong shape
- Structured input with known fields (an address, a config, a booking) → render a form (see flow-forms), optionally AI-prefilled, not twenty conversational turns.
- Single-field transformation (rewrite this sentence, summarize this cell) → inline affordance at the content, not a detour to a chat panel.
- Chat is for open-ended, iterative, multi-step work. Forcing it everywhere trades one good interface for a slower universal one.

### Message column
- **Single scrolling column**, message text capped at ~65–75ch on desktop for readability; the column centers in wide viewports.
- User vs assistant messages distinguished by one consistent system — alignment, background, or avatar; pick one, don't stack all three.
- Assistant output is rich: Markdown, tables, code blocks with language label + copy button. User messages stay plain.
- **Auto-scroll follows the stream only while the user is at the bottom.** The moment they scroll up to read, stop following and show a "jump to latest" pill. Yanking the viewport mid-read is a top complaint in every shipped chat product.
- Timestamps on hover/tap, not inline noise. Day dividers for long histories.

### Context indicators — show what the AI can see
- Attached files, the active document, selected code, screen/page visibility, and connected tools all render as visible, removable chips (Cursor's @-context chips; Claude's attachment chips).
- If the AI can see the user's screen, selection, or current page, say so explicitly in the UI. Silent context is a privacy failure; invisible *missing* context ("why doesn't it know about my file?") is a capability failure. Both erode trust in opposite directions.
- If long-term memory exists across sessions, make it inspectable and editable — a visible list of remembered facts, each deletable.

### Tool-step and agent-run blocks
- Running: icon + verb + object one-liner ("Searching: EU AI Act fines"). Done: chevron-expandable summary. Failed: inline error state within the step, with its own retry — a failed step must not silently vanish.
- Nest sub-steps one level maximum; deeper nesting is unreadable in a chat column.
- Streaming code renders into a code block with language label and copy button from the first fence. File edits show a diff view, not a wall of replacement code (Cursor, v0).
- Agent runs that modify files create a restorable checkpoint before applying; expose "restore to before this run" alongside the run summary (Cursor).
- Runs >30 seconds need a persistent progress surface that survives navigating away, plus a completion notification path.

### Citations
- Inline numbered markers at the claim, resolving to a source list with favicon + title + domain (Perplexity's pattern). Hover/tap previews the source.
- Citations must come from real retrieval. If the answer wasn't grounded, don't dress it in source chrome — fake citation UI is manufactured credibility and a legal risk.
- Date-sensitive or low-confidence answers carry "as of" markers or hedges; never style a guess with the same confident chrome as a grounded answer.

### Latency budgets
- **<1s:** first visible activity (shimmer counts).
- **<3s:** first token, or explicit progress replaces it ("Searching 12 sources…").
- **>10s of one step:** show sub-progress or interim findings.
- **>30s total:** persistent/backgroundable progress + notify on completion.

### History
- Conversation list with auto-generated titles (editable), delete, and search. New-chat is always one click. Deleting a conversation follows flow-errors destructive rules (undo, not confirm).
- Pin/star for conversations users return to; recency ordering otherwise.
- Sharing/exporting a conversation produces a readable transcript and strips private context (attachments, memory) unless explicitly included — state what's included before the link exists.
- Cold start is where latency budgets die: the first message of a session should still hit <1s to visible activity — prewarm, or mask with instant optimistic UI.

### Mobile specifics
- Composer stays pinned above the software keyboard; the conversation resizes, it doesn't hide behind the keyboard.
- Return key inserts a newline; only the send button sends — mobile users compose multi-line messages constantly.
- Message actions (copy, regenerate, feedback) move from hover to long-press or an inline overflow menu — hover doesn't exist.
- Voice input is a first-class composer affordance on mobile, not buried in settings.
- Streaming must not fight the on-screen keyboard: opening the keyboard keeps the latest message visible.

### Tone and persona
- An assistant should be **deferential without being fawning.** Josh Clark's framing: deferential is not flirty, not fawning, not feminine-by-default — don't dress an agent in a submissive or performative persona to make deference legible. State the boundary or the uncertainty plainly instead of apologizing around it.
- Treat the assistant as there to **empower the user's judgment, not just assist** (Clark) — the tone should read as capability handed to the user, not a service performing helpfulness at them. This is why "Whoops, my bad! 😅" (below) fails: it performs personality where the user needed a plain cause and a retry.
- Output is a **signal, not a fact** (Clark, citing Bender et al.'s "stochastic parrots" framing): a fluent response is not the same as a correct one, and the UI's job is to keep that distinction visible rather than let confident phrasing borrow authority the model didn't earn. This is the same instinct behind hedges and "as of" markers above — extend it to word choice, not just chrome.

### Feedback & quality signals
- Thumbs up/down on assistant messages, with an optional one-tap reason on the downvote ("inaccurate", "didn't follow instructions"). Never a mandatory free-text form — friction kills the signal.
- Show which model/version answered when users can choose models; silent model switches break users' calibration of what to trust.
- "Report" is separate from thumbs-down and routes to safety review.

### Accessibility
- Streamed responses announce via a single `aria-live="polite"` region updated in sentence/paragraph chunks — per-token announcements make screen readers unusable. Announce "response complete" at the end.
- Stop, regenerate, copy, and step-expanders are real buttons, keyboard-reachable, with state (`aria-expanded` on collapsibles).
- Enter/Shift+Enter behavior is documented in the composer's accessible description.
- Shimmer/typing indicators respect `prefers-reduced-motion` with a static "Generating…" alternative.
- User vs assistant messages distinguished by more than tint — alignment, labels, or headings a screen reader can navigate by.

## Quick Reference

| Situation | Pattern |
|---|---|
| First-run / empty thread | Capability line + 3–6 concrete clickable prompts, composer focused |
| Waiting for first token | Shimmer/typing indicator, <1s to appear |
| Response in progress | Progressive Markdown render + stop button in the send slot |
| AI is using tools | Live status lines → collapsible completed steps |
| Agent wants to act on the world | Preview exact action + Approve/Edit/Reject, scoped approval |
| Bad response | Regenerate with version pager on assistant msg |
| Wrong question asked | Edit user message → explicit fork |
| Grounded answer | Inline numbered citations → source cards |
| Ungrounded answer | No citation chrome; hedge/"as of" markers |
| Generation failed | Inline error + retry, partial output kept |
| Refusal | One-line reason + viable alternative, in-conversation |
| User scrolls up mid-stream | Stop auto-scroll; show jump-to-latest pill |
| Files/selection/tools in context | Visible removable chips; never silent context |
| Run >30s | Persistent progress, survives navigation, notifies on done |
| Context window full | Summarize-and-continue or linked new chat |
| Known-fields input | Form (AI-prefilled), not conversational turns |
| Mobile message actions | Long-press / overflow menu, not hover |
| Multiple models available | Visible model label per response |
| Rating a response | One-tap thumbs + optional reason; never mandatory text |
| Sharing a conversation | Readable transcript; private context stripped unless opted in |
| Screen-reader user mid-stream | Chunked polite live-region + "response complete" announcement |
| Deleting a conversation | Undo toast, not a confirm dialog (see flow-errors) |
| First message of a session | Same <1s activity budget — prewarm or mask cold start |

## Anti-Patterns

| Anti-pattern | Why it breaks | Fix |
|---|---|---|
| Spinner until the full response is ready | 10–30s of dead air; feels broken | Stream tokens, render progressively |
| No stop control during generation | User trapped watching a wrong answer complete | Stop swaps into the send button |
| Raw text streamed, reformatted at the end | Jarring double-render and reflow | Progressive rich rendering from first token |
| Silent tool use ("Thinking…" for 40s) | Reads as a hang; hides failures | Named visible steps with live status |
| Agent executes destructive actions unprompted | Trust destroyed on the first bad action | HITL preview + approve gate for consequential actions |
| "Always allow all" as the approval default | One click removes every future safety gate | Per-action or narrowly scoped approvals |
| Auto-scroll fights the reading user | Viewport yanked mid-read | Follow only at bottom; jump-to-latest pill |
| Empty state = blank box | Blank-page paralysis, weak first prompts | Concrete example prompts as buttons |
| Dead composer during generation | Product feels locked | Queue or interject; keep input alive |
| Fake citation chrome on ungrounded output | Manufactured credibility; legal risk | Citations only from real retrieval |
| Regenerate silently deletes the old answer | Users compare versions; the loss is felt | Version pager (1/2, 2/3) |
| Edit-message fork left unexplained | User can't find their old thread | Label the fork; show branch state |
| Refusal as boilerplate lecture | No path forward | State the boundary + an alternative |
| Hidden context (AI sees screen silently) | Privacy anxiety + "why doesn't it know X" | Context chips + explicit visibility notice |
| Confident styling on hallucinated specifics | Wrong answers inherit the UI's authority | Hedges, grounding labels, "as of" dates |
| Prompt suggestions vanish forever after first use | New users' only scaffolding disappears | Keep examples reachable (new-chat screen, help menu) |
| "Whoops, my bad! 😅" on real failures | Personality filler where users need facts | Plain cause + retry; save charm for low stakes |
| Length limit discovered only on send-reject | Long prompt written, then bounced | Show remaining capacity as the limit approaches |
| Unsearchable, untitled history | Past work is unfindable, so it's redone | Auto-titles + search + pinning |
| Silent model downgrade under load | Users misattribute quality drop to themselves | Label the model per response; announce switches |

Proven by: ChatGPT (streaming, stop, regenerate versions, prompt suggestions), Claude (tool-use blocks, context chips, artifacts), Perplexity (citations, source cards), Cursor (agent steps, diff previews, command approval), v0 (generation progress, streamed code/preview), Josh Clark's *Sentient Design* (tone/persona framing — deferential vs. fawning, signals not facts).
