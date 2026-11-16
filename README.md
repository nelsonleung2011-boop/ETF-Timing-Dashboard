# ETF Timing Dashboard

A self-contained ETF timing dashboard with 6,758 days of embedded history,
optional live market quotes, a 0–100 Opportunity Score, backtests, audits and
plain-language explanations.

## Start here — no technical knowledge needed

You do **not** need Python, a command prompt, a terminal, a web server, or
browser developer tools. Choose **one** of these options, in order of
simplicity:

### Option 1 — Use the hosted GitHub Pages version (easiest)

Open the web link shared with you, like opening any website. Nothing to
install, works on phones and tablets, and live market quotes normally work
there. Bookmark the link.

If you are the person publishing the files:

1. Upload **`index.html`** to a new GitHub repository. That one file contains
   everything the app needs.
2. In the repository go to **Settings → Pages**.
3. Set **Build and deployment → Source** to **Deploy from a branch**, choose
   **main** and **/(root)**, then press **Save**.
4. After 1–2 minutes, open the `github.io` link GitHub displays.

To update the hosted version later, replace `index.html` in the repository and
keep the same link. The in-app **Help & Setup** tab repeats these steps.

### Option 2 — Double-click the launcher (Windows)

1. Keep `Launch Dashboard.bat` (or `Launch Dashboard.cmd`),
   `serve_dashboard.ps1`, `index.html` and `ETF_Timing_Dashboard.html`
   together in the same folder. Both launcher extensions behave identically —
   use whichever one is present; this folder ships the `.cmd` variant.
2. Double-click **`Launch Dashboard.bat`** (or **`Launch Dashboard.cmd`**).
3. Your browser opens the dashboard automatically. Nothing to type.

The launcher finds a free local port, starts a private server reachable only
from your own computer, waits until it answers, and then opens the browser. It
uses Python if already installed; otherwise it uses built-in Windows
PowerShell. A small background window titled "ETF Dashboard local server"
keeps the dashboard working — close that window when you are finished.

If Windows shows a blue "Windows protected your PC" screen, click
**More info → Run anyway**.

### Option 3 — Manual local server (advanced users only)

Not needed for normal use. If you already have a technical setup, run
`python -m http.server 8000` in this folder and open
`http://localhost:8000/`.

---

## If you opened the HTML file directly

When the dashboard detects `file://` (a file opened from your own computer), it
shows this notice automatically:

> This dashboard is running directly from your computer. Browser security
> restrictions may prevent live market data retrieval.

It offers two buttons:

- **Continue with embedded data** — switches the whole app to a guaranteed
  offline mode. No network calls are made; every tab still works. The choice is
  remembered on this browser. Click the "Embedded data mode" badge in the
  header to try live data again.
- **Open deployment instructions** — jumps to the in-app **Help & Setup** tab
  with the ranked options above.

When live quotes cannot be reached at any time, the message is:

> Live market quotes are currently unavailable.
> The dashboard remains fully functional using the embedded historical dataset
> dated 2026-11-13.
> No action is required.

The technical reason is available under a collapsed "Why is live data
unavailable?" section and in the Diagnostics tab — non-technical users never
have to open it.

## What works without internet

Everything except the two optional online extras:

| Feature | Offline? |
|---|---|
| Scores for any date in 2000-01-03 → 2026-11-13, score decomposition, signals | ✅ |
| Compare dates, backtest explorer, buy-score trend charts (embedded data) | ✅ |
| Manual Score Calculator | ✅ |
| Audit, provenance, diagnostics and transparency reports | ✅ |
| Live quote retrieval | Needs a web link or the launcher (not `file://`) |
| Charts | Need internet once to load the ECharts library; scores/tables still work |

---

## For people who maintain the files

### Folder layout

```
ETF_Timing_Dashboard/
├── ETF_Timing_Dashboard.html   canonical built app (self-contained)
├── index.html                  identical copy — GitHub Pages + launcher entry point
├── Launch Dashboard.cmd        double-click launcher (Windows; .bat behaves identically)
├── serve_dashboard.ps1         private fallback web server for the launcher
├── ETF_Diagnostics_Audit.html  written audit: failure map, live-data proof, fixes
├── dashboard_template.html     HTML/JS source with __DASH_DATA__ placeholder
├── dashboard_data.json         embedded payload injected into the template
├── build_dashboard.py          one-shot rebuild (also refreshes index.html)
├── build_dashboard_data.py     CSV → dashboard_data.json
├── score_history.csv           build input: daily score + components
├── daily_features.csv          build input: 6,758 days of raw/derived features
├── test_dashboard_v2.js        automated test harness (Node.js)
├── data_raw/                   raw downloaded inputs (rebuild data only)
└── research/                   original study, review report, upstream scripts
```

### Rebuild and test

```
python build_dashboard.py        # regenerates BOTH html files + JS syntax check
node test_dashboard_v2.js        # offline validation suite
node test_dashboard_v2.js --live # also tests real Yahoo Finance calls
```

Paths in the build script are resolved relative to the script, so the working
directory does not matter.

### External dependencies (no files to ship)

| Resource | Used for |
|---|---|
| `https://cdn.jsdelivr.net/npm/echarts@5/dist/echarts.min.js` | all charts |
| `https://query1.finance.yahoo.com/v8/finance/chart/` | optional live SPY / QQQ / ^VIX / TLT / ^TNX / ^IRX |

No API keys are stored in the frontend. An optional `DATA_CONFIG.proxyEndpoint`
hook exists for a backend proxy. To show a real "Open hosted version" button
in the app, set `DEPLOY_CONFIG.hostedUrl` near the top of the inline script in
`dashboard_template.html`.

### Data and scoring notes

- The app is a hybrid: embedded data powers history, backtests and offline
  fallback; live data powers on-demand score generation.
- Scoring caps: Trend ≤25, Dip ≤25, VIX ≤25, Macro ≤15, Seasonality ≤10;
  bands 0–48 Wait, 49–60 Neutral, 61–74 Buy, 75–100 Strong Buy.
- The **Diagnostics** tab exposes connection tests, a fetch log, raw provider
  responses, validation, provider health, an audit log and a failure-point
  workflow diagram — no Developer Tools required.
- Disclaimer: for research/education only, not investment advice.
