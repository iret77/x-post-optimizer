# Example Traces

Two worked examples showing the expected output format for generate and review mode. Use these as a calibration anchor — both for structure and for the level of epistemic labeling.

---

## Example 1 — Generate mode (short post)

**User prompt:** "Write me an X post saying that most 'AI agents' are just cron jobs with better PR."

**Output:**

### Draft 1: Provocation with a quotable nugget

> Most "AI agents" out there are cron jobs with better PR.
>
> Trigger → API call → if/else → done.
>
> Where's the agency, exactly?

**Algorithmic trace:**
- Sharp claim as opening hook — [HEURISTIC]: pattern-interrupt phrasing targets P(favorite) and P(quote). Standalone-comprehensible, so [INFERENCE] for P(follow_author) too.
- Middle line is independently quotable — [INFERENCE from P(share_via_copy_link)]: screenshot-worthy single line.
- Closing question triggers P(reply) — [INFERENCE from the 15 predicted actions]: open questions are the most direct reply-bait.

**Not optimized for:**
- P(dwell) — too short to dwell on. Would require long-post format.
- P(click) — deliberately no link; off-platform clicks would suppress dwell time.

### Draft 2: Definition framing

> "AI agent" now means: a cron job that occasionally hallucinates.

**Algorithmic trace:**
- Compact, sarcasm-carried — [HEURISTIC]: definition-framing ("X now means: Y") performs above average with tech audiences.
- Less reply-bait than Draft 1 (no hook for response) — primarily targets P(favorite) and P(repost). [INFERENCE]
- Highly quotable for people with their own take ("Not in my case..."). [INFERENCE from P(quote)]

**Not optimized for:**
- P(reply) — no open question.
- P(follow_author) for cold viewers — shorter, less author context.

---

**Honesty note:** These drafts will perform with high variance. The disclosed Phoenix architecture tells us *which* signals are weighted, but not *how strongly*. The concrete weight values are excluded from the open-source drop. Author context (your previous engagement history) is also a factor I don't know — posts that fit your established niche rank more reliably.

---

## Example 2 — Review mode (short post)

**User prompt:** "Review this: 'Had an interesting conversation with a customer about AI adoption today. There are a lot of misconceptions. What are your experiences?'"

**Output:**

### Review of: customer-conversation post with reply question

**Strengths** (mapped to algorithm signals):
- Closing question — bids for P(reply). [FACT]: P(reply) is one of the 15 positive engagement targets.

**Weaknesses** (mapped to algorithm signals):

- **Hook is missing entirely** — [HEURISTIC]: "Had an interesting conversation" is generic setup with no payoff in the first 8-10 words. Cold viewers scroll past, which [INFERENCE] yields weak P(favorite) and P(reply) predictions.
  Rewrite anchor: make the *misconception itself* the first line.

- **"Interesting conversation" without content** — [INFERENCE]: no concrete substance in the post. P(quote) and P(share) are nearly impossible because there's nothing to amplify or extend.
  Rewrite anchor: name a *specific* misconception.

- **Vague question** — [HEURISTIC]: "What are your experiences?" is low-friction enough that it *might* get replies, but the replies will be equally vague. Specific questions yield substantive replies, which in turn drive P(reply) on your replies.
  Rewrite anchor: ask about a concrete scenario.

- **P(follow_author) is weak** — [INFERENCE]: a cold viewer learns nothing about your expertise from this post. No reason to follow.

**Suggested rewrite:**

> The most common AI-adoption trap I saw this week:
> Teams introduce tools before defining *what was actually broken*.
> Result: a RAG setup that patches the symptoms of a process problem.
>
> What was your biggest "we should have looked closer first" AI investment?

**Trace of rewrite decisions:**
- Hook is a concrete pattern in line 1 — P(favorite) and P(reply) targets clearly defined. [INFERENCE]
- Lines 2-3 deliver substance that's quotable — P(quote) and P(share). [INFERENCE from the separate signal axes]
- Closing question is specific enough for substantive replies — P(reply), and reply threads compound P(reply) signals. [INFERENCE]
- Standalone-comprehensible for cold viewers — P(follow_author) becomes targetable. [INFERENCE]

**Overall:** The original is classic "vague reply-bait" and effectively targets only P(reply), and weakly at that. The rewrite opens 4-5 signal axes. Expected lift is *substantial*, but performance stays probabilistic — we don't know the exact weights.

**Honesty note:** Reviewer recommendations are inferences from the 15 Phoenix targets, not from their concrete weights (those are not public). Actual performance also depends on your author context and time-of-day dynamics — both outside what we can know from the open-source code.
