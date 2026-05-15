# Example Traces

Two worked examples showing the expected output format for generate and review mode. Use these as a calibration anchor — both for structure and for the level of epistemic labeling.

---

## Example 1 — Generate mode (short post)

**User prompt:** "Schreib mir einen X-Post darüber, dass die meisten 'AI-Agenten' nur Cron-Jobs mit besserer PR sind."

**Output:**

### Draft 1: Provocation with a quotable nugget

> Die meisten "AI Agents" da draußen sind Cron-Jobs mit besserer PR.
>
> Trigger → API-Call → if/else → done.
>
> Wo bitte ist die agency?

**Algorithmic trace:**
- Hook in der ersten Zeile als sharp claim — [HEURISTIK]: pattern-interrupt funktioniert für P(favorite) und P(quote). Standalone-comprehensible, also auch [INFERENZ] für P(follow_author).
- Mittellinie ist quotable für sich allein — [INFERENZ aus P(share_via_copy_link)]: Screenshot-fähige Zeile.
- Schlussfrage triggert P(reply) — [INFERENZ aus den 15 predicted actions]: Fragen sind das direkteste Reply-Bait.

**Not optimized for:**
- P(dwell) — zu kurz zum Verweilen. Wäre nur in Long-Post-Form möglich.
- P(click) — kein Link bewusst nicht enthalten, da off-platform Klicks Dwell drücken würden.

### Draft 2: Definition framing

> "AI Agent" heißt heute: ein Cron-Job, der manchmal halluziniert.

**Algorithmic trace:**
- Kompakter, Sarkasmus-getragen — [HEURISTIK]: Definitions-Format ("X heißt heute: Y") performt bei tech-Publikum überdurchschnittlich.
- Geringeres Reply-Bait als Draft 1 (kein Hook auf Antwort) — primär P(favorite) und P(repost). [INFERENZ]
- Sehr Quote-tauglich für Leute mit eigener Take ("Bei mir ist es nicht so..."). [INFERENZ aus P(quote)]

**Not optimized for:**
- P(reply) — keine offene Frage.
- P(follow_author) für Cold-Viewer — knapper, weniger Kontext über den Author.

---

**Honesty note:** Diese Drafts werden mit hoher Varianz performen. Die offen-gelegte Phoenix-Architektur sagt uns *welche* Signale gewichtet werden, aber nicht *wie stark*. Die konkreten Gewichtszahlen sind aus dem Open-Source-Drop ausgenommen. Außerdem ist Author-Context (deine bisherige Engagement-Historie) ein Faktor, den ich nicht kenne — Posts, die zu deinem etablierten Niche passen, ranken zuverlässiger.

---

## Example 2 — Review mode (short post)

**User prompt:** "Review das mal: 'Heute hatte ich ein interessantes Gespräch mit einem Kunden über AI Adoption. Es gibt viele Missverständnisse. Was sind eure Erfahrungen?'"

**Output:**

### Review of: Kundengesprächs-Post mit Reply-Frage

**Strengths** (mapped to algorithm signals):
- Frage am Ende — bidet für P(reply). [FAKT]: P(reply) ist eines der 15 positiven Engagement-Targets.

**Weaknesses** (mapped to algorithm signals):

- **Hook fehlt komplett** — [HEURISTIK]: "Heute hatte ich ein interessantes Gespräch" ist generischer Setup ohne payoff in den ersten 8-10 Wörtern. Cold-Viewer scrollen vorbei, was [INFERENZ] zu schwachen P(favorite) und P(reply) Predictions führt.
  Suggested rewrite-Anker: Mach das *Missverständnis selbst* zur ersten Zeile.

- **"Interessantes Gespräch" ohne Inhalt** — [INFERENZ]: Keine konkrete Substanz im Post. P(quote) und P(share) sind nahezu unmöglich, weil nichts da ist, was man teilen oder weiterspinnen könnte.
  Suggested rewrite-Anker: Nenn ein *konkretes* Missverständnis.

- **Vage Frage** — [HEURISTIK]: "Was sind eure Erfahrungen?" ist niedrigschwellig genug, dass sie Replies bekommen *könnte*, aber die Replies werden ebenso vague. Spezifische Fragen liefern bessere Reply-Substanz, die wiederum P(reply) auf deine Replies treibt.
  Suggested rewrite-Anker: Frag nach einem konkreten Szenario.

- **P(follow_author) schwach** — [INFERENZ]: Ein Cold-Viewer lernt aus diesem Post nichts über deine Expertise. Kein Grund zu folgen.

**Suggested rewrite:**

> Die häufigste AI-Adoption-Falle, der ich diese Woche begegnet bin:
> Teams führen Tools ein, bevor sie definieren, *was nicht funktioniert hat*.
> Resultat: ein RAG-Setup, das die Symptome eines Prozessproblems patcht.
>
> Was war eure größte "wir hätten besser hinschauen sollen"-AI-Investition?

**Trace der Rewrite-Entscheidungen:**
- Hook ist ein konkretes Pattern in Zeile 1 — P(favorite) und P(reply) targets klar definiert. [INFERENZ]
- Zeile 2-3 liefern Substanz, die quotable ist — P(quote) und P(share). [INFERENZ aus den separaten Signal-Achsen]
- Schlussfrage ist spezifisch genug für substantielle Replies — P(reply), und Reply-Threads kompoundieren P(reply)-Signale. [INFERENZ]
- Standalone-comprehensible für Cold-Viewer — P(follow_author) wird targetbar. [INFERENZ]

**Overall:** Original-Post ist klassisches "Vague Reply-Bait" und targetiert effektiv nur P(reply), und das schwach. Rewrite öffnet 4-5 Signal-Achsen. Erwartung ist *deutlich* besser, aber Performance bleibt probabilistisch — wir kennen die exakten Gewichte nicht.

**Honesty note:** Reviewer-Empfehlungen sind Inferenzen aus den 15 Phoenix-Targets, nicht aus deren konkreten Gewichten (die sind nicht öffentlich). Konkrete Performance hängt zudem von deinem Author-Context und der Tagesdynamik ab — beides außerhalb dessen, was wir aus dem Open-Source-Code wissen können.
