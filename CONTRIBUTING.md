# Contributing to RPS Arena

Thanks for your interest. This project has one unusual property that shapes every rule
below: **it is a single, self-contained `index.html` with no build step.** Please read the
constraint before opening a pull request.

---

## The one non-negotiable constraint

> [!IMPORTANT]
> The deliverable is ONE `index.html` that runs by double-clicking the file — no build
> step, no package manager, no server, no account. Any change that requires `npm install`,
> a bundler, a transpiler, or a framework runtime **cannot be accepted**, because it
> destroys the property that defines the artifact's value.

If a change seems to need a toolchain, it almost certainly has a vanilla equivalent. Ask in
an issue first.

---

## Project layout

| File | What it is | Edit it when… |
|---|---|---|
| `index.html` | The entire application (HTML + CSS + one IIFE of ES2022) | You change behaviour, style, or copy |
| `README.md` | User-facing guide | Behaviour, features, or setup change |
| `justification.md` | Decision log — why every choice was made | You make or reverse a design decision |
| `prompt.md` | Prompt lineage + the reusable hardened prompt | A new structural trap is discovered |
| `CHANGELOG.md` | Versioned history | Every user-visible change |

---

## Code conventions

These mirror how `index.html` is already written — match the surrounding code.

- **Vanilla ES2022**, wrapped in one IIFE. No globals except the single `window.RPS` debug
  handle.
- **Named modules in dependency order.** New functionality belongs in the module that owns
  its concern; declare it after everything it depends on.
- **One frozen `CONFIG` object.** No magic numbers anywhere else — add a named key instead.
- **All user-visible strings go in `TRANSLATIONS`.** Never hardcode UI copy; add the key to
  every one of the five languages.
- **JSDoc every function** with `@param` / `@returns`.
- **Guard every DOM query** and wrap every async call, storage write, chart construction,
  and export in `try/catch`.
- **No emoji, anywhere.** Icons are inline SVG on the shared 24×24 grid (1.75 stroke, round
  joins, `currentColor`). Add new marks to the `Icons` module in that style. See
  `justification.md` §6 for the reasoning.
- **Charts read colour from CSS custom properties at runtime** — never hardcode chart
  colours, or they break in one theme.

### Robustness rules — do not regress these

These encode real, observed failures (`prompt.md` §4 / `justification.md` §10). A PR that
reintroduces one will be asked to fix it:

1. Every `Promise` wrapper around an event-driven browser API must race a timeout and
   degrade. On `file://`, `indexedDB.open()` fires **no** event at all.
2. Anything a user created must also mirror to `localStorage`. Regenerable seed data need
   not.
3. Never animate a displayed value up from zero as its only assignment — write the final
   value first, then layer animation with a `setTimeout` guarantee.
4. Mutate third-party default objects in place; never replace them wholesale.
5. Keep `crossorigin="anonymous"` on every CDN `<script>`.
6. Construct `AudioContext` only after an observed user gesture.
7. Suppress celebratory UI until the app is `ready`.

---

## Verifying your change

There is no CI in the box; verification is a procedure you run locally and **report** in the
PR. This is the exact procedure from `prompt.md` §10 that caught eleven of the twelve
logged defects.

1. **Syntax.** Extract the `<script>` block to `app.js` and run:
   ```bash
   node --check app.js
   ```
2. **Every route, zero console output.** Load all 8 routes in headless Chrome with console
   capture; target **0 errors and 0 warnings**:
   ```bash
   chrome --headless=new --enable-logging=stderr --v=0 \
          --virtual-time-budget=45000 --dump-dom \
          "file:///ABSOLUTE/PATH/index.html#/analytics"
   ```
3. **Functional harness.** Drive the real app through `window.RPS` and assert domain rules,
   persistence, filter/sort/pagination, exports, and the AI evidence standard. Report
   pass/fail counts.
4. **Persistence.** Run the harness **twice** on the same browser profile to prove a played
   match survives a reload (match count should rise 640 → 641 → 642). Note: use a
   `localhost` origin, not `file://`, for real IndexedDB persistence.
5. **Contrast.** Re-run the token-parsing contrast audit for all pairs; keep every pair ≥
   AA (4.5:1 for normal text).
6. **Look at it.** Render screenshots in **both** themes and inspect them. Passing tests do
   not mean it looks right — this step alone found four real bugs. See `justification.md` §9.

---

## Pull request checklist

- [ ] Still a single `index.html`, still opens by double-click, still no build step.
- [ ] No emoji added; new icons are inline SVG in the shared style.
- [ ] New strings added to **all five** languages in `TRANSLATIONS`.
- [ ] New numbers live in `CONFIG`, not inline.
- [ ] The seven robustness rules above are intact.
- [ ] Verification steps 1–6 run; results (including "what I did not verify") reported in
      the PR description.
- [ ] `CHANGELOG.md` updated under a new `[Unreleased]` or version heading.
- [ ] `justification.md` updated if a design decision changed.

---

## Honesty requirement

Match the tone of the existing docs: **never claim a metric you did not measure.** If you
did not run Lighthouse, say no score is claimed. If you tested one browser engine, say so.
State every deliberate deviation, with reasoning, when you make it. A PR that reports an
honest gap is stronger than one that claims a coverage it can't back.
