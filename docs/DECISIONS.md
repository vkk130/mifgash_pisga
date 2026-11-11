# Decisions
- Distance = OSRM road km by car. No transit, Hebrew only, max 25 people.
- Fair = minimax (minimise the longest trip; tie → smaller sum). "Everyone equal" rejected.
- Search: 4 rounds of grid search via OSRM `/table` (≤100 coords per call), refining around each mode's best.
- Services (free, no keys): OpenFreeMap+MapLibre map, OSRM table/route, Photon search (`lang=default`), Nominatim reverse (`he`), is.gd short links.
- State in URL hash: base64url `{p:[[name,lat,lon]]}`; opening it recalculates.
- Share message: fastest + fairest point (merged if same), each with Waze link + one-line method; short link to results; "רוצים לבדוק בעצמכם? כנסו ל-<site>". Funny lines only on the site.
- Carpool ("נוסעים ביחד"): meeting point fixed first, then pairs. Everyone has a car by default; toggle 🙋 "צריך טרמפ". Options: pickup at home, or meet at a point on the driver's route (by car). Offer only if whoever detours adds ≤ min(15% of own trip, 20 min), on their own time. Greedy by saving (Clarke–Wright), ride-needers first; no seat limit; no time sync. Hash key `r` = indices needing a ride.
- Venues (Hillel's places API, provider unknown) = next MVP. Fallback if OSRM fails: domain-restricted TomTom key.
