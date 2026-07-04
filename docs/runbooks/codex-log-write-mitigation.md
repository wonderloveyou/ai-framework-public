# Runbook: Codex Log Write Mitigation

Last updated: 2026-06-25

## Why

OpenAI Codex issue `openai/codex#17320` reports excessive writes to `~/.codex/logs_2.sqlite-wal` during streaming because TRACE-level entries are persisted into SQLite despite reduced log-level settings. The reported workaround is to block inserts into the `logs` table or move the database to tmpfs.

This repo uses the SQLite trigger workaround because it is simple, reversible, and preserves the database file path expected by Codex.

## Apply

Windows PowerShell:

```powershell
@'
import sqlite3
from pathlib import Path

p = Path.home() / ".codex" / "logs_2.sqlite"
conn = sqlite3.connect(str(p), timeout=10)
try:
    conn.execute("CREATE TRIGGER IF NOT EXISTS block_log_inserts BEFORE INSERT ON logs BEGIN SELECT RAISE(IGNORE); END;")
    conn.commit()
    conn.execute("PRAGMA wal_checkpoint(TRUNCATE)")
finally:
    conn.close()
'@ | python -
```

Linux:

```bash
python3 - <<'PY'
import sqlite3
from pathlib import Path

p = Path.home() / ".codex" / "logs_2.sqlite"
conn = sqlite3.connect(str(p), timeout=10)
try:
    conn.execute("CREATE TRIGGER IF NOT EXISTS block_log_inserts BEFORE INSERT ON logs BEGIN SELECT RAISE(IGNORE); END;")
    conn.commit()
    conn.execute("PRAGMA wal_checkpoint(TRUNCATE)")
finally:
    conn.close()
PY
```

## Verify

```bash
python3 - <<'PY'
import sqlite3
from pathlib import Path

p = Path.home() / ".codex" / "logs_2.sqlite"
conn = sqlite3.connect(str(p))
try:
    print(conn.execute("SELECT name FROM sqlite_master WHERE type='trigger' AND name='block_log_inserts'").fetchall())
finally:
    conn.close()
for suffix in ["", "-wal", "-shm"]:
    f = Path(str(p) + suffix)
    if f.exists():
        print(f.name, f.stat().st_size)
PY
```

Expected:

- `block_log_inserts` exists.
- `logs_2.sqlite-wal` is small or `0` after checkpoint.

## Remove

```bash
python3 - <<'PY'
import sqlite3
from pathlib import Path

p = Path.home() / ".codex" / "logs_2.sqlite"
conn = sqlite3.connect(str(p), timeout=10)
try:
    conn.execute("DROP TRIGGER IF EXISTS block_log_inserts")
    conn.commit()
finally:
    conn.close()
PY
```

## Applied Here

Applied on 2026-06-25:

- Windows local Codex DB: `{USER_HOME}\.codex\logs_2.sqlite`
  - trigger installed
  - WAL truncated to `0`
- Server A Codex DB: `/root/.codex/logs_2.sqlite`
  - trigger installed
  - WAL checkpoint complete

## Notes

- This disables persistent internal diagnostic log rows, not session history.
- If OpenAI ships an upstream fix, remove the trigger if persistent local diagnostics are needed.
- `CODEX_LOG_LEVEL=ERROR` may reduce some logging, but issue reports indicate SQLite TRACE persistence can bypass ordinary log-level filtering in affected builds.

