# HTMLRadar for Claude, Cursor and any MCP client

HTMLRadar turns a web-page document into a link you control: only the people you choose can open it, you see who read it and which sections they stayed on, and it stays current on the same link.

This repository holds what you need to connect HTMLRadar to your agent: the Claude Code plugin, a skill that teaches your agent when to offer a tracked link, and the description of the connector for the MCP Registry. HTMLRadar itself is a hosted service at [htmlradar.com](https://htmlradar.com).

Your agent publishes the page, checks who read it and updates it, while you decide who gets in. Readers need no account and nothing to install.

## Install

Every client below connects to the same server, `https://mcp.htmlradar.com/mcp`. The first time your agent uses it, a browser page opens: you sign in to HTMLRadar (with Google, Microsoft or your e-mail address) and choose read-only or read-and-publish. There is no key to copy. You can end a connection at any time under **Connected apps** in [Settings](https://htmlradar.com/settings).

### Claude Code

```
/plugin marketplace add htmlradar/htmlradar-mcp
/plugin install htmlradar@htmlradar
```

Then run `/mcp`, choose **htmlradar** and sign in.

Prefer not to use the plugin? This adds the same tools on their own:

```bash
claude mcp add --transport http --scope user htmlradar https://mcp.htmlradar.com/mcp
```

### Claude Desktop and claude.ai

Open Settings, then Connectors, then **Add custom connector**, and paste:

```
https://mcp.htmlradar.com/mcp
```

The first time Claude reaches for a tool, it shows a Connect card that opens the sign-in.

On Claude Team and Enterprise, an Owner adds the connector for the organization first; see [htmlradar.com/mcp](https://htmlradar.com/mcp).

### Cursor

Put this in `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "htmlradar": { "url": "https://mcp.htmlradar.com/mcp" }
  }
}
```

Then log in to HTMLRadar from Cursor's MCP settings. From the command line, `cursor-agent mcp login htmlradar` starts the same sign-in.

### Any other MCP client

- **Remote, with sign-in:** if your client supports remote MCP servers with OAuth sign-in, point it at `https://mcp.htmlradar.com/mcp`. The server publishes standard OAuth discovery and accepts both client metadata documents and client registration.
- **Package, with an API key:** for a client without OAuth sign-in, create an API key at [htmlradar.com/settings](https://htmlradar.com/settings) under **API keys** and run `npx -y htmlradar-mcp` (Node.js 20 or newer) with `HTMLRADAR_API_KEY` set in its environment. The package is [`htmlradar-mcp`](https://www.npmjs.com/package/htmlradar-mcp) on npm. In Cursor, for example:

  ```json
  {
    "mcpServers": {
      "htmlradar": {
        "command": "npx",
        "args": ["-y", "htmlradar-mcp"],
        "env": { "HTMLRADAR_API_KEY": "${env:HTMLRADAR_API_KEY}" }
      }
    }
  }
  ```

The server is listed in the MCP Registry as `com.htmlradar/share`; [`server.json`](./server.json) here is its description.

## The 16 tools

Every tool is visible to every connection. A read-only connection can use the reading tools; the others need read-and-publish.

| Tool | What it does | Changes anything |
| --- | --- | --- |
| `share_html` | Publishes an HTML page as a new tracked link and returns the link for the reader and a dashboard address for you. | Yes |
| `create_share` | Makes another tracked link for a document already on HTMLRadar, so each recipient has their own. | Yes |
| `list_shares` | Lists your tracked links, newest first, with the ids the other tools take. | No |
| `list_documents` | Lists your documents, including ones never sent. | No |
| `get_share_activity` | Reports who opened a link, how long they read, how far they scrolled and which sections held them. | No |
| `get_reading_records` | Returns the reading records, one row per section per read, as CSV text. | No |
| `get_share` | Reads a link's settings: who can open it, the e-mail gate, the end date and the copy lock. | No |
| `update_share` | Changes those settings on the same link. | Yes, ask first |
| `revoke_share` | Switches a link off, or back on. | Yes, ask first |
| `get_document` | Reads a document's current contents so they can be changed. | No |
| `replace_document` | Puts new contents behind links already sent, on the same addresses. | Yes, ask first |
| `list_attachments` | Lists the files attached to a document. | No |
| `add_attachment` | Attaches a file for readers to download beside the page. | Yes |
| `remove_attachment` | Removes an attached file. | Yes, ask first |
| `whoami` | Shows which account the connection belongs to, its plan and how many free tracked links are used. | No |
| `get_page_guidelines` | Returns HTMLRadar's guidelines for pages that read well on phones and give accurate section reading. | No |

"Ask first" marks the tools that change what someone who already holds a link sees. The `share-html` skill in the plugin tells your agent to name the recipient and the document and get a yes before using them.

## Plans

Free accounts can send two tracked links. Plans and prices are at [htmlradar.com/pricing](https://htmlradar.com/pricing).

## Support

Write to [hello@htmlradar.com](mailto:hello@htmlradar.com).

## What is in this repository

| Path | What it is |
| --- | --- |
| `.claude-plugin/marketplace.json` | The Claude Code marketplace that makes the install commands above work. |
| `plugins/htmlradar/` | The plugin: its manifest, `.mcp.json` and the `share-html` skill. |
| `server.json` | The connector's description for the MCP Registry. |
| `LICENSE` | Copyright notice. |

## Licence

Copyright 2026 HTMLRadar. All rights reserved. The files here are provided to install the HTMLRadar plugin and connector.
