# Runbook: Server A Backup And Restore

Last updated: 2026-06-25

Server A: `{SERVER_A_IP}`

## Goal

Back up enough state to rebuild the AI tools server without committing secrets or large caches to git.

## Back Up

Create encrypted/off-server backups for:

| Path | Purpose | Include |
|---|---|---|
| `/opt/hermes/` | Hermes compose, env, runtime data | compose, `.env`, relevant data/logs |
| `/root/.hermes/` | Hermes config, soul, sessions | config, `soul.md`, `user.md`, sessions if needed |
| `/root/.config/opencode/` | OpenCode config | config files only |
| `/root/.codex/` | Codex config/auth | config and auth state |
| `/root/.opencode-secrets` | API env vars | encrypted backup only |
| `/home/ubuntu/framework` | framework clone | prefer git remote as source of truth |
| n8n volume/data | workflows and credentials DB | encrypted backup only |
| PostgreSQL data | app database | use logical dump when possible |

Exclude:

- `node_modules/`
- package caches
- temporary logs unless needed for incident analysis
- Docker image layers
- unencrypted secret archives

## Minimum Manual Snapshot

Run over SSH as root:

```bash
mkdir -p /root/backups/manual
tar -czf /root/backups/manual/hermes-config-$(date +%Y%m%d-%H%M%S).tgz /root/.hermes /opt/hermes
tar -czf /root/backups/manual/ai-tool-configs-$(date +%Y%m%d-%H%M%S).tgz /root/.config/opencode /root/.codex /root/.opencode-secrets
```

Then move the archives to an encrypted off-server location. Do not commit them to git.

## n8n

Before relying on n8n backups, verify where the active n8n data volume is mounted.

Preferred:

1. Stop n8n or put it into a quiet period.
2. Back up the n8n volume/data directory.
3. Store encrypted off-server.
4. Test restore into a temporary container before deleting old data.

## PostgreSQL

Prefer logical dumps for PostgreSQL containers:

```bash
docker exec postgres_newzbot pg_dumpall -U postgres > /root/backups/manual/postgres-$(date +%Y%m%d-%H%M%S).sql
```

Adjust the database user if the container does not use `postgres`.

## Restore Drill

On a clean server:

1. Install Docker, Node.js, git, and base packages.
2. Clone `github.com/wonderloveyou/ai-framework`.
3. Restore `/opt/hermes/`, `/root/.hermes/`, `/root/.config/opencode/`, `/root/.codex/`.
4. Restore secret files with `chmod 600`.
5. Start Hermes and verify:

```bash
docker restart hermes_agent
docker exec hermes_agent hermes -z 'Reply with OK only.' --cli -t ''
```

6. Restore n8n and PostgreSQL.
7. Verify external ports and firewall rules.

## To Do

- Decide encrypted backup destination.
- Automate daily config backup and weekly full backup.
- Add restore test schedule.
- Document exact n8n volume path.

