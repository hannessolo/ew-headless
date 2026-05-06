---
name: init
description: "Initialize an Experience Workspace session. Establishes org/site context and authenticates with the DA API. All other experience-workspace skills require this to run first. Use when the user types `/experience-workspace:init` or when another skill's guard triggers it."
license: Apache-2.0
metadata:
  version: "0.1.0"
---

# Experience Workspace — Init

Experience Workspace is a headless CLI interface for the Adobe Document Authoring (DA) platform. It lets you read, create, edit, list, and delete content pages stored in DA using natural language, without opening the DA browser UI.

## Step 1 — Check if already initialized

If `EW_ORG`, `EW_SITE`, and `DA_TOKEN` are all set in the current session environment and `DA_TOKEN` is still valid, skip to **Step 5**. Do not re-initialize unnecessarily.

## Step 2 — Establish org and site context

Ask the user for their DA org and site if not already provided:

```
What is your DA org name and site (repo) name?
Example: org = "my-org", site = "my-site"
```

Store these as session variables `EW_ORG` and `EW_SITE` for use by other skills.

## Step 3 — Authenticate with DA

Check for a cached token at `~/.aem/da-token.json`. If `expires_at` is more than 60 seconds in the future, load `access_token` as `DA_TOKEN` and skip to Step 4.

Otherwise, obtain a fresh token by running this command with the Bash tool:

```bash
npx github:adobe-rnd/da-auth-helper token
```

This opens a browser window for Adobe IMS login. Once complete, the token is written to `~/.aem/da-token.json`. Load `access_token` from that file as `DA_TOKEN`.

Do not ask the user to run this command themselves — run it directly using the Bash tool.

If the Bash tool fails or the browser cannot be opened, offer these fallbacks:
- **MCP:** Use the DA MCP server authentication tool if configured.
- **Manual:** Ask the user to paste a token copied from da.live DevTools Network inspection.

## Step 4 — Verify the token

```bash
curl -s -o /dev/null -w "%{http_code}" \
  -H "Authorization: Bearer $DA_TOKEN" \
  "https://admin.da.live/list/$EW_ORG/$EW_SITE"
```

Expect HTTP 200. If 401, refresh the token (repeat Step 3). If 403, the user lacks DA permissions for this org/site — tell them and stop.

## Step 5 — Confirm and teach handoff URLs

Tell the user initialization is complete and show the active context:

```
Experience Workspace initialized.
Org: <EW_ORG>  Site: <EW_SITE>

DA live links:
  File:   https://da.live/canvas#/<EW_ORG>/<EW_SITE>/<path>
  Folder: https://da.live/browse#/<EW_ORG>/<EW_SITE>/<path>
```

For the rest of this session, whenever you return a result involving a DA path, include the appropriate link:
- For a file path → `https://da.live/canvas#/<EW_ORG>/<EW_SITE>/<path>`
- For a folder path → `https://da.live/browse#/<EW_ORG>/<EW_SITE>/<path>`

## Prerequisites

- Node.js 18+
- Browser access (for IMS login)
- Network access to `admin.da.live` and `ims-na1.adobelogin.com`
