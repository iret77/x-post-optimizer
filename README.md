# x-post-optimizer

A Claude plugin that drafts and reviews posts for X (Twitter) based on the open-source [xai-org/x-algorithm](https://github.com/xai-org/x-algorithm) repository (released 2026).

Unlike most "X algorithm" guides floating around, this skill is built around a strict epistemic rule: every recommendation is labeled as one of three categories.

- **[FACT]** — directly verifiable from the xai-org/x-algorithm source code or README
- **[INFERENCE]** — logically derivable from facts, but not stated outright in the repo
- **[HEURISTIC]** — established creator practice, not in the code; plausible but unverified

Specifically, the skill **refuses to quote concrete weight multipliers** like "Retweet = 20×" or "Reply = 13.5×" — those numbers are not in the 2026 open-source release. The `params` module containing actual weights was deliberately excluded by xAI for security reasons. Numbers you see online almost always trace back to the 2023 `twitter/the-algorithm` repo (a different system) or to guesswork.

## What it does

Two modes:

- **Generate** — give it a topic or rough idea, get drafts in any X format (short post, long post, thread, reply, quote) with an algorithmic trace explaining which engagement signals each design choice targets.
- **Review** — give it an existing draft, get a structured review against an algorithm-derived checklist with concrete rewrite suggestions.

Triggers on requests like "write a tweet", "review my X post", "draft a thread on X", "will this perform in the For You feed", "score this tweet", and similar.

## What it knows (and what it doesn't)

Grounded in the published xai-org/x-algorithm code:

- The 15 engagement signals Phoenix predicts (favorite, reply, repost, quote, click, profile_click, video_view, photo_expand, share, dwell, follow_author, not_interested, block_author, mute_author, report)
- Plus fine-grained signals from `weighted_scorer.rs` (share_via_dm, share_via_copy_link, vqv_score, quoted_click, dwell_time)
- The 10 pre-scoring filters and 2 post-selection filters
- Architecture: Thunder (in-network) + Phoenix Retrieval (out-of-network) → Phoenix transformer ranker with candidate isolation → Author Diversity scoring

Not grounded — flagged as `[HEURISTIC]` if mentioned:

- Concrete weight values (excluded from open-source release)
- Sentiment-based suppression (not in the repo)
- "Links hurt reach" (P(click) is a positive signal; the link tradeoff is inferential)
- Time-of-day, post-frequency, verified-account effects (not addressed by the open-source ranker)

## Install

This is a **Claude plugin** for the Claude Desktop app (Cowork mode and regular chat). To install from this repository:

In the Claude Desktop app, open the plugin marketplace and add `https://github.com/iret77/x-post-optimizer` as a custom plugin source, then install `x-post-optimizer`. Alternative installation paths may exist depending on your Claude version — consult the in-app plugin manager.

For **Claude Code CLI**, you can use the bundled `SKILL.md` directly:

```bash
git clone https://github.com/iret77/x-post-optimizer.git /tmp/x-post-optimizer
cp -r /tmp/x-post-optimizer/skills/x-post-optimizer ~/.claude/skills/
```

Restart your Claude session. The plugin/skill auto-triggers on relevant prompts.

## Structure

```
x-post-optimizer/
├── .claude-plugin/
│   ├── plugin.json                    # Plugin manifest
│   └── marketplace.json               # Marketplace metadata
└── skills/
    └── x-post-optimizer/
        ├── SKILL.md                   # Workflow + triggers
        └── references/
            ├── algorithm-facts.md     # Canonical [FACT] list with repo citations
            ├── format-playbooks.md    # Per-format guidance (short, long, thread, reply, quote)
            ├── review-checklist.md    # Six-section review checklist
            └── example-traces.md      # Two worked examples
```

## Source attribution

Primary source: [xai-org/x-algorithm](https://github.com/xai-org/x-algorithm), licensed under Apache 2.0. This plugin quotes architectural facts and signal names from the README and source files — facts are not copyrightable, and the plugin contains no code or substantial text passages from the upstream repository.

For legacy context only: [twitter/the-algorithm](https://github.com/twitter/the-algorithm) (2023 release, different system).

## Disclaimer

This plugin is **not affiliated with, endorsed by, or sponsored by X Corp, xAI, or any subsidiary**. "X", "Twitter", "Phoenix", "Thunder", and "Grok" are trademarks of their respective owners and are used here in a nominative, descriptive sense only.

Algorithmic performance is probabilistic. The plugin helps reason about *which* signals a post can target and *why*, based on the published architecture — but it cannot predict whether any specific post will perform, because (a) concrete weights are non-public, (b) the ranker is conditioned on user-specific engagement history, and (c) the underlying transformer is stochastic over its hash-based embeddings.

## License

[MIT](LICENSE) — Copyright (c) 2026 iret77.

## Contributing

Issues and pull requests welcome. Particularly valuable contributions:

- New `[FACT]` entries verifiable against later updates of the upstream repo
- Counterexamples where a `[HEURISTIC]` is empirically wrong (with evidence)
- Additional format playbooks (e.g., for X Spaces transcripts, X Articles)

Please preserve the epistemic-labeling discipline — that's the whole point of the plugin.
