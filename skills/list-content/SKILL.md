---
name: list-content
description: "List files and folders at a DA path. Use when the user wants to browse, explore, or see what exists at a path in Document Authoring. Trigger on: 'list', 'browse', 'what's in', 'show me the files', 'explore'."
license: Apache-2.0
metadata:
  version: "0.1.0"
---

# List Content

List the files and folders at a given DA path via the Admin API.

## Guard

If `EW_ORG`, `EW_SITE`, or `DA_TOKEN` are not set, run `/experience-workspace:init` first.

## Step 1 — Resolve path

Ask for the path to list if not provided. Use an empty path or `/` to list the root of the site.
Example: `docs/` or leave blank for root.

## Step 2 — Fetch the listing

```bash
curl -s \
  -H "Authorization: Bearer $DA_TOKEN" \
  "https://admin.da.live/list/$EW_ORG/$EW_SITE/<path>"
```

Check the HTTP status:
- **200** — success, parse and display the result
- **401** — token expired; re-run `/experience-workspace:init` to refresh
- **403** — insufficient permissions for this path
- **404** — path does not exist

## Step 3 — Display and offer handoff

The response is a JSON array of entries. Each entry has at minimum a `name` field and an `ext` field (empty string for folders). Present the listing clearly, distinguishing files from folders.

For each entry, include a DA link:
- **Folder** → `https://da.live/browse#/<EW_ORG>/<EW_SITE>/<entry-path>`
- **File** → `https://da.live/canvas#/<EW_ORG>/<EW_SITE>/<entry-path>`
