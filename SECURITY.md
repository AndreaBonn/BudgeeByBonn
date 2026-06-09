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

### Brute-force protection

Firebase Authentication blocks repeated failed login attempts with a `too-many-requests` response.

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
- Third-party API keys (Telegram, Google) are stored server-side in Firebase Cloud Functions configuration, not in frontend code.

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
| Strict-Transport-Security | `max-age=31536000; includeSubDomains` | Forces HTTPS for 1 year, including subdomains |
| X-Content-Type-Options | `nosniff` | Prevents MIME sniffing |
| X-Frame-Options | `DENY` | Blocks embedding in iframes (clickjacking protection) |
| Referrer-Policy | `strict-origin-when-cross-origin` | Limits the referrer sent to third parties |
| Permissions-Policy | `camera=(), microphone=(), geolocation=()` | Disables unused browser features |

### Content Security Policy (CSP)

A Content Security Policy controls which resources the browser can load:

- Scripts: only from the app's own domain and trusted sources (Firebase, Google APIs, Chart.js CDN)
- Styles: only from the app and Google Fonts
- Connections: only to Firebase, Google APIs, the Telegram API and the exchange rate API
- Plugins: fully blocked (`object-src 'none'`)
- Base URL: locked to the app's own domain (`base-uri 'self'`)

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
- Repeated errors trigger alerts on a private Telegram channel.

---

## Offline security

### Service Worker

Budgee works offline through a Service Worker that caches essential resources:

- Network-First strategy: fresh data is always tried first; the cache is used only when offline.
- Version-scoped cache: each app version has its own cache; old caches are cleaned up on update.
- API exclusion: calls to Firebase, Google and Telegram are never cached and always require network.

### Offline data sync

- Transactions created offline land in a pending changes queue.
- When connectivity returns, pending changes are synced with retry logic (up to 3 attempts with exponential backoff).
- If the sync fails, the user is notified and data is kept locally until the next successful sync.

---

## Dependency security

### npm packages

- All dependencies use pinned versions (`==`) to avoid unexpected updates.
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
- Data portability: you can export all your data to CSV at any time.
- Right to deletion: you can delete your account and all associated data from the settings.

---

## Known limitations and roadmap

Transparency is part of security. Here is what Budgee currently does not do, and what is planned:

| Limitation | Status | Notes |
|-----------|--------|-------|
| Two-factor authentication (2FA) | Planned | Currently relies on email/password plus email verification |
| Client-side encryption | Not implemented | Data is encrypted at rest by Firebase, but not end-to-end |
| CSP `'unsafe-inline'` directive | Being phased out | Required for legacy code; the EventDelegate refactor is replacing it |
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

*Last updated: April 2026*

</div>
