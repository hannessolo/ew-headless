---
name: delete-page
description: "Delete a DA page or folder. Use when the user explicitly wants to remove or delete content from Document Authoring. Trigger on: 'delete page', 'remove page', 'delete file'."
license: Apache-2.0
metadata:
  version: "0.1.0"
---

# Delete Page

Permanently delete a DA page or folder via the Admin API.

## Guard

If `EW_ORG`, `EW_SITE`, or `DA_TOKEN` are not set, run `/experience-workspace:init` first.

## Step 1 — Resolve path

Ask for the path to delete if not provided.

## Step 2 — Confirm explicitly

Deletion is permanent and cannot be undone. Ask the user to confirm by stating the full path they intend to delete. Do not proceed until they confirm explicitly.

Example prompt:
```
This will permanently delete: <EW_ORG>/<EW_SITE>/<path>
Type the path to confirm, or say "cancel" to abort.
```

## Step 3 — Delete the page

```bash
curl -s -w "\n%{http_code}" -X DELETE \
  -H "Authorization: Bearer $DA_TOKEN" \
  "https://admin.da.live/source/$EW_ORG/$EW_SITE/<path>"
```

Check the HTTP status:
- **200** or **204** — successfully deleted
- **401** — token expired; re-run `/experience-workspace:init` to refresh
- **403** — insufficient permissions
- **404** — page did not exist (already deleted or wrong path)

## Step 4 — Confirm to user

Tell the user the outcome. If successful, confirm what was deleted. Do not offer a handoff link — the page no longer exists.
