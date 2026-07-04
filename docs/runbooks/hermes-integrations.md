# Hermes Integrations Guide

Key integrations not covered by the CLI/Docker setup. Based on Hermes desktop app, but most also work on VPS.

## 1. Google Workspace (Gmail, Calendar, Drive, Docs, Sheets)

The most powerful integration — Hermes reads/writes your email, calendar, and documents.

### Setup (Google Cloud Console)

1. Open [Google Cloud Console](https://console.cloud.google.com)
2. **Create Project** → name it (e.g. `hermes-integration`)
3. **Enable APIs** one by one:
   - Gmail API
   - Google Calendar API
   - Google Drive API
   - Google Docs API
   - Google Sheets API
4. **OAuth Consent Screen** → External → fill app name `Hermes`, your email
5. **Create OAuth Client** → Desktop App → download `client_secret_*.json`
6. **Test Users** → add your email

### Auth in Hermes

```text
1. Drag the client_secret JSON file into Hermes chat
2. Say: "I'm setting up Google Workspace, here's the JSON"
3. Hermes will respond with an auth URL — click it
4. Google shows "unverified app" → Continue → grant permissions
5. You get a redirect to localhost — copy the full URL
6. Paste the URL into Hermes chat
```

### Test it

```text
Check my inbox — what action items do I have this week?
Schedule a meeting "Review Q3 plan" tomorrow at 2PM
Find and update my vacation doc — add a 3-day itinerary for Tokyo
```

### Security pattern

Give Hermes a separate Gmail account + read-only access to your primary inbox. Share only specific Google Drive folders it needs to write to.

---

## 2. Voice Replies (Telegram TTS)

### Enable

```text
Turn on voice replies
```

That's it — Hermes has the voice skill pre-installed. It uses Edge TTS by default.

### Switch voice

```text
What voice options are available?
Switch to Emma Neuro
```

300+ voices available (US, UK, Australian, etc.)

### Optimize

```text
Turn streaming off                        # wait for full response, not word-by-word
Stop showing me these system messages     # cleaner Telegram UX
From now on, only reply via voice         # voice-only mode
Switch back to text                       # revert
```

---

## 3. Cron Jobs / Routines

Three-step process: **build skill → test → cron it**.

### Weekend Planner

1. Build the skill:
```text
Create a weekend planner skill that searches for family-friendly activities
in the Bay Area, within 30-60min driving distance, with kids age 4 and 8.
```
2. Test:
```text
Run the weekend planner skill and show results
```
3. Schedule:
```text
Set up a cron job to send me this every Friday at 9 AM
```

### Morning Briefing

Depends on Google Workspace. Prompt:

```text
Set up a morning briefing routine:
- Review my calendar for today
- Check recent emails
- Send me a concise briefing: top 3 action items,
  upcoming meetings, open email threads needing response
Schedule it for every weekday at 7 AM
```

### Health Review

```text
Create a weekly health review that includes:
- My weight (from Withings API / Apple Health)
- Workouts I did this week
- Trends and recommendations
Send every Sunday at 6 PM
```

### Business Review

Advanced — integrates multiple APIs:
- Mercury (income/expenses)
- YouTube (video stats)
- Substack (subscriber stats)
- Granola (meeting notes)
- Google Docs (business plan)

Same process: build skill → test → cron.

### Cron Management

```text
Show my currently scheduled routines
Pause the weekend planner
Delete the morning briefing
Change the health review to every Saturday at 10 AM
```

---

## 4. Troubleshooting

```bash
# Auto-fix most issues
ermes doctor --fix

# Restart gateway (fixes Telegram/WhatsApp connection)
ermes gateway restart
```

If `ermes doctor` doesn't fix it — paste the error into Codex/Claude Code and ask it to debug your Hermes folder.

---

## 5. Other Integrations

| Service | How | Notes |
|---------|-----|-------|
| **WhatsApp** | Needs a second phone number (Google Voice or prepaid SIM) | Ask Hermes: "Walk me through WhatsApp setup" |
| **Slack** | Create Slack app, install, configure | Ask Hermes to guide you |
| **Granola** | `Connect to Granola using this MCP: ...` | Meeting notes, one-liner setup |
| **GitHub** | `Back up my Hermes folder in a private GitHub repo` | Backup your config |
| **YouTube** | YouTube API (via Google Cloud) or `yt-dlp` | Extract transcripts for research |

---

## 6. Desktop App Setup (Mac/Windows)

Simpler than VPS — no Docker, no SSH.

1. Download from [hermes.ai](https://hermes.ai) (search "Hermes desktop app")
2. Drag to Applications, launch, hit Install
3. Pick a model:
   - **OpenAI** (recommended): $20/mo ChatGPT subscription, use GPT-5.5
   - **Claude**: great reasoning but expensive API
   - **Hermes subscription**: 300+ models (overkill)
4. Set model: `openaicodedx/gpt-5.5` with effort: high, fast mode: on
5. The Mac Mini runs 24/7 — create a separate macOS user `zoe` for Hermes with its own credentials

### Why Desktop over VPS

| | Mac Mini | VPS |
|---|---|---|
| Setup time | 30 min | 2-4 hours |
| Maintenance | Near zero | Regular |
| Cron reliability | Native 24/7 | Docker + systemd |
| Cost | $500 one-time | $5-10/mo |
| Security | Local network | Public-facing |
