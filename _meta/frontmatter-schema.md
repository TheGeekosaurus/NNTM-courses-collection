# Lesson Frontmatter Schema

Every lesson markdown file starts with a YAML frontmatter block, fenced by `---` on its own line before and after. This block holds all metadata. The lesson **body** below the frontmatter stays clean and copy-paste ready — no inline citations, no "source: X" notes in the middle of paragraphs, no appendix.

When you copy a lesson into your course software, select everything after the closing `---` and paste. Done.

## Required fields

| Field | Type | Notes |
| --- | --- | --- |
| `title` | string | Lesson title, title case. |
| `topic` | string | Must match a topic `name` in `_meta/taxonomy.yaml` **exactly** (including capitalization). |
| `nntm` | enum | One of: `labs`, `capital`, `ventures`. Matches the top-level folder. |
| `status` | enum | One of: `draft`, `review`, `approved`. See below. |
| `last_updated` | date | ISO format `YYYY-MM-DD`. Bumped every time the body changes. |
| `sources` | list | Source courses that contributed to this lesson. See structure below. |

### `status` values

- `draft` — Freshly synthesized, not yet reviewed by a human.
- `review` — Currently under human review (a PR is open).
- `approved` — Locked-in. Safe to ship into course software.

### `sources` entry structure

Each entry in the `sources` list must include:

- `course` — Human-readable course name, matching the `Name` field in the Airtable `Original Courses` table.
- `lesson_id` — Airtable record ID of the specific source lesson contributing, format `recXXXXXXXXXXXXX`.
- `contribution` — Short free-text description of what this source brought. Be specific: "Primary framework," "Added the tertiary-category layering strategy," "Updated tactics for 2026 GBP UI changes."

## Optional fields

| Field | Type | Notes |
| --- | --- | --- |
| `module` | string | Module name, if the course is organized into modules. |
| `order` | integer | Sort key for course software that needs explicit ordering. |
| `tags` | list of strings | Free-text tags for cross-cutting themes (`gbp`, `reviews`, `citations`). |
| `estimated_read_minutes` | integer | Rough read time. |

## Example

See [`EXAMPLE-lesson.md`](./EXAMPLE-lesson.md) for a fully populated lesson.
