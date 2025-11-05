# 🎮 LatestGames.com – Data & Page Flow Summary

This document explains how game data flows through the entire LatestGames.com system — from RAWG API → database → JSON → GitHub → WordPress → dynamic pages.

---

## 🧩 1. Data Fetching (Backend)

**Script:** `fetch_games_v2_fixed_age_clean.py`

- Connects to the RAWG API and enriches local data for each game.
- Updates SQLite database: `latestgames.db`
  - Tables include: `games`, `screenshots`, `genres`, `platforms`, `stores`, etc.
- Cleans up orphans, updates missing fields, and applies correct logic:
  - ✅ “13+ Teen”, “17+ Mature”, “Not Rated”, and fallback “N/A” distinctions.
  - ✅ Metascore color-coding (green, yellow, red, gray for N/A).
- Can run by single ID or batch (auto rate-limit and resume).

---

## 🗃️ 2. JSON Export

**Script:** `export_latestgames_json.py`

- Reads from `latestgames.db`
- Exports a normalized JSON file:
  ```
  C:\Users\Adam Byrd\Documents\GitHub\LatestGames.com\data\latestgames.json
  ```
- Overwrites the existing file each time you run it.
- Prints a clean summary in the console for verification.

---

## 🌐 3. GitHub Sync

**Repository:**
```
https://github.com/CODE-polarbear/LatestGames.com
```

**Sync flow:**
1. Use **GitHub Desktop** linked to the repo.
2. After each JSON export:
   - Stage → Commit → Push to `main`
3. WordPress automatically fetches from that same GitHub JSON file (via shortcode integration).

This ensures your live site always reflects your latest exported data.

---

## 💻 4. WordPress Integration

**File source:**  
`latestgames.json` is pulled directly from your GitHub repo’s `/data` folder.

### Active Shortcodes:
- `[latestgames]` – shows grid of all games
- `[latestgames_card slug="portal"]` – shows a compact single-card layout
- `[latestgames_game slug="portal"]` – full game detail page layout

### Full Game Page Layout
- Title, release date, genres, platforms, developers, publishers, tags
- Cover, screenshots, store links, “DLCs and editions”, and suggestions
- Rating + metascore pills, with proper N/A + color handling
- Age rating always displays (“Not Rated” vs “N/A” distinction preserved)

---

## 🚀 5. Dynamic Game Routing

**Rewrite Rule Registered:**
```
^games/([^/]+)/?$ → index.php?page_id=8&lg_game_slug=$matches[1]
```

**Behavior:**
- Any URL under `/games/<slug>` automatically routes to the main “Games” page.
- The shortcode `[latestgames_game]` dynamically loads the correct game by slug.
- No need for manual child pages — 900k+ games supported instantly.
- If a slug isn’t found → clean “Game not found.” message (not 404).

---

## ⚙️ 6. Maintenance & Tools

### Routine Steps
1. Run `fetch_games_v2_fixed_age_clean.py`
2. Run `export_latestgames_json.py`
3. Commit + Push via GitHub Desktop
4. WordPress auto-updates via live JSON link

### Utilities
- **Temporary Snippet:** “Show Rewrite Rules (temporary)” — used for verifying routes  
  (✅ Currently disabled)
- **Rewrite Rule Inspector:** optional plugin to visualize active rules
- **Bluehost Cache Flush:** required after major permalink or data changes

---

## 🧱 7. Tech Stack Overview

| Layer | Tool / Platform | Description |
|-------|------------------|-------------|
| Data Source | RAWG.io API | Base metadata, screenshots, stores |
| Local DB | SQLite (`latestgames.db`) | Persistent cache of all game info |
| Exporter | Python script | Converts DB → JSON |
| Repository | GitHub (`LatestGames.com`) | Versioned data + frontend/backend code |
| CMS | WordPress (Bluehost) | Frontend display & dynamic routing |
| Hosting | Bluehost / cPanel | Live production hosting |
| Optional | Next.js Frontend | Alternate UI for future LatestGames front app |

---

## ✅ Current System Status

| Component | Status |
|------------|---------|
| RAWG Fetcher | ✅ Working, complete |
| JSON Exporter | ✅ Working, overwrites GitHub data |
| GitHub Desktop Sync | ✅ Tested and verified |
| WordPress Integration | ✅ Shortcodes & JSON reading fine |
| Dynamic Routing | ✅ Active (`^games/([^/]+)/?$`) |
| Cache Handling | ✅ Working after manual flush |
| Temporary Debug Tools | ✅ Disabled safely |

---

_Last verified: November 2025_  
_Managed by CODEpolarbear (Adam Byrd)_
