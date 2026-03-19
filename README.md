# Pulse Status Page

A standalone status page for [usepulseapp.com](https://usepulseapp.com), designed to be hosted independently from the production infrastructure so it remains accessible during provider outages.

## Hosted at

**`status.usepulseapp.com`** via GitHub Pages

## Setup — GitHub Pages

### 1. Create the Repository

```bash
# From this directory
cd portal-root/status-page

git init
git add .
git commit -m "Initial status page"
git remote add origin https://github.com/Chef-Dev-Ctrl/pulse-status.git
git push -u origin main
```

### 2. Enable GitHub Pages

1. Go to **Settings → Pages** in the `pulse-status` repo
2. Source: **Deploy from a branch**
3. Branch: `main` / `/ (root)`
4. Save

The page will be live at `https://chef-dev-ctrl.github.io/pulse-status/` within a minute.

### 3. Custom Domain (status.usepulseapp.com)

1. **DNS:** Add a CNAME record at your DNS provider:
   ```
   status.usepulseapp.com  →  CNAME  →  chef-dev-ctrl.github.io
   ```

2. **GitHub Pages:** In repo Settings → Pages → Custom domain, enter:
   ```
   status.usepulseapp.com
   ```

3. **Enforce HTTPS:** Check the box once DNS propagates (~5 min).

4. **CNAME file:** Already included in this directory. GitHub uses it to route the custom domain.

## Updating the Status Page

### During an incident

Edit `index.html` → the `STATUS_DATA` object near the top of the `<script>` block:

```javascript
const STATUS_DATA = {
  overall: 'outage',          // 'operational' | 'degraded' | 'outage' | 'maintenance'
  overrideMessage: null,       // Optional custom banner text
  lastUpdated: '2026-03-19T12:30:00+02:00',

  services: [
    { name: 'Web Application', status: 'outage' },
    { name: 'API',             status: 'outage' },
    // ...
  ],

  incidents: [
    {
      title: 'Production Outage — Description',
      status: 'investigating',    // 'investigating' | 'identified' | 'monitoring' | 'resolved'
      updates: [
        {
          time: '2026-03-19T12:00:00+02:00',
          label: 'Investigating',
          text: 'We are investigating reports of...',
        },
      ],
    },
    // ...previous incidents
  ],
};
```

Then commit and push:

```bash
git add index.html
git commit -m "Incident: outage detected"
git push
```

GitHub Pages deploys within ~60 seconds.

### When resolved

1. Change `overall` back to `'operational'`
2. Reset all services to `'operational'`
3. Update the incident `status` to `'resolved'`
4. Add a final "Resolved" update entry
5. Commit & push

### Housekeeping

- Keep the last ~10 incidents for history; archive older ones
- Update `lastUpdated` each time you edit
- The "Run Live Check" button lets visitors verify in real-time by hitting the health endpoint

## File Structure

```
status-page/
├── index.html      # Single-page status app (HTML + CSS + JS)
├── favicon.svg     # Pulse logo
├── CNAME           # GitHub Pages custom domain config
└── README.md       # This file
```

## Design

- Dark theme matching Pulse brand (#0f172a background, #39b54a green)
- Mobile responsive
- No dependencies — pure HTML/CSS/JS
- Live health check hits `/api/index.php?route=health` with CORS
- Service-level status indicators
- Incident timeline with chronological updates

## Why GitHub Pages?

- **Free** and reliable (99.99% SLA from GitHub)
- **Independent** from Xneelo — stays up even when our provider is down
- **Global CDN** via GitHub's infrastructure (Fastly)
- **HTTPS** by default with custom domains
- **No server** to maintain — just push HTML
