---
name: conclude-docs-writer
description: Use when writing or reviewing knowledge-base docs for Conclude.fyi import — covers the kb-starter folder schema (numeric-prefixed categories, YAML frontmatter, _category.md files), a researcher → writer → editor loop, and how to align existing docs with current product features.
---

# Conclude.fyi docs writer

## Overview

Conclude.fyi imports a folder of markdown files where folders become categories and files become articles. Folder/file order is set by numeric prefixes; metadata is in YAML frontmatter. This skill covers the import schema, a three-role workflow loop, and a gap-audit method for keeping docs aligned with shipped features.

**Core principle:** Docs are part of the product surface. When the code ships a new feature, plan limit, or tier behaviour, the docs that explain it must ship in the same week.

## Preflight — read the branch first

Before any doc work, sample what's actually changed. The branch and diff are the strongest signal of which docs need updating.

```bash
git branch --show-current
git log "$(git merge-base HEAD main 2>/dev/null || git merge-base HEAD master)"..HEAD --stat
```

Classify changed paths against these categories — touching any of them flags a likely doc update:

- **User-facing routes / pages** — `app/`, `pages/`, `routes/` (excluding tests and API internals).
- **Tier / entitlements / pricing config** — top source of doc drift.
- **Auth / signup flow** — onboarding articles drift first when auth changes.
- **Public env vars or feature flags** — anything user-visible or that gates UI.
- **FAQ source files** — if FAQs live in code.

Branch-name hints (`feat/`, `feature/`, `fix/`) only nudge the call — diff content is the real signal. If the diff is tests-only, internal refactors, infra, or unrelated tooling, skip the doc pass and say so.

When the diff is doc-relevant, lead with a punch list — "`<feature>` → article X needs updating; pricing change → article Y; new tier → write article Z" — before opening the researcher loop. Don't draft until the user picks the scope.

If no docs tree exists yet, hand off to the `docs-init` skill before writing articles.

## Placement — where docs live (ask once, cache)

Before creating or editing files, confirm the docs root.

**Monorepo markers:** `pnpm-workspace.yaml`, `turbo.json`, `nx.json`, `lerna.json`, `apps/<x>/package.json`, `packages/<x>/package.json`.

If detected, ask the user once:

> "Monorepo detected. Where should the KB live?
> 1. Root `kb-starter/` (one KB across the whole product)
> 2. `apps/<name>/kb-starter/` for a specific app
> 3. Other path"

For single-package repos, look for an existing docs root — `kb-starter/`, `docs/`, `content/docs/`, `website/docs/` — and confirm before writing anywhere new.

Cache the answer in `.claude/writers-toolkit.json` so subsequent runs don't re-ask:

```json
{
  "docsRoot": "apps/marketing/kb-starter",
  "scope": "app:marketing"
}
```

Re-ask only if the file is missing, the path no longer exists, or the user explicitly asks to switch scope.

## The three-role loop

Use the loop for any doc work larger than a single-paragraph fix — new categories, gap audits, multi-article rewrites, post-release sweeps. Each role is a distinct pass over the work; the loop iterates until the editor signs off.

```
Researcher  ──▶  Writer  ──▶  Editor
   ▲                              │
   └─── send back if gaps ────────┘
```

### Role 1 — Researcher (gather facts)

**Goal:** produce a fact sheet for the writer. No prose yet.

Output a short brief covering:
- The article's user (who hits this page, what state they're in).
- Source-of-truth references — file paths, env vars, config keys, route paths, commit hashes.
- Current product behaviour, in bullets.
- Anything ambiguous or undocumented in the code that needs a decision before drafting.

Sources to read (every project has analogues):
- Routes and pages the user navigates.
- The tier / entitlements module (free vs paid limits).
- The pricing / billing config.
- The auth and onboarding flow.
- Recent commits relevant to the topic — `git log --oneline -- <relevant-paths>`.
- FAQ source files if FAQs live in code.

If the brief is missing something, the writer hands back. Don't write past unknowns.

**Tip:** for big audits, run the researcher as a subagent so the main session doesn't carry every file's contents in context.

### Role 2 — Writer (draft from the brief)

**Goal:** turn the brief into article(s) that match the schema below.

Rules:
- One article per concept. If it's two concepts, split into two articles.
- 200-400 words (150-250 for FAQs).
- Lede first, then `## What this is`, `## How to ...`, edge-case subsections, closing `## Related`.
- Cross-link to related articles using relative `.md` paths.
- Use real product terminology from the brief — no invented names.
- Only ship what the brief covers. If a section needs a fact the brief doesn't have, send back to the researcher.

### Role 3 — Editor (review against schema and quality bar)

**Goal:** catch schema violations, drift, and weak prose before import.

Checklist (run for each article):

1. **Frontmatter** — `title`, `category`, `order`, `description` all present and non-empty.
2. **Body** — no `# Title` heading at the top (title is in frontmatter).
3. **Word count** — within the target band (200-400, FAQs 150-250). Flag outliers.
4. **Cross-links** — every `../<folder>/<file>.md` resolves; numeric prefixes match.
5. **No placeholders** — strip any `[Screenshot: ...]`, `TODO`, lorem ipsum.
6. **Tier and limit drift** — every cited limit (`up to N`, "30 years", "A$X") matches the live config.
7. **Tone** — reader is mid-task. Marketing tone gets sent back.
8. **Voice** — third person, present tense, plain English.

**Editor decisions:**
- ✅ **Approve** — article is shippable.
- 🔁 **Send back to writer** — prose, structure, length, missing cross-links.
- 🔍 **Send back to researcher** — the underlying fact is unclear or wrong.

**Loop exits when:** every article in the batch passes all 8 checks. Don't import or commit until that's true.

## The schema (must match exactly)

```
kb-starter/
  01-getting-started/
    _category.md                       # optional, overrides display
    01-create-your-account.md
    02-first-steps.md
  02-features/
    _category.md
    01-feature-a.md
    02-feature-b.md
  03-troubleshooting/
    01-common-issues.md
  ...
```

**Folder rules:**
- Subfolders = categories.
- Numeric prefix on folder name sets display order (`01-`, `02-`, ...).
- Folder name (after prefix) is the category slug.
- Use 4-8 articles per category. 4-6 categories is typical; product-specific structures may justify more.

**File rules:**
- Articles are `.md` files inside a category folder.
- Numeric prefix on file name sets article order within the category.
- Use kebab-case for the slug part.

**Article frontmatter (required, exact field set):**

```yaml
---
title: Human-readable article title
category: The category display name (matches the folder)
order: 2
description: One-line summary used in lists and search
---
```

**`_category.md` (optional, per folder):**

```yaml
---
title: Category display name (overrides folder slug)
order: 3
description: One-line summary of the category
icon: lucide-icon-name
---
```

No body needed for `_category.md` — frontmatter only.

## Article body conventions

1. Lede paragraph stating the user's situation.
2. `## What this is` (or similar) section explaining the concept.
3. `## How to ...` section with numbered steps.
4. Reference subsections (`### Edge case`, `### Pro-only`, etc.).
5. `> **Tip:**` or `> **Note:**` for inline asides.
6. Closing `## Related` section with internal cross-links.

Skip marketing. Show CLI / API / config in fenced code blocks. Don't include `[Screenshot: ...]` placeholders, lorem ipsum, or `# Heading` lines (title belongs in frontmatter).

Internal links use relative paths with the `.md` extension, e.g. `../02-features/01-feature-a.md`. Update path numerics whenever folder prefixes change.

## Gap audit (the researcher's job, formalised)

Run this before any doc-update sprint, after major feature releases, and when the user asks "are our docs current?"

**Step 1 — inventory the docs:**
- `find <docs-root> -name "*.md"` to list every article.
- Sample each — check word counts (`wc -w`) for outliers.

**Step 2 — inventory shipped features.** What to read depends on the stack; the *categories* of source-of-truth to look for are:
- **Routes / pages** — file-based routers, server route definitions, sitemap config.
- **Tools / sub-products** — directories grouping user-facing utilities (dashboards, exports, integrations, calculators, etc.).
- **Tier / entitlements config** — the module defining free-vs-paid feature lists and limits. Drift here is the #1 source of stale docs.
- **Pricing / plan config** — current prices, billing periods, feature gates.
- **Auth / signup flow** — actual sign-in mechanism (password, magic link, SSO). Onboarding articles drift first when auth changes.
- **FAQ source** — products often keep FAQs in code so marketing and KB stay in sync. Find that constant; use it to generate the FAQ category.
- **Recent commits** — `git log --oneline -30` to see what's shipped lately. Cross-reference against doc updates in the same range.

**Step 3 — diff:**

| Doc state vs reality | Action |
|---|---|
| Doc references feature that no longer exists | Remove or rewrite |
| Feature exists, no doc | Write new article in matching category |
| Doc cites old limit (e.g. an old quota or horizon) | Update inline |
| Doc uses old branding / domain / contact email | Search-and-replace across the tree |
| Doc cites placeholder UI like `[Screenshot: ...]` | Strip — KB import doesn't render these |
| Frontmatter missing or malformed | Fix; broken frontmatter rejects on import |

**Step 4 — write or patch.** Each fix is a separate edit. Commit logically grouped changes.

**Step 5 — validate before import** (the editor's job, condensed):
- Every article has the four required frontmatter fields.
- Every internal link resolves (paths reflect numeric prefixes).
- No `# Title` headings inside article bodies.
- Word counts mostly inside 200-400 (FAQs 150-250 OK).

## Quick reference

| Item | Format |
|---|---|
| Folder name | `NN-kebab-case-slug/` (e.g. `01-getting-started/`) |
| Article filename | `NN-kebab-case-slug.md` |
| Required frontmatter | `title`, `category`, `order`, `description` |
| Optional `_category.md` fields | `title`, `order`, `description`, `icon` |
| Internal link | `../NN-other-category/NN-article.md` |
| Word target | 200-400 (FAQs 150-250) |

## Common mistakes

- **Skipping the researcher pass** — writer invents details from training data, gets caught by editor, three rounds of rework. Always brief first.
- **Forgetting numeric prefix on folders** — categories load in alphabetical order, not the intended sequence.
- **Putting `# Title` at top of article body** — duplicates frontmatter title; some importers render both.
- **Using absolute URLs for internal links** — breaks when domain changes; use relative paths.
- **Skipping the description field** — search results and listings show empty rows.
- **Letting docs lag the product** — pricing, plan limits, and feature lists are the most common drift. Re-read these after every billing or entitlements change.
- **Including `[Screenshot: ...]` placeholders** — clutter, no real image, looks broken in the rendered KB.
- **Hardcoding tier limits in articles** — when an entitlement moves between tiers, every article that mentions the old limit must change. Prefer phrasing that won't drift.

## Workflow signals

These signals mean "run the gap audit":

- User mentions Conclude.fyi or knowledge-base import.
- User says "review the docs" or "are our docs current?"
- A major feature ship just landed (new tool, new tier, new auth method).
- Pricing or plan limits changed.
- Brand or domain changed.
- Working on a feature branch where the diff touches user-facing paths (run the preflight to confirm).

After the audit, write a concise punch list of what needs updating before opening edits. Don't batch unrelated changes in one commit — split by category or by concern.
