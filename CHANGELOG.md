# Changelog

All notable changes to this plugin will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

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
