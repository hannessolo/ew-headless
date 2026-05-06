---
name: create-page
description: "Create a new DA page at a given path. Use when the user wants to create, add, or write a new page in Document Authoring. Trigger on: 'create page', 'new page', 'add page', 'write a page'."
license: Apache-2.0
metadata:
  version: "0.1.0"
---

# Create Page

Create a new HTML page in DA at a given path via the Admin API.

## Guard

If `EW_ORG`, `EW_SITE`, or `DA_TOKEN` are not set, run `/experience-workspace:init` first.

## Step 1 — Gather inputs

Ask the user for:
1. **Path** — where to create the page (e.g. `docs/new-article.html`). Ensure it ends with `.html`.
2. **Content** — the HTML to write. If the user has not provided content, help them draft it before proceeding.

## Step 2 — Confirm before writing

Show the user the path and a summary or preview of the content. Ask for explicit confirmation before creating the file, since this cannot be undone without deleting.

## Step 3 — Write the page

Save the content to a temporary file, then POST it as multipart form data:

```bash
TMP=$(mktemp /tmp/da-page-XXXXXX.html)
cat > "$TMP" << 'HTMLEOF'
<content goes here>
HTMLEOF

curl -s -w "\n%{http_code}" \
  -H "Authorization: Bearer $DA_TOKEN" \
  -F "data=@$TMP;type=text/html" \
  "https://admin.da.live/source/$EW_ORG/$EW_SITE/<path>"

rm -f "$TMP"
```

Check the HTTP status (last line of output):
- **200** or **201** — success
- **401** — token expired; re-run `/experience-workspace:init` to refresh
- **403** — insufficient permissions
- **409** — a file already exists at this path; use `edit-page` instead

## Step 4 — Confirm and offer handoff

Tell the user the page was created and provide a link to open it:

```
Created: https://da.live/canvas#/<EW_ORG>/<EW_SITE>/<path>
```
