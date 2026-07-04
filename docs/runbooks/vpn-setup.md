# VPN Setup — Xray + AmneziaWG

Self-hosted VPN with Xray (VLESS Reality) and AmneziaWG 2.0 on Ubuntu 24.04.

## Architecture

Two VPS servers:

| Server | Role | Tech |
|--------|------|------|
| `{SERVER_C_IP}` | Main VPN + Monitoring | Xray VLESS + AWG |
| `{SERVER_B_IP}` | Secondary VPN | AWG |

## AmneziaWG 2.0 Setup

### Server-side (serv.host Finland)

```bash
# Install
AUTO_INSTALL=y bash /tmp/awg-install.sh

# Status
systemctl status awg-quick@awg0
awg show awg0

# Add client
AUTO_INSTALL=y bash /tmp/awg-install.sh --add-client <name>
```

Network: `10.77.77.0/24`, IPv6 `fd42:42:43::/64`

### Clients

Each device gets a unique IP from the subnet:

| Device | Example IP |
|--------|-----------|
| PC desktop | `10.77.77.3` |
| Phone | `10.77.77.4` |
| Tablet | `10.77.77.5` |
| MacBook | `10.77.77.6` |

## Xray Reality (VLESS)

```bash
# Status
systemctl status xray

# Client SOCKS proxy
127.0.0.1:1080
```

Xray runs alongside AWG for split-tunneling fallback.

## Split Tunneling

Route only specific traffic through VPN:
- AI tool traffic (Hermes, OpenCode API calls)
- Server management (SSH)
- All other traffic goes direct

## Management

```bash
# AWG
systemctl status awg-quick@awg0
awg show awg0

# Xray
systemctl status xray

# Add more AWG clients
AUTO_INSTALL=y bash /tmp/awg-install.sh --add-client <name>
```

## Config Files

Server and client configs contain private keys — never commit them to git.
Store securely and generate fresh configs for each new device.

## Security Notes

- Change default ports from stock configs
- Use different keys for each server
- AWG with QUIC signatures enabled (i1/i2) for obfuscation
- Xray with `xtls-rprx-vision` flow for anti-detection
