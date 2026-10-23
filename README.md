# Manusights MCP server

Manusights checks a manuscript before submission. This remote MCP server lets an AI assistant check
references for retractions and DOI mismatches, look up a journal's recorded AI-use rules, and start a
pre-submission preview of a manuscript.

- **Server URL:** `https://manusights.com/mcp` (streamable HTTP)
- **Registry name:** `com.manusights/manusights` in the [official MCP Registry](https://registry.modelcontextprotocol.io)
- **Sign-in:** OAuth with dynamic client registration. Reference checks and journal lookups work without
  an account; the review tools ask you to sign in the first time you use them.

This repository holds the server's public description and setup notes. The server itself runs at
manusights.com; there is no code to install.

## Tools

| Tool | What it does | Account |
| --- | --- | --- |
| `check_references` | Checks references against Crossref, the DOI registry and Retraction Watch. References without a DOI are also searched in Semantic Scholar, PubMed, arXiv, Open Library and CRAN. Reports retractions, corrections, expressions of concern, DOI mismatches and references with no matching record. | Optional |
| `get_journal_ai_policy` | Returns a journal's recorded AI-use and disclosure rules with the official policy link and the date they were checked. | Optional |
| `review_manuscript_text` | Starts a preview from the complete manuscript text: a readiness score, a verdict and the main risks a reviewer may raise. Figures are not reviewed from text. | Required |
| `review_manuscript` | Starts a preview from an attached file, in clients that can pass the file's download URL (ChatGPT). | Required |
| `get_manuscript_review` | Reads a preview or review on your account. | Required |
| `list_manuscript_reviews` | Lists your account's 20 most recent reviews. | Required |

Matching a record shows that a reference exists, not that it supports the claim it is cited for. A
reference with no matching record may be a typo or a gap in the registries, not an invented paper.

## Set up

**Claude** (claude.ai, desktop and mobile): open Customize, then Connectors, choose Add custom connector,
name it Manusights and enter `https://manusights.com/mcp`. Guide: https://manusights.com/claude

**ChatGPT:** install Manusights from the ChatGPT app directory. Guide: https://manusights.com/chatgpt

**Claude Code:**

```sh
claude mcp add --transport http manusights https://manusights.com/mcp
```

Then run `/mcp`, choose manusights and authenticate when you need a preview.

**Codex:**

```sh
codex mcp add manusights --url https://manusights.com/mcp
codex mcp login manusights
```

**Cursor:** add to `.cursor/mcp.json` in a project, or `~/.cursor/mcp.json` for every project:

```json
{ "mcpServers": { "manusights": { "url": "https://manusights.com/mcp" } } }
```

**VS Code:** add to `.vscode/mcp.json`:

```json
{ "servers": { "manusights": { "type": "http", "url": "https://manusights.com/mcp" } } }
```

Ask for Manusights by name, for example: "Use Manusights to check these references before I submit."

## Data

A reference check sends only the references to Manusights. A preview sends the manuscript text or file
you share and saves the preview to your Manusights account. Manusights does not write or rewrite
manuscripts. Privacy policy: https://manusights.com/privacy. Terms: https://manusights.com/terms.
Developer reference: https://manusights.com/developers/connector

## Support

team@manusights.com
