# Runbook: Codex Server Verification

Last updated: 2026-06-25

Server A: `{SERVER_A_IP}`

## SSH

```powershell
ssh -o IdentitiesOnly=yes -i {USER_HOME}\.ssh\id_ed25519 root@{SERVER_A_IP}
```

## Login

For remote VPS login, use device auth. Browser `localhost` callback is not suitable because it opens on the local Windows machine, not on the VPS.

```bash
cd /home/ubuntu/framework
codex login --device-auth
codex login status
```

Expected status:

```text
Logged in using ChatGPT
```

## Basic Verification

```bash
cd /home/ubuntu/framework
codex --version
codex doctor
codex mcp list
```

Verified on 2026-06-25:

- Codex CLI: `0.142.2`
- Auth: ChatGPT auth configured
- Doctor: `17 ok`, `0 warn`, `0 fail`
- Websocket: connected
- Config parse: ok
- Repo root detected: `/home/ubuntu/framework`
- Project trust: `/home/ubuntu/framework` trusted
- MCP servers enabled:
  - `context7`
  - `filesystem`
  - `memory`
  - `playwright`
  - `sequential-thinking`

## Non-Interactive Smoke Test

When running `codex exec` over SSH from PowerShell, close stdin with `</dev/null`. Without this, Codex can print `Reading additional input from stdin...` and wait for more input.

```bash
cd /home/ubuntu/framework
codex exec -s read-only --ephemeral 'Reply with OK only.' </dev/null
```

Expected final response:

```text
OK
```

Verified on 2026-06-25 after updating Codex to `0.142.2` and installing `bubblewrap`.

## Process Cleanup If A Test Hangs

If a smoke test hangs:

```bash
pkill -f 'codex exec' || true
ps aux | grep '[c]odex' || true
```

## Maintenance

Update Codex:

```bash
npm install -g @openai/codex
```

Install sandbox prerequisite:

```bash
apt-get update
apt-get install -y bubblewrap
```

