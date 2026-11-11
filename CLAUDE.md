# מפגש פסגה — agent guide

Static Hebrew site: friends enter starting points, get the meeting point with the least total driving (sum) and the fairest one (minimax). Shares to WhatsApp.
Live: https://vkk130.github.io/mifgash_pisga/ · Owner: Hillel (`vkk130`) · Read `docs/` only if the task needs it.

## Code
Everything is in `index.html` (HTML+CSS+JS, no build, no backend, no keys). `rides.html` = standalone rides-only page rendered from its hash (keep sentences in sync with `poolLines`). Jump with `grep -n "// ----------" index.html`.

## Rules
- UI/message text: Hebrew, short, light, young (Pepper/Blink style), not cringe. Place names in Hebrew.
- Hide "Palestinian territories" / West Bank / Gaza Strip map labels. Keep footer "© כל הזכויות שמורות להלל בנבג'י".
- OSRM public server: ≤1 req/s (shared `lastCall`).
- After edits: extract `<script>` and `node --check` it. Push to `main` (Pages auto-deploys). Repo settings: ask Hillel.
- Same commit: update `docs/PROGRESS.md` (one line), `docs/DECISIONS.md` if a decision changed, this file if rules changed. Keep all three short. Never invent; ask Hillel.
