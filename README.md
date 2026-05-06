# Experience Workspace

A Claude Code plugin for headless content management on [Adobe Document Authoring (DA)](https://da.live). Read, write, list, and delete DA content pages from natural language — no browser required.

## Prerequisites

- [Claude Code](https://claude.ai/code) CLI installed
- Node.js 18+
- A DA org and site you have write access to

## Installation

```bash
claude plugin add github:hannessolo/ew-headless
```

## Skills

| Skill | Trigger |
|-------|---------|
| `init` | Start here — sets org/site context and authenticates with DA |
| `list-content` | Browse files and folders at any DA path |
| `read-page` | Fetch the HTML content of a page |
| `edit-page` | Update an existing page |
| `create-page` | Create a new page |
| `delete-page` | Delete a page or folder |

## Usage

Start every session with init:

```
/experience-workspace:init
```

You'll be prompted for your org and site name (e.g. `my-org` / `my-site`). Claude will authenticate via Adobe IMS and verify access. After that, use natural language or invoke skills directly:

```
/experience-workspace:list-content
/experience-workspace:read-page docs/getting-started
/experience-workspace:edit-page docs/getting-started
```

Authentication tokens are cached at `~/.aem/da-token.json` and reused across sessions until they expire.
