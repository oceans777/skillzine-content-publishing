---
name: skillzine-content-publishing
description: >-
  Use when researching, drafting, or publishing a Skillzine skill/agent review
  (English and/or Simplified Chinese): discover a real skill, research/test it,
  write review MDX + Reference JSON + media, optional zh translation at
  reviews/zh/{slug}.mdx, then open a Git PR for Vercel preview (never buy
  domains, never enable indexing, never push straight to main).
---

# Skillzine content publishing

Canonical product contract: `beyondchenlin/skillzine` README / PRD / `docs/i18n/` / `docs/11-i18n-contract.md`.  
This skill is the **repeatable editorial workflow** for Skillzine（技志）— not GK2/Emberville, not a DRM store.

Companion glossary: [`docs/i18n/EDITORIAL-GLOSSARY.md`](../i18n/EDITORIAL-GLOSSARY.md).

## When

- User asks to find / review / publish a skill or agent on Skillzine
- Ongoing editorial cadence after M3
- Need a PR that adds `reviews/*.mdx` and/or `reviews/zh/*.mdx` + related Reference cards

## Hard gates (fail-closed)

1. Origin is **`beyondchenlin/skillzine`** only; upstream template is `PlayGuideLab/game-sites`.
2. **No direct push to `main`.** Branch + PR only.
3. Preview stays **noindex**; never set `INDEXING_ENABLED=true`; never buy domains.
4. Not a store: no paywalled downloads, no fake install buttons that imply Skillzine distributes binaries.
5. Facts: unknown → omit or mark unverified; never invent install counts, ratings, or vendor claims.
6. **Fully-qualified review gate:** real skill + how-to + pros/cons + screenshot + GIF + screen recording + bidirectional cards.
7. **No thin translations:** never ship zh chrome over English body; never mark thin/machine-only copy as `translated` or indexable; never put thin MT into sitemap/hreflang.
8. Untranslated EN reviews must **not** get empty `/zh/reviews/<slug>/` shells.

## Two publishing paths

### Path A — English only (default SEO mainline)

```
discover → select → research/test → draft EN MDX + JSON + media
  → validate → PR → preview skim → merge
```

- File: `reviews/<slug>.mdx` → URL `/reviews/<slug>/`
- Keep English as the SEO primary; discovery still biased to English search intent
- Do **not** create `reviews/zh/<slug>.mdx` until Path B

### Path B — English + Simplified Chinese (`zh` / `zh-Hans`)

1. Prefer Path A first: EN review **fully-qualified and on a green preview** before opening a translation PR (same PR only if EN was already live on main and you are adding zh).
2. Add `reviews/zh/<slug>.mdx` with the **same slug** as English.
3. Frontmatter extras (**required** on zh):
   - `locale: zh`
   - `translationGroup: <english-slug>`
   - `translationStatus: translated` (never claim translated for machine-dump / draft stubs)
4. Localize title, description, Verdict, pros/cons, how-to, captions/alt. **Reuse** language-agnostic media paths under `/reviews/<slug>/…`; rewrite captions/alt in zh.
5. Reference JSON: add zh `localizations` (at least `canonicalName` / `summary` / `seo`) for linked skill/agent/tag ids used by the review.
6. Language switcher must not send readers to English body under zh chrome; missing zh → hide sibling link or send to `/zh/` hub only.

Do **not** migrate English files to `reviews/en/` unless a later signed migration says so.

## Pipeline detail

### 1) Discover

- Public sources only (official registries, GitHub, vendor docs, changelogs).
- Prefer English SEO intent; zh is secondary reach, not a substitute for EN quality.
- Skip abandonware, malware risk, DRM/warez framing.

### 2) Select

Score: search intent, differentiability, verifiability, graph fit. Reject without a primary source URL. Record one-line rationale in the PR body.

### 3) Research / test

- Read official docs + license; note host compatibility.
- Hands-on when feasible. If not: `verification: secondary` / “not hands-on”.
- Note setup friction, failures, alternatives, check date (Asia/Shanghai).

### 4) Generate artifacts

**A. Editorial MDX (EN)** → `reviews/<slug>.mdx`

```yaml
title: "…"
description: "…"   # ~150 chars, English
date: YYYY-MM-DD
skillIds: ["sz:skill:…"]
agentIds: []
draft: false
```

Body shape: Dek → Quick verdict → What it is → Setup & how-to → Pros & cons → Alternatives → Sources.  
Tone: concise high-trust English tech editorial. No hype stack, no emoji spam.

**A2. Editorial MDX (zh)** → `reviews/zh/<slug>.mdx`

Same body shape in Simplified Chinese editorial tone（技志）. Keep product names as proper nouns (Cursor, React, MCP, Skill, Vercel, Playwright, …). See glossary.

**B. Reference JSON cards** (`content/games/skillzine/batch-m3.json` records)

- Stable `id`, `slug`, `verification`, `relations[]`, `seo`, `updatedAt`
- Bidirectional: MDX `skillIds` ↔ JSON relations / related review
- For zh: `localizations.zh` with at least `canonicalName`, `summary`, `seo.{title,description}`

**C. Media checklist** (fully-qualified)

```
sites/skillzine/public/reviews/<slug>/
  cover.webp|png
  shot-*.webp|png
  flow.gif
  demo.mp4
```

Root-relative URLs in MDX (`/reviews/<slug>/flow.gif`). `sync-site-public` copies into root `public/`. Compress; no unauthorized Git LFS. Shared media across locales — do not duplicate binaries per language.

### 5) Validate locally

```bash
npm run content:validate
npm run seo:validate
npm run i18n:validate
npm run locale:purity
SITE_ID=skillzine npm run build
```

Fix orphans, broken relations, missing frontmatter, purity failures, empty zh shells before PR.

### 6) Git PR → Vercel preview

```bash
git checkout -b content/review-<slug>          # Path A
# or
git checkout -b content/review-<slug>-zh       # Path B add-on
git push -u origin HEAD
gh pr create …
```

Use the repo PR template checkboxes:

- Locale path: EN only / EN+zh
- `translationGroup` + `translationStatus` (if zh)
- Media triad (if claiming fully-qualified)
- Validates green
- **Not** requesting indexable (default)
- No domain buy / no `INDEXING_ENABLED` flip

Confirm preview 200, still **noindex**. Human skim facts + tone before merge.

## Glossary (keep untranslated / consistent)

| Prefer | Notes |
| --- | --- |
| Skill / Agent Skill | 可写「技能」一次后保留 Skill |
| Rules | 保留 Rules |
| MCP | 保留 MCP |
| Cursor / Claude / Vercel / GitHub / Playwright | 专有名词不译 |
| Verdict | UI「结论」；正文「快速结论」 |
| Skillzine / 技志 | 英文站 Skillzine；中文可「技志 Skillzine」 |

Full table: [`docs/i18n/EDITORIAL-GLOSSARY.md`](../i18n/EDITORIAL-GLOSSARY.md).

Forbidden: raw MT dump without edit; Traditional Chinese in `zh`; English body with `lang=zh-Hans`; marking thin copy indexable.

## Visual bar

Batch editorial: white bg, teal `#20a8e0`, coral `#f65b66`. No flashy templates, no thin directory look, no full Fumadocs docs skin, no GK2 game skin, no forest green `#0f6e56` regression.

## Out of scope (escalate)

- Domain purchase, CF production DNS, AdSense real ID, `INDEXING_ENABLED=true`
- Engine refactors (separate PR; prefer upstream `PlayGuideLab/game-sites` first)
- Adding `ja` / `zh-tw` (P4 — needs separate sign-off)
- Touching GK2 / Emberville production content

## Return to parent / user

Summarize: selected skill, locale path (EN / EN+zh), paths touched, PR URL, preview URL, hands-on yes/no, open risks. Do not claim production index/publish unless user gates say so.
