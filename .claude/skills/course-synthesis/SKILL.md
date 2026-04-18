---
name: course-synthesis
description: Run a synthesis pass on source course material to build or update the NNTM "ultimate" courses in the TheGeekosaurus/NNTM-courses-collection repo. Use this skill whenever the user wants to synthesize, consolidate, merge, or rebuild courses — including requests like "run a synthesis pass on Local SEO," "fold this new course into the ultimate Local SEO," "compare these lessons and propose a merge," "process the new course I just added to Airtable," or anything that involves reading source course transcripts and turning them into consolidated lessons. Also trigger when the user mentions the Redo Courses project, NNTM courses, or opening PRs against the NNTM-courses-collection repo, even if they don't say "synthesis" explicitly.
---

# Course Synthesis

This skill drives the synthesis half of the Redo Courses pipeline. N8N already handles the mechanical work (extraction → transcription → chunking → embedding). Your job when this skill activates is the judgment layer: comparing new source material against what's already in the ultimate course, deciding what (if anything) should change, and proposing the change as a pull request for human review.

## Why this exists

Denis takes many third-party courses on the same topic. Instead of re-watching five Local SEO courses every time he wants to refresh his curriculum, he's building one "ultimate" course per topic that combines the best teaching from all sources. Chunks in Supabase serve his AI agents; the prose in the GitHub repo serves his course-creation software. This skill is what turns a pile of source transcripts into a single coherent teaching artifact — and keeps that artifact evolving as new source courses arrive.

## The four-outcome decision framework

Every new source lesson maps to exactly one of:

- **Skip.** The concept, depth, and framing are already well covered. Nothing new to graft in. Don't touch the ultimate course; note in the PR why you skipped.
- **Replace.** The new lesson teaches the same concept meaningfully better — clearer explanation, stronger examples, more current tactics. Swap the body of the existing lesson and add the new source to its `sources` array with a contribution note like "rewrite — clearer framing of X."
- **Enrich.** The core is the same but the new lesson adds a novel angle, tactic, example, or caveat worth folding into the existing lesson. Graft the new material into the existing body and add the new source with a contribution note describing what it added.
- **Create.** The new lesson genuinely covers ground no existing ultimate lesson covers. Create a new lesson file (new module if needed) with the new source as its first contributor.

When in doubt between skip and enrich, prefer enrich — dropping useful nuance is a worse failure mode than a slightly longer lesson. When in doubt between replace and enrich, prefer enrich — preserving continuity and prior approvals outweighs minor improvements. When in doubt between enrich and create, ask: "would a student expect these in the same lesson?" If yes, enrich. If no, create.

## The workflow

### Step 1 — Orient

- Confirm which ultimate course is the target (NNTM + topic). E.g., `labs/local-seo/`.
- Confirm which source lessons are in scope. Usually Denis will name a course or point at Airtable records; sometimes he'll say "everything new since the last pass."
- Pull the current state of the target ultimate course from the GitHub repo (clone or pull the latest `main`). Read the course-level `README.md` and any existing lesson files so you know what's already covered.

### Step 2 — For each source lesson in scope

- Read the full transcript from Drive (or from the Airtable `Lessons` table `Lesson transcript` field, which is the same text).
- Query Supabase (via the agent server, or directly if access is granted) for semantically similar chunks already in the knowledge base. High similarity = concept already covered; low similarity = probably new.
- If similar chunks point to one or more existing ultimate lessons, read those lessons' full bodies too. Compare in full prose — similarity scores narrow the search, they don't make the decision.
- Classify the outcome using the framework above. Write a one-paragraph rationale. This rationale goes into the PR body.

### Step 3 — Stage the change

Branch naming: `synthesis/<nntm>-<topic-slug>/<short-descriptor>` — e.g., `synthesis/labs-local-seo/gbp-category-audit`, `synthesis/labs-local-seo/batch-joe-smith-course`.

For **skip** outcomes: no file change, but still open a PR (title: `Skip: <source lesson name> — already covered by <existing lesson path>`) with the rationale. This gives an audit trail of "we looked at this and chose not to add it."

For **replace / enrich / create**: edit or create the lesson file(s). Follow the frontmatter schema in `_meta/frontmatter-schema.md`. Body stays clean — no inline citations, no appendix. Bump `last_updated` to today. Append to (don't overwrite) the `sources` array with the new contribution.

Update `labs/<topic>/README.md` (or equivalent) if the module structure changed.

### Step 4 — Open the PR

- Title: imperative, short. E.g., "Enrich GBP categories lesson with secondary-category strategy from Jane Doe course" or "Create new lesson: GMB Services section".
- Body: the rationale from Step 2 per lesson, plus a decision summary at the top (`Skip: 3, Replace: 0, Enrich: 2, Create: 1`) if the PR covers multiple source lessons. Reference the Airtable lesson IDs being folded in.
- Leave `status: draft` on any new/changed lesson until Denis reviews.

### Step 5 — Hand off

Report back to Denis with the PR URL and a one-paragraph summary of what you proposed. He'll review in GitHub's UI, comment or approve, and merge. After merge, if he asks, bump the affected lessons to `status: approved` in a follow-up commit.

## Principles

- **Judgment over rules.** This is a skill, not a script. Every synthesis decision is context-dependent — two lessons on "NAP consistency" may be near-duplicates (skip) or one may have a case study worth grafting in (enrich). Use the framework as a decision scaffold, not a checklist to mechanically apply.
- **Preserve the teacher's best framing.** When replacing or enriching, pick the explanation that would actually help a student understand, not the longest one or the most technical one. Sometimes the third source's one-paragraph explanation beats the first source's five-paragraph one.
- **Attribution is cheap; dilution is expensive.** Err on the side of adding every source that meaningfully contributed to the `sources` array. But don't add sources that didn't actually change the body — that's noise.
- **Don't batch decisions across topics.** One PR per target topic, usually. A PR that touches `labs/local-seo/` and `capital/business-credit/` is harder to review and risks one bad call tainting the other.
- **When unsure, draft the change and let Denis reject it.** A PR that gets closed unmerged is cheap; a missed angle that never makes it into the course is forever.

## Key references

- **Frontmatter schema**: [`_meta/frontmatter-schema.md`](../../../_meta/frontmatter-schema.md) in this repo.
- **Topic taxonomy**: [`_meta/taxonomy.yaml`](../../../_meta/taxonomy.yaml). Any `topic` in a lesson frontmatter must match a `name` here. If the topic doesn't exist yet, add it to taxonomy.yaml in the same PR.
- **Example lesson**: [`_meta/EXAMPLE-lesson.md`](../../../_meta/EXAMPLE-lesson.md).
- **Memory**: `projects/redo-courses.md` in Cowork memory has system IDs, URLs, and project state.

## Access and credentials

The GitHub PAT, repo name, and Drive folder ID are stored in `/sessions/<session>/mnt/Course creator/.env`. Source it before any git or curl operation. Git identity is already configured globally as `denis-cowork-bot <denis@nanotomlabs.com>`.

Supabase access isn't always granted directly — when it isn't, query chunks via the agent server endpoints (Denis will provide them when needed), or fall back to reading full transcripts from Drive/Airtable and doing the comparison on prose alone. Prose comparison is always acceptable; Supabase is an efficiency layer, not a requirement.
