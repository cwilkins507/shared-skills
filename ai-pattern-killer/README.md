# AI Pattern Killer

A Claude Code skill that detects and rewrites AI-sounding patterns in your content.

You paste a draft. It runs through three agents — detection, rewriting, scoring — and hands back clean text that doesn't read like a machine wrote it.

## How it works

**Agent 1 (Detector)** scans your draft against a database of banned words, phrases, and structural patterns. It flags everything inline.

**Agent 2 (Rewriter)** takes the flagged draft and fixes it. Cuts filler. Rewrites the rest using human-sounding alternatives. Checks its own output to make sure it didn't introduce new AI patterns.

**Agent 3 (Scorer)** scores every sentence on a 1-10 humanization scale. Anything below the threshold gets another rewrite pass. You get the final text plus a score report.

## Setup

1. Install this skill in your Claude Code environment
2. Paste any draft and ask to humanize it
3. The pipeline runs automatically

## Customization

Edit `config.yaml` to change:

- `sensitivity` — how aggressively patterns are flagged (low / medium / high / paranoid)
- `min_score_threshold` — minimum score a sentence needs to pass (default 6)
- `active_content_type` — blog, technical, social, or email (changes rewriting tone)
- `voice_profile` — describe how you write and the rewriter will match your voice

## Adding new patterns

Two ways:

**Automatic**: After the skill delivers output, it asks if anything still sounds off. Flag it. The skill logs it, adds it to the pattern database, and uses it going forward.

**Manual**: Add entries directly to the JSON files in `patterns/`. Follow the format of existing entries.

## Pattern database

- `patterns/banned_words.json` — individual words (69 entries)
- `patterns/banned_phrases.json` — multi-word patterns (62 entries)
- `patterns/banned_structures.json` — sentence and paragraph structures (44 entries)
- `patterns/exceptions.json` — things you've approved that shouldn't be flagged

## Self-updating

The skill learns from your corrections. After every 10 pieces of feedback, it consolidates: merges redundant rules, removes patterns you keep overriding, and adjusts priority weights. The changelog lives in `learning/changelog.json`.

## File structure

```
ai-pattern-killer/
├── SKILL.md              # entry point — pipeline orchestration
├── config.yaml           # settings and thresholds
├── patterns/             # the detection database
├── feedback/             # user correction processing and logs
├── rewriting/            # strategies and before/after examples
├── learning/             # self-updating engine and changelog
└── prompts/              # standalone prompts for each agent
```
