# writers-toolkit

Two skills for writing and reviewing long-form content — knowledge-base articles and blog posts — using a **researcher → writer → editor** loop. Built for Claude Code, Codex, and Gemini CLI.

## Skills

- **`conclude-docs-writer`** — write or review knowledge-base articles for [Conclude.fyi](https://conclude.fyi) import. Covers the kb-starter folder schema (numeric-prefixed categories, YAML frontmatter, `_category.md` files), and a gap-audit method for keeping docs aligned with shipped product features.
- **`blog-writer`** — draft, edit, or plan long-form blog posts. Covers topic selection, outline and hook craft, voice and length, SEO metadata, internal linking, and the AI-tells to strip.

Both skills run the same three-role loop:

```
Researcher  ──▶  Writer  ──▶  Editor
   ▲                              │
   └─── send back if gaps ────────┘
```

## Install

### Claude Code

```bash
/plugin marketplace add Durga-Digital/writers-toolkit
/plugin install writers-toolkit@writers-toolkit
```

After install, the skills appear as:

- `writers-toolkit:conclude-docs-writer`
- `writers-toolkit:blog-writer`

Trigger them by description match (e.g. "review my blog draft", "are our docs current?") or invoke directly via the `Skill` tool.

### Gemini CLI

Add as a Gemini extension. The `gemini-extension.json` and `GEMINI.md` at the repo root tell Gemini where to find the skills:

```bash
gemini extensions install https://github.com/Durga-Digital/writers-toolkit
```

Skills activate via Gemini's `activate_skill` tool when the description matches the user's request.

### Codex (and other agents)

Codex doesn't yet have a first-class plugin manager. The simplest install is to symlink the `skills/` directory into Codex's personal-skills folder:

```bash
git clone https://github.com/Durga-Digital/writers-toolkit.git ~/Src/writers-toolkit
ln -s ~/Src/writers-toolkit/skills/conclude-docs-writer ~/.agents/skills/conclude-docs-writer
ln -s ~/Src/writers-toolkit/skills/blog-writer ~/.agents/skills/blog-writer
```

`git pull` updates both skills atomically.

### ChatGPT

ChatGPT has no equivalent skill / plugin loader. The realistic options:

1. Open `skills/<skill-name>/SKILL.md` in this repo, copy the content, and paste it as the system prompt of a new Custom GPT in the GPT Store.
2. Or paste the content into a chat as one-shot instructions when you want to apply a skill to a specific task.

Auto-trigger-on-context (the magic of Claude / Codex / Gemini skills) isn't possible on ChatGPT today.

## Updates

```bash
/plugin update writers-toolkit          # Claude Code
gemini extensions update writers-toolkit  # Gemini CLI
git -C ~/Src/writers-toolkit pull         # Codex (symlinked install)
```

## Authoring guidelines

If you'd like to add a skill to this repo or refine an existing one, see [`AGENTS.md`](AGENTS.md) for the contributor flow and the principles each skill is expected to honour.

## License

MIT — see [`LICENSE`](LICENSE).
