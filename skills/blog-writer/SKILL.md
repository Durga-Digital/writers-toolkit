---
name: blog-writer
description: Use when drafting, editing, or planning long-form blog posts — covers topic selection, outline and hook craft, voice and length, SEO metadata, internal linking, and a researcher → writer → editor loop. For audience-facing prose meant to drive search traffic and conversions, not utility KB articles.
---

# Blog writer

## Overview

Blog posts are different from KB articles. KB articles unblock a user mid-task; blog posts attract a stranger via search, hold their attention through an argument, and convert them into a customer or subscriber. This skill covers the craft of long-form prose that earns ranking and reader trust.

**Core principle:** Authority comes from specificity, not declaration. Strong nouns, real numbers, concrete examples. Adjectives and intensifiers are the AI tell — strip them.

**Not for:** schema-strict knowledge-base content. Use the conclude-docs-writer skill for that.

## Preflight — is this even a blog?

Before drafting, ground the post in what actually shipped. Check the branch and diff:

```bash
git branch --show-current
git log "$(git merge-base HEAD main 2>/dev/null || git merge-base HEAD master)"..HEAD --stat
```

Then run the **blog-worthy gate**. A change earns a blog post when at least two of these hold:

1. **Story** — a regulatory shift, an integration launch, a new tier, a "first" claim, or a number a reader will remember.
2. **Search-intent reach** — addresses a query strangers run, not just an existing user mid-task.
3. **Six-month half-life** — still useful in six months, not a release-note timestamp.

Two of three → likely blog. One or zero → release note or KB update; hand to `conclude-docs-writer`.

Bug fixes, refactors, internal tooling, dependency bumps, and copy tweaks are not blog-worthy by default. Don't manufacture an angle for them. If the user insists, surface the weak signal and ask what story they want to tell before drafting.

## The three-role loop

Use the loop for any post longer than ~600 words. Each role is a distinct pass; the loop iterates until the editor signs off.

```
Researcher  ──▶  Writer  ──▶  Editor
   ▲                              │
   └─── send back if gaps ────────┘
```

### Role 1 — Researcher (build the brief)

Before drafting, produce a short brief. The brief, not the writer's instinct, is the source of truth for facts.

The brief should cover:
- **Reader intent** — what query brought them here? What state of mind?
- **One claim per post** — a single sharp argument, not a survey
- **Evidence** — 3-6 specific data points, examples, or quotes that support the claim
- **Counter-arguments** — strongest objections the reader will raise, and how the post addresses them
- **Internal links** — at least 3 existing pages on the site to link to (product pages, calculators, related posts)
- **External authorities** — 1-3 credible third-party sources (regulators, peer-reviewed research, well-known practitioners) to cite
- **SEO target** — primary keyword phrase, 1-2 secondary phrases, search intent (informational, commercial investigation, transactional)

Sources to mine:
- The product itself — features, calculators, pricing, real outputs
- Site analytics — what people already search and click
- Competitor blog posts on the same topic — find what they don't cover
- Subreddits, forums, and review sites for the actual language readers use
- Recent regulatory or industry news that makes the topic timely

If the brief lacks a fact, the writer hands back. Don't draft past unknowns.

### Role 2 — Writer (draft from the brief)

Rules of craft:

- **One claim, top to bottom.** Every section serves the central argument. If a paragraph could be cut without weakening the claim, cut it.
- **Length follows the topic, not a quota.** Most blog posts: 1200-2500 words. Cornerstone / pillar posts: 2500-4000 words. Resist padding — readers leave fluff.
- **Hook in the first three sentences.** Open with a concrete situation, a number, or a contradiction the reader didn't expect. No "In today's fast-paced world..." or "Have you ever wondered..."
- **H2s are scannable promises.** Treat them as a table of contents. A reader scanning H2s should be able to extract the post's argument without reading the body.
- **Show working.** When you cite a number, name the source. When you make a claim, give an example. When you simplify, mark the simplification.
- **Paragraphs of 1-3 sentences.** Long paragraphs lose mobile readers.
- **Voice:** plain, direct, second-person. Australian English if the audience is AU. No hedging stack-ups ("it's possible that some users may sometimes find that...").
- **Cut the AI tells:** "delve", "tapestry", "in conclusion", "navigating the complexities of", "in today's digital age", "moreover", "furthermore", three-adjective stacks. If a sentence reads like LinkedIn, rewrite it.
- **Examples and numbers earn the post.** A post with 3 specific examples beats a post with 30 generalities.
- **Close with one action.** What should the reader do next? One CTA, one anchor — not a bouquet of options.

### Role 3 — Editor (review against craft and SEO bar)

Run this checklist for each post.

**Craft:**
1. Single claim — would a reader summarise the post in one sentence?
2. Hook lands — does the opening earn the next paragraph?
3. H2s scan — do they form a coherent argument on their own?
4. Specifics — at least 3 concrete examples, named sources, or real numbers.
5. AI tells absent — search for the banned phrases (see Common mistakes).
6. Length matches topic — not under 800 if the topic warrants depth, not over 3500 if it doesn't.
7. Closing CTA is singular and clear.

**SEO:**
8. Title under 60 chars, contains the primary keyword, would a person click it.
9. Meta description 140-160 chars, sells the click without being clickbait.
10. URL slug is kebab-case, short, contains the primary keyword.
11. H1 matches title intent (not necessarily verbatim).
12. Primary keyword appears in: H1, first 100 words, at least one H2, meta description.
13. At least 3 internal links to relevant existing pages.
14. At least 1 external citation to a credible authority.
15. Structured data added if applicable: `Article` JSON-LD at minimum, `FAQPage` if there's a Q&A section, `HowTo` if step-by-step.
16. Canonical URL set; OG image either bespoke or generated by template.

**Editor decisions:**
- ✅ **Approve** — ship.
- 🔁 **Send back to writer** — craft, length, structure, missing CTA.
- 🔍 **Send back to researcher** — claim isn't supported, missing evidence, weak counter-argument.

**Loop exits when** every check passes. Don't publish before that.

## Outline template

A post outline before drafting saves an hour of rewriting:

```
Working title:    <draft title — refine after writing>
Primary keyword:  <what they searched>
Reader intent:    <what they want to know / decide>
One-claim:        <the post's argument in 15 words>

Hook:             <opening situation / number / contradiction>

H2: <promise 1>
  - Specific point
  - Example or number
H2: <promise 2>
  - ...
H2: <promise 3 — the strongest counter-objection>
  - ...
H2: <takeaway / what to do next>

CTA:              <single action — sign-up, calculator, related post>
Internal links:   <list of 3+ paths>
External cites:   <list of 1-3 sources>
```

## SEO mechanics, briefly

- Title is for the human first, the algorithm second. A clickable, specific title beats a keyword-stuffed one.
- Match search intent. Informational queries don't want product pages; transactional queries don't want explainer essays.
- Internal linking compounds. Every new post should link to at least 3 existing pages and be linked from at least 1 cornerstone page.
- One canonical URL per topic. Don't write 4 thin posts about the same query — write one strong one.
- Update, don't republish. When a post drifts, edit it in place and bump `lastModified`. New URL only when the topic genuinely changed.

## Common mistakes (and the AI tells to strip)

- **Generic openings:** "In today's fast-paced world", "Have you ever wondered", "It's no secret that". Open with a fact.
- **Adjective stacks:** "robust, scalable, comprehensive solution". One precise adjective beats three vague ones.
- **Verb softeners:** "delve into", "explore", "unpack". Just say what you mean — "explain", "show", "compare".
- **Throat-clearing transitions:** "Moreover", "Furthermore", "Additionally", "It's worth noting that". Cut.
- **Empty closings:** "In conclusion", "to wrap up", "at the end of the day". End on the strongest sentence, not a meta-comment.
- **Parade of statistics:** stats without a claim are noise. One stat per claim, with a source.
- **Hedging stacks:** "It may be the case that some users could potentially find that...". Make the claim or don't.
- **Unmotivated lists:** bullet lists where prose would be clearer. Use lists for parallel items, not just to break up text.
- **No CTA, or five CTAs.** One.
- **Length padding.** If a 1500-word post says everything, don't stretch it to 2500. Search engines reward depth, not word count.

## When to use this skill vs others

| Task | Skill |
|---|---|
| Audience-facing long-form post on the marketing site | this skill |
| KB / help-centre article for existing users | conclude-docs-writer |
| Internal docs (architecture, runbooks) | neither — use plain markdown in the repo |
| Microcopy, error messages, button labels | clarify (or similar UX-writing skill) |
| Marketing landing page copy | not this skill — landing pages have different constraints (above-the-fold, hero, social proof) |

## Workflow signals

These signals mean "engage the loop":

- User says "write a blog post about X" or "draft a blog".
- User says "review my blog draft" or "edit this post".
- User asks "what should I blog about" — start with the researcher role.
- A topical event (rate change, regulatory shift, product launch) creates a window for a timely post.
- Existing post needs a refresh because facts have drifted.
- A feature branch ships changes that pass the blog-worthy gate above.

After research, write a one-paragraph brief and confirm the angle with the user before drafting. Don't ship a 2000-word post if the angle was wrong.
