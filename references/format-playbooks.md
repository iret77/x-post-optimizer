# Format Playbooks

Each format has a different algorithmic profile because the *set* of engagement signals it can plausibly trigger varies. Read the relevant section based on what the user is producing.

## Short Post (≤280 chars)

The default. Has to do everything in one breath.

**Realistic positive signals to target:** P(favorite), P(reply), P(repost), P(quote), P(profile_click), P(share)
**Hard to target:** P(dwell), dwell_time (too short to dwell on), P(video_view) (no video), P(photo_expand) (unless media is attached)

### Structural patterns

- **Hook → payoff in one beat.** A reader decides in ~1 second whether to engage. The first 8-10 words carry the load.
- **One claim, not three.** [INFERENCE] Posts that try to compress multiple ideas usually trigger a like at best; one sharp claim invites reply/quote because there's something specific to respond to.
- **Reply-bait that isn't trash.** A question or unfinished thought invites P(reply). Strong opinion invites P(quote). Both are weighted positives. [INFERENCE from the 15 signals]
- **Standalone comprehensibility.** [INFERENCE] Since P(follow_author) is a positive signal, the post should make sense to someone who has never seen your other posts — they need enough context to decide you're worth following.

### Common mistakes

- Burying the hook behind a setup ("So I was thinking about..."). Cuts dead weight.
- Vague subtweets — no entity to engage with means no reply, no quote, no share.
- Off-platform link as the entire payload — [HEURISTIC] community lore says links suppress reach; the repo doesn't confirm this, but P(click) being positive while P(dwell) drops (link click = leaving platform) suggests a *real* tradeoff.

### Example trace

> "Most 'AI strategy' decks are just a list of tools with the word 'enterprise' sprinkled on top."

Targets: P(favorite) [agreement], P(reply) [disagreement or examples], P(quote) [signal-boosting with own twist]. Standalone-readable, so P(follow_author) is plausible.

---

## Long Post (Premium, up to 25k chars)

A different game. The reader has committed to the unfold; now dwell time matters.

**Realistic positive signals to target:** P(dwell), dwell_time, P(favorite), P(reply), P(repost), P(quote), P(follow_author), P(share), P(share_via_dm)
**Hard to target:** P(profile_click) (they don't need to click — they're already reading you)

### Structural patterns

- **Lead with the conclusion.** [HEURISTIC + INFERENCE from dwell] The first paragraph has to earn the unfold. If a reader scrolls past after seeing only the preview, you got the worst case (no dwell signal, possible P(not_interested) if it looked spammy).
- **Internal hooks every ~500 chars.** [HEURISTIC] Sub-headlines, numbered breaks, or short punchy lines break the wall of text and reset attention. There's no [FACT] about this in the repo, but dwell_time being a continuous signal makes "did they actually read?" matter.
- **One thesis, full argument.** Long form is where you finish the thought a short post can only gesture at.
- **Quotable nuggets sprinkled.** [INFERENCE] Lines that are screenshot-worthy invite P(share_via_copy_link) and P(quote).

### Common mistakes

- Treating it as a thread mashed together with no breaks — that triggers the "did they actually dwell or just bounce" question with the model uncertain.
- No payoff in the first 200 chars — the unfold is the *commitment*, the first paragraph is the *justification*.

---

## Thread (sequence of short posts)

A thread is multiple posts. The algorithm ranks each post independently (candidate isolation), but practical reach comes from the **first post** carrying the whole thread.

**Realistic signals — post 1:** P(favorite), P(reply), P(repost), P(profile_click), P(click) on "show this thread"
**Realistic signals — subsequent posts:** P(dwell) [via scroll-through], P(reply), P(repost) [on individual bangers]

### Structural patterns

- **Post 1 is a self-contained promise.** [INFERENCE from candidate isolation] Since Phoenix scores each post separately, post 1's ranking determines whether the rest of the thread gets seen at all. It must work as a standalone hook.
- **Numbered ("1/", "2/") signals "more coming"** — [HEURISTIC] not in the repo, but matches established creator practice.
- **Each post should earn its own engagement.** A thread that's actually a long post awkwardly split across tweets won't get individual posts engaged with. Make every post quotable on its own.
- **Last post: CTA or callback.** [HEURISTIC] Reply-bait ("which one resonated?"), repost-bait ("repost the first if you found this useful"), or follow-bait ("more like this — follow"). These map to P(reply), P(repost), P(follow_author) respectively.

### Common mistakes

- Post 1 says "Here are 7 lessons I learned about X 🧵". [INFERENCE + HEURISTIC] No payoff in post 1 itself — the model can't predict high engagement on the first post because there's nothing to engage with. Lead with the *most surprising* lesson instead and use that as the hook.
- All posts the same shape. Variety in length (some short, some with media) gives the algorithm more signal types to work with.

---

## Reply

Replies are competing with other replies in a conversation. Algorithmic dynamics shift: you're now bidding for visibility *within* a thread (different ranker on X for in-conversation surfacing), and *also* for For You Feed surfacing as a standalone post.

**Realistic positive signals to target:** P(favorite), P(reply) [back-and-forth], P(profile_click), P(follow_author), P(quote)
**Hard to target:** Reposts of replies happen but are rarer. P(dwell) bounded by post length.

### Structural patterns

- **Add information, not agreement.** "Great point!" type replies do not surface. A reply that *extends* the original post (counter-example, missing nuance, related data) is what gets seen by people outside the conversation.
- **Be quotable.** [INFERENCE] If your reply is sharper than the original, P(quote) becomes plausible — and a quoted reply often outperforms the original.
- **Reply to small accounts you actually engage with.** [INFERENCE from "engagement sequence" learning] The transformer learns your engagement patterns; consistent replying to the same niche reinforces that you're "in" that niche, helping out-of-network discovery for your own posts.

### Common mistakes

- Reply-guying — high-volume low-substance replies on big accounts. [INFERENCE] If those replies generate P(not_interested) or P(mute_author) from other viewers, that's a measured negative signal feeding back into your account's ranking.
- One-word replies that add nothing. No engagement axis is meaningfully targeted.

---

## Quote Post

The most underused format. You inherit the original post's reach if your quote performs.

**Realistic positive signals to target:** P(favorite), P(reply), P(repost), P(quote) [of your quote], P(profile_click), P(follow_author), P(quoted_click) (specifically tracked, per `weighted_scorer.rs`)

### Structural patterns

- **Add a take, not just an "agree".** "This." with a quoted post is a wasted slot. State *why*, *what's missing*, or *what it implies*.
- **Make your text strong enough to stand without the quote.** [INFERENCE] If the original gets deleted or hidden, your quote should still make sense as a standalone post.
- **Steelman or sharpen.** The two highest-performing quote modes: (a) "This is the best version of X argument" or (b) "Almost — but this misses Y". Both invite P(reply) from both sides.

### Common mistakes

- Quote-posting to dunk on small accounts — [INFERENCE] high probability of P(block_author), P(mute_author), P(report) from viewers sympathetic to the target. Negative signals carry weight.
- Quote-posting your own posts as a "boost" — no new information, audience overlap is high, returns are weak.

---

## Format selection — when the user hasn't specified

Ask, unless context makes it obvious. A rough decision tree:

| Constraint | Use |
|---|---|
| Idea fits in 1-2 sentences | Short post |
| User has Premium AND idea needs > 500 chars to land | Long post |
| Idea has 3+ discrete beats AND each beat could stand alone | Thread |
| User is responding to someone else's post | Reply or Quote |
| User wants to amplify + add to someone else's post | Quote |
| User wants to participate in their conversation | Reply |
