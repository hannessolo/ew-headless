---
name: edit-page
description: "Edit an existing DA page. Use when the user wants to update, modify, change, or rewrite content on an existing DA page. Trigger on: 'edit page', 'update page', 'change', 'modify', 'rewrite'."
license: Apache-2.0
metadata:
  version: "0.1.0"
---

# Edit Page

Read, modify, and write back a DA page via the Admin API. Always versions the page before overwriting.

## Guard

If `EW_ORG`, `EW_SITE`, or `DA_TOKEN` are not set, run `/experience-workspace:init` first.

## Step 1 — Resolve path

Ask for the page path if not provided. Paths must end with `.html`.
Example: `docs/index.html`

## Step 2 — Read current content

```bash
curl -s \
  -H "Authorization: Bearer $DA_TOKEN" \
  "https://admin.da.live/source/$EW_ORG/$EW_SITE/<path>"
```

If 404: the page doesn't exist — offer to use `create-page` instead.
If 401: token expired; re-run `/experience-workspace:init`.

Show the current content to the user so they can confirm what will be changed.

## Step 3 — Apply edits

Work with the user to produce the updated HTML content. Show a clear diff or summary of what will change.

## Step 4 — Confirm before writing

Ask for explicit confirmation before overwriting. State clearly what is changing.

## Step 5 — Create a version first

Before writing, save a version snapshot of the current content:

```bash
curl -s -w "\n%{http_code}" -X POST \
  -H "Authorization: Bearer $DA_TOKEN" \
  "https://admin.da.live/versionsource/$EW_ORG/$EW_SITE/<path>"
```

If this returns non-200, warn the user but do not block the edit — versioning is best-effort.

## Step 6 — Write the updated page

Save the new content to a temporary file, then POST it:

```bash
TMP=$(mktemp /tmp/da-page-XXXXXX.html)
cat > "$TMP" << 'HTMLEOF'
<updated content goes here>
HTMLEOF

curl -s -w "\n%{http_code}" \
  -H "Authorization: Bearer $DA_TOKEN" \
  -F "data=@$TMP;type=text/html" \
  "https://admin.da.live/source/$EW_ORG/$EW_SITE/<path>"

rm -f "$TMP"
```

Check the HTTP status:
- **200** — success
- **401** — token expired; re-run `/experience-workspace:init`
- **403** — insufficient permissions
- **404** — page was deleted between read and write; inform the user

## Step 7 — Confirm and offer handoff

Tell the user the page was updated and provide a link to review it:

```
Updated: https://da.live/canvas#/<EW_ORG>/<EW_SITE>/<path>
```
