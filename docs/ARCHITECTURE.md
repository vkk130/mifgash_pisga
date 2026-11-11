# Architecture and decisions

## Goal
Help friends decide where to meet. User enters people (name + starting point) and gets:
1. **הכי קצר ביחד** — point minimising the sum of everyone's driving distance.
2. **הכי הוגן** — point minimising the longest single driving distance (minimax); ties broken by the smaller sum.
Then a WhatsApp share message with the point, a Waze link, each person's km with a funny line, an explanation of how the point was chosen, and a link to the site.

## Decisions (agreed with Hillel)
| Decision | Choice | Why |
|---|---|---|
| Distance type | Road driving distance (km), not air distance | Realistic |
| Transport | Car only. No public transit (for now) | Scope |
| "Fair" definition | Minimax, NOT "everyone equal" | Equal distance can be satisfied by absurd far-away points; Hillel explicitly rejected it |
| Venue / places API | Not in this MVP — just a point on the map | Hillel has a places API, saved for the next MVP |
| Hosting | GitHub Pages, single static file | Free, no server |
| Language | Hebrew only | For now |
| Max people | 25 | OSRM public table limit is 100 coordinates; leaves ≥75 for candidates |
| OSRM reliability risk | Accepted | Low usage expected. Fallback idea if it fails: a TomTom key restricted to the site's domain |
| fastbus reuse | Only the map tiles (OpenFreeMap). Not fastbus's TomTom/HERE keys or its Jerusalem-only geocoder | Keys would be exposed in a static site; free quotas too small; need nationwide search |

## External services (all free, no keys, called from the browser)
- Map tiles/style: OpenFreeMap `liberty` style, rendered with MapLibre GL 4.7.1 (unpkg) + mapbox-gl-rtl-text plugin.
- Driving distances: OSRM public demo `router.project-osrm.org` — `/table` (distance matrix) and `/route` (road geometry for drawing).
- Address autocomplete: Photon (komoot), `lang=default` so names come back in the local (Hebrew) form, biased to an Israel bbox.
- Reverse geocoding (name of the result point / map-picked points): Nominatim, `accept-language=he`.

## Algorithm (`optimise()`)
d_i(x) = OSRM road distance from person i to point x.
- Sum mode: argmin_x Σ d_i(x). Fair mode: argmin_x max_i d_i(x), tie → smaller sum.
- No closed form, so grid search with refinement, 4 rounds (`ROUNDS`):
  - Round 1: n×n grid over the people's bounding box (half-size ×1.1, min 0.01°), n = ⌊√(100−P)⌋. One `/table` call.
  - Rounds 2–4: for each mode separately, an n'×n' grid (n' = ⌊√((100−P)/2)⌋) centred on that mode's current best, half-width = previous grid step. One `/table` call per round containing both modes' candidates.
  - Each candidate is snapped by OSRM to the nearest road; the snapped location is what's reported.
- Throttle: ≥1.1 s between OSRM calls; retries on 429/errors.

## State & sharing
- All state lives in the URL hash: base64url(JSON `{p:[[name,lat,lon]...]}`), coords rounded to 4 decimals (older links with addr/`m` still load). Opening such a link re-runs the calculation (deterministic for the same input).
- Share message (Hillel's spec): only the two points (fastest + fairest, merged into one if they're within ~300 m), each with a one-line explanation of how it was chosen; then a short link to the result page (per-person distances + routes); then "רוצים לבדוק בעצמכם? כנסו ל-<site root>". Each point has a Waze link (`waze.com/ul?ll=lat,lon`). No per-person lines in the message.
- The result link is shortened with is.gd (JSONP, no key, 5 s timeout, falls back to the long link). Hillel wants a normal-size link, not a huge one.
- The funny lines (`BANK`) now appear on the site under each name in the results table, not in the message.
- Share via `wa.me/?text=` (window opened synchronously, then navigated, to avoid popup blocking) or copy (ClipboardItem with a promise for iOS).

## Map rendering
- Each person has a colour (`COLORS`, by index in `people`). Their route to the result is drawn along roads in that colour with a white casing; a dashed straight line shows until the route loads. Routes load one by one (rate limit) and are cached per (start, destination).
- Destination is an SVG pin (orange = sum, green = fair).
- `hebrewLabels()` rewrites every symbol layer's `text-field` to `name:he` → `name`, and blanks labels matching Palestin/פלסטינ/West Bank/Gaza Strip.
