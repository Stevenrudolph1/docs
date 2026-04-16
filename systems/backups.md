# Backups

Daily encrypted incremental backups of M4 and M1 to Cloudflare R2 via restic.

## How It Works

```
M4 (3:00 AM ICT) → scripts/backup-m4.sh
  1. Export D1 database (wrangler d1 export, OAuth auth)
  2. Export DNS records (xavigate.com, renergence.com via CF API)
  3. Export R2 bucket inventories (xavigate-maps, xavigate-public)
  4. restic backup ~/Projects, ~/.claude, ~/bin, shell configs → R2
  5. restic prune (keep 30 daily, 12 weekly, 6 monthly)

M1 (3:30 AM ICT) → scripts/backup-m1.sh
  1. restic backup ~/.openclaw, ~/Projects, ~/.zshrc → R2
```

## Key Details

- **Storage:** R2 bucket `sr-backups/` (~14 GB total, ~$1/mo)
- **Tool:** restic (encrypted, deduplicated, incremental)
- **Credentials:** `~/Projects/backups/.env` (gitignored)
- **Logs:** `~/Projects/backups/logs/backup-m4-YYYY-MM-DD.log`
- **Restore guide:** `bash ~/Projects/backups/scripts/restore-guide.sh`

## Dependencies

- restic (`/opt/homebrew/bin/restic`)
- wrangler (for D1 export — uses cached OAuth, NOT the API token)
- Cloudflare R2 S3 API credentials
- `CLOUDFLARE_API_TOKEN` for DNS/R2 inventory exports

## What's Backed Up

| Source | Backed Up By | Frequency |
|--------|-------------|-----------|
| ~/Projects/ | restic → R2 | Daily |
| ~/.claude/ | restic → R2 | Daily |
| ~/bin/, shell configs | restic → R2 | Daily |
| D1 database | SQL dump → disk → restic | Daily |
| DNS records | JSON export → disk → restic | Daily |
| R2 bucket inventory | JSON snapshot → disk → restic | Daily |
| Git repos | GitHub (separate) | On push |
| M1 OpenClaw workspace | restic → R2 | Daily |

## What's NOT Backed Up

- `archive/` folder (28GB, excluded)
- Worker secret values (names documented, values in password manager)
- macOS system/apps (reinstall is faster than restoring)

## Related Files

- `~/Projects/backups/` — scripts, logs, exports, credentials
- `~/Projects/backups/STATUS.md` — operational status and change log
