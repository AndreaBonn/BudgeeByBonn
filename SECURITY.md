# Budgee - Security documentation

<div align="center">

[![Italiano](https://img.shields.io/badge/Leggi_in_Italiano-009246?style=for-the-badge)](./SECURITY_IT.md) &nbsp; [![Back to README](https://img.shields.io/badge/Back_to_README-4A90E2?style=for-the-badge)](./README.md)

</div>

---

This document describes the security measures in place in Budgee. Budgee handles sensitive financial information, so security choices are written down in the open and kept up to date.

---

## Table of contents

- [Authentication and account security](#authentication-and-account-security)
- [Data protection](#data-protection)
- [Database security](#database-security)
- [Network security](#network-security)
- [Application security](#application-security)
- [Offline security](#offline-security)
- [Dependency security](#dependency-security)
- [Privacy](#privacy)
- [Known limitations and roadmap](#known-limitations-and-roadmap)

---

## Authentication and account security

### Email and password authentication

Budgee uses Firebase Authentication for all account management.

- Email verification is mandatory; new accounts must verify their address before they can use any feature.
- Password requirements:
  - At least 8 characters
  - At least one uppercase letter (A-Z)
  - At least one lowercase letter (a-z)
  - At least one digit (0-9)
- Password reset is handled by Firebase via signed email links. Budgee never stores or transmits passwords in plain text.

### Session management

- Sessions are managed by Firebase with JWT tokens.
- Sessions expire automatically following Firebase's policies.
- Logout clears local data: session tokens, cached data and user preferences.

### Account deletion

Account deletion goes through several confirmation steps:

1. Explicit confirmation dialog
2. Re-authentication is required: you must enter your password again
3. All user data is deleted from the database (expenses, income, budgets, investments, financings, goals, settings)
4. The Firebase Authentication account is removed
5. All locally cached data is cleared

Firestore does not delete subcollections along with their parent document. Without an explicit visit they stay orphaned and invisible, so the deletion walks a single registry of everything an account is made of, the same one the export reads. The user document is marked before the work starts and removed last: an interrupted deletion leaves a trace that the next sign-in recognises and resumes.

### Brute-force protection

Firebase Authentication blocks repeated failed login attempts with a `too-many-requests` response.

### Accepting the terms of use

Registering requires accepting the terms of use and the privacy notice. The box is never pre-ticked, and both documents are linked next to the point of acceptance, not only at the foot of the page.

The acceptance is recorded, and the record is built so that nobody can alter it afterwards:

- it is created and read, never updated: the database rules refuse `update`;
- one constraint does the work: `acceptedAt == request.time`. It forces the client to use the server's clock, the only value that satisfies the equality. Without it, a client-chosen date would be accepted and nothing would look out of place;
- the document id is `<document>-<version>`, so re-accepting the same version cannot produce a second record;
- the rules pin the exact key set, the document name, the version format and a non-empty locale, so the stored shape cannot drift from what the reader expects.

What this proves, stated without overselling it: that an authenticated registration accepted a specific version of the text, at a moment it could not pick, and that nobody rewrote it afterwards. Not who was at the keyboard, and not what they read. It is a click-wrap, not a signature.

---

## Data protection

### Encryption

| Layer | Method | Details |
|-------|--------|---------|
| In transit | TLS/HTTPS | All connections between your browser and Firebase are encrypted. HSTS is enforced for 1 year, including subdomains. |
| At rest | AES-256 | Firebase Firestore encrypts stored data with AES-256. The setting is managed by Google Cloud infrastructure and cannot be turned off. |

### Data isolation

Per-user data is isolated:

- Database security rules require that you can only read and write your own data.
- There is no admin panel that exposes other users' data.
- Every database operation checks the authenticated user's identity before proceeding.

### Sensitive token handling

- Google Drive OAuth tokens live in `sessionStorage` (cleared when the tab closes), not in persistent storage.
- User credentials are never stored locally; only the Firebase-managed session token is kept.
- Budgee has no server-side code of its own, so there is no place to hide a secret: the Google OAuth client ID travels in the frontend, as that protocol expects, and the permission to act rests on the consent you grant and on the `drive.file` scope, not on the ID being secret.
- The Gemini API key you supply for receipt scanning lives in your own account, and is excluded at the source from both the data export and the Drive backup. A key that ends up inside a backup file stays readable for as long as the file exists.
- The Drive integration uses the `drive.file` scope: Budgee can only see and touch the files it created. The rest of your Drive is invisible to it.

---

## Database security

### Firestore security rules

Budgee follows a default-deny approach: every path is denied unless an explicit rule allows it.

Key protections:

- Owner-only access: every read and write requires authentication and verifies that the requester owns the document.
- Field validation rejects data that does not match expected types, ranges and sizes:
  - Monetary amounts must be positive numbers below 10 million
  - Strings (descriptions, names) have a 200-character maximum
  - Currency must be in the approved list (EUR, USD, GBP, PLN)
  - Interest rates must be between 0 and 100
  - Arrays have size limits (for example, 10,000 expenses per user)
- Document size limits cap the number of fields and the overall document size.
- Settings documents are validated field by field rather than accepted wholesale. The meal voucher one, for instance, allows only five known keys, a currency from the supported list, an opening balance of zero or more, and a `YYYY-MM-DD` date with month and day in range: `2026-13-01` is not a date, and a lexical comparison on it would sort wrongly.
- The rules are exercised against the Firestore emulator by a dedicated suite (`npm run check:rules`), covering both the cases that must be accepted and the ones that must be refused. A permission that is too broad shows up only when something tries to walk through it.
- Error logging is write-only from the client; reports cannot be read back, which avoids information leakage.
- Any path not explicitly allowed is blocked:

  ```
  match /{document=**} {
    allow read, write: if false;
  }
  ```

---

## Network security

### HTTP security headers

Budgee sets these headers on every response:

| Header | Value | Purpose |
|--------|-------|---------|
| Strict-Transport-Security | `max-age=31556926; includeSubDomains; preload` | Forces HTTPS for 1 year, including subdomains. This is the value served on the `web.app` domain, which is already on the browsers' preload list; the project config declares an equivalent one |
| X-Content-Type-Options | `nosniff` | Prevents MIME sniffing |
| X-Frame-Options | `DENY` | Blocks embedding in iframes (clickjacking protection) |
| Referrer-Policy | `strict-origin-when-cross-origin` | Limits the referrer sent to third parties |
| Permissions-Policy | `camera=(), microphone=(), geolocation=()` | Disables unused browser features |

### Content Security Policy (CSP)

A Content Security Policy controls which resources the browser can load:

- Scripts: only from the app's own domain and from the Google origins used for Firebase, the APIs and sign-in. No `'unsafe-inline'`: the handlers written inside the markup have all been removed
- Styles: only from the app and Google Fonts
- Connections: only to Firebase, Google APIs and the exchange rate API
- Plugins: fully blocked (`object-src 'none'`)
- Base URL: locked to the app's own domain (`base-uri 'self'`)

Chart.js and SheetJS do not come from a CDN: they are served from the app's own domain, so no third party can change what they contain.

### Cache control

- HTML pages are revalidated on every request (never served stale).
- JavaScript and CSS are cached for 24 hours; content-hashed filenames ensure fresh versions on update.
- Images and fonts are cached for 1 year (immutable assets with content hashes).

---

## Application security

### XSS prevention

All user-provided data (descriptions, category names, notes) is sanitized before being rendered in the interface:

- HTML escaping converts `<`, `>`, `"`, `'`, `&` to safe equivalents.
- Attribute escaping adds extra protection for data placed inside HTML attributes.
- The Content Security Policy restricts which scripts can execute in the browser.

### Input validation

Every input is validated on the client and enforced on the server:

- Numeric fields are checked for valid format, positive value and a sane range.
- Text fields are trimmed and checked for minimum and maximum length.
- Date fields are validated for the expected format.
- Currency fields must match the allowed list.
- Server enforcement: even if the client validation is bypassed, Firestore security rules reject invalid data.

### Event delegation security

Budgee uses an `EventDelegate` system that centralises event handling with explicit selector allowlists. This reduces the attack surface compared to inline event handlers.

### Error handling

- User-facing errors show generic messages, never stack traces or internal paths.
- Error logging to the database is rate-limited (max 20 per session) and uses a strict field whitelist; no sensitive data, file paths or source code is ever stored.
- The log is writable but not readable by the app: it is consulted from the Firebase console. There is no automatic alerting on errors.

---

## Offline security

### Service Worker

Budgee works offline through a Service Worker that caches essential resources:

- Network-First strategy: fresh data is always tried first; the cache is used only when offline.
- Version-scoped cache: each app version has its own cache; old caches are cleaned up on update.
- API exclusion: calls to Firebase and Google are never cached and always require network.

### Handing over to a new version

A new version no longer takes control while the open page is still running the old code. That code asks for files the current release no longer serves, and the result is an app breaking halfway through an operation with no explanation. The new version now waits and takes over only when the user accepts; with several tabs open the handover applies to all of them, because a tab left on the old code is exactly the case this mechanism exists to avoid.

### Offline data sync

- Transactions created offline land in a pending changes queue.
- When connectivity returns, pending changes are synced with retry logic (up to 3 attempts with exponential backoff).
- If the sync fails, the user is notified and data is kept locally until the next successful sync.

---

## Dependency security

### npm packages

- Almost every development dependency is pinned to an exact version; three (`vite`, `axe-core`, `@firebase/rules-unit-testing`) still use a `^` range. The lockfile is committed either way, so every install reproduces the tree that was checked.
- `npm audit` runs before deployment to check for known vulnerabilities.
- Security patches are applied promptly when vulnerabilities are disclosed.

### CDN dependencies

- Firebase SDK scripts loaded from CDN include Subresource Integrity (SRI) hashes; the browser refuses tampered files.
- A dedicated audit script checks CDN dependencies against the [OSV.dev](https://osv.dev/) vulnerability database.
- Chart.js is hosted locally to remove the runtime external dependency.

### CI/CD pipeline

- `npm audit --audit-level=high` runs on every push and blocks deployment if a high-severity vulnerability is found.
- The CDN dependency check warns about externally loaded libraries with known issues.
- ESLint rules catch common security smells (unused variables, shadowed variables, loose equality).

---

## Privacy

- No analytics, no tracking: no Google Analytics, no Facebook Pixel, no other tracker.
- No ads.
- No data sharing: financial data is never sent to third parties, except the services you connect explicitly (such as Google Drive).
- Data portability: you can export the whole account at any time, as a ZIP holding a complete JSON plus a CSV per section. It is read from the database rather than from what the app has in memory, so nothing is silently left out.
- Right to deletion: you can delete your account and all associated data from the settings.
- Receipt scanning is off unless you turn it on. It asks for a consent of its own, separate from using Budgee, because the image reaches Google and a receipt can reveal medicines, medical visits and habits. The consent is versioned: if the notice changes in substance, the question comes back rather than the old answer being reused.
- A [privacy notice](https://financial-management-by-bonn.web.app/src/pages/privacy.html) in Italian and English states what is collected and why. It is also reachable from the sign-in screen, before registering: collection starts at that moment, and GDPR art. 13 wants the notice available there, not afterwards.
- The [terms of use](https://financial-management-by-bonn.web.app/src/pages/terms.html) state what Budgee is and is not. The tax sections are informational, the estimates come from the data you enter, and anything with tax consequences should be checked by an accountant. A notice repeats this inside the wizard, because nobody opens the terms before using a calculator.
- The notice and the terms exist only in the languages where a person has read the text. A machine translation of a binding document is not a translation.
- Archives that Budgee builds sanitise the entry names taken from Drive. Those names are not under Budgee's control and can carry path separators or directory traversal, and delivering a harmless archive is the responsibility of whoever produced it.

---

## Known limitations and roadmap

Transparency is part of security. Here is what Budgee currently does not do, and what is planned:

| Limitation | Status | Notes |
|-----------|--------|-------|
| Two-factor authentication (2FA) | Planned | Currently relies on email/password plus email verification |
| Client-side encryption | Not implemented | Data is encrypted at rest by Firebase, but not end-to-end |
| `'unsafe-inline'` on styles | Left over | Scripts no longer need it. It remains on styles, for the `style` attributes written into the markup: removing it means moving all of them into classes |
| Two CDNs in the script allowlist | To be narrowed | `cdn.jsdelivr.net` and `cdnjs.cloudflare.com` stayed in the policy from when Chart.js came from there. No script loads from those origins today, so the policy is wider than it needs to be |
| Read rate limiting on the database | Not implemented | Firebase does not offer client-side read rate limiting; monitoring is server-side |

---

## Reporting a vulnerability

If you find a security issue, please report it privately:

- Email: [andreabonacci95@protonmail.com](mailto:andreabonacci95@protonmail.com)
- Subject: `[SECURITY] Budgee - Brief description`
- Include steps to reproduce and any relevant details

Reports are read and answered as quickly as possible. Please do not disclose the issue publicly until it is addressed.

---

<div align="center">

**© 2025-2026 Andrea Bonacci**

*Last updated: September 2026*

</div>
