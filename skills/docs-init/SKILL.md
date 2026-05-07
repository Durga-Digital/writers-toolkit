---
name: docs-init
description: Use when a repo has no knowledge-base tree yet and the user asks to set up, init, or bootstrap docs — covers monorepo placement, stack-aware category suggestions, and a kb-starter scaffold ready for the writer to fill in.
---

# Docs init

## Overview

Bootstraps a knowledge-base tree (kb-starter schema) for a repo that has no docs yet. Detects the stack to suggest plausible starter categories, asks once where the tree should live, scaffolds the folder layout, and hands off to `conclude-docs-writer` for drafting.

**Core principle:** A scaffold is hypotheses, not commitments. Generate the layout in one pass, let the user prune and rename, then start writing.

## When to Use

- Repo has no `kb-starter/`, `docs/`, or `content/docs/` tree and the user says "set up docs", "init the KB", "start a knowledge base", or "bootstrap docs".
- A new app inside an existing monorepo needs its own KB.

**Don't use when:**
- A docs tree already exists — use `conclude-docs-writer` for audit / write.
- The user wants internal architecture docs or runbooks — those belong as plain markdown in the repo, not in a KB schema.
- The user wants a marketing or developer documentation site — different schema.

## Step 1 — Confirm the location

Detect monorepo markers: `pnpm-workspace.yaml`, `turbo.json`, `nx.json`, `lerna.json`, `apps/<x>/package.json`, `packages/<x>/package.json`.

If detected, ask the user once:

> "Monorepo detected. Where should the KB live?
> 1. Root `kb-starter/` (one KB across the whole product)
> 2. `apps/<name>/kb-starter/` for a single app
> 3. Other path"

For single-package repos, propose `./kb-starter/` and confirm.

Cache the chosen path in `.claude/writers-toolkit.json`:

```json
{
  "docsRoot": "kb-starter",
  "scope": "root"
}
```

This file is the source of truth for `conclude-docs-writer` from now on. Re-ask only if the path is gone or the user asks to switch scope.

If the target path already contains a docs tree, bail with "tree exists at `<path>` — use `conclude-docs-writer` to audit or extend." Don't overwrite.

## Step 2 — Detect stack signals

Skim the repo for clues that suggest which categories the KB needs. Detection is shallow — file presence, `package.json` deps, env-var names — not a full audit.

| Signal | Suggested category |
|---|---|
| Stripe, Lemon Squeezy, Paddle in deps | `Billing & plans` |
| Auth lib (Clerk, Auth0, NextAuth, Supabase auth, Lucia) | `Account & access` |
| File-based router (`app/`, `pages/`, `routes/`) with public pages | `Getting started` (always include) |
| Multiple sub-tools or feature dirs | `Features` (one article per tool) |
| Email lib (Resend, Postmark, SendGrid) | mention inside `Account & access` |
| Webhooks or public API surface | `Integrations & API` |
| Tier / entitlements module | influences `Billing & plans` (limits article) |
| FAQ constant in code | `FAQ` category, articles seeded from the constant |

Always include: `Getting started`, `Troubleshooting`. Cap at 4-6 categories for v1 — more is paralysis.

## Step 3 — Scaffold the tree

Create the kb-starter schema. See `conclude-docs-writer` for full schema rules. Scaffold structure only — no article bodies, just placeholders the writer will fill in:

```
kb-starter/
  01-getting-started/
    _category.md
    01-create-your-account.md
    02-first-steps.md
  02-features/
    _category.md
    01-overview.md
  03-billing-and-plans/         # only if billing detected
    _category.md
    01-pricing.md
    02-limits.md
  04-account-and-access/        # only if auth detected
    _category.md
    01-sign-in.md
    02-password-and-recovery.md
  05-troubleshooting/
    01-common-issues.md
  06-faq/                       # only if FAQ source detected
    _category.md
```

Each placeholder article has the four required frontmatter fields filled with placeholder values and a single `<!-- TODO: draft -->` body line. Each `_category.md` has frontmatter only.

Article frontmatter template:

```yaml
---
title: <human-readable title>
category: <category display name>
order: <n>
description: <one-line summary — replace before publishing>
---

<!-- TODO: draft -->
```

`_category.md` template:

```yaml
---
title: <category display name>
order: <n>
description: <one-line summary>
icon: <lucide-icon-name>
---
```

## Step 4 — Hand off

Print a punch list:

- Location confirmed and cached at `.claude/writers-toolkit.json`.
- Categories scaffolded (list them).
- Placeholders ready for the writer.

Then hand off to `conclude-docs-writer` for drafting article-by-article through the researcher → writer → editor loop. Don't draft inside `docs-init` — different skill, different concerns.

## Common mistakes

- **Scaffolding more than 6 categories.** Trim aggressively; users add categories later.
- **Filling placeholders with generated content.** That's the writer's job and skips the researcher pass — facts will be hallucinated.
- **Skipping the location question on monorepos.** The wrong root is painful to migrate. Ask once, cache, move on.
- **Re-running on a repo that already has docs.** Detect existing trees and bail with a pointer to `conclude-docs-writer`.
- **Inventing categories with no stack signal behind them.** If the repo has no billing code, don't scaffold a billing category "just in case".

## Workflow signals

- "Set up docs", "init the KB", "start a knowledge base", "bootstrap docs".
- New app inside a monorepo with no existing KB.
- Bare repo where `find . -maxdepth 3 -type d \( -name kb-starter -o -name docs -o -name "content" \)` returns nothing user-facing.
