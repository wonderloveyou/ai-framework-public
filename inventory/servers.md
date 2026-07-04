# Servers Inventory

Last updated: 2026-06-25

## Server A - AI Tools

| Field | Value |
|---|---|
| IP | `{SERVER_A_IP}` |
| Role | AI tools / control plane |
| OS | Ubuntu 24.04 |
| RAM | 12GB |
| Access | SSH + WireGuard |
| SSH note | Use `IdentitiesOnly=yes` with the explicit Ed25519 key |

Services verified running on 2026-06-25:

| Service | Status | Notes |
|---|---|---|
| `hermes_agent` | running | Telegram gateway, direct DeepSeek through OpenAI-compatible provider |
| `uptime-kuma` | running, healthy | Exposed on `3001` |
| `n8n` | running | Exposed on `5678` |
| `rsshub` | running | Internal container port `1200` |
| `postgres_newzbot` | running, healthy | PostgreSQL container |

Important paths:

| Path | Purpose |
|---|---|
| `/opt/hermes/compose.yaml` | Hermes Docker Compose |
| `/opt/hermes/.env` | Hermes environment variables, not in git |
| `/root/.hermes/config.yaml` | Hermes runtime config |
| `/root/.hermes/soul.md` | Hermes personality/instructions |
| `/root/.config/opencode/opencode.json` | OpenCode config |
| `/root/.opencode-secrets` | OpenCode/API environment variables, not in git |
| `/root/.codex/config.toml` | Codex config |
| `/home/ubuntu/framework` | Server clone of this framework repo |

## Server B - Projects

| Field | Value |
|---|---|
| IP | `{SERVER_B_IP}` |
| Role | Projects / bots / databases |
| RAM | 1.9GB |
| Access | SSH |

Known workloads from handoff:

- Telegram bots: `smm4love`, `cifra`, `svxbot`, `dspwbot`, `wdrlvtgbot`
- `skachivalka` FastAPI app
- MySQL
- `guardian-ai`

Status: pending detailed inventory and backup plan.

## Server C - VPN And Monitoring

| Field | Value |
|---|---|
| IP | `{MONITOR_IP}` |
| Role | VPN + monitoring |
| Access | WireGuard |

Known workloads:

- AWG/Xray
- WireGuard/Amnezia connectivity

Status: pending detailed inventory.

