# Sprint Interval Timer

A single-file interval timer for a 12-week sprint program: short all-out efforts with long recovery
walks, followed by a continuous run at a steady target heart rate. No build step, no dependencies to
install, no backend — one `index.html` that runs straight off GitHub Pages.

**Live:** https://k1832.github.io/hiit-timer/

---

## Session structure

```
Warm-up        3:00        brisk walk → easy jog        60–70% HRmax
Sprint × N     0:20 each   all-out, RPE 9–10            no HR target
Recovery × N   2:00 each   walk                         nothing shown
Continuous     X:00        steady                       75–85% HRmax
Cool-down      3:00        walk, fixed
```

`total = 180 + N × 140 + X + 180` seconds

| Week | Sprints | Continuous | Total |
|---:|---:|---:|---:|
| 1–2 | 3 | 8 min | 21:00 |
| 3–4 | 4 | 8 min | 23:20 |
| 5–6 | 4 | 10 min | 25:20 |
| 7–8 | 5 | 10 min | 27:40 |
| 9–10 | 5 | 12 min | 29:40 |
| 11–12 | 6 | 12 min | 32:00 |

Three sessions a week, at least one rest day between. Only one variable changes per step — sprint count
and continuous duration never increase together.

## Why recovery is 120 seconds, not 40

At 40 seconds the later sprints stop being all-out and their quality collapses. Sprint count per session
does not drive VO2max gains (Hutchinson 2026), so fewer, better reps is the efficient trade. Gillen 2016
got +19% VO2peak from 3 × 20 s all-out with 2-minute recovery, matching 45 minutes of continuous
exercise. The time freed up goes into the continuous run.

The recovery blocks are kept deliberately quiet: a countdown, one short prompt, and nothing else. Skip
and Stop stay reachable but are rendered small, so they are hard to hit by accident. The cool-down is
fixed at 3 minutes and cannot be extended.

## Heart rate

**The app reads no sensors and stores no measurements.** It only computes target zones so you can read
them off whatever watch or strap you already wear.

- HRmax defaults to the Tanaka formula, `208 − 0.7 × age` (187 bpm at 30). Override it with a measured value if you have one.
- Zones can be calculated as **%HRmax** or, if you enter a resting HR, **Karvonen** (`(HRmax − RHR) × intensity + RHR`).
  Karvonen returns higher targets for the same intensity, so the active method is always labelled on screen.
- Sprints have no HR target by design: heart rate lags, 20 seconds never reaches the ceiling, and the peak
  arrives 20–40 seconds *after* the sprint. The only sprint criterion is subjective all-out effort.

## What it stores

Session date, week, sprint count, continuous duration, elapsed time, RPE (1–10), a recovery-discomfort
rating (0–100), and an optional note.

Storage is `localStorage` in the browser on that device. Nothing is uploaded. Settings → **Export as JSON**
dumps everything; clearing site data erases it.

## Running locally

Just open the file:

```sh
git clone https://github.com/k1832/hiit-timer.git
cd hiit-timer
open index.html          # or: xdg-open index.html
```

`file://` works fine. If you want a local server anyway:

```sh
python3 -m http.server 8000   # then http://localhost:8000
```

## Deploying to GitHub Pages

The repo *is* the site — `index.html` at the root, no build.

1. Push to `main`.
2. Repo **Settings → Pages**.
3. **Source: Deploy from a branch**, branch `main`, folder `/ (root)`. Save.
4. It publishes at `https://<user>.github.io/<repo>/` within a minute or two.

To use a custom domain, add a `CNAME` file at the root containing the domain and point DNS at GitHub Pages.

## Implementation notes

- React 18 + Babel Standalone + Tailwind, all from CDNs, compiled in the browser. That is why there is no
  build step; it also means the page needs network access on first load and is not usable fully offline.
- The countdown is **clock-based** (`Date.now()` deltas), not an interval counter, so it neither drifts nor
  stalls when the browser throttles background tabs.
- Screen Wake Lock is requested during a session and re-acquired when the tab becomes visible again, so the
  phone does not sleep mid-workout. Browsers without the API just run without it.
- Audio cues use the Web Audio API and are unlocked by the START tap. Cues fire on every phase change, on
  each of the last 5 seconds of a sprint, and once at 30 seconds left in a recovery block. Spoken phase
  names use `speechSynthesis` and can be turned off separately.

## Disclaimer

This program includes all-out sprints. If you have any cardiovascular history or risk factors, get medical
clearance before starting.

## References

- Gillen et al. 2016, *PLOS ONE* 11:e0154075
- Hutchinson et al. 2026, *Scand J Med Sci Sports*
- Tanaka et al. 2001, *J Am Coll Cardiol* 37:153–156
