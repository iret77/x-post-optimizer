# X For You Feed Algorithm — Canonical Facts

All [FACT] entries below are directly attributable to the `xai-org/x-algorithm` repository (released January 2026, last verified May 2026). Quote and link the source when challenged.

Repo: <https://github.com/xai-org/x-algorithm>

## Architecture

[FACT] The For You feed has a **two-source candidate pool**: in-network (Thunder — posts from accounts you follow) and out-of-network (Phoenix Retrieval — ML similarity search across the global corpus). Both pools are merged before ranking.
Source: README §Overview, §System Architecture.

[FACT] Ranking happens via **Phoenix**, a Grok-based transformer model ported from the Grok-1 open-source release.
Source: README §Overview, §Phoenix, phoenix/README.md.

[FACT] Phoenix uses **candidate isolation** during inference: each post is scored independently, with attention only to the user and engagement history, not to other candidates in the batch. Scores are therefore consistent and cacheable.
Source: README §Key Design Decisions §2; phoenix/README.md §Attention Mask.

[FACT] The system has **no hand-engineered features** for content relevance. The transformer learns everything from user engagement sequences.
Source: README §Key Design Decisions §1.

## Predicted engagement signals

[FACT] Phoenix predicts **15 engagement probabilities per (user, post) pair**, listed in the README:

| # | Signal | Direction |
|---|---|---|
| 1 | P(favorite) | positive |
| 2 | P(reply) | positive |
| 3 | P(repost) | positive |
| 4 | P(quote) | positive |
| 5 | P(click) | positive |
| 6 | P(profile_click) | positive |
| 7 | P(video_view) | positive |
| 8 | P(photo_expand) | positive |
| 9 | P(share) | positive |
| 10 | P(dwell) | positive |
| 11 | P(follow_author) | positive |
| 12 | P(not_interested) | **negative** |
| 13 | P(block_author) | **negative** |
| 14 | P(mute_author) | **negative** |
| 15 | P(report) | **negative** |

Source: README §Scoring and Ranking.

[FACT] The `weighted_scorer.rs` implementation references additional fine-grained signals: `share_via_dm`, `share_via_copy_link`, `vqv_score` (video quality view), `quoted_click`, and `dwell_time`. These are combined with the 15 above into the final score.
Source: home-mixer/scorers/weighted_scorer.rs.

[FACT] **Final Score = Σ (weight_i × P(action_i))** — a linear weighted sum, not a more complex non-linear combination.
Source: README §Scoring and Ranking.

[FACT] **The concrete weight values are NOT public.** The `params` module containing the actual numbers is excluded from the open-source release for security reasons.
Source: `home-mixer/scorers/weighted_scorer.rs` (the `WeightedScorer` references `params` but the values are not in the public repo).

⚠️ **This is the key epistemic anchor.** Any number you have seen online ("Retweet × 20", "Reply × 13.5") is either from the 2023 legacy `twitter/the-algorithm` repo (a different system, heavy_ranker.scala) or from third-party guesswork. The README confirms only **the direction** (positive vs. negative) and **the set** of predicted actions.

## Pre-scoring filters (a post is dropped before scoring if it triggers any)

| Filter | What it removes |
|---|---|
| `DropDuplicatesFilter` | Duplicate post IDs |
| `CoreDataHydrationFilter` | Posts that failed core metadata fetch |
| `AgeFilter` | Posts older than a threshold |
| `SelfpostFilter` | The viewer's own posts |
| `RepostDeduplicationFilter` | Reposts of content already in the candidate pool |
| `IneligibleSubscriptionFilter` | Paywalled posts the viewer can't access |
| `PreviouslySeenPostsFilter` | Posts the viewer has already seen |
| `PreviouslyServedPostsFilter` | Posts served in the current session |
| `MutedKeywordFilter` | Posts containing the viewer's muted keywords |
| `AuthorSocialgraphFilter` | Posts from accounts the viewer has blocked or muted |

Source: README §Filtering §Pre-Scoring Filters.

## Post-selection filters (after ranking, before the final feed is returned)

| Filter | What it removes |
|---|---|
| `VFFilter` | Posts that are deleted, spam, violence, gore, etc. |
| `DedupConversationFilter` | Multiple branches of the same conversation thread |

Source: README §Filtering §Post-Selection Filters.

## Author diversity

[FACT] An `AuthorDiversityScorer` **attenuates scores for repeated authors** in the candidate set, to prevent the feed from being dominated by one account.
Source: README §System Architecture diagram, §Scoring.

[FACT] An `OON Scorer` (Out-Of-Network) adjusts scores for out-of-network content separately from in-network.
Source: README §How It Works §Scoring.

## Phoenix retrieval (out-of-network discovery)

[FACT] Out-of-network content is discovered via a **two-tower model**:
- **User Tower** encodes user features and engagement history into an embedding.
- **Candidate Tower** encodes all posts into embeddings.
- Retrieval uses **dot product similarity** to fetch top-K candidates.
Source: README §Phoenix §Retrieval; phoenix/README.md §Retrieval: Two-Tower Model.

[FACT] Embeddings use **multiple hash functions** for lookup (hash-based embeddings).
Source: README §Key Design Decisions §3; phoenix/README.md §Hash-Based Embeddings.

## What the algorithm does NOT do (or is silent about)

[FACT, by absence in repo] No mention of:
- Sentiment analysis affecting ranking
- Penalties for external links (P(click) is treated as a *positive* signal)
- Time-of-day weighting at the ranking layer
- Verified/Premium account boosts at the ranking layer
- Reply-from-author boost ("150× a like") — this is a 2023-era claim, not in the 2026 README

If a user asks about any of these, answer honestly: "Not in the open-source release. Possibly true, possibly not — I can't verify."

## Key inferences from the facts (label as [INFERENCE] when used)

These are not stated in the repo, but follow logically from the [FACT]s above. Mark them clearly:

- **[INFERENCE]** Since `P(reply)`, `P(repost)`, `P(quote)`, `P(share)`, `P(share_via_dm)`, `P(share_via_copy_link)` are all separate positive signals, content that invites multiple distinct responses (e.g., a question + a quotable insight) targets more weight axes than content optimized for likes alone.
- **[INFERENCE]** Since `P(dwell)` and `dwell_time` are predicted positives, longer-form content that holds attention is rewarded *if* the user actually stays — but posts that get a glance and a scroll-past will not benefit.
- **[INFERENCE]** Since `P(not_interested)`, `P(block_author)`, `P(mute_author)`, `P(report)` are predicted negatives, content that polarizes a non-target audience can be net-negative for reach (negative signals reduce future distribution).
- **[INFERENCE]** Since `P(follow_author)` is a positive signal, posts that make a new viewer want to follow you are weighted up — this rewards posts that work standalone (someone seeing it cold can decide based on this post).
- **[INFERENCE]** The Author Diversity Scorer means posting many times in quick succession doesn't compound — only one post per author tends to surface per feed cycle.
- **[INFERENCE]** Since the Phoenix transformer learns from your full engagement *sequence* (not just recent likes), niche-consistent posting helps the model classify your audience reliably.
