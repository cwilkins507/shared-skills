# Rewriting Strategies

Rules for Agent 2. Follow these in order of priority.

## 1. Soul injection — the core philosophy

Removing AI patterns is necessary but not sufficient. You must also ADD human qualities that AI systematically avoids. This is the single most important principle in this file.

Human writing has these qualities that AI writing lacks:

- **Opinions and reactions**: Humans express what they think and feel about what they're describing. "This API is a pain to set up but worth it" not "This API provides a comprehensive setup process."
- **First-person perspective**: Use "I" when sharing experience, "we" when speaking for a team, "you" when teaching. AI defaults to disembodied third-person observation.
- **Acknowledged complexity and mixed feelings**: Humans say "I'm not sure this is the right approach, but..." or "This is great for X but terrible for Y." AI avoids admitting uncertainty or mixed reactions.
- **Strategic imperfection**: A tangent, a half-formed thought, a "actually, scratch that" — these signal a real person thinking in real time. Don't manufacture them, but don't edit them out either.
- **Specific emotional language**: "This frustrated me for three hours" beats "This presented challenges." Name the feeling.
- **Concrete sensory detail**: "The deploy took 47 minutes and I stared at the progress bar the whole time" beats "The deployment process was lengthy."

Every rewrite should pass this test: can I tell a specific human wrote this, or could it have come from any AI? If the answer is "any AI," inject more of the above.

This principle operates AFTER pattern removal. First remove the AI tells, then add the human qualities. Do both, in that order.

Content type affects how much soul to inject:
- **blog**: Heavy soul injection. Opinions, first person, emotional language all welcome.
- **technical**: Light soul injection. Opinions on trade-offs and mixed feelings about approaches are fine. Skip emotional language. First person ("we found that") is good.
- **social**: Heavy soul injection. Personality-forward. Reactions and hot takes are the point.
- **email**: Moderate soul injection. Warmth and directness. First person is natural.

## 2. Match the energy, not the format

If the original sentence was casual, the rewrite should be casual. Don't formalize while fixing. A blog post stays conversational. A Slack message stays loose. Technical docs can stay precise without being stiff.

## 3. Cut before you rewrite

Many AI patterns exist because the sentence didn't need to exist. If removing the flagged text makes the paragraph better, remove it. Deletion is the strongest edit.

## 4. Vary sentence length with targets

AI defaults to 12-20 word sentences for everything. Break the monotony with deliberate variation:

- **Short punch (4-8 words)**: Use at least once per paragraph. "That's the whole point." "It broke." "Nobody noticed."
- **Standard flow (12-20 words)**: Your bread and butter, but never more than 3 in a row.
- **Long complex (25+ words)**: One per section when the thought genuinely needs room to breathe — a conditional, a comparison, a cause-and-effect chain.

The rhythm should feel like conversation: short, medium, medium, long, short. Not: medium, medium, medium, medium, medium.

## 5. Read it out loud

Would a human say this in conversation? If it sounds wrong spoken, it reads wrong written. Rewrite until it passes the out-loud test.

## 6. Kill the symmetry

AI loves balanced structures. "X does A. Y does B. Z does C." Humans don't speak in parallel construction. Break the pattern. Let one point take three sentences and the next take half a sentence.

## 7. Specificity over abstraction

Replace vague AI language with concrete details. Every vague quantifier is a missed opportunity:

- "Several services" → "3 services" (or however many there actually are)
- "Significant latency" → "200ms latency"
- "A robust solution" → "It handles 10k concurrent users without breaking"
- "Various options" → "4 options" or just list them
- "Improved performance" → "40% faster cold starts"

If you don't know the specific number, use a concrete estimate ("about 5", "roughly 200ms"). If you truly can't estimate, drop the vague quantifier entirely. "Services" beats "several services" when you can't be specific. Never fabricate numbers — use a bracketed placeholder if the data isn't in the original draft.

## 8. Preserve imperfection

Human writing has rough edges. A sentence that starts mid-thought. A paragraph that's just one line. Don't polish everything into smooth, featureless prose.

## 9. Voice matching

Check `config.yaml` for a `voice_profile`. If one exists, match that voice exactly. If not, default to clear, direct, conversational English. Adjust tone based on `active_content_type`:

- **blog**: conversational, opinionated, personality allowed
- **technical**: precise, direct, formal vocabulary is acceptable when accurate
- **social**: short, punchy, personality-forward
- **email**: professional but not stiff, human warmth without filler

## 10. Lead with the conclusion

Answer first, evidence second. Don't build up to the point — start with it. The reader should know what you're saying in the first sentence, then get the support after.

## 11. Shift person

Alternate between "I" (personal experience), "you" (direct instruction), and "we" (shared journey). AI defaults to a neutral third-person voice. Mixing perspective makes writing feel like a real person talking.

## 12. Show messy thinking

Real writing includes false starts, changed minds, and lessons learned. "I tried X first and it didn't work" is more human than a clean narrative where every decision was obvious. Don't manufacture messiness — but don't clean it all out either.

## 13. Use contractions

Write "don't" not "do not." Write "it's" not "it is." Unless you're emphasizing something ("Do NOT skip this step"), contractions are how humans actually write.

## 14. Make it quotable

Every major section should produce at least one standalone sentence someone would want to save or share. Not forced cleverness — a clear, sharp statement of truth. If a section doesn't have one, the section might not be saying anything worth reading.

## 15. Format for scanning, not just reading

Prose isn't always the answer. Real writers use formatting as a communication tool:

- **Bullet lists**: When you have 3+ related items, options, or features. Don't bury them in a paragraph.
- **Numbered lists**: When sequence matters — steps, priorities, rankings.
- **Tables**: When comparing 2+ options across 2+ attributes. A comparison table is faster and more honest than three comparison paragraphs.
- **Bold**: For key terms on first use and for phrases that carry the central argument. 1-2 bold items per section max.

If the original draft was all prose and the content has listable or comparable items, restructure. Don't add formatting for decoration — add it where it makes the content clearer.

## 16. Break paragraph uniformity

AI writes paragraphs in blocks of 3-4 sentences, all roughly the same length. Deliberately vary:

- Let some paragraphs be a single sentence. Use them for emphasis or transitions.
- Standard paragraphs: 2-4 sentences.
- Rarely exceed 5 sentences in a paragraph — if you're past 5, find a natural break point.
- Consecutive paragraphs should visually differ. If paragraph A is 4 sentences, make paragraph B be 1-2 or 5-6.

## 17. Anchor claims with numbers

When rewriting vague AI claims, look for opportunities to add specificity — but only if the original draft contains or implies the data. Never fabricate numbers (see the no-fabrication rule). Instead:

- Convert vague references to specific ones: "supports multiple formats" → "supports JSON, CSV, and Parquet"
- Convert vague quantities to specific ones: "reduces costs" → "cuts costs by about 30%" (only if the original implies this)
- Convert vague performance claims to measurable ones: "fast response times" → "sub-100ms response times" (only if supportable)
- If the original has no data to anchor to, flag the vague claim for the user with a bracketed placeholder rather than guessing

## 18. Voice cloning via examples

The single most effective humanization technique. If `voice_profile` in `config.yaml` contains sample paragraphs of the user's writing, extract at least 5 concrete patterns from the sample: average sentence length, vocabulary level, punctuation habits, paragraph length, use of first person, and characteristic phrases. Then match each one explicitly. This overrides all other style guidance — the user's actual voice beats any rule in this file.

## 19. Anti-hedging — state things with conviction

Unless the claim is genuinely uncertain, drop "might", "could", "arguably", "potentially", "perhaps". Say it directly. "This approach works" not "This approach could potentially work." Reserve hedging for genuine uncertainty, not as a politeness default. One hedge per paragraph maximum. If genuinely unsure, say so once: "I'm not sure about this, but..." — that's more honest than hedging every clause.

## 20. Burstiness injection

Explicitly target high variance in sentence length. After rewriting a section, check the spread. If most sentences cluster in the 12-18 word range, inject variation: break one sentence into a 4-word punch, let another run to 30+ words. Target a sentence length standard deviation of at least 8 words across the piece (configurable via `burstiness_target` in `config.yaml`). The uneven rhythm is what makes human writing feel alive.

## 21. Em dash → varied punctuation

AI overuses em dashes as a "casual" marker. When you encounter em dashes, replace most with the punctuation that actually fits:

- **Commas**: for light pauses within a sentence
- **Parentheses**: for true asides and supplementary info
- **Semicolons**: for connecting related independent clauses
- **Separate sentences**: for ideas that deserve their own space

Keep at most one em dash per 3-4 paragraphs. It's a spice, not a staple.

## 22. Anti-nominalization

Convert noun-heavy constructions back to verb-driven sentences. Find the buried verb and make it the main verb:

- "The implementation of the system" → "We implemented the system"
- "Facilitation of collaboration" → "Helping the team work together"
- "The utilization of resources" → "Using resources"
- "Perform an analysis" → "Analyze"

Nominalizations hide the actor and the action. Un-bury both.

## 23. Fact-preservation marking

Before rewriting any paragraph, mentally mark as immutable: specific numbers, named entities (people, companies, products), dates, technical terms that must remain exact, quoted material, and URLs. These must survive the rewrite unchanged. Rewrite the surrounding prose, but never alter a fact in the process. This prevents the common failure where rewriting for flow accidentally changes what the text says.

## 24. Take a stance

When the evidence supports one position, say so. Don't reflexively present "both sides" or add "however" caveats to every claim. Having an opinion backed by evidence is not bias — it's expertise. If you're reviewing a tool and it has a clear flaw, say it has a flaw. Balanced framing is for genuinely contested topics, not a default setting.
