# MCP Servers Reference

Cleaned template — no API keys, no IPs. Fill `{env:VAR}` from your `.env` or secrets file.

## Local MCP Servers

| Server | Package | Purpose | Config |
|--------|---------|---------|--------|
| **filesystem** | `@modelcontextprotocol/server-filesystem` | Read/write project files | Directories: workspace, vault, skills |
| **playwright** | `@playwright/mcp` | Browser automation (scraping, testing) | No auth needed |
| **sequential-thinking** | `@modelcontextprotocol/server-sequential-thinking` | Chain-of-thought reasoning | No auth needed |
| **firecrawl** | `firecrawl-mcp` | Web scraping + search | `FIRECRAWL_API_KEY={env:FIRECRAWL_API_KEY}` |
| **memory** | `@modelcontextprotocol/server-memory` | Knowledge graph persistence | `MEMORY_FILE_PATH={env:MEMORY_FILE_PATH}` |
| **github** | `@modelcontextprotocol/server-github` | GitHub API (PRs, issues, commits) | `GITHUB_PERSONAL_ACCESS_TOKEN={env:GITHUB_TOKEN}` |
| **perplexity** | `@perplexity-ai/mcp-server` | Web-grounded search & research | `PERPLEXITY_API_KEY={env:PERPLEXITY_API_KEY}` |
| **prod-ssh** | `ssh-mcp-server` | SSH to production servers | `SSH_PRIVATE_KEY={env:SSH_KEY_PATH}` |
| **obsidian** | `second-brain-lite-mcp` | Read/write Obsidian vault | Vault path + OBSIDIAN_API_KEY |
| **chrome-devtools** | `chrome-devtools-mcp` | Chrome DevTools protocol | No auth needed |
| **vision** | `openrouter-image-mcp` | Image analysis via vision models | `OPENROUTER_API_KEY={env:OPENROUTER_API_KEY}` |

## Remote MCP Servers

| Server | URL | Auth | Purpose |
|--------|-----|------|---------|
| **context7** | `https://mcp.context7.com/mcp` | Bearer token | Library/framework docs |
| **supabase** | `https://mcp.supabase.com/mcp?project_ref={REF}` | Bearer token | Database management |

## MCP Profiles

Group MCP servers by workflow to minimize risk:

| Profile | Servers | When |
|---------|---------|------|
| **research** | context7, perplexity, firecrawl, obsidian, memory | Always on |
| **coding** | + filesystem, github, sequential-thinking | Code work |
| **deploy-readonly** | + ssh (read-only commands) | Deploy checks |
| **deploy-write** | + ssh (restart, deploy), playwright | Explicit, with approval |
