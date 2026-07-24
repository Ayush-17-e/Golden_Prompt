# Security Policy

RPS Arena is a **fully client-side, single-file application**. Understanding its trust model
matters more than a vulnerability count, so this policy leads with the model.

---

## Trust model — what is and isn't protected

> [!IMPORTANT]
> There is **no backend, no server, no account, no telemetry, and no analytics.** Nothing you
> do in the app leaves your browser. The only outbound network requests are to fetch the CDN
> libraries listed below, each of which has an offline fallback.

**Where your data lives.** Entirely on your own device:

| Store | Contents |
|---|---|
| IndexedDB (`rps_db`) | matches, rounds, alerts, audit log, model weights, reports, players |
| localStorage | config, session, achievements, notifications, filter presets, and a mirror of your most recent 200 played matches |

Because there is no server, there is no server-side data to breach, no credential to
exfiltrate to a backend, and no shared multi-user surface. The realistic threats are local:
another user of the same device/browser profile, or a malicious browser extension — neither
of which this app can defend against, and neither of which it claims to.

---

## The authentication is simulated — read this

> [!WARNING]
> The login / profile screen is a **local session *simulation*** for demonstration only.
> Role selection, session expiry, and the password-strength meter are UI behaviour, not a
> security boundary. The session token is **XOR + base64 — obfuscation, not encryption.**
> There is no secret and no server to validate against.
>
> **Do not reuse this login as an authentication pattern, and do not store real secrets in
> it.** It is labelled as a simulation in the UI, and it is repeated here so there is no
> ambiguity.

---

## Third-party libraries

The app loads a small set of pinned CDN libraries. Each has a named degradation path, so a
blocked or compromised CDN downgrades a feature rather than breaking the app:

| Library | If it fails |
|---|---|
| Chart.js | Charts render as accessible data tables |
| jsPDF | PDF export falls back to CSV |
| Fuse.js | Fuzzy search falls back to substring matching |
| Papa Parse | CSV built by a hand-rolled RFC 4180 writer |
| Day.js | Relative times fall back to a manual formatter |

All CDN `<script>` tags carry `crossorigin="anonymous"`. Versions are pinned exactly (never
`@latest`) so the loaded code is reproducible.

---

## Supported versions

| Version | Supported |
|---|---|
| 1.0.x | ✅ |
| < 1.0 | ❌ |

Because the app is a single file with no dependencies to patch, "updating" means replacing
`index.html` with a newer copy.

---

## Reporting a vulnerability

If you find a security issue — for example a stored-XSS vector in how user-entered names or
imported data are rendered, or a way the simulated auth could be mistaken for real — please
report it **privately** rather than opening a public issue.

> [!NOTE]
> **Contact:** _replace this line with your preferred private channel_ — e.g. a GitHub
> private security advisory, or a dedicated security email. (No contact address is hardcoded
> here so that a real one can be chosen deliberately rather than guessed.)

Please include:

- A description of the issue and its impact.
- Steps to reproduce, ideally against a freshly opened `index.html`.
- The browser and version you observed it on.

Since the app runs locally with no server, most reports will concern client-side injection
(untrusted input rendered without escaping) or misleading security *claims* in the UI or
docs. Both are in scope and taken seriously.
