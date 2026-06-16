# Knowledge Base — Schema Reference

This document defines the data structure for all topic files in this knowledge base. It is the single source of truth for anyone (human or AI) creating, editing, or merging topic files.

---

## Repository Structure

```
knowledge-base/
├── index.json           ← manifest listing all topics and their file paths
├── SCHEMA.md            ← this file
└── topics/
    ├── creatine.json
    ├── sleep.json
    ├── metabolic-health.json
    └── ...              ← one file per topic
```

---

## index.json

The index is a lightweight manifest. It does **not** contain takeaway data — only pointers to topic files.

```json
{
  "meta": {
    "title": "Jordan's Knowledge Base",
    "updated": "YYYY-MM-DD",
    "version": "2.0",
    "schema": "SCHEMA.md"
  },
  "topics": [
    {
      "id": "topic-slug",
      "file": "topics/topic-slug.json",
      "title": "Human-readable Topic Title"
    }
  ]
}
```

**Rules:**
- `updated` must be set to today's date whenever any topic file changes.
- `id` must exactly match the `id` field inside the corresponding topic file.
- `file` path is always relative to the repo root.
- Topics are listed in the order they should appear in the UI.

---

## Topic File Schema

Each topic lives in its own file under `topics/`. The filename must match the topic `id` with a `.json` extension (e.g. `id: "sleep"` → `topics/sleep.json`).

### Full structure

```json
{
  "id": "topic-slug",
  "title": "Human-readable Title",
  "tags": ["tag1", "tag2"],
  "summary": "One or two sentences synthesising the topic across all sources.",
  "takeaways": [ ... ],
  "sources": [ ... ]
}
```

---

### Field: `id`

- **Type:** string
- **Format:** lowercase, hyphen-separated (`kebab-case`)
- **Examples:** `"creatine"`, `"metabolic-health"`, `"ai-economy"`
- **Rules:** Unique across all topics. Never change once set — it is used as a URL anchor and as the cross-reference key.

---

### Field: `title`

- **Type:** string
- **Format:** Title Case
- **Examples:** `"Creatine"`, `"Metabolic Health & Diet"`, `"AI & the Economy"`

---

### Field: `tags`

- **Type:** array of strings
- **Format:** lowercase, single words or short hyphenated phrases
- **Examples:** `["supplementation", "health", "neuroscience"]`
- **Rules:** 2–6 tags per topic. Reuse existing tags wherever appropriate — consistent tags enable cross-topic filtering. Common tags in use: `health`, `longevity`, `neuroscience`, `exercise`, `nutrition`, `supplementation`, `sleep`, `hormones`, `cognition`, `AI`, `work`, `career`.

---

### Field: `summary`

- **Type:** string
- **Format:** 1–3 sentences. Plain prose, no markdown. Present tense.
- **Rules:** Must synthesise across **all** sources in the topic — not just the most recent one. Update whenever a new source materially changes the overall picture.

---

### Field: `takeaways`

- **Type:** array of takeaway objects
- **Rules:** Ordered from most fundamental to most specific/advanced. No duplicates — if two sources cover the same point, merge them into one takeaway with both sources listed.

#### Takeaway object

```json
{
  "id": "xx-001",
  "headline": "Short punchy claim, max ~12 words",
  "point": "Full explanation with evidence, numbers, and context.",
  "sources": ["Source Label 1", "Source Label 2"]
}
```

#### Takeaway `id`

- **Format:** `{topic-prefix}-{NNN}` where prefix is 2–3 letters from the topic slug and NNN is zero-padded to 3 digits.
- **Examples:** `"cr-001"` (creatine), `"sl-023"` (sleep), `"mh-010"` (metabolic-health), `"ae-005"` (ai-economy)
- **Rules:** Never reuse or reassign an ID, even if a takeaway is deleted. IDs are permanent identifiers.

#### Takeaway `headline`

- **Format:** Short, declarative, scannable. Max ~12 words. Should be readable as a standalone claim.
- **Style:** Use em-dashes for contrast (`"Fasted training for longevity; fuelled training for performance"`), numbers for precision (`"3.5 minutes of vigorous activity daily cuts cancer risk 40%"`).
- **Rules:** Must be unique within the topic. Do not repeat the topic title in the headline.

#### Takeaway `point`

- **Format:** Full prose explanation. No markdown formatting inside the string. Numbers, study citations, and practical implications should all be included here.
- **Rules:** This is the detail layer — write it so that reading the headline + point gives a complete, actionable understanding. Aim for 2–5 sentences.

#### Takeaway `sources`

- **Type:** array of strings
- **Format:** `"Outlet – Author/Show Name"` — matches exactly the source label used in the topic's `sources[]` array.
- **Examples:** `"DCEO – Dr. Matthew Walker"`, `"AI Work 101 – Parth Patil"`
- **Rules:** Always an array, even for a single source. When two sources confirm the same point, list both — this is how corroboration is tracked.

---

### Field: `sources`

- **Type:** array of source objects
- **Rules:** Every source referenced in any takeaway must appear here. IDs are local to the topic file (two topics can both have a `src-001` — they are independent).

#### Source object

```json
{
  "id": "src-001",
  "title": "Episode or content title",
  "outlet": "Publication, podcast, or platform name",
  "type": "podcast | course | book | paper | article | video",
  "date": "YYYY-MM",
  "url": ""
}
```

#### Source `id`

- **Format:** `src-{NNN}`, zero-padded, local to the topic file.
- **Rules:** Sequential within the file. IDs are not globally unique.

#### Source `type`

Allowed values:
| Value | Use for |
|-------|---------|
| `podcast` | Podcast episodes |
| `course` | Online courses (MasterClass, Coursera, etc.) |
| `book` | Books |
| `paper` | Academic papers or journals |
| `article` | Written articles, blog posts |
| `video` | Standalone videos (YouTube, etc.) |

#### Source `date`

- **Format:** `YYYY-MM` (year and month only)
- Leave `url` as `""` if not available; populate when known.

---

## Rules for Adding a New Topic

1. Create `topics/{new-topic-id}.json` following the full schema above.
2. Choose a unique `id` in `kebab-case`.
3. Choose a 2–3 letter prefix for takeaway IDs (e.g. `"environmental-toxins"` → `et-`).
4. Add a corresponding entry to `index.json`.
5. Update `meta.updated` in `index.json` to today's date.

---

## Rules for Adding Takeaways to an Existing Topic

1. Open the relevant `topics/{id}.json`.
2. Check existing takeaways — if the new point overlaps an existing one, **add the new source to that takeaway's `sources[]` array** rather than creating a duplicate.
3. If the point is genuinely new, append a new takeaway object at the end of `takeaways[]`.
4. Assign the next sequential ID (check the highest existing NNN and increment by 1).
5. If the source is new to this topic, add it to the topic's `sources[]` array.
6. Update `meta.updated` in `index.json`.

---

## Rules for Adding a New Field

When a new field needs to be added to the schema:

1. Define it here in `SCHEMA.md` first — name, type, format, allowed values, rules.
2. Add it to **all existing topic files** in one operation (Claude Code can do this as a bulk update).
3. Update the "Full structure" example block above.
4. Bump the `version` in `index.json`.

Do not add fields to individual topic files without updating the schema and all other files — schema drift makes automated merging unreliable.

---

## Source Label Convention

The string used in `takeaway.sources[]` must be consistent and human-readable. The format is:

```
"{Outlet Abbreviation} – {Author or Show Name}"
```

**Current labels in use:**

| Label | Full name |
|-------|-----------|
| `DCEO – Dr. Darren Candow` | Diary of a CEO — Dr. Darren Candow episode |
| `DCEO – Dr. Matthew Walker` | Diary of a CEO — Dr. Matthew Walker episode |
| `DCEO – Dr. Michael Bruce` | Diary of a CEO — Dr. Michael Bruce episode |
| `DCEO – Dr. Ben Bikman` | Diary of a CEO — Dr. Ben Bikman episode |
| `DCEO – Dr. Mindy Pelz` | Diary of a CEO — Dr. Mindy Pelz episode |
| `DCEO – Dr. Rhonda Patrick` | Diary of a CEO — Dr. Rhonda Patrick episode |
| `AI Wealth 101 – Erik Brynjolfsson` | AI Wealth 101 MasterClass |
| `AI Wealth 101 – Michelle Meyer` | AI Wealth 101 MasterClass |
| `AI Wealth 101 – Dambisa Moyo` | AI Wealth 101 MasterClass |
| `AI Work 101 – Erik Brynjolfsson` | AI Work 101 MasterClass |
| `AI Work 101 – Parth Patil` | AI Work 101 MasterClass |
| `AI Work 101 – Cat Goetze` | AI Work 101 MasterClass |

Add new rows to this table whenever a new source is introduced.

---

## Claude Prompt Template

When asking Claude to extract takeaways from a new source, use this as your base prompt:

```
Extract takeaways from this transcript/article for my knowledge base.

Rules:
- Check my existing topics — if a point overlaps an existing takeaway, note the existing ID so the source can be added to it rather than duplicating the point.
- For genuinely new points, use the correct topic file and assign the next sequential takeaway ID.
- If no existing topic fits, propose a new topic with a suitable id, title, tags, and summary.
- Format all output as JSON matching the schema in SCHEMA.md.
- Source label format: "{Outlet} – {Author}"

Return:
- new_topics: [] (array of complete new topic objects, if any)
- merge_into_existing_topics: [] (array of objects with topic_id, new takeaways, source additions to existing takeaways, and new source entry if needed)
```
