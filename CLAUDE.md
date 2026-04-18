# CLAUDE.md

Conventions for Claude Code, Cowork, or any agent editing this repo. Read this once on session start; everything else follows from it.

## What this repo is

Canonical prose for the NNTM "ultimate" courses — each one a consolidated teaching artifact synthesized from multiple third-party source courses on the same topic. See [`README.md`](./README.md) for the architecture and how this fits with the rest of the pipeline (Drive, Airtable, N8N, Supabase).

## How to work in this repo

### Branch strategy

- `main` is canonical. Never commit directly.
- Feature branches use the pattern `synthesis/<nntm>-<topic-slug>/<short-descriptor>` for synthesis work (e.g., `synthesis/labs-local-seo/gbp-categories`).
- Structural or meta changes (taxonomy updates, schema tweaks, scaffold improvements) use `scaffold/<descriptor>` or `meta/<descriptor>`.
- Every change arrives via pull request. Human review before merge.

### Commit identity

Git is already configured globally as `denis-cowork-bot <denis@nanotomlabs.com>`. Don't override it — consistent authorship makes the audit trail readable.

### Credentials

GitHub PAT lives at `/sessions/<session>/mnt/Course creator/.env` under `GITHUB_PAT`. Source the file before git operations. Don't commit the `.env` — it's in `.gitignore`.

## Lesson file conventions

Every lesson is a markdown file with YAML frontmatter. See [`_meta/frontmatter-schema.md`](./_meta/frontmatter-schema.md) for the authoritative field list. See [`_meta/EXAMPLE-lesson.md`](./_meta/EXAMPLE-lesson.md) for a worked example.

The body of a lesson is **copy-paste clean**. Denis will paste it directly into his course software. That means:

- No inline citations like "(source: Joe Smith's course)."
- No appendix listing references at the bottom.
- No editor comments left as HTML comments in the prose.
- Formatting that renders well in GitHub and in most course platforms (standard headings, lists, fenced code blocks, tables).

All attribution and audit data lives in the frontmatter's `sources` array. When editing a lesson, append new sources — don't rewrite or remove existing entries unless a contribution genuinely turned out to be wrong.

## Topic taxonomy

The `topic` field in every lesson must match a `name` from [`_meta/taxonomy.yaml`](./_meta/taxonomy.yaml) exactly, including capitalization. If the topic you're working on isn't in the taxonomy yet, add it in the same PR that introduces the first lesson for that topic.

Topics are scoped per NNTM (`labs`, `capital`, `ventures`). A single topic never lives under two NNTMs — pick the one that best fits the business use.

## The synthesis workflow

For the full workflow, including the four-outcome decision framework (skip / replace / enrich / create), use the [`course-synthesis`](./.claude/skills/course-synthesis/SKILL.md) skill in this repo. It auto-activates for synthesis requests.

## Anti-patterns to avoid

- Don't commit large binaries (videos, audio, PDFs of source material). Those live in Drive. The repo holds prose only. `.gitignore` covers most of these but resist the urge to add them even if git would let you.
- Don't rename lesson files casually. Course software may depend on stable paths. If a rename is needed, do it in its own PR so the diff is reviewable.
- Don't "clean up" multiple lessons in a single PR. One PR per coherent change keeps review sane.
- Don't introduce new top-level directories. The shape (`labs/`, `capital/`, `ventures/`, `_meta/`) is deliberate. New topics go under an existing NNTM.
- Don't strip the `sources` array on a lesson you're editing. Even if your change is a rewrite, prior contributions are still part of the audit trail.
