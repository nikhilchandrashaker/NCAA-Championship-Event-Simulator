# NCAA Championship Event Monte Carlo Simulator

A single self-contained HTML file (`ncaa-100m-simulator.html`) that simulates
2026 NCAA Indoor & Outdoor Championship finals using Monte Carlo sampling,
built from the real finalist fields in `2026_NCAA_Track_Field_Championships_Dataset.xlsx`.

Open the file directly in any modern browser — no build step, no server,
no install. It loads React, ReactDOM, and Babel from cdnjs at runtime and
transpiles the embedded JSX in-browser.

## What it does

1. Pick a **Championship** (Outdoor / Indoor), **Gender**, and **Event**.
   The dropdowns are driven entirely by what's actually in the dataset, so
   only real event/gender/round combinations are selectable.
2. Click **Run Simulation** to:
   - Draw each entrant's performance from a normal distribution centered on
     their actual final-round mark, `N` times (1,000 or 10,000 draws).
   - Tally win % / top-3 % / median result per entrant.
   - Trigger one animated instance of the event using one of those draws.
3. Track events (100m, hurdles, relays, distance races, etc.) animate as an
   oval, multi-lane track — lane count scales to however many entrants are
   in that event (5 to 16 in this dataset). Field events get their own
   visual: Shot Put and Triple Jump animate a launch-and-landing arc; High
   Jump animates a rise to a bar at the simulated height; Heptathlon (an
   aggregate score, not a single physical motion) shows a fill bar instead.
4. **Pause / Resume** freezes and resumes the in-progress animation.
5. Hurdle events (110m/100m/400m/60m Hurdles) show white hurdle bars across
   each lane. Relay events (4x100m, 4x400m) show dashed yellow bars marking
   the three baton-exchange zones, and each runner is drawn as a ring
   (team color) around a smaller dot that cycles white → yellow → blue →
   orange as the baton moves through the four legs.
6. Track position now scales to the real event distance in laps of a 400m
   oval: 100m ≈ a quarter lap, 400m = 1 lap, 800m = 2 laps, 1500m ≈ 3.75
   laps, up to 10,000m = 25 laps. Shorter races only cover part of the
   track instead of always doing a full loop; longer ones lap it multiple
   times. Hurdle and baton-exchange marks scale with this too, so they land
   at the right point along the actual race distance. Animation speed is
   still compressed for watchability, not real pace.

## Important limitations (read before treating outputs as real predictions)

- **The dataset has one final-round mark per athlete per event.** There is
  no season-best, qualifying, or semifinal data, which means there's no way
  to measure any individual athlete's actual race-to-race or attempt-to-
  attempt variability from this data.
- **The spread used for sampling is a modeled assumption, not a
  measurement**: roughly 0.6–1% of the mark for sprints, up to ~1% for
  longer track races, ~2.5% for jumps/throws, and ~1.5% for Heptathlon
  points. These are reasonable ballpark heuristics for elite meet-to-meet
  variability, not derived from this athlete's history.
- **Track laps and jump/throw arcs are stylized, not physical simulations.**
  One "lap" of animation represents one full race regardless of whether
  it's the 100m or the 10,000m; flight paths for jumps/throws are simple
  cosmetic arcs scaled to the field, not projectile-motion physics.
- Treat every win %, median, etc. as an illustration of what a plausible
  outcome distribution could look like given the assumptions above — not a
  forecast of the actual 2026 result.

## Files

| File | Purpose |
|---|---|
| `ncaa-100m-simulator.html` | The simulator itself (data + logic + UI, all inline). |
| `README.md` | This file. |

## Extending it

The dataset is embedded directly in the HTML as a `DATA` array of objects:
`{c: championship, g: gender, e: event, n: athlete/team name, s: school, v: numeric mark, u: unit type ('time'|'distance'|'points')}`.

To add more events or seasons:
1. Add rows to `2026_NCAA_Track_Field_Championships_Dataset.xlsx` (or a new
   workbook) following the same `Gender / Event / Athlete_or_Team / School /
   Mark / Unit_Type / Round / Is_DNF_or_DNS` columns.
2. Re-export to the same `{c,g,e,n,s,v,u}` JSON shape and swap it into the
   `const DATA = [...]` line in the HTML file.

To make the variance model data-driven instead of assumed, add each
athlete's season-best, qualifying, and semifinal marks to the dataset, then
replace the flat `sdFor()` heuristic with a per-athlete standard deviation
computed from their own results.
