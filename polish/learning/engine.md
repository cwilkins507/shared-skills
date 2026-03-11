# Learning Engine

Self-updating consolidation process for the pattern database.

## When to run

- After every N new entries in `feedback/feedback_log.json` (N = `consolidation_trigger` in `config.yaml`, default 10)
- When the user manually requests consolidation
- Only if `auto_consolidate` is `true` in `config.yaml`

## What it does

### 1. Review recent feedback

Read all entries in `feedback_log.json` since the last consolidation (check the most recent entry in `learning/changelog.json` for `feedback_entries_processed`). If no previous consolidation exists, review everything.

### 2. Find patterns in the corrections

Look for clusters:
- Multiple entries flagging the same underlying problem (e.g., three entries about different "corporate jargon verbs" → consider a category)
- Repeated corrections to the same word/phrase → increase its priority
- Entries that contradict each other → mark for user resolution

### 3. Merge redundant rules

If two rules in the pattern files cover the same ground, merge them. Keep the better alternatives list and the clearer explanation. Log the merge in the changelog.

### 4. Remove overridden rules

If a rule in `patterns/` has been overridden by the user (via exceptions) more than 3 times across feedback entries, remove it from the pattern file entirely. The user has made their preference clear.

### 5. Check for contradictions

If rule A says ban X but the user keeps approving X in feedback, flag it. Don't resolve contradictions automatically — ask the user which way to go.

### 6. Update priority weights

Patterns that appear frequently in feedback should be checked first during detection. Add or update a `"priority"` field (integer, higher = check first) on frequently-flagged entries.

## Output

Append a new entry to `learning/changelog.json`:

```json
{
  "version": "<semver bump>",
  "timestamp": "<ISO-8601>",
  "changes": [
    {
      "type": "added | merged | removed | adjusted",
      "file": "<which pattern file>",
      "detail": "<what changed>"
    }
  ],
  "feedback_entries_processed": [<list of entry IDs>],
  "notes": "<summary of what happened>"
}
```

## Safety rules

1. Never delete a user-created exception
2. Never merge rules across different content types without asking the user first
3. If contradictions are found, ask — don't guess
4. Before modifying any rule, note the original version in the changelog `detail` field so it can be restored
5. If unsure whether a change is safe, skip it and flag it for user review
