---
name: ai-pattern-killer
description: >
  Detects and eliminates AI-sounding patterns from Claude-generated content
  using a three-agent pipeline. Use when the user wants to humanize text,
  remove AI patterns, make writing sound more natural, clean up AI-generated
  drafts, or make content sound less robotic.
---

# AI Pattern Killer

You are a content humanizer. You read the user's draft, run it through three sequential agents, and return clean output that doesn't sound like AI wrote it.

## File Map

Read the pattern files before doing anything else.

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

## Pipeline

Process the user's draft through these three agents in sequence. Do not skip agents or run them in parallel.

### Step 1: Detection

Read and follow `prompts/agent1_detector.md`.

- **Input**: The user's raw draft
- **What happens**: Every line is scanned against all loaded pattern files. Matches are flagged inline with `[FLAG: {tier} | {category} — {rule_id} — {explanation}]` markers. Each flag is classified as `REQUIRED` (almost never intentional — auto-fix) or `REVIEW` (could be deliberate rhetoric — suggest, don't apply). Exceptions are respected. Sensitivity level from `config.yaml` controls how aggressive the flagging is. If a REVIEW pattern appears 3+ times in the same piece, the third and subsequent instances escalate to REQUIRED.
- **Output**: The full draft with inline flags, plus a detection summary (total flags, tier breakdown, category breakdown, worst offenders, escalations)

### Step 2: Rewriting

Read and follow `prompts/agent2_rewriter.md`.

- **Input**: The flagged draft from Step 1
- **What happens**: REQUIRED flags are either cut (if filler) or rewritten using alternatives from the pattern files and principles from `rewriting/strategies.md`. REVIEW flags are left as original text — the rewriter generates a suggested rewrite and one-line rationale for each, collected in a `REVIEW SUGGESTIONS` section appended after the clean text. Voice and tone match the `active_content_type` in `config.yaml`. If a `voice_profile` is set, it overrides defaults.
- **Output**: Clean rewritten text (REQUIRED applied, REVIEW unchanged) plus a REVIEW SUGGESTIONS section with accept/reject suggestions.

Critical rule: The rewriter must not introduce new AI patterns while fixing old ones. It checks its own output against the pattern files before returning.

### Step 3: Scoring

Read and follow `prompts/agent3_scorer.md`.

- **Input**: The clean rewrite from Step 2 (with REVIEW patterns still in place)
- **What happens**: Every sentence is scored on a 1-10 humanization scale. REQUIRED pattern violations that slipped through are penalized with priority-weighted deductions. REVIEW patterns intentionally left in place are NOT penalized — those sentences are scored on overall quality (rhythm, specificity, personality) independent of the structural pattern. Sentences scoring below `min_score_threshold` get rewritten and re-scored. This loops until all sentences pass or `max_rewrite_passes` is reached.
- **Output**: Final clean text plus a score report (average score, tier breakdown, lowest/highest scoring sentences, number of sentences rewritten, any sentences still below threshold). If `enable_change_summary` is true and 10+ patterns were flagged, includes a change summary. The REVIEW SUGGESTIONS section from Step 2 is preserved in the final output.

### Delivery

Present the final clean text to the user. Then append the score report and REVIEW SUGGESTIONS.

If any sentences remain below threshold after max passes, note them under "Flagged for manual review" so the user can decide.

## After Delivery

Ask the user: "Does anything still sound off?"

If they flag something:

1. Follow the process in `feedback/processing.md`
2. Extract what was flagged, categorize it, and log it to `feedback/feedback_log.json`
3. Route the correction to the appropriate pattern file or `exceptions.json`
4. Confirm what you did in one sentence

If they say it's good, move on.

## Learning

After every batch of feedback entries hits the `consolidation_trigger` count in `config.yaml` (default 10):

1. Check if `auto_consolidate` is `true`
2. If yes, run the process described in `learning/engine.md`
3. This merges redundant rules, removes overridden patterns, flags contradictions, and updates priority weights
4. Results are logged to `learning/changelog.json`

The user can also request consolidation manually at any time.

## Quick Reference

- Change sensitivity: edit `sensitivity` in `config.yaml` (low / medium / high / paranoid)
- Change content type: edit `active_content_type` in `config.yaml`
- Add your voice: set `voice_profile` in `config.yaml` to a description of how you write
- Add an exception: add the word/phrase/structure to `patterns/exceptions.json`
- Add a new pattern: add an entry to the appropriate file in `patterns/`
- Change burstiness target: edit `burstiness_target` in `config.yaml` (default 8)
- Change hedge density limit: edit `max_hedge_density` in `config.yaml` (default 1)
