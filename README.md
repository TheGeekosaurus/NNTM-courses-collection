# NNTM Courses Collection

Canonical prose for the "ultimate" courses synthesized across the NNTM businesses.

## What this repo is

Every NNTM business unit (Labs, Capital, Ventures) has courses it teaches. Rather than reinventing the wheel for each topic, we consume a lot of third-party courses on the same subjects (Local SEO, business credit, deal sourcing, etc.) and synthesize a single "ultimate" version that combines the best teaching from every source.

This repo holds that ultimate version — the actual prose of each lesson, one markdown file at a time. Content lands here via pull request after an AI-assisted synthesis pass, gets reviewed, and merges to `main` when approved.

## How this fits with the other systems

| System | Role |
| --- | --- |
| **Google Drive** ("MEGA #2") | Raw source courses live here: videos, PDFs, slides. This is the input. |
| **Airtable** (`Redo Course AI` base, `appjJilCWriP0Eo4o`) | Index of source courses / chapters / sub-chapters / lessons; status tracking; buttons to trigger N8N workflows. |
| **N8N** (workflows 1.01 → 5.01) | Deterministic pipeline: extract folders → transcribe lessons → chunk & atomize → embed & upsert to Supabase. |
| **Supabase** | Chunked + embedded knowledge for agent retrieval (RAG). Powers the skills and endpoints on the agent server. |
| **This repo** | Synthesized human-readable course prose. Source of truth for what the ultimate course actually says. Not the same shape as Supabase — Supabase is atomized for retrieval, this is continuous for teaching. |

## The synthesis workflow

```
New source course in Airtable
        │
        ▼
N8N pipeline (1.01 → 5.01)        [mechanical: extract, transcribe, chunk, embed]
        │
        ▼
Cowork agent synthesis pass       [judgment: compare, decide, propose]
        │
        ▼
Pull request against main         [review: human-in-loop approval]
        │
        ▼
Merge to main                     [canonical: ultimate course updated]
```

For each new source lesson, the synthesis agent decides between four outcomes:

- **Skip** — same concept, same depth, nothing new to add
- **Replace** — new lesson teaches it better; swap in place
- **Enrich** — same core, but new angle / example / tactic worth grafting in
- **Create** — genuinely novel content, new module or lesson

Each PR represents one or more of these decisions, with the source lesson(s) cited in frontmatter.

## Repo structure

```
labs/        NNTM Labs     (marketing / growth)
capital/     NNTM Capital  (funding / business credit)
ventures/    NNTM Ventures (business acquisition)
_meta/       Taxonomy, frontmatter schema, examples, conventions
```

Inside each NNTM folder: one subfolder per topic (e.g., `labs/local-seo/`). Each topic folder contains a `README.md` (course-level table of contents) plus the lesson markdown files, optionally grouped into module subfolders.

## Conventions

- **Every lesson is a markdown file** with a YAML frontmatter block at the top. Frontmatter carries all metadata and attribution; the body stays clean and copy-paste ready. See `_meta/frontmatter-schema.md`.
- **Topic names are controlled.** Use the exact spelling from `_meta/taxonomy.yaml`. Add new topics there before using them in a lesson.
- **Source attribution lives in the frontmatter `sources` array** — never inline in the body. The body is what gets pasted into course software.
- **All synthesis arrives via PR.** No direct commits to `main` for content changes.
- **Commit history is the audit trail.** Every merge tells the story of how a lesson evolved as new sources were folded in.
