---
name: read-page
description: "Read the HTML content of a DA page. Use when the user wants to view, inspect, or retrieve a page from Document Authoring. Trigger on: 'read page', 'get page', 'show me the content of', 'fetch page'."
license: Apache-2.0
metadata:
  version: "0.1.0"
---

# Read Page

Fetch and display the HTML content of a DA page via the Admin API.

## Guard

If `EW_ORG`, `EW_SITE`, or `DA_TOKEN` are not set, run `/experience-workspace:init` first.

## Step 1 — Resolve path

Ask for the page path if not provided. Paths should start without a leading slash and end with `.html`.
Example: `docs/index.html`

## Step 2 — Fetch the page

```bash
curl -s \
  -H "Authorization: Bearer $DA_TOKEN" \
  "https://admin.da.live/source/$EW_ORG/$EW_SITE/<path>"
```

Check the HTTP status:
- **200** — success, display the content
- **401** — token expired; re-run `/experience-workspace:init` to refresh
- **403** — insufficient permissions for this path
- **404** — page does not exist at this path

## Step 3 — Display and offer handoff

Show the returned HTML content to the user. Then offer a link to open it in the DA editor:

```
View in DA: https://da.live/canvas#/<EW_ORG>/<EW_SITE>/<path>
```
