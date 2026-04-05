# 🔐 Budgee — Security Documentation

<div align="center">

[![Italiano](https://img.shields.io/badge/🇮🇹_Leggi_in_Italiano-009246?style=for-the-badge)](./SECURITY_IT.md) &nbsp; [![Back to README](https://img.shields.io/badge/📖_Back_to_README-4A90E2?style=for-the-badge)](./README.md)

</div>

---

This document describes the security measures implemented in Budgee to protect your data and ensure a safe user experience. Budgee handles sensitive financial information, and security is a core part of its design — not an afterthought.

---

## 📑 Table of Contents

- [Authentication & Account Security](#-authentication--account-security)
- [Data Protection](#-data-protection)
- [Database Security](#-database-security)
- [Network Security](#-network-security)
- [Application Security](#-application-security)
- [Offline Security](#-offline-security)
- [Dependency Security](#-dependency-security)
- [Privacy](#-privacy)
- [Known Limitations & Roadmap](#-known-limitations--roadmap)

---

## 🔑 Authentication & Account Security

### Email & Password Authentication

Budgee uses **Firebase Authentication**, a battle-tested identity platform by Google, for all account management.

- **Email verification** is mandatory — new accounts must verify their email address before accessing any feature
- **Password requirements** enforce a minimum standard of complexity:
  - At least 8 characters
  - At least one uppercase letter (A-Z)
  - At least one lowercase letter (a-z)
  - At least one number (0-9)
- **Password reset** is handled entirely by Firebase via secure email links — Budgee never stores or transmits passwords in plain text

### Session Management

- Authentication sessions are managed by Firebase with **JWT tokens**
- Sessions expire automatically according to Firebase's security policies
- **Logout** clears all local data — session tokens, cached data, and user preferences

### Account Deletion

Deleting your account is a multi-step process with strong safeguards:

1. Explicit confirmation dialog
2. **Re-authentication required** — you must enter your password again to prove identity
3. All user data is deleted from the database (expenses, income, budgets, investments, financings, goals, settings)
4. The Firebase Authentication account is permanently removed
5. All local cached data is cleared

### Brute-Force Protection

Firebase Authentication includes built-in protection against brute-force login attempts. After multiple failed login attempts, the system temporarily blocks further attempts with a `too-many-requests` response.

---

## 🛡️ Data Protection

### Encryption

| Layer | Method | Details |
|-------|--------|---------|
| **In transit** | TLS/HTTPS | All connections between your browser and Firebase are encrypted. HSTS (HTTP Strict Transport Security) is enforced with a 1-year duration, including subdomains. |
| **At rest** | AES-256 | Firebase Firestore automatically encrypts all stored data using AES-256 encryption. This is managed by Google Cloud infrastructure and cannot be disabled. |

### Data Isolation

Every user's data is completely isolated:

- Database security rules enforce that **you can only read and write your own data**
- There is no admin panel or shared data — even the developer cannot access individual user data through the app
- Each database operation checks the authenticated user's identity before proceeding

### Sensitive Token Handling

- **Google Drive OAuth tokens** are stored in `sessionStorage` (automatically cleared when the browser tab is closed), never in persistent storage
- **User credentials** are never stored locally — only the Firebase-managed session token
- **API keys** for third-party services (Telegram, Google) are stored server-side in Firebase Cloud Functions configuration, never exposed in the frontend code

---

## 🗄️ Database Security

### Firestore Security Rules

Budgee uses a **whitelist approach** to database security — everything is denied by default, and only specific operations are explicitly allowed.

**Key protections:**

- **Owner-only access** — every read and write operation requires authentication and verifies that the requesting user owns the document
- **Field validation** — the database rejects data that doesn't match expected types, ranges, and sizes:
  - Monetary amounts must be positive numbers below a reasonable maximum (10 million)
  - Strings (descriptions, names) have maximum length limits (200 characters)
  - Currency values must be from an approved list (EUR, USD, GBP, PLN)
  - Interest rates must be between 0% and 100%
  - Arrays have size limits (e.g., max 10,000 expenses per user)
- **Document size limits** — prevents abuse by limiting the number of fields and overall document size
- **Write-only error logging** — error reports can be created but never read or modified from the client, preventing information leakage
- **Default deny** — any database path not explicitly allowed is automatically blocked:
  ```
  match /{document=**} {
    allow read, write: if false;
  }
  ```

---

## 🌐 Network Security

### HTTP Security Headers

Budgee configures the following security headers on every response:

| Header | Value | Purpose |
|--------|-------|---------|
| **Strict-Transport-Security** | `max-age=31536000; includeSubDomains` | Forces HTTPS for 1 year, including subdomains |
| **X-Content-Type-Options** | `nosniff` | Prevents browsers from guessing file types (MIME sniffing attacks) |
| **X-Frame-Options** | `DENY` | Prevents the app from being embedded in iframes (clickjacking protection) |
| **Referrer-Policy** | `strict-origin-when-cross-origin` | Limits referrer information sent to third parties |
| **Permissions-Policy** | `camera=(), microphone=(), geolocation=()` | Disables unnecessary browser features (camera, microphone, geolocation) |

### Content Security Policy (CSP)

A Content Security Policy controls which resources the browser is allowed to load:

- **Scripts**: only from the app's own domain and trusted sources (Firebase, Google APIs, Chart.js CDN)
- **Styles**: only from the app and Google Fonts
- **Connections**: only to Firebase, Google APIs, Telegram API, and the exchange rate API
- **Plugins**: completely blocked (`object-src 'none'`)
- **Base URL**: locked to the app's own domain (`base-uri 'self'`)

### Cache Control

- **HTML pages**: always revalidated with the server (never served stale)
- **JavaScript and CSS**: cached for 24 hours (content-hashed filenames ensure fresh versions after updates)
- **Images and fonts**: cached for 1 year (immutable assets with content hashes)

---

## 🔒 Application Security

### XSS (Cross-Site Scripting) Prevention

All user-provided data (descriptions, category names, notes) is **sanitized before being displayed** in the interface:

- **HTML escaping** — characters like `<`, `>`, `"`, `'`, `&` are converted to safe equivalents before rendering
- **Attribute escaping** — additional protection for data placed inside HTML attributes
- **Content Security Policy** — restricts which scripts can execute in the browser

### Input Validation

Every user input is validated both on the client and enforced on the server:

- **Numeric fields**: checked for valid number format, positive value, and reasonable range
- **Text fields**: trimmed of whitespace, checked for minimum/maximum length
- **Date fields**: validated for correct format
- **Currency fields**: must match the allowed currency list
- **Server enforcement**: even if client-side validation is bypassed, Firestore security rules reject invalid data

### Event Delegation Security

Budgee uses an `EventDelegate` system that centralizes event handling with explicit selector-based allowlists, reducing the attack surface compared to inline event handlers.

### Error Handling

- **User-facing errors** show generic, helpful messages — never technical details, stack traces, or internal paths
- **Error logging** to the database is rate-limited (max 20 per session) and uses a strict field whitelist — no sensitive data, file paths, or source code is ever stored
- **Critical error monitoring** — the system detects repeated errors and sends alerts through a private Telegram channel

---

## 📴 Offline Security

### Service Worker

Budgee works offline through a Service Worker that caches essential resources:

- **Network-First strategy** — always tries to fetch fresh data from the server; falls back to cache only when offline
- **Version-scoped cache** — each app version has its own cache; old caches are automatically cleaned up on update
- **API exclusion** — calls to Firebase, Google, and Telegram APIs are never cached (always require network)

### Offline Data Sync

- Transactions created offline are stored in a **pending changes queue**
- When connectivity returns, pending changes are synced to the server with **retry logic** (up to 3 attempts with exponential backoff)
- If sync fails, the user is notified and data is preserved locally until the next successful sync

---

## 📦 Dependency Security

### npm Packages

- All dependencies use **pinned versions** (`==`) to prevent unexpected updates
- **`npm audit`** is run to check for known vulnerabilities before deployment
- Security patches are applied promptly when vulnerabilities are disclosed

### CDN Dependencies

- Firebase SDK scripts loaded from CDN include **Subresource Integrity (SRI) hashes** — the browser verifies that the downloaded file hasn't been tampered with
- A dedicated audit script checks CDN dependencies against the [OSV.dev](https://osv.dev/) vulnerability database
- Chart.js is hosted locally (not loaded from CDN) to eliminate external dependency at runtime

### CI/CD Pipeline

- **Automated security audit** on every push — `npm audit --audit-level=high` blocks deployment if high-severity vulnerabilities are found
- **CDN dependency check** — warns if any externally-loaded library has known issues
- **ESLint** enforces code quality rules that catch potential security issues (unused variables, shadowed variables, strict equality)

---

## 🔏 Privacy

- **No analytics or tracking** — Budgee does not use Google Analytics, Facebook Pixel, or any other tracking service
- **No ads** — the app is completely ad-free
- **No data sharing** — your financial data is never sent to third parties (except the services you explicitly connect, like Google Drive)
- **Data portability** — you can export all your data to CSV at any time
- **Right to deletion** — delete your account and all associated data permanently from the settings

---

## ⚠️ Known Limitations & Roadmap

Transparency is part of security. Here's what Budgee currently does **not** do, and what's planned:

| Limitation | Status | Notes |
|-----------|--------|-------|
| Two-factor authentication (2FA) | Planned | Currently relies on email/password + email verification |
| Client-side encryption | Not implemented | Data is encrypted at rest by Firebase, but not end-to-end encrypted |
| CSP `'unsafe-inline'` directive | Being phased out | Required for legacy code; actively being replaced with EventDelegate |
| Read rate limiting on database | Not implemented | Firebase does not offer client-side read rate limiting; monitored server-side |

---

## 📬 Reporting a Vulnerability

If you discover a security issue, please report it responsibly:

- **Email**: [andreabonacci95@protonmail.com](mailto:andreabonacci95@protonmail.com)
- **Subject line**: `[SECURITY] Budgee — Brief description`
- Please include steps to reproduce the issue and any relevant details

I take all reports seriously and will respond as quickly as possible. Please do not disclose the issue publicly until it has been addressed.

---

<div align="center">

**© 2025-2026 Andrea Bonacci**

*Last updated: April 2026*

</div>
