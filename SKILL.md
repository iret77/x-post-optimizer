---
name: x-post-optimizer
description: Write or review posts for X (Twitter) so they perform well in the For You feed, grounded in the open-source xai-org/x-algorithm code (released 2026). Use whenever the user wants to draft a tweet, X post, long post, thread, reply, or quote post — also for reviewing or improving existing X drafts, asking "how do I make this perform on X", "score this tweet", "is this on-brand for the X algorithm", "will this hit the For You feed", or wants engagement-optimized X content. This skill is the right call even when the user doesn't mention "algorithm" — any request to produce or improve a post intended for X should pull this in.
---

# X Post Optimizer

This skill helps draft and review posts for X (Twitter) based on the **open-source For You feed algorithm** released by xai-org in 2026. It produces drafts in any X format (short post, long post, thread, reply, quote) and reviews existing drafts against algorithm-derived heuristics.

## Core principle: epistemic separation

The X algorithm has been partially open-sourced but **not all parameters are public**. To stay honest, every recommendation in this skill is labeled:

- **[FACT]** — directly verifiable from xai-org/x-algorithm source code or README
- **[INFERENCE]** — logically derivable from [FACT] but not stated outright in the repo
- **[HEURISTIC]** — established creator practice, not in the code; treat as plausible but unverified

When generating or reviewing, **never present a [HEURISTIC] as a [FACT]**. If asked "why should I do this", trace it back to which category it belongs to. This is non-negotiable — the value of this skill collapses if the labels blur.

## How to invoke

Two modes. Pick based on user intent — ask if unclear:

1. **Generate mode** — user gives a topic, idea, or rough text and wants a draft. Output: one or more drafts in the requested format, plus a short "why this should work" trace showing which [FACT]/[INFERENCE]/[HEURISTIC] each design choice rests on.
2. **Review mode** — user gives an existing draft and wants feedback. Output: a structured review using the checklist in `references/review-checklist.md`, with concrete rewrite suggestions for any item flagged.

If the user asks for both, do generate first, then review the result.

## Workflow

**Step 1 — Load the algorithm facts.** Read `references/algorithm-facts.md`. This is the canonical [FACT] list. Do not paraphrase from memory; the facts are precise and over-paraphrasing has caused the "Retweet × 20" myth that this skill is built to avoid.

**Step 2 — Determine format.** X supports several text formats with different algorithmic profiles:
- Short post (≤280 chars, default)
- Long post (Premium only, up to 25k chars)
- Thread (sequence of short posts)
- Reply (text in another author's conversation)
- Quote post (text wrapping someone else's post)

If the user hasn't specified, ask. The format choice changes which algorithm signals matter most — see `references/format-playbooks.md`.

**Step 3 — Apply format playbook.** Read the relevant section of `references/format-playbooks.md` for the chosen format. Each playbook lists:
- Which engagement signals from the Phoenix scorer this format can realistically optimize for
- Format-specific structural patterns
- Common mistakes that depress scoring

**Step 3.5 — Voice source (optional).** Tone-matching is *not* algorithm-grounded; it's stylistic. Offer this step only if (a) the user explicitly wants drafts in a specific voice — their own or someone else's — or (b) generic voice would clearly miss the brief. Three sources, in preference order:

1. **Reference X account.** If X tools are available in your environment (e.g. an X/Twitter MCP that can fetch a user's timeline), ask for a handle. Fetch ~20–40 of the account's original posts (skip reposts; skip replies for short-post mode) and extract patterns: hook structures, sentence length, vocabulary, punctuation habits, what they consistently DO and DON'T do.
2. **Pasted samples.** If X tools are not available, ask the user to paste 3–5 representative posts from the account they want to mirror.
3. **Persona description.** Fallback. Less precise, still usable.

When voice patterns inform a draft, tag those choices `[STYLE-MATCH]` — separately from the algorithm tags. This keeps the epistemic discipline intact: `[FACT/INFERENCE/HEURISTIC]` are algorithm-derived; `[STYLE-MATCH]` is mimicry-derived and carries no claim about ranking performance.

If the user hasn't asked for voice-matching, skip this step entirely — a neutral, clear voice is fine and doesn't dilute the algorithm-grounded workflow.

**Step 4 — Draft or review.**
- Generate mode: produce 1-3 variants. For each, give a 2-3 line trace ("This opens with a question to bid for P(reply) — [INFERENCE from the 15 predicted actions in Phoenix]").
- Review mode: walk `references/review-checklist.md` in order. Flag each miss with a [FACT/INFERENCE/HEURISTIC] tag and a suggested rewrite.

**Step 5 — Honest output.** End every response with:
- A short note on what the algorithm does *not* tell us (e.g., concrete weight values are not in the open-source release)
- A reminder that algorithmic performance is probabilistic, not deterministic

## What this skill deliberately avoids

Some claims widely repeated online are **not in the 2026 open-source release** and should not be presented as facts:

- Specific weight multipliers like "Retweet = 20×", "Reply = 13.5×", "Block = -74×". The `params` module with concrete weights is **excluded** from the open-source repo for security reasons. Numbers from third-party blogs typically trace back to the 2023 Twitter `the-algorithm` repo (different system) or to guesswork. If a user insists on numbers, label them explicitly: "[HEURISTIC, from 2023 legacy code, possibly outdated]".
- "Sentiment analysis suppresses negative tones." Not in the repo. There is a `VFFilter` that removes deleted/spam/violence/gore content post-selection, but no sentiment ranker is documented.
- "External links are penalized." Not in the repo. `P(click)` is one of the predicted actions, suggesting link-clicks are a positive signal. The "links hurt reach" claim may be a [HEURISTIC] based on dwell-time tradeoffs but is not verifiable from the code.
- Time-of-day, post frequency, blue-check effects. Not addressed by the open-source ranker. Mention only if the user asks, labeled clearly as [HEURISTIC].

## Reference files

- `references/algorithm-facts.md` — Canonical [FACT] list from the xai-org/x-algorithm repo. Read first.
- `references/format-playbooks.md` — Per-format guidance for short post, long post, thread, reply, quote.
- `references/review-checklist.md` — Structured review checklist for review mode.
- `references/example-traces.md` — Worked examples of generate-mode and review-mode outputs.

## Output format

**Generate mode:**

```
## Draft <N>: <one-line description>

<the actual post text, ready to copy>

**Algorithmic trace:**
- <design choice> — [FACT/INFERENCE/HEURISTIC]: <2-line explanation>
- <voice choice, if voice-matching was used> — [STYLE-MATCH]: <what pattern from the reference you mirrored>
- ...

**Not optimized for:** <signals this draft doesn't target, and why>
```

**Review mode:**

```
## Review of: <short description of the draft>

**Strengths** (mapped to algorithm signals):
- <strength> — [FACT/INFERENCE/HEURISTIC]

**Weaknesses** (mapped to algorithm signals):
- <issue> — suggested rewrite: "<new text>" — [FACT/INFERENCE/HEURISTIC]

**Overall:** <one paragraph>
```

End every response with the honesty note (see Step 5).

## Source

Primary: <https://github.com/xai-org/x-algorithm> (released January 2026, Phoenix/grox update May 2026).
Secondary (for legacy context only): <https://github.com/twitter/the-algorithm> (2023, different system).
