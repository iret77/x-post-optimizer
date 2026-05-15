# x-post-optimizer

A Claude skill that drafts and reviews posts for X (Twitter) based on the open-source [xai-org/x-algorithm](https://github.com/xai-org/x-algorithm) repository (released 2026).

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

### Easiest: download the `.skill` file and drop it into your Claude app

Works in **every Claude app** — macOS, Windows, Linux, iOS, Android.

1. Download [`x-post-optimizer.skill`](https://github.com/iret77/x-post-optimizer/releases/latest/download/x-post-optimizer.skill) from the [latest release](https://github.com/iret77/x-post-optimizer/releases/latest).
2. Open <https://claude.ai/customize/connectors> in your browser (the Claude app links here too — look for the "manage / customize" entry in settings).
3. Upload the `.skill` file.
4. Start a new conversation. The skill triggers automatically on relevant prompts.

That's it. No CLI, no git, no manual file copying.

### Claude Code (CLI users)

For Claude Code, clone the repo into your skills directory:

```bash
git clone https://github.com/iret77/x-post-optimizer.git ~/.claude/skills/x-post-optimizer
```

Restart your Claude Code session.

### Verify the install worked

After restarting, ask Claude in a new conversation:

> Do you have a skill called x-post-optimizer available?

A successful install gets you a "yes, here's what it does"; a failed install gets you a "no, but I can help you draft tweets anyway". If it failed, open an [issue](https://github.com/iret77/x-post-optimizer/issues).

## Structure

```
x-post-optimizer/
├── SKILL.md                       # Workflow + triggers (the manifest Claude reads)
├── LICENSE                        # MIT
├── README.md                      # This file
└── references/                    # Loaded on demand when the skill triggers
    ├── algorithm-facts.md         # Canonical [FACT] list with repo citations
    ├── format-playbooks.md        # Per-format guidance (short, long, thread, reply, quote)
    ├── review-checklist.md        # Six-section review checklist
    └── example-traces.md          # Two worked examples
```

The repo is the skill — the whole folder is what Claude loads. The `.skill` distribution file is just a zip of this same folder.

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

- New `[FACT]` entries verifiable against later updates of the upstream repo
- Counterexamples where a `[HEURISTIC]` is empirically wrong (with evidence)
- Additional format playbooks (e.g., for X Spaces transcripts, X Articles)

Please preserve the epistemic-labeling discipline — that's the whole point of the skill.
