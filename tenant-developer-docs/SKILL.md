---
name: tenant-developer-docs
description: Use when writing or reviewing tenant-facing docs, README files, and skill instructions to keep a strict tenant developer perspective.
license: MIT
homepage: https://github.com/wotaso/prodinfos-skills
metadata: {"author":"wotaso","version":"1.0.0","openclaw":{"emoji":"✍️","homepage":"https://github.com/wotaso/prodinfos-skills"}}
---

# Tenant Developer Docs Voice

## Use This Skill When

- writing or editing docs in `apps/docs/**`
- updating tenant-facing README files
- reviewing `skills/prodinfos-*/**` wording

## Voice Rules

- Write for the implementing team: `you`, `your app`, `your workspace`, `your project`.
- Use neutral product terms: `Prodinfos dashboard`, `Prodinfos API`, `collector endpoint`.
- Avoid provider-centric phrasing such as `our SaaS`, `we provide`, or `from our side`.
- Keep content task-first: what the tenant developer must do next.

## Rewrite Patterns

- Replace `in the SaaS dashboard` with `in your Prodinfos dashboard workspace`.
- Replace `we provide` with `you can use`.
- Replace `our API` with `the Prodinfos API`.

## Scope Defaults

- Apply by default to docs, quickstarts, and setup guides.
- If a page is strictly internal operator content, label it clearly as internal and keep it outside tenant docs.

## QA Checklist

- No provider-first language in tenant-facing sections.
- All setup steps can be executed by a tenant developer without internal context.
- Credentials are clearly scoped and named (`readonly_token`, write key, `projectId`, endpoint).
