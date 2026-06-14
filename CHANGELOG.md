# Budgee Changelog

This document summarises the significant progress of the project from its inception (January 2026) to today, grouped by **value eras** rather than by individual session. Purely cosmetic commits (formatting, variable renames, UI alignment without behaviour change), internal-only cleanups with no measurable impact (removing `console.log`, fixing unused CSS selectors) and empty sessions have been deliberately excluded.

Time span: **January 2026 → June 2026**, 83 working sessions.

---

## Era 1 - Functional foundations (January - February 2026)

**Value delivered**: the features that defined what Budgee does for the user. This era introduces the distinctive features that set Budgee apart from a plain expense tracker.

### "Available liquidity" system (sessions #4 → #11)

The app does not simply sum incomes and subtract expenses. It tracks three separate dimensions:

- **Savings goals**: every goal has a name, target amount, deadline (or "continuous"), priority and colour. Automatic computation of the monthly amount required, coloured progress bars, badge with the number of active goals.
- **Earmarked savings**: an "Add Savings" button on each goal, with quick shortcuts (+50, +100, +200, Complete). The liquidity card shows `€1,000 (+€500 earmarked)`, with €1,000 freely spendable.
- **Investments from liquidity**: each investment declares the funds origin (external, fully from liquidity, partially from liquidity). New top-ups ("Invest" button) and withdrawals ("Release" button) with full flow tracking.

Critical bug fixed (session #11): available-liquidity calculation was double-counting investments performed before the manual balance set. Now only investments after that date are considered.

### "Open accounts" tab (session #8)

Tracking of debts and credits towards individuals or service providers (e.g. "Dr. Rossi - Dentist"). Each account has type (debt/credit), category, progress bar. Buttons: Pay/Collect (auto-creates a linked expense or income), + Amount (new invoice on the same account), Edit, Close.

### Interactive deductible expenses (session #1)

Clicking "Total Expenses" or "Total Incomes" opens the monthly summary. Clicking a month shows every transaction for that month (date, description, category, amount), with distinctive icons for deductible and recurring entries.

### Analyses and comparisons

- **Expenses-by-currency breakdown** (#14): the Expenses tab shows spend in EUR, PLN, USD, GBP, with the original value + conversion. Percentage bars over the total.
- **Income vs Expense comparison** (#15): the Categories tab lets users freely select macro-categories (incomes vs expenses) through clickable "pills". A second sub-category level enables finer analysis. Clicking a category opens a popup with every related transaction.
- **Period summary** (#24-#25): summary card on Expenses/Incomes/Savings with balance (green positive / red negative), total incomes, total expenses, recurring forecast. Clickable cards opening the transaction detail.

### Internationalisation and categories

- **Recurring-popup i18n** (#13): full IT/EN translation of titles, labels, dropdowns (frequencies, weekdays, payment methods, recurrence types), messages.
- **Crypto categories** (#12): "Cryptocurrencies" under "Finance & Savings", aligned across expenses and incomes. Automatic suggestion on crypto descriptions.
- **Descriptive labels** (#12b): every generic "Other" entry was replaced with specific names ("Other Transport Expenses", "Other Healthcare Expenses") in both languages.

---

## Era 2 - Security and data protection (February → June 2026)

**Value delivered**: every growth step of the app was paired with a security audit. The security sessions did not add visible features but closed specific attack vectors before they could be exploited.

### XSS protection

- **#18, #32**: XSS protection on every user input field (descriptions, categories). Characters `&`, `<`, `>` display as text, never interpreted as HTML. The search keeps highlighting results correctly.
- **#29, #33**: sanitisation of 126 `innerHTML` sites, 16 files touched by the XSS prevention.
- **#52, #75**: deep audit, removal of every remaining XSS vector, vulnerability fixed on search with special characters in payment methods.
- **#80**: XSS in goals modal (targetAmount/savedAmount/deadline unescaped). Fixed with `escapeHtml(String(...))`.
- **#83**: `escapeAttr` not imported in `investments-modals-popups.js`, masked by the global test mock.

### CSV injection protection

- **#63**: vulnerability found in the financings export — names with `=HYPERLINK(...)` were interpreted as formulas by Excel. Fixed with apostrophe prefix on dangerous characters.
- **#65**: same protection extended to investments.
- **#80**: `escapeCsvCell` helper with RFC 4180 quote-doubling + leading `'` on `=`, `+`, `-`, `@`, `\t`, `\r` for the open-accounts export.

### Database and auth hardening

- **#33**: cloud-database data validation (stricter Firestore rules), HTTP protection headers, robust offline sync.
- **#36**: Firestore rules hardened to accept only expected fields, errors no longer expose technical details, Telegram notifications no longer carry stack traces.
- **#38**: migration to UUID (`crypto.randomUUID()`) for every identifier (expenses, incomes, investments, ...). Eliminates the risk that quick double-clicks silently overwrite different records.
- **#57**: password complexity rules (8+ characters, upper + lower + digits), auto-recovery on Firestore operations on network error, Telegram messages protected against injection, stricter cloud rules with size limits.
- **#34**: manual database backup script (`scripts/firestore-backup.sh`), centralised error tracking, auto-logout after inactivity.
- **#82**: fiscal year validation (parseInt overflow `9999999999` rejected, range `[1900, 2200]`).

### Silent failures surfaced

The app had several spots where a network or state error was swallowed without informing the user. Systematic fix in #66, #70, #80:

- `loadFinancings` / `loadOpenAccounts`: now display `error-loading`.
- `removePaymentFromAccount` / `removeReceiptFromAccount`: now display `error-updating`.
- `saveRecurringExpenses` / `saveRecurringIncome`: `sync-error` notification.
- `recordPayment` with empty template: falls back to `payment-exceeds-remaining`.
- `handleEditSubmit` on account-not-found: now displays `account-not-found`.
- Faulty cloud save on goals (unreported), faulty load wiping in-memory data (now preserved), adding savings to a deleted goal (now with feedback).
- 3 diagnostic logs added for silently-failing patterns: popup not re-opened, dropdown not populated, unknown user action.

---

## Era 3 - Performance and load optimisation (February → March 2026)

**Value delivered**: load times went from unacceptable to competitive. Every intervention was measured.

| Intervention                                     | Session  | Measured outcome                            |
| ------------------------------------------------ | -------- | ------------------------------------------- |
| On-demand JavaScript code-splitting              | #17      | Initial bundle 1.73 MB → 900 KB (-48%)      |
| PNG → WebP image conversion                      | #23      | Image assets 6.2 MB → 340 KB (-95%)         |
| Full offline cache                               | #22      | App fully usable offline                    |
| Main-bundle code-splitting                       | #43      | `main.js` 668 KB → 383 KB (-43%)            |
| ES Modules + Vite build migration                | #42      | 56 separate files → 1 optimised bundle      |
| Build system with CSS+JS minification            | #29      | CSS bundle 362 KB → 46 KB gzip (-87%)       |
| Event-listener memory leak                       | #40      | 240 `addEventListener` vs 5 `removeEventListener`: 11:1 ratio cut via centralised event delegation |
| Service Worker re-aligned to new structure       | #41      | Cache active, no console errors             |

**Specific bug fixed** (#43): investment popups failed to close via X / outside click / Escape. "Investment not found" error on Edit. Both fixed.

---

## Era 4 - Architectural modernisation (March 2026)

**Value delivered**: the codebase moved from a monolithic script to a modular architecture. The foundation that made all the later test and refactor work possible.

### Structural reorganisation

- **#39**: project structure reordered. From 60+ files in the repo root to thematic subdirectories under `src/` (15 subdirectories).
- **#37**: `app.js` reduced by 61% (from 4,452 to 1,734 lines). 6 specialised files extracted.
- **#30**: `app.js` reduced by 23.7% (from 13,150 to 10,030 lines). The section-personalisation system (visibility, ordering, drag-drop) was duplicated 8 times → now a single reusable `SectionsManager`.
- **#54**: investments file split into 4 smaller files (was 2,829 lines).
- **#44**: main CSS from 7,982 lines → 16 modular files (max 1,492 lines each), organised by functional area.

### New internal systems

- **#34**: 5 new dedicated services (backup, error tracking, storage management, ...). Auto-logout after inactivity.
- **#35**: centralised `localStorage` via StorageService (87% migrated), event-driven section navigation.
- **#36**: 61/63 inline `onclick` migrated to event delegation (97%).
- **#40**: `event-delegate.js` and `lifecycle-mixin.js` to prevent memory leaks.
- **#42**: 67 JS files migrated to ES modules, 120+ `typeof` checks removed, 55+ `window.X` assignments eliminated.
- **#46**: ~540 inline styles → ~85 left (84% reduction); colours and sizes in CSS classes to support dark theme.
- **#47**: ESLint flat config + fix of 131 pre-existing issues hidden by the old configuration (loose comparisons, duplicate translations, ...).
- **#50**: auto-categorise keyword list extracted into a dedicated JSON.
- **#55-#56**: removal of every inline `onclick` handler for CSP compliance.

### Second-generation architectural refactor (June 2026)

Sessions #58-#83 completed the work on every remaining god-class.

| Functional area                       | Main file                                    | Pure modules extracted                                                                                                            |
| ------------------------------------- | -------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| **Investments** (modals & popups)     | `investments-modals.js` (1,439 → 41)         | edit, transactions (5 modals), search filter, portfolio popups (6 aggregate popups)                                               |
| **Documents / Google Drive**          | `documents.js`                               | OAuth token-state, session-data, folder-preferences, script-loader with polling and retry                                         |
| **Savings goals**                     | `goals-manager.js`                           | form-templates (HTML), form-routing (action dispatch, form parsing)                                                               |
| **Financings**                        | `financing.js`, `financing-details-modal.js` | form-parsing, action-routing (7 actions), grouping by type, amount/progress/interest computations                                 |
| **Open accounts**                     | `open-accounts.js`, `open-accounts-modals.js` | form-parsing + 3 validators, immutable record-builders (apply/remove transactions, account close/reopen), CSV export, multi-criteria filtering, 12-action routing |
| **Recurring expenses**                | `recurring-expenses.js`, `recurring-modals.js` | action-routing (lock, safe dispatcher), reject-flow descriptor, recurrence builder, frozen type-i18n bundle              |
| **Advanced insights**                 | `advanced-insights-ui.js`                    | data processors (sort, safe-division percentage, savings-rate colour thresholds, IT/EN benchmark), source routing (savings rate, impulse spending, heatmap) |
| **Monthly budgets**                   | `budget-manager.js`                          | messages, sort helpers, routing                                                                                                   |
| **Incomes**                           | `income-manager.js`                          | list-item template, view helpers, messages, routing                                                                               |
| **Deductible expenses**               | `deductible-manager.js`                      | reopen-plan (unifies 7 duplicated callsites), subcategory-dropdown (dedups 3 identical implementations), popup action-routing (9 cases) |
| **Authentication**                    | `auth-manager.js`                            | messages, Firestore user-doc payloads, error routing, form-state                                                                  |
| **Totals popups / Edit popup / Period filters / Reports / Export** | several files          | routing + form-state + aggregation, ~30 helpers extracted in total                                                                |

Reference: see also the coverage balance under Era 7.

---

## Era 5 - Critical bug fixes

**Value delivered**: bugs that, once discovered, should never have stayed in production. Resolved with priority.

- **#49 Italian timezone**: between midnight and 2 a.m., the app showed transactions from the previous month instead of the current one (used UTC instead of Europe/Rome). Fixed on every date in the app.
- **#45 Console errors**: offline cache on missing images, recurring-expense init, Firefox scrollbars.
- **#16b Goals permissions**: missing Firestore rule for savings goals; report button removed where no longer present.
- **#11 Liquidity double-count**: investments pre-balance subtracted twice.
- **#72 Recurring binding**: "Edit" and "Delete" buttons on recurring expenses not properly wired.
- **#73 Recurring investments**: titles displaying "Recurring Expense" instead of "Recurring Investment"; "Linked Financing" field mistakenly shown for investments.
- **#74 Deductible expenses - buttons**: added "Non-Deductible" and "Delete" buttons on regular expenses marked as deductible.

### Latent bugs surfaced by tests (sessions #61-#75)

The TDD refactor exposed pre-existing bugs that the system was hiding:

- "Last 12 months" Savings filter broken from January to November (badly generated dates at year start).
- "undefined 0.00" in savings stat cards when the currency symbol had not yet loaded.
- "Invalid Date" as X-axis label in savings charts on malformed cloud months.
- "Return: 0" instead of the real negative value for losing investments.
- Missing amounts as "undefined" in CSVs.
- Corrupted financings (total instalments = 0 or absent) shown as "closed" when they should have been "open".
- Investment load errors shown as an indistinguishable empty list.
- "Invest from liquidity" without a set balance displayed phantom liquidity computed from zero.
- Deductible dates "2024-13-45" not blocked by validation.
- "NaN" and "Invalid Date" visible in categories on malformed data.
- "Convert to regular" deductibles button pointing at a non-existent method.
- Timezone bug assigning an expense to the wrong month.
- "Recurring Investment" shown as "Recurring Expense".
- NaN propagation in `computeComparisonTotals` on specific numeric data.
- i18n bugs (untranslated strings, missing "Cryptocurrencies" category in IT).

---

## Era 6 - Mature UI/UX (March 2026)

**Value delivered**: the look-and-feel goes from "AI-generated" to "designed with intent" (declared references: Apple, Linear).

### Visual refresh

- **#27**: refreshed look — clean title colours without gradient effects, more refined cards, animations reduced to functional ones, Figtree font for pleasant reading. Keyboard accessibility with restored focus indicators. Notification system using the browser-native `<dialog>` element.
- **#28**: cleaner and flatter background colours (Apple/Linear style), neutral subtle shadows, immediate touch feedback. Notch support (iPhone X+, Pixel) via safe-area insets.
- **#29**: animated section transitions, form errors near the field, working browser back button, mobile swipe between sections, "Skip" button on tutorial. Hardcoded `rgba()` 388 → 12 (-97%).
- **#26**: smoother animations on smartphones, robust layout on small screens, improved keyboard/screen-reader navigation, empty sections suggesting an action (assessed "UI score 5.5/10 → 8/10").
- **#31**: standardised animations, safer click system on list buttons, loading state during data download.
- **#46**: light/dark theme works better (colours no longer hardcoded), single theme to change in one place.

### Empty-state buttons

- **#27**: every empty section displays a clear message with a green "Add" button opening the form directly.

### A11y dark mode

- **#35**: 6/6 WCAG AA failures fixed (colour contrast in dark mode).
- **#20**: 3 patch CSS files removed, dark-text-on-green-background issues solved at the root.

---

## Era 7 - Massive test coverage (March → June 2026)

**Value delivered**: Budgee started with virtually no tests. In six months it became a project with 65% global coverage and over 5,500 behavioural tests. Future regressions will be caught before deploy.

### Coverage growth

| Period                   | Total tests | Coverage Lines | Reference session       |
| ------------------------ | ----------- | -------------- | ----------------------- |
| Project start            | 0           | 0%             | -                       |
| March (#48)              | 74          | n/a            | #48 - computation tests |
| March (#53)              | 105         | n/a            | #53 - Firestore tests   |
| May (#58)                | 1,186       | n/a            | #58 - god-class wave 1  |
| May (#60)                | 1,439       | n/a            | #60 - 5 god-classes     |
| May (#61)                | 1,568       | n/a            |                         |
| May (#62)                | 1,703       | 34.17%         | #62 - +4 god-classes    |
| May (#63)                | 1,875       | 38.61%         | #63 - +3 god-classes    |
| May (#67)                | 2,699       | 47.23%         | #67 - +13.06 pp leap    |
| May (#68)                | 2,893       | 47.54%         |                         |
| May (#69)                | 3,100       | n/a            |                         |
| May (#70)                | 3,439       | 49.68%         |                         |
| May (#71)                | 3,813       | 51.57%         |                         |
| June (#75)               | 4,019       | 51.93%         |                         |
| June (#78)               | 4,618       | 55.03%         | #78 - 10 god-classes    |
| June (#79)               | 4,885       | 55.85%         |                         |
| June (#80)               | 5,132       | 56.73%         | #80 - 4 god + 8 modules |
| June (#81)               | 5,232       | n/a            | #81 - Google Drive      |
| June (#82)               | 5,376       | 61.33%         | #82 - 3 god, audit      |
| June (#83) - **current** | **5,510**   | **65.37%**     | #83 - investments+auth  |

### Coverage on critical files (before → after)

| File                       | Before        | After         |
| -------------------------- | ------------- | ------------- |
| `auth-manager.js`          | **0%**        | **97.51%**    |
| `budget-manager.js`        | 0%            | **93.6%**     |
| `income-manager.js`        | 0%            | **89.57%**    |
| `deductible-manager.js`    | 0.6%          | **32%** (manager) + 3 pure modules at 100% |
| `investments-modals.js`    | 6.89%         | **100%** (barrel) + 4 pure modules at 91-100% |
| `documents.*` pure modules | non-existent  | **100%**      |

### Test philosophy

Tests are **behavioural**, not tautological. Every new module goes through a **red-check** (intentional break to verify the test catches the regression). Confirmed red-checks:

`buildLiquiditySummaryHtml` (goals templates), `buildIncomeListItemHtml` with broken XSS escape, `isInvestmentTransactionEditable`, `buildRecurrenceData` monthly-specific, `validateOpenAccountForm`, `buildRejectFlowDescriptor`, `getInterestVisuals`, heatmap source resolution, set-view boolean in open-accounts routing, `mapRegisterValidationError`, `resolvePasswordToggle`, and ~30 other critical points.

---

## Era 8 - Transparency for the user

**Value delivered**: making the project inspectable by anyone using it.

- **Presentation READMEs** (session #32): three public READMEs (originally over 290 technical lines each) rewritten for readers who want to grasp what Budgee does in 60 seconds. Identical structure in Italian and English.
- **Header link to the repository** (June 2026): the "Budgee by Bonn" header title is now a direct link to the GitHub repository.

---

## Aggregate metrics across the project's history

| Metric                                           | Value                                            |
| ------------------------------------------------ | ------------------------------------------------ |
| Working sessions                                 | 83 (Jan 2026 → Jun 2026)                         |
| User-facing features introduced                  | 15+                                              |
| Security vulnerabilities closed                  | Multiple XSS + 3 CSV injections + 5 recent HIGH  |
| Silent failures made visible                     | 15+                                              |
| Total tests on 9 June 2026                       | **5,510**                                        |
| Global Lines coverage                            | 0% → **65.37%**                                  |
| Initial bundle                                   | 1.73 MB → 900 KB (-48%)                          |
| Image assets                                     | 6.2 MB → 340 KB (-95%)                           |
| `app.js` reduction                               | 13,150 → 1,734 lines                             |
| `investments-modals.js` reduction                | 1,439 → 41 lines (barrel)                        |
| Inline styles removed                            | ~540 → ~85 (-84%)                                |
| Hardcoded `rgba()`                               | 388 → 12 (-97%)                                  |
| New pure modules extracted                       | 75+                                              |

---

## What this means for Budgee users today

In six months Budgee evolved from a working but fragile app into a product that is:

1. **Fast**: initial bundle halved, images down 95%, minified build, full offline.
2. **Secure**: every known XSS vector closed, CSV injection blocked, password complexity rules, UUIDs as IDs, restrictive Firestore rules, auto-logout on inactivity.
3. **Reliable**: errors that used to vanish silently are now visible. A button that does not work tells you why.
4. **Verifiable**: 5,510 tests covering 65% of the code. Critical functions (login, budgets, investments, exports) are covered above 90%. A future change that breaks something is caught before deploy.
5. **Transparent**: the code is linked from the app's header. Anyone can verify what Budgee does with their data.

The next development cycle introduces new features on a foundation that is dramatically more protected by tests and security audits than it was six months ago.
