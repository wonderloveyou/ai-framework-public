# Linux Setup — AI Framework Server

> Open this file ONLY when working on Linux VPS.
> Windows and macOS agents: skip this file.

## Paths

| Config | Linux path |
|--------|-----------|
| OpenCode | `~/.config/opencode/opencode.json` |
| Codex | `~/.codex/config.toml` |
| Hermes | `~/.hermes/config.yaml` |
| n8n | `/opt/n8n/` (Docker volume) |
| SSH keys | `~/.ssh/id_ed25519_*` |
| Workspace | `/home/ubuntu/workspace/` |

## Required packages
```bash
sudo apt update && sudo apt install -y \
  curl wget git build-essential \
  python3.11 python3.11-venv python3-pip \
  nodejs npm \
  ufw fail2ban \
  htop tmux jq
```

## Docker
```bash
curl -fsSL https://get.docker.com | bash
sudo usermod -aG docker $USER
sudo systemctl enable docker
```

## Node.js 20+
```bash
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs
```

## Security
```bash
# Firewall
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw allow <VPN_PORT>/udp
sudo ufw enable

# fail2ban
sudo systemctl enable fail2ban
```

## systemd example (Hermes)
```ini
[Unit]
Description=Hermes Agent
After=docker.service
Requires=docker.service

[Service]
Type=simple
WorkingDirectory=/opt/hermes
ExecStart=/usr/bin/docker compose up
ExecStop=/usr/bin/docker compose down
Restart=always
RestartSec=10
EnvironmentFile=/opt/hermes/.env

[Install]
WantedBy=multi-user.target
```
