# Changelog

All notable changes to this plugin will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.2.0] — 2026-05-07

### Added
- `docs-init` skill — bootstrap a `kb-starter/` tree for a repo that has no docs yet. Detects monorepo layout, asks once where the KB should live, caches the answer in `.claude/writers-toolkit.json`, suggests starter categories from stack signals (Stripe, auth libs, routers, FAQ constants), and scaffolds placeholder articles for the writer to fill in.
- `conclude-docs-writer`: preflight section that reads the current branch and diff to flag doc-relevant changes (routes, entitlements, auth, public env vars, FAQ source) and produce a punch list before opening the researcher loop.
- `conclude-docs-writer`: placement section covering monorepo detection and the cached docs root in `.claude/writers-toolkit.json`.
- `blog-writer`: preflight section with a "blog-worthy gate" — a change earns a post when at least two of {story, search-intent reach, six-month half-life} hold. Bug fixes and refactors are not blog-worthy by default.

### Changed
- Both existing skills add a workflow signal for "working on a feature branch with shipped user-facing changes".

## [0.1.0] — 2026-05-07

### Added
- `conclude-docs-writer` skill — write or review knowledge-base articles for Conclude.fyi import. Covers the kb-starter folder schema, YAML frontmatter, `_category.md` files, and a gap-audit workflow.
- `blog-writer` skill — draft, edit, or plan long-form blog posts. Covers topic selection, outline and hook craft, voice and length, SEO metadata, internal linking, and AI-tells to strip.
- Both skills include a researcher → writer → editor loop.
- Claude Code plugin manifest (`.claude-plugin/plugin.json`).
- Self-hosted marketplace manifest (`.claude-plugin/marketplace.json`).
- Gemini CLI extension manifest (`gemini-extension.json` + `GEMINI.md`).
- Cross-agent contributor guide (`AGENTS.md`).
- README with install instructions for Claude Code, Codex, and Gemini CLI.
