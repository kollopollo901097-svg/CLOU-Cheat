# CLOU-Cheat
# 🤍 CLOU Chess 
 CLOU Chess — Python Dependencies
# Install with: pip install -r requirements.txt

fastapi>=0.100.0
uvicorn[standard]>=0.23.0
chess>=1.10.0
websockets>=11.0
python-multipart>=0.0.6
httpx>=0.24.0



# 🤍 CLOU Chess — Complete Guide

> **3700+ ELO AI Chess Suite** · Multi-Engine Council · Holy Gambit · Brilliant Detection · Syzygy Tablebases

---

## 📋 Table of Contents
1. [What is CLOU Chess?](#what-is-clou-chess)
2. [Package Contents](#package-contents)
3. [System Requirements](#system-requirements)
4. [Quick Start](#quick-start)
5. [Installing the Chrome Extension](#installing-the-chrome-extension)
6. [Engine & Syzygy Setup](#engine--syzygy-setup)
7. [Features Deep Dive](#features-deep-dive)
8. [HUD Panel Guide](#hud-panel-guide)
9. [Arrow Colors](#arrow-colors)
10. [Troubleshooting](#troubleshooting)
11. [API Reference](#api-reference)
12. [Changelog](#changelog)

---

## What is CLOU Chess?

**CLOU Chess** is a local, high-strength chess analysis and training suite powered by a **Multi-Engine Council** (Stockfish 19 + Berserk 14 + Obsidian 16), endgame **Syzygy Tablebases**, and tactical **Holy Gambit** & **Brilliant (`!!`)** move verification.

It consists of two tightly integrated components:
- **CLOU Arena (`clou-arena/`)** — A local FastAPI + WebSocket backend and web application (`http://127.0.0.1:8010`) where you can play against CLOU or other UCI engines, inspect real-time MultiPV evaluations, test Holy Gambit sacrifices, and view an interactive evaluation graph.
- **CLOU Chess Chrome Extension (`chesscheat-master/`)** — A Manifest V3 browser extension that connects to the local CLOU backend over WebSocket and renders a live HUD overlay, best-move arrows, Brilliant badges, and an evaluation graph directly on `chess.com`.

> **Note:** CLOU Arena is a 100% private, local testing and study environment. Using real-time engine assistance in rated/competitive games on external platforms violates their Terms of Service — use the browser extension strictly for personal study, post-game review, and private analysis.

---

## Package Contents

```text
CLOU-Chess-Package/
│
├── START_CLOU.bat              # One-click Windows launcher (double-click to run)
├── INSTALL_CLOU.ps1            # All-in-one PowerShell installer & launcher
├── requirements.txt            # Python dependencies
├── README.md                   # This documentation
│
├── clou-arena/                 # Python Backend & Local Web App
│   ├── app.py                  # FastAPI + WebSocket server (port 8010)
│   ├── holy_gambit.py          # Holy Gambit, Brilliant detection & Polyglot book logic
│   ├── start_arena.ps1         # Quick-start script (skips pip install)
│   ├── requirements.txt        # Python dependencies
│   ├── web/                    # Frontend UI (index.html, styles.css, app.js)
│   └── books/                  # Optional Polyglot opening books (.bin)
│
└── chesscheat-master/          # Chrome Extension (Manifest V3)
    ├── manifest.json           # Extension configuration & host permissions
    ├── popup.html / popup.js   # Extension popup UI & server status indicator
    ├── icon.png                # Extension icon
    └── scripts/
        ├── main.js             # Board observer, HUD panel & SVG arrows
        ├── background.js       # WebSocket bridge (port 8010 / legacy 8000)
        └── styles.css          # HUD styling
```

*(Optional)* You can also place an `engines/` folder and a `tablebases/` folder directly inside `CLOU-Chess-Package/` (or use the default `CLOU Chess - Copia\server\` directory).

---

## System Requirements

| Requirement | Minimum | Recommended |
|---|---|---|
| **Operating System** | Windows 10 (64-bit) | Windows 11 (64-bit) |
| **Python** | 3.10 | 3.12+ |
| **RAM** | 4 GB | 16 GB |
| **CPU** | 4 cores | 8+ cores (AVX2 supported) |
| **Browser** | Chrome / Edge | Chrome 120+ |
| **Disk Space** | 500 MB | 2+ GB (with Syzygy tablebases) |

> **Need Python?** Download it from [python.org/downloads](https://www.python.org/downloads/) and make sure to check **"Add Python to PATH"** during installation.

---

## Quick Start

### Method 1 — One-Click Double-Click (Recommended)

Double-click **`START_CLOU.bat`** in the package root.

It will automatically:
1. Verify Python 3.10+ is installed
2. Install all required packages from `requirements.txt`
3. Check for chess engines (`Stockfish`, `Berserk`, `Obsidian`) and Syzygy tablebases
4. Free port `8010` if another process is using it
5. Print Chrome extension setup instructions
6. Start the CLOU Arena server and open `http://127.0.0.1:8010` in your browser

### Method 2 — PowerShell (With Optional Flags)

Open PowerShell in `CLOU-Chess-Package/` and run:

```powershell
.\INSTALL_CLOU.ps1
```

Available flags:
```powershell
.\INSTALL_CLOU.ps1 -SkipInstall     # Skip pip install (faster after first run)
.\INSTALL_CLOU.ps1 -SkipBrowser     # Start server without opening the browser
.\INSTALL_CLOU.ps1 -Port 8011       # Run on a custom port (default: 8010)
```

> **If PowerShell blocks scripts:** Run PowerShell as Administrator once and execute:
> ```powershell
> Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
> ```

### Method 3 — Manual CLI

```powershell
pip install -r requirements.txt
cd clou-arena
python -m uvicorn app:app --host 127.0.0.1 --port 8010 --log-level info
```
Then open `http://127.0.0.1:8010` in your browser.

---

## Installing the Chrome Extension

1. Open Google Chrome and go to **`chrome://extensions`**
2. Enable **Developer mode** using the toggle in the top-right corner
3. Click **Load unpacked**
4. Select the **`chesscheat-master`** folder inside `CLOU-Chess-Package`
5. Pin the **CLOU Chess** icon to your Chrome toolbar

### Using the Extension
1. Start the backend server first via `START_CLOU.bat` or `INSTALL_CLOU.ps1`
2. Open [chess.com](https://www.chess.com) and load a board or analysis game
3. Click the **CLOU Chess** toolbar icon — verify the status indicator is green (connected to `127.0.0.1:8010`)
4. Toggle analysis on to display the live HUD, arrows, and evaluation graph

---

## Engine & Syzygy Setup

### Engine Locations

By default, CLOU searches for UCI engines in either:
1. **Local package folder:** `CLOU-Chess-Package\engines\` (if present, `INSTALL_CLOU.ps1` automatically sets `CLOU_SERVER_DIR`)
2. **Default server directory:** `C:\Users\<YourUsername>\OneDrive\Desktop\CLOU Chess - Copia\server\engines\`

| Engine | Expected Relative Path |
|---|---|
| **Stockfish 19 (CLOU)** | `engines\stockfish\stockfish-windows-x86-64-universal.exe` |
| **Berserk 14** | `engines\berserk\berserk-14-avx2.exe` |
| **Obsidian 16** | `engines\obsidian\Obsidian160-avx2.exe` |
| **Komodo / Dragon / Other** | Auto-detected anywhere under `engines\**\*.exe` |

### Syzygy Endgame Tablebases

Place your `.rtbw` and `.rtbz` tablebase files in:
- `CLOU-Chess-Package\tablebases\` **or**
- `C:\Users\<YourUsername>\OneDrive\Desktop\CLOU Chess - Copia\server\tablebases\`

You can download 3-4-5 piece Syzygy tablebases from [tablebase.lichess.ovh/tables](https://tablebase.lichess.ovh/tables/).

### Environment Variables (Optional)

Set these in PowerShell before launching if you want custom paths or resource limits:

```powershell
$env:CLOU_SERVER_DIR = "D:\MyChess\server"   # Custom folder containing engines/ and tablebases/
$env:ENGINE_THREADS  = "12"                  # Threads per engine (default: CPU_COUNT - 4, min 1)
$env:ENGINE_HASH_MB  = "2048"                # Engine hash table RAM in MB (default: 1024)
```

---

## Features Deep Dive

### 🤝 Multi-Engine Council
When **ELO Tier** is set to **`3700` (3700 MAX)** and helper engines (Berserk / Obsidian) are present:
- Stockfish, Berserk, and Obsidian analyze the position **in parallel**.
- If the engines disagree on the best move, a final verification pass with `root_moves` resolves the tie and selects the strongest consensus move.

### 🔥 Holy Gambit Mode
Enable **Holy Gambit** for aggressive, verified tactical play:
1. **Opening Book:** Queries any Polyglot `.bin` book placed in `clou-arena/books/` (or falls back to the built-in gambit book).
2. **MultiPV Scan:** Scans **5 candidate lines** in parallel (`MultiPV=5`) during the middlegame.
3. **Sacrifice Verification:** Any promising tactical sacrifice is double-checked with an extra 0.65s deep verification pass.
4. **Winning Position Guard:** When already clearly winning (`+1.50` or better), Holy Gambit tightens its centipawn tolerance to just `8 cp` to avoid throwing away a won game.
5. **Configurable Threshold:** Adjust `threshold_cp` (default: `30` cp / `-0.30` pawns) to control how aggressive the gambit selection is.

### ✨ Brilliant (`!!`) Detection
A move is flagged as **`!! Brilliant`** only when it involves a genuine material sacrifice or counter-intuitive tactical shot that passes double-depth verification without losing the evaluation.

### 🛡️ Anti-Repetition Guard
CLOU **never suggests a draw by threefold repetition** when you have an advantage:
- Activates automatically when evaluation is `>= +0.20` pawns.
- Tracks previously seen board FENs (`seen_fens`) and re-evaluates non-repeating alternatives.
- Accepts a slight centipawn trade-off (up to `-0.10`) if needed to keep playing for a win.

### ♟️ ELO Tiers

| Tier | Behavior |
|---|---|
| **2500** | `UCI_Elo=2500` — strong club/master training level |
| **2900** | `UCI_Elo=2900` — Grandmaster level |
| **3400** | `UCI_Elo=3190` (Stockfish UCI Elo cap) |
| **3700 MAX** | Full unrestricted strength + **Multi-Engine Council** |

---

## HUD Panel Guide

| HUD Element | Description |
|---|---|
| `CLOU • HOLY GAMBIT` | Gold/red status badge shown when Holy Gambit mode is active |
| `D24 • 1348k n/s • TB:1` | Current search depth, nodes per second, and Syzygy tablebase hits |
| **Best Move** | Top recommended move in algebraic (SAN) notation |
| **PV Lines** | Principal variations (1–3 lines, or 5 in Holy Gambit mode) |
| `!! Brilliant` | Highlighted badge for double-verified tactical sacrifices |
| **Accuracy %** | Estimated move accuracy across the current game |
| **ELO Tier** | Selector for `2500` / `2900` / `3400` / `3700 MAX` |

---

## Arrow Colors

| Color | Meaning |
|---|---|
| 🟢 **Green** | Best move (Rank 1) |
| 🔵 **Blue** | Second / third principal variation |
| 🌊 **Turquoise / Cyan** | Brilliant move (`!!`) |
| 🟡 **Gold** | Holy Gambit move |

---

## Troubleshooting

### ❌ `"CLOU: engine event loop dead"`
- **Cause:** A UCI engine process crashed or exited unexpectedly.
- **Auto-fix:** CLOU automatically restarts the engine handle within ~0.9s.
- **Manual fix:** Restart the server using `START_CLOU.bat` or `.\INSTALL_CLOU.ps1`.

### ❌ Server fails to start (Port `8010` already in use)
`INSTALL_CLOU.ps1` and `START_CLOU.bat` automatically kill stale processes on port `8010`. To free it manually in PowerShell:
```powershell
$p = (Get-NetTCPConnection -LocalPort 8010 -State Listen).OwningProcess
Stop-Process -Id $p -Force
```

### ❌ Chrome extension cannot connect to server
1. Confirm the server is running by opening `http://127.0.0.1:8010/api/health` in Chrome.
2. Go to `chrome://extensions` and click the **Reload** icon on **CLOU Chess**.
3. Ensure `manifest.json` includes `http://127.0.0.1:8010/*` and `ws://127.0.0.1:8010/*` in `host_permissions`.

### ❌ `"Invalid FEN"` or illegal position warnings
- Happens briefly while a board is animating or after checkmate/stalemate.
- CLOU detects game-over states automatically and returns a `gameover` message instead of an error.

### ❌ PowerShell execution policy error
If Windows says *"running scripts is disabled on this system"*:
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

### ❌ High RAM usage
Lower the engine hash size before starting:
```powershell
$env:ENGINE_HASH_MB = "256"
.\INSTALL_CLOU.ps1 -SkipInstall
```

---

## API Reference

### REST — `GET /api/health`

Returns server status, discovered engines, thread/hash configuration, Syzygy availability, and active Holy Gambit book.

```json
{
  "status": "ok",
  "engines": ["clou", "stockfish", "berserk", "obsidian"],
  "council": ["stockfish", "berserk", "obsidian"],
  "threads": 8,
  "hash_mb": 1024,
  "syzygy": true,
  "holy_gambit_book": "built-in"
}
```

### WebSocket — `ws://127.0.0.1:8010/ws/arena`

#### Client → Server Messages

- **Analyse position (`analyse`):**
  ```json
  {
    "type": "analyse",
    "id": "req-1",
    "fen": "rnbqkbnr/pppppppp/8/8/4P3/8/PPPP1PPP/RNBQKBNR b KQkq - 0 1",
    "preset": "fast",
    "elo_tier": "3700",
    "multipv": 3,
    "holy_gambit": false,
    "threshold_cp": 30,
    "seen_fens": []
  }
  ```
  - `preset`: `"fast"` (1.2s), `"balanced"` (2.8s), or `"max"` (8.0s)
  - `elo_tier`: `"2500"`, `"2900"`, `"3400"`, or `"3700"`

- **Stop current analysis (`stop`):**
  ```json
  { "type": "stop" }
  ```

- **Play move against engine (`play_move`):**
  Used by CLOU Arena to request the opponent engine's reply.

#### Server → Client Messages

- `info` — Streaming search updates (`depth`, `nps`, `tbhits`, `lines`)
- `done` — Final analysis result (`depth`, `nps`, `tbhits`, `book`, `brilliant`, `council`, `holy_gambit_move`, `lines`)
- `gameover` — Position is checkmate, stalemate, or draw (`result`)
- `opponent_move` — Engine move response for `play_move`
- `error` — Invalid FEN or engine error (`message`)

---

## Changelog

| Version | Highlights |
|---|---|
| **v2.2** | Unified `CLOU-Chess-Package` distribution; English documentation; `INSTALL_CLOU.ps1` & `START_CLOU.bat` one-click launchers with local/legacy engine discovery |
| **v2.1** | Fixed engine event-loop race condition; Anti-Repetition Guard; auto-retry on crash; normalized `seen_fens` matching |
| **v2.0** | Multi-Engine Council (Stockfish + Berserk + Obsidian); Holy Gambit with sacrifice verification; Brilliant `!!` detection |
| **v1.5** | Chrome Extension MV3; HUD overlay with evaluation graph and SVG arrows |
| **v1.0** | Initial release — Stockfish overlay for chess.com |

---

*CLOU Chess — Personal chess study & analysis suite. All bundled/supported engines and libraries are open source.*
