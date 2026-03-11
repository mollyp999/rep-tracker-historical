# REP PERFORMANCE TRACKER — HISTORICAL VERSION
**Administrator Guide**
Oct–Dec 2025 hardcoded data · Read-mostly · Gist-backed roster and meetings

---

## Overview

The Historical version is a read-mostly tracker covering October, November, and December 2025 — the first three months of operation. Rep performance data (funded amounts, apps, etc.) is hardcoded in the `SHEET` and `RAW` constants in the source file. The Gist backend is used only for roster metadata, meeting records, and supplemental data entry.

It is intended as a permanent record and reference. New rep data going forward should be entered in the Live (v4) version instead.

---

## Deployments & Access

| Field | Value |
|---|---|
| Historical URL | https://mollyp999.github.io/rep-tracker-historical |
| GitHub Repo | github.com/mollyp999/rep-tracker-historical |
| Gist ID | 34e0b4e2aa64701dea371cfc1389b3da |
| Gist URL | https://gist.github.com/mollyp999/34e0b4e2aa64701dea371cfc1389b3da |
| Token localStorage Key | `rep-tracker-gh-token` |
| Source File | `/home/claude/rep-tracker-deploy/app.jsx` |
| Compiled Output | `/mnt/user-data/outputs/index.html` |

---

## Authentication

Same token gate as the Live version, but stored under a different localStorage key (`rep-tracker-gh-token`) so both apps can run independently in the same browser session.

- Scope: `gist` (read and write)
- Account: `mollyp999`
- Generate at: github.com/settings/tokens

---

## Data Architecture

### Hardcoded Data (source file)
The core performance data lives in two constants in `app.jsx`:

| Constant | Contents |
|---|---|
| `SHEET` | All rep metadata: name, start date, group, position, tier, targets, and monthly funded/apps figures for Oct, Nov, Dec 2025 |
| `RAW` | Raw call activity data (dials, contacts, contact rate) |
| `ALL` | Derived from SHEET — builds the `.mos` arrays used throughout the app |

To update or correct historical figures, edit `SHEET`/`RAW` in `app.jsx` and redeploy.

### Gist-Backed Data (cloud)

| Key | Contents |
|---|---|
| `leaderboard-data-v1` | Supplemental monthly entries (Data Entry tab), keyed as `rep::YYYY::Mon` |
| `leaderboard-meetings-v1` | 1-on-1 meeting scheduled/met dates and history per rep |
| `leaderboard-roster-v1` | Roster overrides: active/inactive status, departure dates, notes |
| `leaderboard-benchmarks-v1` | Custom benchmark thresholds |
| `leaderboard-depthead-v1` | Dept Head pipeline data |
| `leaderboard-products-v1` | Product mix breakdown per rep/month |
| `leaderboard-extra-reps-v1` | Reps added via Roster UI (roster display only — not in leaderboard) |

---

## Period Coverage

| Field | Value |
|---|---|
| Active Months | Oct 2025, Nov 2025, Dec 2025 |
| Default Period | Dec 2025 |
| Year Filter (Data Entry) | 2024, 2025 only — 2026+ excluded intentionally |
| Period Tabs | Oct, Nov, Dec (monthly) · 2025 YTD · All Time |

---

## Views & Features

### Leaderboard
Ranked view of all reps for the selected period. Period selector covers Oct, Nov, Dec, 2025 YTD, and All Time.

### Points Groups
Group-level aggregated view comparing team leader sub-groups.

### Team Comparison
Side-by-side period performance across all reps with charts.

### Rep Compare
Select two reps for a detailed head-to-head metric comparison.

### Dept Heads
Dedicated panel for Max Gualtieri and Dave Salas.

### Product Mix
Product type breakdown by rep and month (MCA, Term, SBA, etc.).

### Data Entry
Supplemental data entry for any months not covered in `SHEET`. Supports manual entry and CSV import. Year options: 2024 and 2025 only.
- CSV headers: `Rep Name, Month, Year, Apps Sent, Received, Approved, Units Funded, Funded Amount, Meeting %`
- Headers auto-detected in any order, case-insensitive
- Entries are staged locally and saved to Gist on confirm

### Roster
Manage rep active/inactive status, departure dates, and notes. Reps are sourced from `SHEET` (hardcoded). The **+ Add Rep** button adds to a separate Gist key (`leaderboard-extra-reps-v1`) for roster display only — these reps do not appear on the leaderboard.

---

## Reps on Record

| Field | Value |
|---|---|
| Total | 35 reps |
| Active (as of Dec 2025) | 26 |
| Dept Heads | Max Gualtieri, Dave Salas (excluded from main leaderboard) |
| Former | Sebastian Devia, Jabril Perryman, Jacob Margolies, Shyann Franklin, Charlize Farahmand, Lucianna Julian, Max Gualtieri, Dave Salas, Carlo Moreno |

---

## Build & Deploy Process

### Step 1 — Compile
```bash
cd /home/claude/rep-tracker-deploy
node -e "const babel=require('@babel/core'),fs=require('fs');const r=babel.transformSync(fs.readFileSync('app.jsx','utf8'),{presets:['@babel/preset-react'],filename:'app.jsx'});fs.writeFileSync('/home/claude/app-compiled.js',r.code);"
```

### Step 2 — Inject into HTML
```python
existing = open('/mnt/user-data/outputs/index.html').read()
code = open('/home/claude/app-compiled.js').read()
start = existing.rindex('<script>') + len('<script>')
end = existing.rindex('</script>')
final = existing[:start] + '\n' + code + '\n  ' + existing[end:]
open('/mnt/user-data/outputs/index.html','w').write(final)
```

### Step 3 — Upload to GitHub
Go to `github.com/mollyp999/rep-tracker-historical`, open `index.html`, click Edit, select all, paste the contents of `index.html`, and commit. GitHub Pages deploys in ~30 seconds.

---

## Known Constraints & Notes

- **Performance data for Oct–Dec 2025 is hardcoded in `SHEET`/`RAW`** — corrections require a source edit and redeploy
- **Rep additions via Roster UI do NOT appear on the leaderboard** — stored in `leaderboard-extra-reps-v1` for display only
- **Do not use esbuild with `--minify`** — mangles `Set` and causes runtime crashes
- **Year range for data entry: 2024 and 2025 only** — 2026+ excluded intentionally
- **The Gist file must be named exactly `storage.json`**
- **Token key `rep-tracker-gh-token`** is separate from the Live version's `rep-tracker-v4-gh-token`
