---
name: analyticscli-content-marketing
description: Internal/private playbook for creating human-quality AnalyticsCLI and AI Growth Engineer blog posts, SEO pages, LLM-readable content, and launch copy.
license: UNLICENSED
homepage: https://analyticscli.com
metadata:
  {
    'author': 'wotaso',
    'version': '0.1.1',
    'private': true,
    'audience': 'internal',
    'publish': 'do-not-list',
  }
---

# AnalyticsCLI Content Marketing

## Use This Skill When

- drafting, editing, or reviewing blog posts for `apps/landing/src/content/blog`
- creating SEO landing pages, comparison pages, integration pages, or LLM-readable summaries
- turning product positioning into content that teaches founders and engineers
- using AI to create first drafts that still need to sound human, specific, and trustworthy

## Core Positioning

AnalyticsCLI and the AI Growth Engineer help founders optimize products from production data that already exists but is usually underused.

Most mobile app and SaaS founders only manually analyze part of their analytics, revenue, crash, feedback, store, and code data. AnalyticsCLI turns that data into agent-readable context. The AI Growth Engineer skill then helps automate product optimization with the founder's existing coding-agent subscription, such as Codex, OpenClaw, Claude Code, or Cursor.

The product promise:

- feed your AI coding agent the important production logic and product signals it normally cannot see
- connect analytics, RevenueCat, Sentry, App Store Connect, feedback, GitHub/code context, and business rules
- automate mobile app and SaaS optimization from real production data
- generate growth ideas, marketing advice, GitHub issues, PR plans, and implementation tasks
- get more value from an existing AI coding-agent subscription by giving it product context and measurable goals

Use concise wording such as:

> AnalyticsCLI connects production product signals to your coding agent so it can find growth opportunities, create issues or PR tasks, and help optimize your SaaS or mobile app from real data.

## Editorial Standard

Content must be useful even if the reader never buys AnalyticsCLI.

## Product Fact Recovery

When drafting or reviewing exposes a product/docs/CLI/SDK mismatch, broken workflow, or missing capability:

1. Check the newest relevant skill, CLI, or SDK version first instead of writing around stale behavior.
2. Re-verify the product fact against the updated artifact.
3. If no newer version is available, the update is blocked, or the newest version still has the gap, submit concise AnalyticsCLI product feedback with `analyticscli feedback submit`.

Feedback is for the AnalyticsCLI SaaS owner about AnalyticsCLI itself. Do not confuse it with tenant-owned end-user feedback. Include the content surface, checked version/update attempt, expected behavior, actual behavior, and any temporary wording or workaround used.

## Headlines

Write short, punchy headlines. Avoid long, fully-loaded titles that try to explain the entire product in one sentence.

Good headline patterns:

- `10 things that make X work`
- `Why X is bad`
- `X vs Y: what actually matters`
- `Stop doing X manually`
- `Your X is guessing`
- `The X trap`
- `What X misses`
- `How to turn X into Y`

Rules:

- Prefer 4 to 9 words.
- Use one clear idea, not three.
- Make the reader curious, then explain in the description and intro.
- Put the concrete audience or workflow in the description when the headline would otherwise become too long.
- Avoid stacked phrases like `analytics, revenue, crashes, feedback, store signals, and code context` in titles.

Examples for this product:

- `Your Coding Agent Is Guessing`
- `Stop Manually Optimizing Your App`
- `Turn Analytics Into Issues`
- `Blank AI Prompts Are Bad Marketing`
- `Why Dashboards Do Not Ship Fixes`
- `AI Growth Engineer vs Dashboards`

Every post must include:

- a concrete founder/operator problem
- specific examples from SaaS, mobile apps, onboarding, paywalls, retention, crashes, reviews, or marketing
- clear tradeoffs and failure modes
- internal links to relevant product pages, docs, use cases, or integrations
- a practical next step the reader can apply
- screenshots, diagrams, or generated visuals with meaningful alt text

Avoid:

- generic AI hype
- claims that the product fully replaces human product judgment
- pretending automatic PR creation is always safe
- "10x growth" style guarantees
- vague phrases like "unlock insights" without showing the actual workflow
- publishing AI drafts without human review

## AI Drafting Policy

AI may generate outlines, first drafts, image briefs, metadata, FAQs, and distribution snippets. AI must not publish directly.

Required human pass:

1. Check every factual claim against current product behavior.
2. Add one or more specific examples that sound like the team actually builds this product.
3. Remove generic filler and repetitive positioning.
4. Ensure the article has a point of view.
5. Confirm screenshots/images exist or create a clear image task.
6. Verify frontmatter, internal links, and SEO metadata.

If a post sounds like it could appear on any AI SaaS blog, reject the draft.

## Blog File Format

Create posts as MDX files in:

```text
apps/landing/src/content/blog/<slug>.mdx
```

Use frontmatter matching `apps/landing/src/content/config.ts`:

```mdx
---
title: 'Specific Human Title'
description: 'One sentence that names the reader, problem, and outcome.'
pubDate: 2026-05-07
author: 'AnalyticsCLI Team'
image: '/blog/<slug>/cover.png'
tags:
  - AI Growth Engineer
  - Product Analytics
---
```

Place public images under:

```text
apps/landing/public/blog/<slug>/
```

## Recommended Structure

Use this shape for most posts:

1. Problem: what founders currently do manually or ignore
2. Stakes: why partial data analysis leads to bad product decisions
3. Workflow: how AnalyticsCLI plus the AI Growth Engineer changes the loop
4. Concrete example: mobile app, SaaS onboarding, paywall, retention, crash, or marketing case
5. Guardrails: what should stay human-reviewed
6. Implementation notes: SDK/events/connectors/agent workflow where relevant
7. Next step: link to a use case, integration, docs page, or setup guide

Keep paragraphs short. Use descriptive H2s. Use bullets only where they improve scanning.

## Image Rules

Every article should include:

- one 1200x630 cover image or OG image
- at least one in-article visual for workflows, screenshots, diagrams, or example outputs
- alt text that describes the actual value of the image

Preferred visuals:

- real product/dashboard screenshots
- CLI output screenshots
- workflow diagrams showing signals -> agent reasoning -> issue/PR/task
- tasteful generated illustrations only when real UI is not available

Generated image direction:

- Use restrained editorial blog imagery similar to respected product and engineering blogs.
- Prefer simple metaphors, physical objects, screenshots cropped as abstract surfaces, clean workspace scenes, or minimal abstract compositions.
- Use one focal idea per image.
- Avoid glowing neon dashboards, robots, sci-fi command centers, floating data explosions, excessive icons, and obvious AI-generated complexity.
- Avoid readable text inside images unless the exact text has been reviewed.
- Thumbnails must read at small sizes without looking like a cluttered infographic.

Do not use dark, generic, atmospheric stock imagery as the primary article image.

## SEO and LLM Rules

Every post should target one clear search intent. Do not stuff keywords.

Include:

- one primary keyword phrase in the title or first paragraph when natural
- two to four related phrases across headings/body copy
- links to `/ai-growth-engineer`, `/use-cases`, `/integrations`, `/pricing`, or docs where relevant
- a concise answerable summary paragraph near the top for LLM extraction
- FAQs only when they answer real buyer/operator questions

Preferred topic clusters:

- AI Growth Engineer for mobile apps
- AI Growth Engineer for SaaS founders
- product analytics for coding agents
- production data for AI coding agents
- automating issue and PR creation from analytics
- RevenueCat plus analytics for subscription growth
- Sentry plus analytics for product prioritization
- App Store reviews as product feedback
- GDPR-friendly analytics for agent workflows
- using existing Codex/OpenClaw/Claude Code subscriptions more effectively

## Programmatic SEO Research Loop

Use `scripts/seo-research.mjs` before creating SEO pages at scale.

Default cost-effective path:

```bash
pnpm seo:research:local -- --tool analyticscli --seed "analyticscli,product analytics cli,ai growth engineer"
```

For sibling tools, pass explicit seeds and product-fit terms instead of forcing the AnalyticsCLI profile:

```bash
pnpm seo:research:local -- \
  --tool <tool-name> \
  --seed "<tool-name>,<tool-name> cli,<tool-name> alternative" \
  --product-term "<tool-name>,developer tools,automation"
```

Paid enrichment path, only when keyword metrics are needed:

```bash
DATAFORSEO_LOGIN=<login> DATAFORSEO_PASSWORD=<password> \
  pnpm seo:research -- --provider dataforseo --confirm-paid --max-paid-requests 3
```

CSV-first path for Ahrefs, Semrush, DataForSEO, or Search Console exports:

```bash
pnpm seo:research:local -- --csv exports/ahrefs.csv --gsc-csv exports/search-console.csv
```

Rules:

- DataForSEO is the default paid API because it is pay-as-you-go and easy to cap.
- Ahrefs should usually be used through manual/CSV validation first; API usage is only justified once organic traffic converts and the weekly loop needs large-scale KD/traffic-potential checks.
- Google Search Console data is free and should be used as soon as published pages receive impressions.
- Never scrape Ahrefs' free UI.
- Treat `data/seo/programmatic-seo-plan.json` as research input, not publishable content.
- Create or update public pages only from `brief_ready` entries.
- Keep pages `noindex` or unpublished when a brief cannot be filled with real commands, SDK examples, connector behavior, product screenshots, or measurable use-case evidence.
- Do not publish hundreds of near-identical pages. Prefer fewer pages with specific intent, real examples, and useful internal links.

Required review before publishing a programmatic SEO page:

1. Verify the keyword has real demand from DataForSEO, Ahrefs/Semrush CSV, or Search Console.
2. Confirm the page has a useful reason to exist beyond ranking.
3. Add product-specific evidence: CLI command, SDK snippet, integration flow, analytics query, or Growth Engineer output shape.
4. Check for duplicate/cannibalizing pages in `apps/landing/src/data/seo-pages.ts`, integrations, alternatives, docs, and blog.
5. Build the landing app and inspect the generated page before publishing.

## Publishing Gate

Before publishing:

```bash
pnpm --filter @agentic-analytics/landing build
```

Then verify:

- the post appears under `/blog/<slug>/`
- the page has title, description, canonical URL, and Article JSON-LD
- images load and have useful alt text
- the sitemap includes the post only after it is intended to be public
- the article does not contradict current pricing, connectors, regions, or product capabilities

## References

- [Blog Brief Template](references/blog-brief-template.md)
- [Blog MDX Template](references/blog-mdx-template.mdx)
