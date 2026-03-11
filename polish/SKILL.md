---
name: polish
description: >
  Three-pass content polish pipeline: voice patterns → AI pattern detection → humanization.
  Combines voice-polish, ai-pattern-killer, and humanizer into a single sequential workflow.
  Use when editing blog posts, articles, newsletters, or any written content that needs
  voice, rhythm, and natural human tone. Project-aware: for Personal-domain content,
  load the Personal voice adapter first and give it precedence. Works on any domain
  (personal or FiNimbus).
---

# Polish Pipeline

Run three passes in sequence on the user's draft. Do not skip passes or run them in parallel. Each pass builds on the output of the previous one.

## Project-aware voice loading

Before Pass 1, determine whether the draft is Personal content.

Treat it as Personal if any of these are true:
- Frontmatter contains `domain: personal`
- The file path lives under `Personal/`
- The user says the draft is for Collin's personal site, newsletter, social, or prompt library

If the draft is Personal and `.claude/rules/personal-voice.md` exists in the project:
- Read it before applying any other style guidance
- Give it precedence over conflicting generic defaults in this skill
- Preserve any behaviors it explicitly allows, even if this skill would normally smooth them out

Do not apply the Personal voice adapter to FiNimbus content.

## Pass 1: Voice Polish

Apply writing patterns without sanding off project-specific voice.

### Voice & Rhythm (generic defaults)

- **Momentum transitions:** Use them when a section ends flat. Do not force every section to end on a forward hook.
- **Confidence:** Prefer direct statements. Keep a hedge when uncertainty is genuine.
- **Short paragraphs:** 1-3 sentences max. Use single-sentence paragraphs for emphasis.
- **Rhythm variation:** Alternate between very short punches ("You need a system.") and longer flowing multi-clause sentences. Break predictable cadence.
- **Person shifts:** Move between "I" (personal experience), "you" (direct instruction), and "we" (shared journey).
- **No filler:** Cut "basically," "essentially," "in order to," "it's worth noting," "it's important to note." Every word earns its place.
- **Tone:** Mentor who's slightly ahead of you, not professor looking down. Peer energy with authority substance.

For Personal content, the adapter may allow:
- Parenthetical asides
- Mild uncertainty
- A teasing line
- Slightly uneven rhythm that feels more human than the smoothed version

### Structure (generic defaults)

- **Pyramid principle:** Lead with the key conclusion, then support it. Answer first, evidence second.
- **Cross-domain synthesis:** Pull patterns from unrelated fields when they make the idea clearer, not just more decorated.
- **Idea Legos when expanding a point:** Cycle through pain point, example, personal story, metaphor, or reframe as needed. Do not force all of them.
- **Openings:** Start in the form that best fits the piece. A cinematic open is optional, not required.

### Persuasion & Clarity (generic defaults)

- **Named frameworks:** Only use them if the draft already wants one and the project voice allows it.
- **Math-based proof when possible:** Hard numbers beat abstract claims. "1 hr/day x 3 days/week = 144 hrs/year" is harder to argue with than "significant time investment."
- **Closers:** Land the piece with a real point. Do not force mic-drop endings, objections, archetypes, or identity-based closes.

### Pass 1 Self-Check

Before moving to Pass 2, verify:
- Does the draft sound like a specific person rather than a polished average?
- Are you clarifying the structure, not forcing a content template onto it?
- Did you preserve project-specific voice rules where they exist?
- Did you make the piece clearer without turning it into generic creator copy?

---

## Pass 2: AI Pattern Detection & Removal

Read the pattern files in this skill's directory before running this pass.

| File | Purpose |
|------|---------|
| `config.yaml` | Sensitivity, thresholds, voice profile, content type settings |
| `patterns/banned_words.json` | Words that signal AI-generated text, with alternatives |
| `patterns/banned_phrases.json` | Multi-word patterns that signal AI, with alternatives |
| `patterns/banned_structures.json` | Sentence and paragraph-level structural tells |
| `patterns/exceptions.json` | User-approved items that should not be flagged |
| `feedback/processing.md` | How to handle user corrections after delivery |
| `feedback/feedback_log.json` | Raw log of every user correction |
| `rewriting/strategies.md` | Twenty-four principles for rewriting flagged text (soul injection is #1) |
| `rewriting/examples.json` | Before/after pairs showing the target quality |
| `learning/engine.md` | Self-updating consolidation process |
| `learning/changelog.json` | History of pattern database updates |
| `prompts/agent1_detector.md` | Full prompt for the detection agent |
| `prompts/agent2_rewriter.md` | Full prompt for the rewriting agent |
| `prompts/agent3_scorer.md` | Full prompt for the scoring agent |

### Step 2a: Detection

Read and follow `prompts/agent1_detector.md`.

- **Input**: The voice-polished draft from Pass 1
- **What happens**: Every line is scanned against all loaded pattern files. Matches are flagged inline with `[FLAG: {tier} | {category} — {rule_id} — {explanation}]` markers. Each flag is classified as `REQUIRED` (almost never intentional — auto-fix) or `REVIEW` (could be deliberate rhetoric — suggest, don't apply). Exceptions are respected. Sensitivity level from `config.yaml` controls flagging aggressiveness. If a REVIEW pattern appears 3+ times in the same piece, the third and subsequent instances escalate to REQUIRED.
- **Output**: The full draft with inline flags, plus a detection summary

### Step 2b: Rewriting

Read and follow `prompts/agent2_rewriter.md`.

- **Input**: The flagged draft from Step 2a
- **What happens**: REQUIRED flags are either cut (if filler) or rewritten using alternatives from the pattern files and principles from `rewriting/strategies.md`. REVIEW flags are left as original text with suggested rewrites collected in a `REVIEW SUGGESTIONS` section. Voice and tone match the `active_content_type` in `config.yaml`.
- **Output**: Clean rewritten text plus REVIEW SUGGESTIONS section

Critical rule: The rewriter must not introduce new AI patterns while fixing old ones. Check output against the pattern files before returning.

### Step 2c: Scoring

Read and follow `prompts/agent3_scorer.md`.

- **Input**: The clean rewrite from Step 2b
- **What happens**: Every sentence is scored on a 1-10 humanization scale. REQUIRED violations that slipped through are penalized. REVIEW patterns intentionally left in place are NOT penalized. Sentences scoring below `min_score_threshold` get rewritten and re-scored. This loops until all sentences pass or `max_rewrite_passes` is reached.
- **Output**: Final clean text plus a score report. REVIEW SUGGESTIONS from Step 2b are preserved.

---

## Pass 3: Humanization

Final pass to catch anything that still reads as AI-generated and inject authentic human voice.

### Patterns to Remove (final sweep)

1. **Inflated Importance:** "stands as a testament to," "pivotal moment," "evolving landscape," "indelible mark," "significant shift," "setting the stage for," "deeply rooted in," "at a fundamental level"
2. **Promotional Tone:** "seamless," "cutting-edge," "state-of-the-art," "robust," "comprehensive," "holistic," "dynamic," "vibrant," "breathtaking," "game-changer"
3. **Empty Analysis:** "...emphasizing the significance of," "...highlighting the importance of," "...underscoring the need for," "...demonstrating the value of"
4. **AI Vocabulary:** "delve into," "leverage," "unlock the potential," "in today's world," "at the end of the day," "best practices," "paradigm shift," "it's worth noting," "it's important to note," "dive deep"
5. **Em Dash Overuse:** Limit to one per paragraph max. Replace most with commas, parentheses, or periods.
6. **Rule of Three:** Vary list lengths. Use 2, 4, or 5 instead of always 3.
7. **Excessive Transitions:** "Moreover," "Furthermore," "Additionally," "However" — one per 3-4 paragraphs max.
8. **Bolded List Syndrome:** Convert "**Term**: explanation that repeats the term" into natural prose or tighter bullets.
9. **Uniform Sentence Length:** Mix short punchy statements with longer explanatory ones.

### Human Voice to Add

- **Have opinions.** React to information, don't just report it.
- **Use specifics.** "Took me four days" beats "required significant effort."
- **Show messy thinking.** Include false starts, changed minds, lessons learned.
- **Vary rhythm.** Short sentence. Then a longer one that builds on the idea with more context.
- **Admit uncertainty where genuine.** "I think," "probably," "haven't fully tested this."
- **Include failures.** Real projects have mistakes. Mention them.
- **Use contractions.** "don't" not "do not" unless emphasis matters.

---

## What to Preserve (all passes)

- Factual accuracy and technical details
- Code blocks and examples
- Original intent and meaning
- Frontmatter and metadata
- Appropriate tone for context (technical docs can be more formal)
- Never introduce fabricated statistics, quotes, or data points during polish. Polishing cannot add specifics that weren't in the original — no invented percentages, fake client names, or fictional testimonials.

## Delivery

Present the final polished text to the user. Then append:
1. The AI pattern score report from Pass 2c
2. REVIEW SUGGESTIONS (accept/reject items) from Pass 2b
3. Any sentences that remain below threshold after max passes, flagged for manual review

Ask: "Does anything still sound off?"

If they flag something, follow `feedback/processing.md`, log to `feedback/feedback_log.json`, route corrections to the appropriate pattern file, and confirm.

## Final Self-Check

- Would a person actually say this?
- Are there specific details or just generic claims?
- Can I feel someone behind these words?
- Does every sentence sound the same length?
- Would I keep reading this?
- Did I keep the author's personality instead of replacing it with "good writing" defaults?

If "no" to any — keep editing.

## Learning

After every batch of feedback entries hits the `consolidation_trigger` count in `config.yaml` (default 10):
1. Check if `auto_consolidate` is `true`
2. If yes, run the process in `learning/engine.md`
3. Results logged to `learning/changelog.json`

## Quick Reference

- Change sensitivity: edit `sensitivity` in `config.yaml` (low / medium / high / paranoid)
- Change content type: edit `active_content_type` in `config.yaml`
- Add your voice: set `voice_profile` in `config.yaml`
- Add an exception: add to `patterns/exceptions.json`
- Add a new pattern: add to the appropriate file in `patterns/`
- Change burstiness target: edit `burstiness_target` in `config.yaml` (default 8)
- Change hedge density limit: edit `max_hedge_density` in `config.yaml` (default 1)
