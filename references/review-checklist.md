# Review Checklist

Walk this list in order when in review mode. For every flagged item, give a [FAKT/INFERENZ/HEURISTIK] tag and a concrete suggested rewrite. Don't just list problems — show the fix.

## A. Filter risk (would the post get dropped before scoring?)

These are [FAKT] from the repo's filter list. Any "yes" here means the post never reaches the scorer at all.

- [ ] **Muted-keyword risk.** Does the post contain phrases your target audience commonly mutes (e.g., overused engagement-bait phrases, well-known spam triggers)? [INFERENZ from `MutedKeywordFilter`]
- [ ] **VFFilter risk.** Could the post be misclassified as spam/violence/gore/policy-violation by automated classifiers in the grox content-understanding service? [FAKT that VFFilter exists; classification is ML, so risk is non-zero even for benign content with edge wording.]
- [ ] **Selfpost / repost confusion.** If it's a quote of your own earlier post, `RepostDeduplicationFilter` may kill it. [FAKT]
- [ ] **Stale content.** Is the topic obviously dated? `AgeFilter` only filters by post timestamp, but topical staleness affects engagement and thus the score. [FAKT for the filter; INFERENZ for the engagement implication.]

## B. Engagement-signal coverage (which of the 15+ Phoenix predictions does this post target?)

[FAKT] Phoenix predicts 15 engagement actions. Each is a separate dimension in the weighted scorer. Posts that target multiple positive dimensions have more upside than posts optimized for likes alone.

- [ ] **P(favorite)** — Is there something a reader nods at and wants to mark? Sharp claim, useful observation, clever phrasing.
- [ ] **P(reply)** — Is there a hook that invites a response? Question, hot take, deliberately incomplete thought, asks for examples.
- [ ] **P(repost) / P(share)** — Is the post quotable without modification? Could a reader endorse it to their audience as-is?
- [ ] **P(quote)** — Is there room for someone to add their take? (Hot takes and frameworks invite quote; finished thoughts don't.)
- [ ] **P(profile_click)** — Does the post make someone curious who you are? Specific expertise, unusual perspective, or pattern-interrupting wording all help.
- [ ] **P(follow_author)** — Could a cold viewer decide to follow based on this single post? Does it work standalone, without context?
- [ ] **P(dwell) / dwell_time** — For longer formats: does the post hold attention all the way through?
- [ ] **P(click)** — If there's a link, is the link genuinely click-worthy and contextual? (Don't add links just for the signal — see negative-signal risk below.)
- [ ] **P(video_view) / P(photo_expand)** — If media is attached, does it pull eyes? Is the post viable without the media (in case media autoplay is off)?
- [ ] **P(share_via_dm) / P(share_via_copy_link)** — Is there a moment readers would want to send this to a specific friend? (Specific niche references, "this is so X" moments.)

## C. Negative-signal risk (would this trigger P(not_interested), P(block), P(mute), P(report)?)

[FAKT] These 4 actions are predicted with negative weights. Triggering them shrinks future distribution.

- [ ] **Off-niche.** Does this post fit the audience that follows you? An off-topic post may get P(not_interested) clicks from people who normally engage. [INFERENZ]
- [ ] **Hostile or insulting framing.** Even content that's "engagement-positive on Twitter" — pile-ons, dunks, character attacks — invites P(block/mute/report). [INFERENZ; the repo doesn't measure tone but does measure these negative actions.]
- [ ] **Misleading hook.** If the hook promises more than the post delivers, P(not_interested) is the natural reaction.
- [ ] **Spam patterns.** Excessive hashtags, "RT if you agree" baiting, generic engagement-bait. [HEURISTIK + possible filter hit.]

## D. Structural quality

- [ ] **Hook in the first 8-10 words.** Especially for short posts, the first beat decides scroll-or-engage. [HEURISTIK]
- [ ] **One thesis per post.** Multiple unrelated ideas dilute engagement targeting. [INFERENZ — Phoenix scores each post independently, so each post should optimize for its own engagement set.]
- [ ] **Standalone comprehensibility.** Out-of-network discovery means strangers will see this. Can a cold viewer parse it? [INFERENZ from P(follow_author) being a positive signal.]
- [ ] **Length matches format.** Short posts shouldn't try to be long posts; long posts shouldn't have unstructured walls of text.

## E. Author-context fit

[INFERENZ from "no hand-engineered features; transformer learns from engagement sequence"] The model has a learned representation of your account from past engagement patterns. Posts that match that representation get cleaner Phoenix predictions; off-pattern posts confuse the model.

- [ ] **Topic consistency.** Does this fit the niche the model has learned you're in?
- [ ] **Voice consistency.** Does this match your established voice? [HEURISTIK — the model doesn't measure voice directly, but engagement patterns from your audience implicitly enforce it.]
- [ ] **Frequency.** Have you posted heavily in the last hour? `AuthorDiversityScorer` will attenuate. [FAKT]

## F. Honest limitations (always include in the review)

Tell the user what the review *cannot* tell them:

- The repo does not publish concrete weight values. We don't know *how much more* a repost is worth than a like in 2026.
- We don't have access to the user's specific account history, so we can't predict topic-fit confidence quantitatively.
- All "would this work" claims are probabilistic. The Phoenix transformer is non-deterministic over its hash-based embeddings, and individual post performance has high variance.

## Scoring synthesis (optional)

If the user wants a single rating, use this rubric and *show your work*:

- **Strong** — Targets 6+ positive signals, low negative-signal risk, hooks in first 10 words, standalone-readable.
- **Good** — Targets 4-5 positive signals, no obvious negative-signal risk, hook present.
- **Weak** — Targets 2-3 positive signals, or has hook problems, or carries negative-signal risk.
- **Don't post** — Filter risk (would likely not reach scoring), or significant negative-signal risk.

Always cite which signals you're counting and why. The rating is a synthesis, not a substitute for the per-item walkthrough.
