# NotebookLM as Agent Memory / RAG

Connect NotebookLM to AI agents (Codex, OpenCode, Hermes, n8n) as external knowledge base.

All tools use **undocumented internal Google APIs** — no official consumer API exists. Use at your own risk with a **separate Google account**.

## Architecture

```
Agent → MCP/stdio (local) → NotebookLM (separate Google account)
```

For multi-client access (n8n/Telegram/API):

```
n8n/Telegram/API → HTTP (127.0.0.1 only) → NotebookLM MCP → NotebookLM
```

HTTP mode only on `127.0.0.1`, Tailscale, SSH tunnel, or private LAN.

## Comparison of All Methods

| Tool | Approach | Auth | Transport | Ban Risk | Reliability | Safety | Best For |
|------|----------|------|-----------|----------|-------------|--------|----------|
| **jacob-bd/notebooklm-mcp-cli** | Reverse-engineered RPC + cookie extraction | Cookie (`nlm login`) | stdio MCP | **Medium** — direct API calls detectable by rate patterns | **Medium** — undocumented API breaks often, dev fixes fast | **Medium** — cookies stored locally, session theft risk | Personal CLI, Codex/OpenCode integration |
| **PleasePrompto/notebooklm-mcp** | Chrome automation (Patchright) | Browser session | stdio MCP + Streamable HTTP | **Low-Medium** — browser automation looks human, but detectable | **High** — simulates real user, survives UI changes better | **Medium** — Chrome profile needed, session in browser | Stable daily use, HTTP mode for remote |
| **teng-lin/notebooklm-py** | Playwright browser automation | Browser cookie | CLI + Python API + MCP + REST | **Low-Medium** — realistic typing/mouse patterns | **Medium-High** — mature, active maintenance | **Medium** — passwords not stored (cookie auth) | Python pipelines, Claude Code Skills |
| **gnh1201/notebooklm-rest-api** | FastAPI wrapper over notebooklm-py | Browser cookie | REST API | **Same as notebooklm-py** | **Medium** — wrapper, inherits upstream stability | **Medium** — adds HTTP attack surface | REST API needed, lightweight |
| **roomi-fields/notebooklm-mcp** | Chrome automation (Patchright) + Node.js | Browser session + auto-reauth | MCP + REST (33 endpoints) + Docker | **Low-Medium** — humanization features, multi-account rotation | **High** — production-grade, batch-tested 1000+ questions, auto-reauth | **Medium-High** — most secure features (session rotation, TOTP), but HTTP surface | n8n workflows, batch processing, production |
| **Pantheon-Security/notebooklm-mcp-secure** | Chrome automation + security focus | Browser session | MCP | **Low** — designed for security | **Medium** — smaller community, less battle-tested | **High** — security audit, no API key storage | Security-conscious users |

## Ban Risk Assessment

**No confirmed bans reported** from any of these tools as of July 2026. However:

- **Low risk** — browser automation (PleasePrompto, teng-lin, roomi-fields): simulates real user interactions including typing speed variations and mouse movements. Harder to detect as bot.
- **Medium risk** — direct RPC/API calls (jacob-bd): reverse-engineered internal API calls without browser. Rate patterns may trigger Google's automated access detection.
- **Risk factors:** request frequency, number of accounts, session reuse patterns, source of IP traffic.
- **Google TOS:** prohibits automated access. No bans reported, but risk exists.
- **Mitigation:** separate Google account, reasonable request intervals, avoid 0.0.0.0 binding.

## Detailed Options

### 1. `jacob-bd/notebooklm-mcp-cli` — Lightest, most direct

CLI + MCP server. Python via `uv`/`pip`. Directly calls NotebookLM's undocumented API.

```bash
uv tool install notebooklm-mcp-cli
nlm login
```

Codex config (`~/.codex/config.toml`):
```toml
[mcp_servers.notebooklm]
command = "uvx"
args = ["--from", "notebooklm-mcp-cli", "notebooklm-mcp"]
```

**Pros:** Lightweight, simple, good for personal agent integration.
**Cons:** Direct API calls — highest detection risk, breaks when Google changes API.

### 2. `PleasePrompto/notebooklm-mcp` — Stable MCP + HTTP

Node.js + Patchright (Chrome automation). Supports stdio and Streamable HTTP. Good balance.

```bash
codex mcp add notebooklm npx notebooklm-mcp@latest
# HTTP mode (localhost only)
npx notebooklm-mcp@latest --transport http --port 3000
```

**Pros:** Browser automation = harder to detect, supports HTTP, active community.
**Cons:** Requires Chrome/Chromium, heavier than CLI-only.

### 3. `teng-lin/notebooklm-py` — Python native

Python CLI/API/MCP/REST. Playwright-based browser automation with realistic patterns.

```bash
pip install notebooklm-py
pip install "notebooklm-py[browser]"
playwright install chromium
notebooklm login
```

**Pros:** Full NotebookLM feature access (flashcards, mind maps, reports, audio, sources), Python ecosystem.
**Cons:** Requires Playwright + Chromium, heavier setup.

### 4. `roomi-fields/notebooklm-mcp` — Most feature-rich

Production-grade. 33 REST API endpoints + MCP + Docker. Auto-reauth, multi-account rotation, batch processing.

```bash
git clone https://github.com/roomi-fields/notebooklm-mcp.git
cd notebooklm-mcp && npm install && npm run build && npm run setup-auth
npm run start:http  # REST API on :3000
```

**Pros:** Most complete feature set (Q&A, audio/video/infographic generation, batch, RTFM vault), best for n8n/automation, auto-reauth, tested on 1000+ overnight queries.
**Cons:** Most complex setup, Node.js + Chrome required.

### 5. `Pantheon-Security/notebooklm-mcp-secure`

Security-focused fork, no API key storage, built-in audit.

**Pros:** Security features, audit trail.
**Cons:** Smaller community, fewer features.

## Recommendation

| Use Case | Recommended |
|----------|------------|
| **Personal agent integration** (Codex/OpenCode) | `jacob-bd/notebooklm-mcp-cli` |
| **Stable daily use + HTTP** | `PleasePrompto/notebooklm-mcp` |
| **Python pipeline / Claude Code Skills** | `teng-lin/notebooklm-py` |
| **n8n automation / batch / production** | `roomi-fields/notebooklm-mcp` |
| **Security-first** | `Pantheon-Security/notebooklm-mcp-secure` |

## Notebook Migration Between Accounts

Essential for disaster recovery: if your agent account gets flagged or you want to rotate, migrate notebooks to a fresh account.

### Method 1: Share via NotebookLM UI (Simplest)

NotebookLM has native sharing. Open the notebook → Share → add target account email. Target account gets Editor access. Works cross-account.

**Limitation:** sources stay linked to original account's Drive. Target can view/chat but may lose access if original account is gone.

### Method 2: Source Export + Re-import (Most Reliable)

Extract source list from old account, upload sources to new account.

Using `roomi-fields/notebooklm-mcp`:
```bash
# On old account — list sources per notebook
curl http://localhost:3000/notebook/{id}/sources

# Create notebook on new account
curl -X POST http://localhost:3000/notebook/create \
  -H 'Content-Type: application/json' \
  -d '{"name": "Migrated Notebook"}'

# Add each source to new account (URLs, PDFs, YouTube)
curl -X POST http://localhost:3000/source/add \
  -H 'Content-Type: application/json' \
  -d '{"notebook_id": "new-id", "url": "https://..."}'
```

Using `teng-lin/notebooklm-py`:
```bash
# On old account
notebooklm list
notebooklm source list --notebook <id>
notebooklm download report --notebook <id>  # Download generated content

# On new account
notebooklm create "Migrated Notebook"
notebooklm source add <file.pdf> --notebook <id>
```

### Method 3: Web Research + Re-import (No API)

1. On old account: open each notebook → manually note the sources (URLs, file names)
2. Share notebook to new account via UI (gives temporary access)
3. On new account: re-add sources from original URLs/files
4. Delete shared access on old account when done

### Method 4: Full Programmatic Migration (roomi-fields)

Use auto-discovery to dump all notebook metadata, then recreate on target:

```bash
# Dump all notebooks with metadata
curl http://localhost:3000/notebook/discover

# Batch recreate on new account (switch GOOGLE_ACCOUNT env)
# Requires separate MCP server instance per account
```

### Migration Checklist

- [ ] Create fresh Google account for target
- [ ] Set up NotebookLM MCP on target account
- [ ] Verify target account has NotebookLM access
- [ ] Export source list from old account
- [ ] Re-add all sources to new account
- [ ] Verify Q&A works on new account
- [ ] Keep old account active until migration is confirmed
- [ ] Delete old account's notebooks only after verification

**Recommendation:** run two MCP server instances (one per account) side-by-side during migration. `roomi-fields` supports this natively with multi-account rotation config.

## Security Rules

- **Separate Google account** — never use your primary account
- **No public HTTP** — never bind to `0.0.0.0` without trusted network
- **HTTP only via** `127.0.0.1`, Tailscale, SSH tunnel, or private LAN
- **Do not share** sources as Editor from primary Drive
- **Auto-reauth-aware tools** (roomi-fields) are safer for long-running sessions
- All tools use undocumented internal API — expect breakage

## References

- https://github.com/jacob-bd/notebooklm-mcp-cli
- https://github.com/PleasePrompto/notebooklm-mcp
- https://github.com/teng-lin/notebooklm-py
- https://github.com/gnh1201/notebooklm-rest-api
- https://github.com/roomi-fields/notebooklm-mcp
- https://github.com/Pantheon-Security/notebooklm-mcp-secure
