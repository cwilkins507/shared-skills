# claude.md — AI Pattern Killer Setup Instructions

You are helping the user build a self-updating skill called `ai-pattern-killer`. This skill detects and eliminates AI-sounding patterns from Claude-generated content using a three-agent pipeline. Follow these instructions exactly.

---

## What You're Building

A standalone skill repo with this structure:

```
ai-pattern-killer/
├── README.md
├── SKILL.md
├── config.yaml
├── patterns/
│   ├── banned_words.json
│   ├── banned_phrases.json
│   ├── banned_structures.json
│   └── exceptions.json
├── feedback/
│   ├── processing.md
│   └── feedback_log.json
├── rewriting/
│   ├── strategies.md
│   └── examples.json
├── learning/
│   ├── engine.md
│   └── changelog.json
└── prompts/
    ├── agent1_detector.md
    ├── agent2_rewriter.md
    └── agent3_scorer.md
```

---

## Step 1: Create the Root Files

### README.md

Write a short README that explains:
- What the skill does (detects and rewrites AI patterns in content)
- How to use it (paste a draft, get cleaned output through 3-agent pipeline)
- How to add new patterns (flag something → it gets logged → skill updates)
- How to customize sensitivity in config.yaml

Keep it under 80 lines. No fluff.

### SKILL.md

This is the entry point Claude reads first. It must contain:

1. **Purpose statement**: You are a content humanizer. You read the user's draft, run it through three sequential agents, and return clean output that doesn't sound like AI wrote it.

2. **File map**: Tell Claude where every file lives and what it does. Claude should read the pattern files before doing anything.

3. **Three-agent pipeline instructions**:
   - Agent 1 (Detector): Read all files in `patterns/`. Scan the draft line by line. Flag every match with an inline comment explaining what was caught and why. Reference the specific rule from the pattern files. Output the full draft with flags.
   - Agent 2 (Rewriter): Read `rewriting/strategies.md` and `rewriting/examples.json`. Take the flagged draft from Agent 1. Rewrite every flagged section using the preferred alternatives. Preserve the user's voice and intent. Do not add new AI patterns while fixing old ones. Output the clean rewrite.
   - Agent 3 (Scorer): Score every sentence in Agent 2's output on a 1-10 humanization scale. 1 = obviously AI. 10 = indistinguishable from human writing. Any sentence scoring below 6 gets sent back to Agent 2 logic for another pass. Output the final version with a score summary.

4. **Feedback capture instructions**: After delivering the final output, ask the user if anything still sounds off. If they flag something, log it using the process described in `feedback/processing.md`.

5. **Learning trigger**: After every 10 feedback entries, run the consolidation process described in `learning/engine.md`.

### config.yaml

Create with these default settings:

```yaml
sensitivity: medium          # low | medium | high | paranoid
min_score_threshold: 6       # sentences below this get rewritten
max_rewrite_passes: 3        # prevent infinite loops
voice_profile: null          # user can add their own voice description later
content_types:               # different rules per content type
  - blog
  - technical
  - social
  - email
active_content_type: blog    # default
log_feedback: true           # auto-log every correction
auto_consolidate: true       # run learning engine automatically
consolidation_trigger: 10    # number of feedback entries before consolidation
```

---

## Step 2: Create the Patterns Folder

### patterns/banned_words.json

Seed with common AI tells. Format as an array of objects:

```json
[
  {
    "word": "delve",
    "why": "overused AI filler verb",
    "alternatives": ["dig into", "explore", "look at", "get into"]
  },
  {
    "word": "landscape",
    "why": "vague AI metaphor used as filler",
    "alternatives": ["space", "market", "field", "world"]
  }
]
```

Include 15-20 starter words. Common offenders: delve, tapestry, landscape, leverage, utilize, foster, facilitate, nuanced, multifaceted, comprehensive, robust, streamline, elevate, realm, paradigm, synergy, pivotal, cornerstone, underscore, myriad.

For each one, provide 2-4 human-sounding alternatives.

### patterns/banned_phrases.json

Same format but for multi-word patterns:

```json
[
  {
    "phrase": "It's worth noting that",
    "why": "hedging filler that adds nothing",
    "alternatives": ["", "One thing —", "Also:"]
  },
  {
    "phrase": "In today's [noun] landscape",
    "why": "generic AI opener",
    "alternatives": ["Right now", "These days", "Lately"]
  }
]
```

Include 15-20 starter phrases. Common offenders: "It's worth noting", "at the end of the day", "in an increasingly [adjective] world", "the reality is", "when it comes to", "it's important to remember", "let's dive in", "without further ado", "in today's X landscape", "this is where X comes in", "here's the thing", "the beauty of X is", "at its core", "navigate the complexities", "strikes a balance between", "serves as a testament to", "a game-changer", "the key takeaway is", "moving forward", "let's unpack this".

### patterns/banned_structures.json

These are sentence-level and paragraph-level structural patterns. Format as natural language rules Claude can interpret:

```json
[
  {
    "pattern": "Three consecutive bullet points that each start with a bold word followed by a colon and explanation",
    "why": "the classic AI list format — instantly recognizable",
    "fix": "vary the structure, use some bullets without bold, mix in full sentences, break the rhythm"
  },
  {
    "pattern": "Opening paragraph that restates the user's question back to them before answering",
    "why": "filler behavior, sounds like a customer service bot",
    "fix": "start with the answer or an insight, not a restatement"
  }
]
```

Include 10-15 structural patterns. Common offenders: the bold-colon list, mirroring the question, three-part parallel structure in every paragraph, gerund phrase openers ("By leveraging..."), the "Not only X, but also Y" construction, ending every section with a one-sentence summary, consecutive sentences starting with the same word, the "Here's why that matters:" transition, overuse of em dashes for parenthetical asides, the "Let's break this down:" followed by numbered list pattern.

### patterns/exceptions.json

Start empty:

```json
{
  "allowed_words": [],
  "allowed_phrases": [],
  "allowed_structures": [],
  "notes": "Add items here when the user explicitly approves something the detector flags. This prevents the same false positive from recurring."
}
```

---

## Step 3: Create the Feedback Folder

### feedback/processing.md

Write instructions for how Claude should process user corrections:

1. When the user flags something in the output, extract:
   - The exact text that was flagged
   - What category it falls into (word, phrase, or structure)
   - The user's preferred alternative (if provided)
   - Why it was flagged (Claude should infer if user doesn't explain)

2. Log the raw correction to `feedback_log.json`

3. Determine if this should become a new pattern rule:
   - If it matches an existing category, add it to the appropriate patterns file
   - If it's a new category, create the entry with a note to watch for more examples before promoting to a hard rule
   - If the user says something was wrongly flagged, add it to `exceptions.json`

4. Confirm the update to the user in one sentence

### feedback/feedback_log.json

Start as an empty array:

```json
[]
```

Each entry will be logged in this format:

```json
{
  "id": 1,
  "timestamp": "ISO-8601",
  "flagged_text": "the exact text that was flagged",
  "category": "word | phrase | structure",
  "user_correction": "what the user wanted instead",
  "action_taken": "added to banned_words | added to exceptions | etc",
  "content_type": "blog | technical | social | email"
}
```

---

## Step 4: Create the Rewriting Folder

### rewriting/strategies.md

Write a strategy guide for Agent 2. Include these rewriting principles:

1. **Match the energy, not the format.** If the original sentence was casual, the rewrite should be casual. Don't formalize while fixing.

2. **Cut before you rewrite.** Many AI patterns exist because the sentence didn't need to exist. If removing the flagged text makes the paragraph better, remove it.

3. **Vary sentence length.** AI defaults to medium-length sentences. Mix in short punchy ones. Let some run long.

4. **Read it out loud.** Would a human say this in conversation? If not, rewrite until they would.

5. **Kill the symmetry.** AI loves balanced structures. Humans don't speak in parallel construction. Break the pattern.

6. **Specificity over abstraction.** Replace vague AI language with concrete details. "A robust solution" → "It handles 10k concurrent users without breaking."

7. **Preserve imperfection.** Human writing has rough edges. Don't polish everything into smooth, featureless prose.

8. **Voice matching.** If `config.yaml` has a `voice_profile`, match that voice. If not, default to clear, direct, conversational English.

### rewriting/examples.json

Seed with 10-15 before/after pairs:

```json
[
  {
    "before": "Delving into the intricacies of machine learning reveals a multifaceted landscape.",
    "after": "Machine learning is more complicated than most people think.",
    "rules_applied": ["banned_word:delve", "banned_word:multifaceted", "banned_word:landscape", "banned_phrase:intricacies of"]
  }
]
```

Cover different content types. Show rewrites for blog posts, technical docs, social posts, and emails. Each example should reference which rules triggered the rewrite.

---

## Step 5: Create the Learning Folder

### learning/engine.md

Write instructions for the self-updating consolidation engine:

1. **When to run**: After every 10 new entries in `feedback_log.json` (configurable in `config.yaml`), or when the user manually requests consolidation.

2. **What it does**:
   - Review all feedback entries since last consolidation
   - Identify patterns in the corrections (are multiple entries really the same underlying rule?)
   - Merge redundant rules in the pattern files
   - Remove rules that the user has repeatedly overridden via exceptions
   - Check for contradictions (rule A says ban X, but the user keeps approving X in practice)
   - Update sensitivity weights — patterns flagged frequently should have higher priority

3. **Output**: A changelog entry summarizing what was added, merged, removed, or adjusted. Append to `changelog.json`.

4. **Safety rules**:
   - Never delete a user-created exception
   - Never merge rules across different content types without user approval
   - If contradictions are found, ask the user to resolve them rather than guessing
   - Keep a backup note of any rule before modifying it so it can be restored

### learning/changelog.json

Start as an empty array:

```json
[]
```

Each entry:

```json
{
  "version": "0.1.0",
  "timestamp": "ISO-8601",
  "changes": [
    {
      "type": "added | merged | removed | adjusted",
      "file": "patterns/banned_words.json",
      "detail": "Added 'synergy' with alternatives ['working together', 'collaboration']"
    }
  ],
  "feedback_entries_processed": [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
  "notes": "First consolidation pass. Merged 'utilize' and 'leverage' into a single 'corporate jargon verbs' category."
}
```

---

## Step 6: Create the Prompts Folder

### prompts/agent1_detector.md

Write a standalone prompt for Agent 1 that:
- Instructs Claude to load and internalize all pattern files
- Process the draft line by line
- Insert inline flags using this format: `[FLAG: {category} — {rule_id} — {explanation}]`
- Provide a summary at the end: total flags, breakdown by category, worst offenders
- Be aggressive — better to over-flag than miss something

### prompts/agent2_rewriter.md

Write a standalone prompt for Agent 2 that:
- Takes the flagged draft as input
- Reads `rewriting/strategies.md` and `rewriting/examples.json`
- Rewrites every flagged section
- Removes flags from the output
- Does NOT introduce new AI patterns (critical instruction — emphasize this)
- Maintains the original structure and argument flow
- Outputs clean text ready for scoring

### prompts/agent3_scorer.md

Write a standalone prompt for Agent 3 that:
- Takes the rewritten draft as input
- Scores every sentence on a 1-10 humanization scale
- Scoring criteria:
  - 1-3: Obviously AI (pattern matches, robotic cadence, filler phrases)
  - 4-5: Suspicious (could go either way, slightly stiff)
  - 6-7: Passable (reads fine, no obvious tells)
  - 8-10: Human (natural rhythm, personality, imperfection)
- Any sentence below the threshold in `config.yaml` gets rewritten inline
- Output the final clean version plus a score report (average score, lowest scoring sentence, total sentences rewritten in this pass)

---

## Build Order

Execute in this exact sequence:

1. Create the folder structure
2. Create `config.yaml`
3. Create all pattern files (seed with starter data)
4. Create `feedback/processing.md` and empty `feedback_log.json`
5. Create `rewriting/strategies.md` and seed `rewriting/examples.json`
6. Create `learning/engine.md` and empty `changelog.json`
7. Create all three agent prompts in `prompts/`
8. Create `SKILL.md` (references everything above)
9. Create `README.md`

After building, confirm with the user that the structure is complete and ask them to test it by pasting a draft for processing.

---

## Important Notes

- Every JSON file must be valid JSON. Validate before saving.
- Every `.md` file should be concise and direct. No filler. Practice what the skill preaches.
- The user's voice is the priority. When in doubt, ask them how they'd say it.
- This skill is meant to evolve. The initial seed data is a starting point. The real value comes after weeks of use and feedback.
- If the user provides a `voice_profile` in config.yaml, all rewriting should match that voice.
- When running the three-agent pipeline, process sequentially: Agent 1 → Agent 2 → Agent 3. Do not skip agents or run in parallel.