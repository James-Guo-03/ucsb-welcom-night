# JHUCSSA 2026 Spring Festival Gala Lottery

Single-page lottery app for the JHUCSSA 2026 Spring Festival Gala (春晚). It loads eligible participants from a published Google Sheet, runs a slot-machine draw for one or three winners at a time, and records results in the browser and via Google Apps Script.

## Deployed Version 

- https://jhu-cssa.github.io/spring-festival-2026/

## Features

- Pull candidate names and emails from a published Google Sheets CSV export
- Draw 1 or 3 winners per round with a rolling slot-machine animation
- Skip anyone already drawn in the current session (tracked in `localStorage`)
- Canvas fireworks during the draw
- Optional background music with play/pause and volume control
- POST each winning round to a Google Apps Script web app for logging
- Reset drawn history and refresh the candidate list from the sheet

## Project structure

| File | Role |
| --- | --- |
| `index.html` | UI, styles, and client-side lottery logic |
| `image.jpg` | Full-page background image |
| `audio.mp3` | Looping background music during the event |

## Run locally

No build step or package manager is required. Serve the folder over HTTP so `fetch` can load the Google Sheet CSV (opening `index.html` as a `file://` URL may block those requests).

```bash
cd /path/to/spring-festival-2026
python3 -m http.server 8000
```

Open [http://localhost:8000](http://localhost:8000) in a browser.

## Event workflow

1. Click **获取候选名单** (top left) to load participants from the sheet. Anyone already marked as drawn in this browser is excluded.
2. Choose **1人** or **3人** for how many winners to draw in the round.
3. Click **开始抽奖** to run the animation (~3 seconds), then view the winner cards.
4. Repeat until the remaining pool is empty or the round is complete.
5. Use **重置数据** (top right) to clear `localStorage` drawn history and reload candidates. This does not undo rows already written by Google Apps Script.

## Configuration

In `index.html`, update these constants for your sheet and logging endpoint:

- `CSV_URL` — published Google Sheet CSV URL (`output=csv`)
- `GOOGLE_SCRIPT_URL` — deployed Apps Script web app URL that accepts POST JSON

The CSV is parsed as comma-separated rows with a header row skipped. Each data row needs at least **name** (column 1) and **email** (column 2). Rows missing either field are ignored.

`sendToGoogleScript` uses `mode: 'no-cors'`, so the browser cannot read the response; confirm logging on the Apps Script side.

## Data and persistence

- **Eligible pool:** refreshed from the sheet on each fetch; already-drawn emails in this browser are filtered out.
- **Drawn winners:** stored under the `drawnCandidates` key in `localStorage` until reset.
- **Remote log:** each round POSTs `{ winners, timestamp }` to `GOOGLE_SCRIPT_URL`.

## Browser support

Uses modern APIs (`fetch`, `localStorage`, canvas, CSS `backdrop-filter`). Use a current Chromium-, Firefox-, or Safari-based browser for the gala display.
