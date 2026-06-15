# Knowledge Base

A personal research log — accumulates takeaways from podcasts, articles, and other sources, organized by topic with smart source attribution.

## Structure

```
knowledge-base/
├── index.html      # The site (reads data.json at runtime)
└── data.json       # All knowledge — edit this to add content
```

## Workflow: adding a new source

### 1. Ask Claude to extract takeaways

Paste the transcript/article into Claude and say:

> "Extract takeaways from this in the knowledge base JSON format. 
> Check if any topics already exist in data.json and merge new points 
> in — don't repeat what's already there. Add new topics if needed."

Claude will return a JSON diff: either a new topic object, or new takeaways + a new source entry to add to an existing topic.

### 2. Merge via Claude Code

Open Claude Code in this repo and say:

> "Merge this into data.json: [paste JSON]. Preserve all existing 
> takeaway IDs. Append new takeaways. Add the new source to the 
> relevant topic's sources[] array."

### 3. Push to GitHub

```bash
git add data.json
git commit -m "Add: [source name]"
git push
```

GitHub Pages serves the updated site automatically.

## JSON schema

```json
{
  "meta": { "title": "...", "updated": "YYYY-MM" },
  "topics": [
    {
      "id": "slug",
      "title": "Topic Name",
      "tags": ["tag1", "tag2"],
      "summary": "One-sentence synthesis across all sources.",
      "takeaways": [
        {
          "id": "unique-id",
          "point": "The actual takeaway.",
          "sources": ["Outlet – Show/Author name"]
        }
      ],
      "sources": [
        {
          "id": "src-001",
          "title": "Episode/Article title",
          "outlet": "Outlet name",
          "type": "podcast | article | book | paper",
          "date": "YYYY-MM",
          "url": ""
        }
      ]
    }
  ]
}
```

## Rules for Claude when merging

- **Never duplicate** a takeaway that already exists (even if worded differently from a new source) — add the new source name to that takeaway's `sources[]` instead.
- **Add new takeaways** only when they contain genuinely new information not covered by any existing point.
- **Update `topic.summary`** only when new sources materially change the synthesis.
- **Always append** to `topic.sources[]` when a new source covers an existing topic.
- **IDs**: takeaway IDs follow pattern `{topic-prefix}-{NNN}`, source IDs follow `src-{NNN}`.

## GitHub Pages setup

1. Create a new repo (e.g. `knowledge-base`)
2. Push both files
3. Settings → Pages → Source: `main` branch, root folder
4. Site live at `https://{username}.github.io/knowledge-base/`
