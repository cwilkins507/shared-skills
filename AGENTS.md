# AGENTS.md — shared-skills

Monorepo of Claude Code agent skills (custom and third-party). All skills are symlinked from `~/.claude/skills/` so edits here propagate everywhere.

## Quick Orientation

1. **Read `README.md`** for the full skill inventory and setup instructions.
2. **Run `ls` at the root** to see all skill directories.
3. Each skill directory contains a `SKILL.md` (required) plus optional supporting files.

## Skill Structure

Every skill follows the [Agent Skills](https://agentskills.io) open standard. The minimum viable skill is a single `SKILL.md` file:

```
skill-name/
├── SKILL.md              # Main skill definition (required)
├── references/           # Supporting docs loaded on demand (optional)
├── patterns/             # Pattern databases, JSON files (optional)
├── prompts/              # Agent prompts for multi-step skills (optional)
├── scripts/              # Executable scripts (optional)
├── examples/             # Example outputs (optional)
└── config.yaml           # Skill configuration (optional)
```

## SKILL.md Format

Every `SKILL.md` has two parts:

1. **YAML frontmatter** (between `---` markers) — tells Claude when to use the skill
2. **Markdown body** — instructions Claude follows when the skill is invoked

### Required Frontmatter

```yaml
---
name: skill-name              # Lowercase, hyphens only. Becomes the /slash-command.
description: >                # What the skill does and when to use it.
  One-paragraph description.  # Claude uses this to decide when to auto-load.
---
```

### Optional Frontmatter Fields

| Field | Purpose |
|---|---|
| `disable-model-invocation` | `true` = only user can invoke via `/name`. Prevents Claude from auto-triggering. |
| `user-invocable` | `false` = hidden from `/` menu. Background knowledge only. |
| `allowed-tools` | Tools Claude can use without permission prompts (e.g., `Read, Grep, Glob`). |
| `context` | `fork` = runs in an isolated subagent context. |
| `agent` | Subagent type when `context: fork` (e.g., `Explore`, `Plan`, `general-purpose`). |
| `argument-hint` | Autocomplete hint (e.g., `[issue-number]`). |
| `model` | Model override when skill is active. |

### Body Guidelines

- Lead with **when to use** the skill — clear trigger conditions.
- Include **step-by-step instructions** or **principles** Claude should follow.
- Reference supporting files with relative paths so Claude knows when to load them.
- Keep `SKILL.md` under 500 lines. Move detailed reference material to separate files.
- Use `$ARGUMENTS` for user input. Use `$ARGUMENTS[0]`, `$0`, etc. for positional args.
- Use `` !`command` `` syntax for dynamic context injection (runs before Claude sees the content).

## Rules for Editing Skills

- **Always preserve the frontmatter `name` and `description`** — these are how Claude discovers and triggers the skill. Changing them affects every project that symlinks here.
- **Test after editing.** Invoke the skill with `/skill-name` and verify it still triggers correctly on relevant prompts.
- **Keep skills self-contained.** Each skill directory should work independently. No cross-skill imports or dependencies.
- **No fabrication.** Skills that generate content must never fabricate statistics, quotes, testimonials, or data points. Include this constraint in the skill body if the skill produces written content.
- **Supporting files are lazy-loaded.** Only `SKILL.md` frontmatter is loaded at startup. Full content and supporting files load on invocation. Don't put critical trigger info only in supporting files.
- **Don't break the symlink contract.** Renaming a skill directory breaks every symlink pointing to it. If you must rename, document it in the commit message.

## Custom vs Third-Party

| Type | Skills | Edit policy |
|---|---|---|
| **Custom** (5) | `ai-pattern-killer`, `brand-voice`, `humanizer`, `reddit-patterns`, `voice-polish` | Edit freely. These are maintained here. |
| **Third-party** (28) | Everything else (from `coreyhaines31/marketingskills`, `onewave-ai/claude-skills`, `vercel-labs/skills`, `axtonliu/axton-obsidian-visual-skills`) | Prefer upstream PRs over local patches. If you must edit locally, document the divergence in the skill directory. |

## Adding a New Skill

1. Create a directory at the repo root: `mkdir skill-name`
2. Create `skill-name/SKILL.md` with frontmatter and instructions.
3. Add supporting files if needed (`references/`, `scripts/`, etc.).
4. Symlink to global skills: `ln -s ~/Projects/shared-skills/skill-name ~/.claude/skills/skill-name`
5. Update `README.md` with the skill name and description.
6. Test with `/skill-name` in Claude Code.

## Removing a Skill

1. Remove the symlink first: `rm ~/.claude/skills/skill-name`
2. Remove from any project-local symlinks.
3. Delete the directory from this repo.
4. Update `README.md`.
