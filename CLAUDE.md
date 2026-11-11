# מפגש פסגה (mifgash_pisga) — agent orientation

Load this first. Read `docs/ARCHITECTURE.md` for goals/decisions and `docs/PROGRESS.md` for status and next steps only when the task needs them.

## What it is
Static Hebrew (RTL) website that finds where a group of friends should meet by car.
Two answers: minimum total road distance, and the fairest point (minimax). Results are shared to WhatsApp.
Live: https://vkk130.github.io/mifgash_pisga/ (GitHub Pages, branch `main`, root). Owner: Hillel (GitHub `vkk130`).

## Files
- `index.html` — the whole app: HTML + CSS + JS in one file. No build step, no dependencies to install, no backend, no API keys.
- `README.md` — short public description (Hebrew).
- `CLAUDE.md`, `docs/` — agent docs.

## Finding your way in index.html
CSS is in `<style>` at the top; markup is `<header>`, `<main>` with sections `#formSec`, `#mapSec`, `#results`, then `<footer>`.
JS is one `<script>` split by banner comments — grep `// ----------` to jump:
`state` · `map` (MapLibre setup, `hebrewLabels()`, route layers) · `people UI` · `address autocomplete (Photon)` · `markers` · `the optimisation` (`osrmTable`, `grid`, `score`, `optimise`, `runCalc`) · `results` (`showResults`, `drawResult`, `osrmRoute`, `reverseName`) · `funny lines` (`BANK`, `shareText`) · `link state` (`writeHash`/`readHash`) · `misc` · `boot`.

## Rules
- User-facing text is Hebrew only, light and young in tone (owner cited Pepper and Blink as style references), not cringe, not generic-sounding.
- Place names must appear in Hebrew (e.g. "רעננה", never "Raanana").
- Do not show the "Palestinian territories" / "West Bank" / "Gaza Strip" map labels (owner request).
- Keep the footer line "© כל הזכויות שמורות להלל בנבג'י".
- Be polite to the free public services (~1 request/second to OSRM; throttle via shared `lastCall`).
- Verify JS syntax after edits: extract the `<script>` block and run `node --check`.
- Commit, then push to `main` — Pages deploys automatically. Settings changes (Pages, etc.) can't be done from the agent session; ask Hillel.
