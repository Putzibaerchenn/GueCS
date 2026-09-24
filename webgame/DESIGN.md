# CS Guessr — Design

The game: a GeoGuessr-style game for Counter-Strike 2 maps. You're shown a static first-person screenshot from somewhere on a map — click on the overhead map to guess where.

> **Status: first playable version built** at `webgame/guecs.html` (launched from `webgame/index.html`). Two maps: **Dust II** (20 CS:GO-era wiki screenshots in `assets/images/`, wiki radar as minimap; true-spot coordinates manually corrected by the author via `webgame/calibrate.html` and baked into the tables) and **Mirage** (20 CS:GO-era shots from the wiki gallery's 2020 update + `De_mirage_radar` as minimap; coordinates seeds baked in, all 20 corrected by the author via calibrate → Mirage). The game reads per-map calibration overrides from localStorage on load (`guecs_cal` for Dust II, `guecs_cal_mirage` for Mirage); calibrate.html exports a paste-ready block per map to re-bake them permanently. A map selector sits on the start screen next to the rounds picker. Scoring knobs (`FULL_POINT_RADIUS`, `ZERO_POINT_DIST`, per-map `diagMeters`) are the playtest knobs. NOTE: scoring numbers and the Mirage meter scale are seeded estimates, not playtested. **Visual identity**: a shared `webgame/theme.css` (dark GitHub palette tokens, `.wordmark`, `.btn-primary`, `.pill`, hover/focus system) is linked by all three pages so the launcher, game, and calibrate read as one style.

## The loop

1. A round shows one static, first-person in-game screenshot from a location.
2. **30-second timer**. You study the frame, then click the overhead map to drop a pin. "Lock in" makes it final.
3. Locking in reveals the answer instantly: a line snaps from your pin to the true spot, with the real distance. **The reveal holds 5 seconds, then auto-advances.** No skip button, no early advance — but no forced wait before it appears.
4. If the timer dies without a pin placed: **0 points**. Missed your chance, it's over.
5. A configurable number of rounds per game — **default 5, and can go up to 20** (capped at the map's location count, since locations never repeat). Rounds + map are picked on a small start screen before the game begins.

## Rules

- **No repeats** — a location never appears twice within one game.
- **No speed bonus** — points come from knowledge, not twitch speed (otherwise players who know the maps would hammer games and farm points unfairly).
- **Static screenshots only** in v1: no camera control to build, images are simple files.

## Maps

- **Dust II** — 20 locations from the [Counter-Strike Wiki Dust II Gallery](https://counterstrike.fandom.com/wiki/Dust_II/Gallery) (CS:GO-era), wiki radar as minimap. Coordinates author-calibrated and baked in.
- **Mirage** — 20 locations from the [Mirage gallery](https://counterstrike.fandom.com/wiki/Mirage/Gallery) (CS:GO-era, mostly the Jan 2020 update), `De_mirage_radar` as minimap. Seed coordinates come from official callout-region polygons (TotalCS Mirage callouts page), normalized onto the radar — the two images share orientation, verified by image correlation (identity ≈ 0.79 vs ≤ 0.38 for rotations). Best-effort seeds; refine via calibrate.html → Mirage.
- **Visual consistency rule**: screenshots are **CS:GO-era only** for now (the one consistent style across maps); the 8 CS2 Dust II shots can pad or upgrade individual spots later. A future in-game CS2 capture pass can replace them uniformly.
- **20 locations per map** (configurable rounds means more is better), spread across the whole map — every rough region gets a fair chance. Favour subtle landmarks (crate stacks, distinctive walls, skybox edges) over obvious lane-straight-downs.
- **Next maps**: more wiki galleries (Inferno, Nuke, …), then in-game capture for originality.
- Attribution note: images from the wiki are fan-hosted Valve screenshots; fine for personal use, murky for distribution (same caveat as the radar art).

## Scoring

- A small full-point radius around the true spot; inside it you max out.
- Beyond the radius, points fall off with distance (the farther, the less). Exact numbers to be tuned in playtest.

## Result screen

- Recap showing every round: your guess + the correct spot, all reveal lines on the map at a glance — the story of your own game.
- Then a fresh game can start.

## Minimap art

- Clean, neutral in-game-style **minimap** (traced/redrawn), not a screenshot of the HUD.

## Round screen layout (decided via prototype)

- The screenshot is shown at its **native resolution** (1280×720), centered on a dark stage, scaled down only if the screen is too small. On widescreen/ultrawide monitors nothing is cropped or blown up (full-bleed `cover` was tried first and cut the top/bottom off on widescreen). The minimap is a **bottom sheet** that slides up behind a "Map" button, with the lock-in button beside it. Minimal hero image — guessing feels like a test, not a map-aided search.
- Prototype verdict source: `webgame/prototype.html` (throwaway — rebuild the winner fresh in the real game, don't copy prototype code).

## Round production (researched)

- **Screenshots (launch)**: downloaded from the wiki gallery (full-res originals via each `File:` page); CS:GO-era set for consistency.
- **Screenshots (long-term)**: captured in-game by the author for uniformity/originality. Practice/offline bots match → `sv_cheats 1` → `noclip` to fly, `setpos`/`setang` for exact eye-level placement → F12 (Steam overlay) to shoot. Console `screenshot` commands are dev-only/flaky; F12 is the reliable path.
- **Minimap art**: game files hold the radar texture (extractable via community tools, e.g. cs2-radar-extractor) + a `.txt` with the coordinate mapping. The wiki also hosts the Dust II radar/overview images. Those are Valve assets — fine for a personal project; for distribution either redraw the neutral minimap ourselves or check licensing.

## Future (out of v1)

- **Multiplayer**: in the picture. Reveal will wait until *all* players have guessed. Shape (async rooms vs real-time) undecided — revisit when v1 exists.
- **Daily challenge / endless mode**: bolt-ons, driving retention later.
- More maps (Mirage, Inferno, …) after Dust II.
- 3D in-browser walkabout: the dream version, deferred.

## Not yet decided

- Exact scoring-radius size and fall-off curve (tune in playtest).
- Multiplayer shape.
- Q12 (open): after the recap — always a "Play again"?