# Progress and next steps

## Timeline (2026-11-11)
1. Planning with Hillel; decisions recorded in ARCHITECTURE.md.
2. First version of `index.html` built; repo `vkk130/mifgash_pisga` created by Hillel; Pages enabled by Hillel. Hillel confirmed the site worked.
3. Mobile layout fix: Hillel reported the results screen looked like a desktop page on the phone. Cause assumed: long URL in the share preview widened the page. Fix: grid `minmax(0,1fr)`, wrapping, mobile order form → map → results, inputs 16px (no iOS zoom), auto-scroll to map after calculating.
4. Copy rewrite: slogan "נקודת מפגש. בלי ויכוחים.", one-line explanation + 3 steps in the header; new button/tab texts; new share message ending "רוצים לבדוק בעצמכם? כנסו ל-<site>"; removed the "full result" link. Photon `lang=default` for Hebrew place names.
5. Share message: explicit sentence on how the point was chosen; names bold as a WhatsApp list.
6. Map: per-person coloured road routes (OSRM `/route`), thicker lines with casing, straight SVG pin.
7. Map labels in Hebrew; Palestinian territories / West Bank / Gaza Strip labels hidden.
8. Removed the share-message preview; added the math explanation to "איך זה עובד?"; added the rights footer; added these agent docs.

## Not yet verified in a real browser
The agent environment can't reach the map/OSRM/Photon/Nominatim servers, so items 3–8 were only syntax-checked (`node --check`). Hillel's feedback is the test. Specifically unconfirmed:
- Mobile layout after the fix.
- Photon returning Hebrew names with `lang=default`.
- OpenFreeMap tiles containing `name:he` for most labels; the Palestinian-label filter catching the right label.
- Road routes drawing correctly.

## Next steps (stated by Hillel)
- Next MVP: suggest actual venues near the point using Hillel's places API (which API: not recorded).
- Possibly later: public transport, languages other than Hebrew (both explicitly out of scope for now).

## Known risks / ideas (not requested)
- OSRM public demo server may rate-limit or go down → fallback: TomTom key restricted to the site's domain (Hillel agreed this is acceptable if needed).
- Route drawing is sequential (~1 s per person), so 25 people take ~25 s per mode.
