# APN ERP

A Claude Code plugin that connects Claude to the APN ERP system through an MCP server. Ask Claude to look up invoice line items and sales orders by customer, then summarize, compare, or reconcile them in conversation.

## Contents

- [Overview](#overview)
- [Features](#features)
- [Requirements](#requirements)
- [Installation](#installation)
- [Verify the connection](#verify-the-connection)
- [Usage](#usage)
- [Available tools](#available-tools)
- [Configuration](#configuration)
- [Security and privacy](#security-and-privacy)
- [Troubleshooting](#troubleshooting)
- [Updating](#updating)
- [For maintainers](#for-maintainers)
- [Support](#support)
- [License](#license)

## Overview

APN ERP bundles a remote MCP (Model Context Protocol) server connection into a single installable plugin. Once it is installed and enabled, Claude can call the server's tools to read data from APN ERP and use it to answer questions, build summaries, and draft follow-ups.

The plugin does not store any data itself. It contains only the connection settings for the MCP server.

## Features

- Look up **invoice line items** for a customer.
- Look up **sales orders** for a customer.
- Combine the results with Claude's analysis: totals, comparisons, reconciliation, and written summaries.
- Install and update with two commands.

## Requirements

- [Claude Code](https://code.claude.com), a recent version.
- Network access to the APN ERP MCP server.
- Valid access to the APN ERP system, if the server requires authentication.

## Installation

Run these inside a Claude Code session:

```
/plugin marketplace add APNDEV-dev/APNERP-plugin
/plugin install apn-erp@apn-erp-marketplace
```

Or from your regular terminal:

```
claude plugin marketplace add APNDEV-dev/APNERP-plugin
claude plugin install apn-erp@apn-erp-marketplace
```

If you are not sure of the names, run `/plugin` to see what is available.

### Try it without installing

To load the plugin for a single session from a local copy:

```
claude --plugin-dir ./apn-erp
```

## Verify the connection

1. Run `/plugin` and confirm `apn-erp` is enabled.
2. Run `/mcp` and confirm the `apn-erp` server shows as connected.
3. If the server asks you to log in, authenticate from the `/mcp` screen.
4. Ask Claude a simple question, such as the example prompts below, and watch for a tool call to the server.

## Usage

Replace `[customer]` with a real customer name.

### Finance

- "Use APN ERP to show the invoice line items for [customer] and total them by product."
- "Compare [customer]'s sales orders with their invoices in APN ERP and flag anything not yet billed."
- "List all invoice lines for [customer] from APN ERP and highlight unusual amounts."

### Sales and account management

- "Summarize [customer]'s order history from APN ERP before my call tomorrow."
- "What has [customer] ordered recently in APN ERP, and what is still open?"
- "Draft a follow-up email based on [customer]'s open sales orders in APN ERP."

### Customer support

- "A customer says they were overcharged. Pull their invoice lines from APN ERP so we can check."
- "Find the sales order behind this invoice in APN ERP and explain what was billed."

### Management and reporting

- "Compare invoice totals in APN ERP for [customer A] and [customer B]."
- "Summarize [customer]'s billing activity in APN ERP for the quarter."

## Available tools

| Tool | What it does |
| --- | --- |
| `list-apn-invoices` | Reads APN invoice line items by customer. |
| `list-apn-sales-orders` | Reads APN sales orders by customer. |

Both tools are read-only. They look up data by customer, so questions that span all customers at once may not be supported. The exact fields returned depend on the server.

## Configuration

The plugin's connection settings live in `.mcp.json` at the plugin root:

```json
{
  "mcpServers": {
    "apn-erp": {
      "type": "http",
      "url": "https://86.77.148.132.host.secureserver.net/mcp"
    }
  }
}
```

### Server uses SSE instead of HTTP

Change `"type": "http"` to `"type": "sse"`.

### Server requires a token

Add a `headers` block. Do not commit real tokens to a public repository.

```json
"headers": { "Authorization": "Bearer YOUR_TOKEN" }
```

### Server uses OAuth

Install the plugin, then run `/mcp` and follow the login prompts.

## Security and privacy

- This plugin gives Claude access to business data, including invoices and sales orders. Only install it if you are authorized to view that data.
- Anyone who can read this repository can see the server URL in `.mcp.json`. Access to the data must be enforced by the server itself, for example with authentication, and not by keeping the URL secret.
- Data returned by the server becomes part of your Claude conversation. Follow your organization's policy on sharing customer and financial data with AI tools.
- Never commit API keys, tokens, or passwords to this repository.
- Only connect to servers you control or trust.

## Troubleshooting

| Problem | What to try |
| --- | --- |
| `Plugin not found` or `marketplace not found` | Run `/plugin` and check the exact names. They must match the `name` values in `.claude-plugin/marketplace.json` and `plugin.json`. |
| Server shows as failed in `/mcp` | Change `type` between `http` and `sse` in `.mcp.json`, then run `/reload-plugins`. |
| Server unreachable | Test the URL from your machine with `curl -i <server-url>`. Any HTTP response, even 401 or 405, means the server is reachable. |
| 401 or 403 errors | The server requires credentials. Authenticate from `/mcp` or add a `headers` block. |
| TLS or certificate errors | Check that the server's certificate is valid for its hostname. |
| Tools missing from Claude | Confirm the plugin is enabled in `/plugin`, then run `/reload-plugins`. |
| Marketplace add fails on a private repo | Make sure your machine has git credentials for the repository. |

To test the server independently of the plugin, use the MCP Inspector:

```
npx @modelcontextprotocol/inspector
```

## Updating

```
claude plugin update apn-erp@apn-erp-marketplace
```

Auto-update can be turned on per marketplace in Claude Code's plugin settings.

## For maintainers

### Layout

```
apn-erp/
├── .claude-plugin/
│   ├── plugin.json
│   └── marketplace.json
├── .mcp.json
├── README.md
└── .gitignore
```

### Validate before release

```
claude plugin validate --strict .
```

### Release a new version

1. Make your changes.
2. Increase `version` in `.claude-plugin/plugin.json`.
3. Push to `main`.

Do not rename the plugin after publishing. A renamed plugin counts as a different plugin, and existing installs will break.

## Support

For problems with this plugin, open an issue at https://github.com/APNDEV-dev/APNERP-plugin/issues.

## License

Add your license here, for example MIT, or state that the plugin is proprietary.
