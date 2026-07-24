# Changelog

All notable changes to **RPS Arena** are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [1.0.0] — 2026-07-24

First complete, verified release. A single, self-contained `index.html` that runs by
double-clicking the file — no build step, no package manager, no server, no account.

### Added

- **The opponent** — four difficulty tiers (Easy uniform-random, Medium frequency, Hard
  order-2 Markov, Adaptive five-predictor ensemble with online multiplicative-weights
  learning). The Adaptive model never peeks (trains only on pre-throw history) and forces
  12% exploration so it stays un-invertible. See `justification.md` §2.
- **Eight hash-routed pages** — Dashboard, Play, Analytics, History, Leaderboard, Settings
  (seven tabs), Profile, and Help. See `README.md` §5.
- **Four game modes** — Classic, Speed, Tournament, Practice.
- **AI reasoning panel** — after every throw the model states, in plain language, which
  pattern it spotted, which predictor voted, and at what confidence.
- **Synthetic seeding** — a Mulberry32 PRNG at `seed = 42` generates 640 matches /
  ~18,000 rounds / 20 leaderboard players across five behavioural archetypes, so no screen
  is ever empty on first run. Records are flagged `synthetic:true` and labelled in exports.
- **Three-tier storage** — IndexedDB (`rps_db` v1) primary, a localStorage mirror for
  played matches, and an in-memory working set.
- **Design system** — one indigo accent, three elevation planes, a colour-blind-safe
  6-colour data ramp read from CSS at runtime, and 83 hand-drawn inline SVG icons on one
  24×24 grid. **Zero emoji.**
- **Three themes** — dark (default), light, and a genuine high-contrast mode.
- **Five languages** — English, Spanish, French, Arabic (RTL), Chinese, via a
  `TRANSLATIONS` table with no hardcoded UI strings.
- **Accessibility** — WCAG 2.1 AA across 22 measured contrast pairs (8 reach AAA), skip
  link, keyboard reachability, modal focus trapping, `aria-live` results, and 91 `aria-*`
  attributes.
- **Exports** — CSV, JSON, PDF, and per-chart PNG, each with a named degradation path when
  its CDN library is unavailable.

### Fixed

Twelve defects found during the build (full root-cause table in `justification.md` §10).
The six structural browser traps are encoded into the reusable prompt in `prompt.md` §4.

- Boot hung forever on `file://` because Chrome fires **no** IndexedDB event on an opaque
  origin — every IDB call is now deadline-raced and degrades to localStorage.
- KPIs displayed `0` when `requestAnimationFrame` was starved in a background tab — the
  final value is now written first and animation layered on top with a `setTimeout` guard.
- Chart.js crashed (`this._fn is not a function`) from replacing `Chart.defaults.animation`
  wholesale — defaults are now mutated in place.
- `AudioContext` autoplay warning on load — construction is deferred until an observed
  gesture.
- Opaque `"Script error."` in the console — `crossorigin="anonymous"` added to all 12 CDN
  tags.
- Icon names rendered as text (`"dashboard Dashboard"`), a first-load confetti storm,
  invisible light-mode gridlines, a sub-AA `--text-tertiary` token, NUL bytes in a regex,
  and mis-highlighted settings tabs — all resolved.

### Security

- The login is a **local session simulation** only. The token is XOR + base64
  (obfuscation, not encryption) and is labelled as such in the UI. See `SECURITY.md`.

### Known limitations

- On `file://`, IndexedDB is disabled, so the synthetic corpus regenerates each load
  (deterministically) and played matches are restored from the 200-match localStorage
  mirror. Serve over `http://localhost` for full persistence.
- Verified on Chrome/Chromium only. Firefox, Safari and Edge are targeted with feature
  detection and fallbacks but **not** measured. No Lighthouse score is claimed.

[1.0.0]: https://example.com/rps-arena/releases/tag/v1.0.0
