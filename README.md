# Promptify (Adapted Fork)

An adapted fork of [tolibear/promptify-skill](https://github.com/tolibear/promptify-skill) — a prompt optimization skill for Claude Code.

## Purpose

This fork exists to track upstream changes from tolibear/promptify-skill for potential cherry-picking. The working version of the skill lives in the [Personal-Workspace](https://github.com/pfgannon/Personal-Workspace) `.claude/skills/promptify/` directory and has diverged from the upstream structure.

## Differences from Upstream

- **No separate agent definitions** — agent instructions (codebase researcher, clarifier, web researcher) are inlined directly in SKILL.md
- **No slash command** — `commands/promptify.md` removed; functions as a skill, not a plugin command
- **No plugin manifest** — `.claude-plugin/` directory removed
- **Self-contained** — everything needed is in a single `SKILL.md` file
- **All third-party platform references removed**

## Installation

This skill is **not installed from this repo**. To use it:

1. Copy `SKILL.md` into `.claude/skills/promptify/` in your workspace
2. The skill will be available automatically in Claude Code

## Upstream Tracking

To identify upstream changes worth incorporating:

```bash
git diff main upstream/main -- SKILL.md
```

The structural differences (deleted `agents/`, `commands/`, `.claude-plugin/` directories) are intentional and should not be reconciled.

## License

MIT — see [upstream repo](https://github.com/tolibear/promptify-skill) for original license.
