# x-post-optimizer

A Claude skill that drafts and reviews posts for X (Twitter) based on the open-source [xai-org/x-algorithm](https://github.com/xai-org/x-algorithm) repository (released 2026).

Unlike most "X algorithm" guides floating around, this skill is built around a strict epistemic rule: every recommendation is labeled as one of three categories.

- **[FAKT]** — directly verifiable from the xai-org/x-algorithm source code or README
- **[INFERENZ]** — logically derivable from facts, but not stated outright in the repo
- **[HEURISTIK]** — established creator practice, not in the code; plausible but unverified

Specifically, the skill **refuses to quote concrete weight multipliers** like "Retweet = 20×" or "Reply = 13.5×" — those numbers are not in the 2026 open-source release. The `params` module containing actual weights was deliberately excluded by xAI for security reasons. Numbers you see online almost always trace back to the 2023 `twitter/the-algorithm` repo (a different system) or to guesswork.

## What it does

Two modes:

- **Generate** — give it a topic or rough idea, get drafts in any X format (short post, long post, thread, reply, quote) with an algorithmic trace explaining which engagement signals each design choice targets.
- **Review** — give it an existing draft, get a structured review against an algorithm-derived checklist with concrete rewrite suggestions.

Triggers on requests like "write a tweet", "review my X post", "schreib einen X-Post zu X", "wird das auf X performen", "thread schreiben", and similar.

## What it knows (and what it doesn't)

Grounded in the published xai-org/x-algorithm code:

- The 15 engagement signals Phoenix predicts (favorite, reply, repost, quote, click, profile_click, video_view, photo_expand, share, dwell, follow_author, not_interested, block_author, mute_author, report)
- Plus fine-grained signals from `weighted_scorer.rs` (share_via_dm, share_via_copy_link, vqv_score, quoted_click, dwell_time)
- The 10 pre-scoring filters and 2 post-selection filters
- Architecture: Thunder (in-network) + Phoenix Retrieval (out-of-network) → Phoenix transformer ranker with candidate isolation → Author Diversity scoring

Not grounded — flagged as `[HEURISTIK]` if mentioned:

- Concrete weight values (excluded from open-source release)
- Sentiment-based suppression (not in the repo)
- "Links hurt reach" (P(click) is a positive signal; the link tradeoff is inferential)
- Time-of-day, post-frequency, verified-account effects (not addressed by the open-source ranker)

## Install

For Claude Code or Cowork:

```bash
git clone https://github.com/iret77/x-post-optimizer.git ~/.claude/skills/x-post-optimizer
```

Or download the `.skill` file from the [Releases](../../releases) page and unzip into `~/.claude/skills/`.

For project-scoped use:

```bash
git clone https://github.com/iret77/x-post-optimizer.git <repo>/.claude/skills/x-post-optimizer
```

Restart your Claude session. The skill auto-triggers on relevant prompts.

## Structure

```
x-post-optimizer/
├── SKILL.md                       # Manifest + workflow
└── references/
    ├── algorithm-facts.md         # Canonical [FAKT] list with repo citations
    ├── format-playbooks.md        # Per-format guidance (short, long, thread, reply, quote)
    ├── review-checklist.md        # Six-section review checklist
    └── example-traces.md          # Two worked examples (DE)
```

## Source attribution

Primary source: [xai-org/x-algorithm](https://github.com/xai-org/x-algorithm), licensed under Apache 2.0. This skill quotes architectural facts and signal names from the README and source files — facts are not copyrightable, and the skill contains no code or substantial text passages from the upstream repository.

For legacy context only: [twitter/the-algorithm](https://github.com/twitter/the-algorithm) (2023 release, different system).

## Disclaimer

This skill is **not affiliated with, endorsed by, or sponsored by X Corp, xAI, or any subsidiary**. "X", "Twitter", "Phoenix", "Thunder", and "Grok" are trademarks of their respective owners and are used here in a nominative, descriptive sense only.

Algorithmic performance is probabilistic. The skill helps reason about *which* signals a post can target and *why*, based on the published architecture — but it cannot predict whether any specific post will perform, because (a) concrete weights are non-public, (b) the ranker is conditioned on user-specific engagement history, and (c) the underlying transformer is stochastic over its hash-based embeddings.

## License

[MIT](LICENSE) — Copyright (c) 2026 iret77.

## Contributing

Issues and pull requests welcome. Particularly valuable contributions:

- New `[FAKT]` entries verifiable against later updates of the upstream repo
- Counterexamples where a `[HEURISTIK]` is empirically wrong (with evidence)
- Additional format playbooks (e.g., for X Spaces transcripts, X Articles)

Please preserve the epistemic-labeling discipline — that's the whole point of the skill.
