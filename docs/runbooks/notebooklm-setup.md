# NotebookLM as Agent Memory / RAG

Connect NotebookLM to AI agents (Codex, OpenCode, Hermes, n8n) as external knowledge base.

## Architecture

```
Agent → MCP/stdio (local) → NotebookLM (separate Google account)
```

Or for multi-client access:

```
n8n/Telegram/API → HTTP → NotebookLM MCP → NotebookLM
```

HTTP mode only on `127.0.0.1`, Tailscale, SSH tunnel, or private LAN.

## Options

### 1. `jacob-bd/notebooklm-mcp-cli` (recommended)

CLI + MCP server. Python, installed via `uv`/`pip`. Supports Codex, OpenCode, Claude Code.

```bash
uv tool install notebooklm-mcp-cli
nlm login
```

For Codex (`~/.codex/config.toml`):
```toml
[mcp_servers.notebooklm]
command = "uvx"
args = ["--from", "notebooklm-mcp-cli", "notebooklm-mcp"]
enabled = true
default_tools_approval_mode = "prompt"
```

### 2. `PleasePrompto/notebooklm-mcp`

Node.js + Chrome automation. Supports stdio and Streamable HTTP.

```bash
# Codex
codex mcp add notebooklm npx notebooklm-mcp@latest

# HTTP mode (localhost only)
npx notebooklm-mcp@latest --transport http --port 3000
```

### 3. `teng-lin/notebooklm-py`

Python API/CLI/MCP/REST. Built for persistent agent memory and RAG.

### 4. REST wrappers

- `gnh1201/notebooklm-rest-api` — FastAPI wrapper over notebooklm-py
- `roomi-fields/notebooklm-mcp` — 33 HTTP endpoints, n8n/Zapier/curl compatible

## Security Rules

- **Separate Google account** — never use your primary account
- **No public HTTP** — never bind to `0.0.0.0` without trusted network
- **HTTP only via** `127.0.0.1`, Tailscale, SSH tunnel, or private LAN
- **Do not share** sources as Editor from primary Drive
- All tools use undocumented internal API — expect breakage

## References

- https://github.com/jacob-bd/notebooklm-mcp-cli
- https://github.com/PleasePrompto/notebooklm-mcp
- https://github.com/teng-lin/notebooklm-py
- https://github.com/gnh1201/notebooklm-rest-api
- https://github.com/roomi-fields/notebooklm-mcp
- https://github.com/Pantheon-Security/notebooklm-mcp-secure
