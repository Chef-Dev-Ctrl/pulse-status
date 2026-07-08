# Pulse Status Page

Static GitHub Pages status surface for `status.usepulseapp.com`.

## Data Sources

The page loads status in this order:

1. `https://usepulseapp.com/api/index.php?route=status/public`
2. Same-origin `status.json` published by the Goblin King Pi
3. Browser-side health check against `https://usepulseapp.com/api/index.php?route=health`

The public contract is summary-only:

- `version`
- `generated_at`
- `overall`
- `source`
- `stale_after_seconds`
- `watchpoints`
- `incidents`
- optional `external_checks`

Allowed status values are `operational`, `degraded`, `outage`, `maintenance`, and `unknown`.

## Public Watchpoints

The VPS public endpoint exposes product-facing modules only:

- Core Web
- API
- Auth
- Payments
- Chat
- File Uploads
- Inventory
- Tickets
- Forms
- Projects
- Leads
- Calendar
- Reports
- Notifications

Internal checks such as DB, backups, migrations, logs, hostnames, file paths, and service names remain private to the VPS watchdog.

## Deploy

This directory is its own Git repo:

```bash
cd status-page
git status
git add index.html README.md status.json
git commit -m "Rebuild status page monitor UI"
git push
```

GitHub Pages deploys `main` from the repository root. The `CNAME` file keeps the custom domain attached.

## Pi Publishing

The Pi checker writes local SD-card state under:

- `/home/pi/pulse/status/status.json`
- `/home/pi/pulse/logs/status-checker.log`

When `PULSE_STATUS_PUBLISH=1` and `/home/pi/pulse-status` exists, it commits `status.json` to this repo only on a state change or 30-minute heartbeat.
