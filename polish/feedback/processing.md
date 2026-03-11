# Feedback Processing

How to handle user corrections after delivering the final output.

## Step 1: Extract the correction

When the user flags something in the output, capture:

- **Exact text**: the specific words or sentences they flagged
- **Category**: word, phrase, or structure
- **User's preferred alternative**: if they provided one (may be blank)
- **Reason**: why it was flagged — infer from context if the user doesn't explain

## Step 2: Log it

Append a new entry to `feedback/feedback_log.json`:

```json
{
  "id": <next integer>,
  "timestamp": "<ISO-8601>",
  "flagged_text": "<exact text>",
  "category": "word | phrase | structure",
  "user_correction": "<what the user wanted instead>",
  "action_taken": "<what you did — see Step 3>",
  "content_type": "<from config.yaml active_content_type>"
}
```

## Step 3: Route the correction

Decide where this feedback goes:

1. **Matches an existing category** — add it to the appropriate file in `patterns/`. Use the same format as existing entries. Include the user's preferred alternative if they gave one.

2. **New pattern** — create the entry but add `"confidence": "low"` as a field. Watch for more examples before treating it as a hard rule.

3. **False positive** — the user says something was wrongly flagged. Add it to `patterns/exceptions.json` in the matching array (`allowed_words`, `allowed_phrases`, or `allowed_structures`).

## Step 4: Confirm

Tell the user what you did in one sentence. Example: "Added 'synergy' to banned words with your alternatives."

## Consolidation check

After logging, check the total entries in `feedback_log.json`. If it equals or exceeds the `consolidation_trigger` value in `config.yaml` (and `auto_consolidate` is true), run the learning engine described in `learning/engine.md`.
