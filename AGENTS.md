# Contributor guidelines

This repo is a small plugin: two skills, a researcher → writer → editor loop, multi-agent loaders. Keep it focused.

## Adding a skill

A new skill belongs in this repo if:

- It teaches a writing / reviewing / planning craft that benefits from the researcher → writer → editor loop.
- The technique applies broadly, not to one project.
- It has a clear "use when..." trigger that doesn't overlap with the existing skills.

A new skill does NOT belong here if it's project-specific (those go in your project's `.claude/skills/`), framework-specific (those go in a framework plugin), or really a workflow / hook (those belong in their own skill type).

## Skill structure

Each skill lives at `skills/<skill-name>/SKILL.md` and follows the standard Claude Code skill format:

```markdown
---
name: skill-name
description: Use when [specific triggering conditions and symptoms — third person, no workflow summary]
---

# Skill name

## Overview
What this is. Core principle in 1-2 sentences.

## When to Use
Bullet list of triggers. When NOT to use.

[... rest follows the skill's own structure ...]
```

Description rules:

- Start with "Use when..."
- Third person.
- Triggering conditions only — never summarise the workflow itself, or future agents will follow the description and skip reading the skill body.
- Under 500 characters where possible.

## Testing skills

For new skills, follow the discipline in `superpowers:writing-skills` if you have it installed. Minimum bar:

1. Run a baseline scenario WITHOUT the skill — note where the agent goes wrong.
2. Write the skill addressing those specific failures.
3. Run the same scenario WITH the skill — verify compliance.
4. Look for new rationalisations and tighten the skill until it's bulletproof.

We don't enforce full TDD-for-skills on every PR, but the more rigorous the testing, the more useful the skill.

## Multi-agent compatibility

The repo serves Claude Code, Codex, and Gemini CLI from a single `skills/` directory. Don't duplicate skill content per agent — only the loader stubs at the repo root differ:

- `.claude-plugin/plugin.json` + `.claude-plugin/marketplace.json` — Claude Code.
- `gemini-extension.json` + `GEMINI.md` — Gemini CLI.
- `AGENTS.md` (this file) — Codex and general AI-agent contributors.

When you add a skill, update `GEMINI.md` to add the `@./skills/<new-skill>/SKILL.md` include line. The Claude Code marketplace auto-discovers anything inside `skills/`.

## Quality bar

- Skills are reference guides for proven techniques, not narratives about how you solved a problem once.
- One skill per concept. If you find yourself adding a second `## Workflow` section, it's probably two skills.
- Real, specific guidance. No lorem ipsum, no "this skill helps you do X" filler.
- Plain English, third person, present tense.

## Releases

Bump `version` in:

- `.claude-plugin/plugin.json`
- `.claude-plugin/marketplace.json` (the plugin entry inside the `plugins` array)
- `gemini-extension.json`
- `CHANGELOG.md` — add an entry describing what changed.

Tag the release: `git tag v0.1.1 && git push --tags`.
