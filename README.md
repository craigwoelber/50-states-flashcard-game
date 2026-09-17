# 50 States

Flash cards for learning the fifty US states by their position on the map. One
state lights up, you say the name out loud, tap to check, then rate yourself.

## Deploy

Drop all five files in a repo root (or a `docs/` folder) and turn on GitHub
Pages. Every path in the app is relative, so it works fine from a project
subpath like `username.github.io/50-states/`.

```
index.html
manifest.json
sw.js
icon-192.png
icon-512.png
icon-maskable-512.png
```

Open the Pages URL on the phone and use Share → Add to Home Screen (iOS) or
the install prompt (Android). After the first load it works with no signal.

## How the practice works

Five regions unlock in order: Northeast (9), Southeast (14), Midwest (12),
Southwest (4), West (11). A state counts as known after two correct answers in
a row, and a region is done when all of its states are known.

Inside a region the cards run on three Leitner boxes. A miss drops a state back
to box 1 and it returns in about three cards; each hit promotes it and pushes it
further out. Roughly one card in five is pulled from regions already finished,
so the Northeast doesn't fade while the West is being learned. A missed review
card goes back into rotation but doesn't un-finish the region.

Tapping any unlocked region from the home screen — finished or not — always
drops you into its cards. A region you've already finished just keeps cycling
its own states for review instead of sending you back to the "done" screen,
which only shows up once, right when you first finish a region.

Rhode Island, Connecticut, Delaware, New Jersey, Vermont, Massachusetts, New
Hampshire, Maryland and Hawaii are too small to read on a phone, so those cards
add a zoomed inset with a dashed locator box on the main map showing where it
is looking.

Once a state is known, its review cards occasionally ask for its capital
instead of its name — same map, same flip-to-reveal, same Leitner boxes, just
a separate box/streak per state so learning the capital doesn't reset name
progress (or vice versa). Capitals never block a region from finishing; they
only ride along on the review draws that already visit known states.

## Data

Progress lives in IndexedDB (`states-quiz`), two stores: `states` keyed by
postal code holding box, streak, hits, misses and the due counter for the
name — plus a parallel `capBox`/`capStreak`/`capHits`/`capMisses`/`capDue` set
for the capital — and `meta` holding the current region, card counter and
finished regions. Nothing leaves the phone. "Start over" on the region screen
wipes both stores.

State outlines are pre-projected Albers USA paths generated from
[us-atlas](https://github.com/topojson/us-atlas) (`states-albers-10m`, public
domain) and inlined in `index.html`, so there is no map request at runtime. DC
is drawn on the map but never asked about.

## Changing it

When you edit `index.html`, bump `VERSION` at the top of `sw.js`. Without that
the service worker keeps serving the cached build and the phone never sees your
change.

Things that are easy to adjust, all near the top of the script in `index.html`:

| Constant | Does |
| --- | --- |
| `MASTER_STREAK` | correct answers in a row before a state counts as known |
| `GAP` | cards to wait before a state returns, per Leitner box |
| `MISS_GAP` | cards to wait after a miss |
| `REVIEW_RATE` | share of cards drawn from finished regions |
| `CAP_RATE` | share of those review draws that ask for the capital instead of the name |
| `REGIONS` | region names and which states belong to each |
| `INSET` | which states get the zoomed inset |
