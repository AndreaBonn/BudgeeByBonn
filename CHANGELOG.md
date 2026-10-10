# Budgee Changelog

This document summarises the significant progress of the project from its inception (January 2026) to today, grouped by **value eras** rather than by individual session. Purely cosmetic commits (formatting, variable renames, UI alignment without behaviour change), internal-only cleanups with no measurable impact (removing `console.log`, fixing unused CSS selectors) and empty sessions have been deliberately excluded.

Time span: **January 2026 → October 2026**, 146 working sessions.

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

- **#63**: vulnerability found in the financings export: names with `=HYPERLINK(...)` were interpreted as formulas by Excel. Fixed with apostrophe prefix on dangerous characters.
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

- **#27**: refreshed look: clean title colours without gradient effects, more refined cards, animations reduced to functional ones, Figtree font for pleasant reading. Keyboard accessibility with restored focus indicators. Notification system using the browser-native `<dialog>` element.
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

## Era 9 - AI, data rights and room to grow (June → August 2026)

**Value delivered**: the app stopped asking you to type what a camera can read, gave you a way to take your data out or destroy it, and moved its storage off a ceiling it was heading straight for.

### Receipt scanning

- **AI scan for expenses and income** (June): photograph a receipt or upload a PDF and the amount, date, description and category land in the form. The scan runs on Google Gemini with an API key the user supplies; more than one key can be stored, and a key that hits its limit hands over to the next.
- **A guard against the one silent mistake** (June): scanning something that reads as income while the expense form is open stops the flow and asks. Recording a refund as a purchase is the error nobody notices until the totals stop making sense.
- **Sharing from the system** (August): with Budgee installed, the phone's share sheet lists it among the destinations for an image. The service worker holds the file until the app opens, and the scan starts with the picture already loaded.

### Consent, and what leaves the app

- **A consent of its own for AI scanning** (August): a receipt can reveal medicines, medical visits, habits. Sending it to a third party is a distinct decision from using Budgee, so it is asked separately, versioned, and revocable from the profile. If the notice changes in substance, the old answer no longer covers the new situation and the question comes back.
- **Privacy notice in Italian and English** (August): a page of its own, reachable from the app.
- **The API key never leaves** (August): `settings/ai` is excluded at the source from both the export and the Drive backup. A key that ends up in a backup stays readable forever.

### Taking your data out, or destroying it

- **A single registry of what an account is made of** (August): export and deletion used to walk two hand-written lists that had drifted apart. Deletion pointed at three retired subcollections and left the seven real ones untouched; the export covered four out of ten. Both now read the same registry.
- **Full export as a ZIP** (August): a complete JSON plus a CSV per section, read from the database rather than from what the app has in memory, which held only expenses, income and budgets.
- **Deletion that survives an interruption** (August): Firestore does not delete subcollections with their parent. Without an explicit visit they stay orphaned and invisible, and the account looks deleted while all the financial data is still there. The user document is now marked before the work starts and removed last, so an interrupted deletion leaves a trace that the next sign-in recognises and resumes.

### Payment methods, and the cash you cannot see

- **One canonical list** (August): the codes were duplicated in six places and the copies had diverged. The recurring-income edit select omitted `voucher`, so a recurring income paid by voucher lost its method on save.
- **Required where a person fills the form** (August), with the method you usually use for that category pre-selected, and only when the field is still empty.
- **Coverage and bulk completion** (August): the profile shows how much of the history carries the field and offers to fill the gaps by category, proposing the method most used in each.
- **Cash share** (August): how much of your spending goes through cash, by month and by category, measured on amounts rather than on the number of entries. What has no known method is reported separately instead of quietly diluting the percentage.

### Reading two years at once

- **Year-on-year comparison by category** (August): difference and percentage change, as a table and as paired bars. A category present in one year only stays in the comparison with the other at zero, because a row that disappears is usually the interesting one. One currency at a time.

### Backup on the user's own Drive

- **Weekly copy, eight kept** (August): upload, retention and pruning on Drive, under the `drive.file` scope, so Budgee only ever touches the files it created.
- **Said out loud** (August): with no Cloud Function there is no scheduler. The backup starts when the app opens, and the notice says exactly that. "Weekly backup" would otherwise promise something that happens on its own every seven days.

### The tax package

- **A wizard for the accountant** (August): seven profiles, from an employee filing a 730 to a partner in a company or a business on ordinary accounting. A chapter filter turns roughly twenty sections per profile into only the screens that concern the person in front of it.
- **What Budgee already knows goes in the archive** (August): deductible expenses grouped by category, income, totals.
- **What is missing is named** (August): an empty section does not distinguish "I do not have this" from "I forgot". The list is always generated, even when nothing is missing, because an archive without it leaves the doubt that the check never ran.
- **Cash and the 19% relief** (August): since 2020 the relief generally requires a traceable payment, medicines and public providers aside. Budgee does not record who provided a service, so it flags rather than rules.
- **What is not a file** (August): IBAN, dependants' tax codes, withholding agent details. The archive names them without containing them: a ZIP travelling by email is no place for those.
- **Safe entry names** (August): names coming from Drive are not under Budgee's control and can carry path separators or directory traversal. Sanitising them is the responsibility of whoever builds the archive.

### Room to grow

The account lived in two arrays inside a single user document, which Firestore caps at 1 MiB. That ceiling was a matter of time.

- **Monthly documents** (August): transactions split by month and put back together, with a rule that governs everything: no record may vanish. An unreadable date is not a reason to throw away an expense, so those go into a separate bucket that whoever migrates has to look at.
- **A migration that can be inspected** (August): a pre-migration backup, a plan, verification before and after writing, a report kept on Firestore, and a reason written down whenever it does not advance.
- **Realtime sync** (August): two tabs on the same account stay aligned, with listeners opening only after the startup read has settled, so the decision about where to read from is never made against data that moved underneath it.
- **The SDK's own offline memory** (August): it was already being switched on inside `firebase-config.js`, with both error branches empty. A persistence that never came up looked exactly like one that had.
- **Lazy month loading** (August): only the months the current view asks for, derived from the period filter rather than duplicated from it.

### Investments and transactions

- **Movements per investment** (June): capital added, capital released, returns collected, all editable or removable after the fact, plus recurring returns for what pays out on a schedule.
- **Edit and delete inside every detail modal** (June): including the ones opened from the insights, so a wrong entry is corrected where it was noticed.

### Accessibility

- **Contrast lifted to the AA floor** in both themes across goals, search, open accounts, the remove button and the profile modal titles (June → August).
- **Keyboard trapped inside modal dialogs** (August), so focus cannot wander behind an open modal.
- **Accessible names** on controls that had none, such as the goal currency select.
- **Decorative emoji removed** where the text already said the same thing.
- **Semantic text colours** that follow the theme, instead of values repeated per component.
- **axe-core pinned locally** (August), so the accessibility gate stops depending on the network.

### Showing the app, and explaining it

- **Reproducible demo pipeline** (July): Firebase emulators, seeded fake data, Playwright recording, ffmpeg conversion. Nothing real is touched, and the same seed produces the same video.
- **Per-feature GIFs** (July) in Italian and English, cut from a single continuous tour.
- **Screenshots of the recent features** (August), on the same infrastructure.
- **A user guide** (August), in Italian and English, walking through every section step by step.

### Taking out a backend that was never there

Budgee ran on Firebase's free plan, where Cloud Functions do not exist, yet the
repository still carried four of them and the client still wrote to the database
to feed them. A timer refreshed an activity marker every five minutes for a
function that existed in no file at all, and every password reset filed the
user's address in a collection nothing could read and nobody could empty.

All of it is gone (August). What stays is the error log, which is still readable
from the Firebase console. The app now has no server-side code of its own: the
logic runs in the browser, and the database rules are the only trust boundary.

The same pass fixed a defect nobody east of Greenwich would ever see. Transaction
dates travel as plain strings, which JavaScript reads as midnight UTC: exporting
a CSV from California turned a June 1st expense into May 31st. Dates are now
pinned to local midnight, and the tests run green in Los Angeles, Rome and
Auckland.

---

## Era 10 - The rules of the game, written down (August → September 2026)

**Value added**: the app stopped taking three things for granted that nobody had ever stated. How many languages it can speak, what a person agrees to when they register, and when it may change version under the hands of someone using it. At the end, a new feature for anyone who also gets paid in meal vouchers.

### Languages stop being two

Every language-dependent choice was written as `lang === 'it' ? x : y`, scattered through the code. Adding a third would have meant finding them all again.

- **One registry** (August) declares which languages exist and how they behave: the tag used for dates and numbers, the name of the language in itself, the decimal separator for imported spreadsheets, the language to consult when a key is missing, and whether the language is offered at all or stays hidden until its translation is complete. Adding one is an entry in a list.
- **A selector instead of flags** (August): a row of flags does not scale, and a flag is not a language. English is not American, Italian is also Swiss, and regional emoji do not render on every platform. The control is a native dropdown, so keyboard handling, Esc, type-ahead and the screen reader role come from the browser.
- **Separate dictionaries per language** (August), instead of one file holding IT/EN pairs side by side.
- **Legal pages do not fall back** (August): the interface can show a string in another language without harm, a privacy notice cannot. They appear only where a person has read the text.
- **Categories compared in canonical form** (August): the same category written in two languages is no longer two categories.

### Terms of use, and an acceptance that holds

The app ships a tax guide with calculators producing amounts someone could put in a return. Without a document saying what that guide is, the app was offering tax advice to strangers.

- **A terms page in Italian and English** (August). The text defines the object of the service instead of excluding liability: the Italian Supreme Court (ord. 20945/2026) requires specific approval for a liability-limiting clause even online, and the Consumer Code (art. 33) presumes it unfair against consumers. A void clause takes the credibility of the rest of the document with it, so the text says what Budgee is and is not, that the tax sections are informational, and that the estimates come from user-entered data.
- **Acceptance required to register** (August), with the box never pre-ticked and both documents linked next to the point of acceptance.
- **An acceptance nobody can rewrite** (August): the record is create-and-read, never update, and one constraint does the work: `acceptedAt == request.time`. It forces the client to use the server's clock, the only one it does not choose. Without it a date in 2020 would have been accepted and nothing would have looked wrong. What it proves, stated without overselling: that an authenticated registration accepted a specific version at a moment it could not pick. Not who was at the keyboard.
- **The notice reachable before registering** (August): it lived inside the app, which is to say afterwards. Collection starts at registration, and GDPR art. 13 wants the notice available at that moment.
- **The warning inside the tax wizard** (August): the terms say the tax sections are informational, but nobody opens the terms before using a calculator. The notice stays at the top of the wizard for its whole length rather than flashing past on the first screen.
- **Errors anchored to their field** (August) on registration, goals, investments, financing, open accounts and budgets. One message at a time forced people to discover problems one at a time, one submission each.

### The PWA does what it declares

- **The service worker never registered** (August). `main.js` waited for the `load` event, which in production had already fired by the time that code ran: measured in Chromium, event at 147.6 ms and listener added at 167.7 ms. No registration, therefore no offline cache and no update to announce.
- **Icons the size they claimed** (August): `icon-512.png` was 394x340 and not square, while the manifest and the page both declared it 512x512. Browsers take the declaration at face value and redraw, so the defect only showed up once installed. All sizes regenerated from one source, plus the 32 and 16 the browser tab actually wants: 243 KB in total becomes 100 KB.
- **A notice when a new version ships** (August): the waiting version no longer takes over silently while the open page is still running the old code. A banner offers "Update" and "Later", the update applies only if you accept, and accepting in one tab updates the others.
- **One attempt withdrawn because it cost too much** (August): the section bar had been pinned to the bottom of the screen on mobile, then put back in the flow. Nine buttons on two columns measure 226 px on a 375 px screen, a third of the viewport permanently spoken for while scrolling a transaction list, which is the one thing that screen is there to show.

### Small things you notice every day

- **Excel import in the section header** (August), next to the camera, instead of the button inside the collapsed form. Moving it revealed that in production the button downloaded the xlsx package and stopped there: the manager was never constructed, so the file picker never opened. A regression from the ES modules migration, unnoticed because it produces no error.
- **A failed load now says so** (August): the two on-demand loaders had no error branch. Without a network the button stayed clickable and inert, with no message.
- **The currency stays the one you used last** (August), with a separate memory per form: expenses can stay in zloty while your salary keeps arriving in euro. Forms that edit an already-saved record are excluded, because pre-filling them would misrepresent their contents.

### Meal vouchers (September 2026)

Anyone who gets them carries a second currency around. Real money, but spendable only in certain places, and treating it as cash is the easiest way to believe you are more liquid than you are.

- **A new code, not a recycled one**: `voucher` already existed, but it is the gift voucher, and it is barred from expenses. Relabelling it would have pulled gift cards already recorded into the meal voucher balance. The `meal-voucher` code is the only one allowed on both directions, because a voucher is first received and then spent.
- **A derived balance, never stored**: it starts from a figure the user declares, with a date, and follows the movements from there. It is never written anywhere, so it cannot drift away from them.
- **The same arithmetic as the liquid balance**: the meal voucher balance is the liquidity function applied to the movements paid in vouchers. A second calculation would have been shorter to read and longer to maintain, because two functions that must agree forever end up not agreeing, and the divergence shows up as two balances that do not match while no test fails.
- **Out of liquidity, in everything else**: in the current balance card they sit on their own line and enter neither the total nor the month projection. In period totals, budgets and savings they count like any other movement.
- **No state shows a false number**: with no opening balance the panel invites you to set one instead of showing a zero, which would tell someone with vouchers in their pocket that they have none; a negative balance shows its sign, because it is the only signal that the opening figure is wrong or a credit is missing.
- **Turning it off hides, it does not erase**: past movements stay in the list with their label and the opening balance stays written down.
- **A pre-existing bug found on the way**: the liquidity calculation checked every income and every expense, but not the opening balance. A corrupted amount turned the whole balance into `NaN`, and the interface printed it as if it were a figure.
- **Two gaps found by verification, not by the code**: the entry forms have their options written in the markup, which nobody rebuilds on open, so anyone who had never turned the feature on was still offered "Meal Voucher" on every expense; and the assisted payment method completion listed every code, so it would have assigned past expenses to a channel never held.
- **A defect only running it could find**: after recording an expense in vouchers, the balance stayed on the previous figure until the page was reloaded. It is derived, so it changes only when something redraws it, and nothing did.

---

## Era 11 - Room to grow, and a way in (September → October 2026)

**Value added**: the app answers questions it could not answer before (where the money went, what an investment really earns, what a crypto position is worth), a visitor can try it without registering, and the whole interface went through a review on phone and desktop. Underneath, the storage ceiling stopped being a matter of time.

### Seeing where the money went

- **Year-over-year drill-down** (September): in the comparison table, a category's amount in a year opens the expenses that make it up. The cells are real buttons, reachable from the keyboard. Edit and delete work from the detail, and the table redraws afterwards, because an expense that changes amount, date or category moves to another cell. Amounts are not converted: the table compares one currency at a time, and converting would show a total different from the figure just clicked.
- **Expense location and spending by country** (September): every expense can carry a country, chosen from a searchable list. The home country sits in the header next to the currency, foreign expenses get a badge, and the country travels through recurring expenses, CSV and Excel import and export, and the search filter. A new section ranks countries and draws a map, by year or overall, by continent or worldwide. The map opens on the continent with the most transactions, because a world view where only your own country is lit tells you nothing.
- **A retroactive backfill** (September): the first time the home country is chosen, every past expense receives it, once, after a backup copy is written to the account. Review found that the backfill worked from a snapshot read before the backup, so an expense added during that wait would have been erased. It now applies the change to the live state at the moment of writing.
- **A map that drew lines across the top** (October): outlines crossing the 180th meridian, Russia and Fiji, were projected as a horizontal line from one edge of the map to the other. The outline is now made continuous and the part beyond the edge reappears on the opposite side.

### What an investment really earns

An expense linked to an investment could only be a payment into it. A broker's yearly fee was therefore either capital that was never invested or an expense with no connection to the product it belonged to.

- **Link type** (September): the link is either capital, the historic behaviour and still the default, or a management fee. A fee leaves the invested capital untouched.
- **Costs and net return** (September): the investment card shows management costs, actual net return and expected net return; the totals show actual return gross and net. Fees are computed on the fly from the expenses, with no counter stored on the investment, so there is nothing to drift. The transactions window lists them, and the expense list and recurring expenses label them.
- **A bug found on the way** (September): currency conversion for investments, financings and open accounts added a Promise instead of its value, so totals in mixed currencies printed as strings like `0[object Promise]3`.

### Crypto portfolio

A crypto investment stopped being a fixed amount and became a register of purchases (September).

- **Lots and average cost**: quantity, purchase price and date per lot, with average cost on the card.
- **Live price**: read from CoinGecko without blocking the page, with the market value and the unrealised gain on the card. The daily move sits on its own line, because on a day of market rise a position in loss would otherwise show two numbers of opposite sign side by side. A button asks for the price now. The reading is on for everyone and can be switched off on a single device.
- **Position chart**: each day is valued with the lots bought up to that day, not with today's quantity, which would draw a position that never existed. The cost basis is drawn as steps.
- **Sales**: partial disposals are recorded from the card, and everything shown afterwards describes what is left.
- **Two criteria side by side**: realised gain per calendar year with LIFO, the default of the Italian declaration regime, and with average cost. On a partial sale they diverge and can have opposite signs: in the test case the same sale is a loss of €1,500 under LIFO and a gain of €1,833.33 at average cost. The page states that the figure is not a tax return.
- **Defects found by review**: the chart ignored sales and kept drawing an intact position while the card showed the remainder; a sale of exactly what was left failed on a floating-point comparison and left a zero-coin position on screen as if still open; a sale without a date consumed lots and then vanished from the yearly summary.
- **Three buttons that did nothing** (October): the price refresh, the sale and the position chart listened on a page element that did not exist, so in production none of them responded. Their tests passed because they built that element themselves. They now listen on the real investments section, and a test reads the real page to check it is there.

### Transactions in monthly documents, live

The August work on monthly documents had been written but switched off: in production nobody was using it.

- **Live for everyone** (September): transactions are written to the monthly documents alongside the main one and read from them. On the real account used for the check, the 21 month documents matched the main document record by record, 793 expenses and 65 incomes. The six accounts never migrated run the migration on their next open.
- **The step that lifts the ceiling** (September): stopping the growth of the single document, so the account can keep growing past its size limit, is built and tested on the emulator but stays off by default. It checks before writing anything, takes a fresh backup, and records the new version of the account last, so an interruption in the middle leaves the account as it was. After it, the old document stops being a reference for content, so an audit asks only two questions: did anything present at the step vanish without a deletion mark, and does each month hold what it declares.
- **A way back** (September): a page rebuilds the main document from the monthly ones, with a dry run and a confirmation.
- **Found before shipping**: the step would have rewritten every month from the main document, erasing an expense added from another tab in those seconds; the offline queue did not know about the step and could report a failed write as synchronised; the Firestore rules had two ways around the freeze.

### Search that answers questions

- **Summary bar** (September): count, total in the current currency and average of the results, pinned above them. Items without an exchange rate are excluded and declared, and the total carries a "≈" when a rate is missing.
- **Statistics panel** (September): category breakdown, monthly trend, the largest item, the top month and the period covered.
- **Detail in place** (September): a result card expands to its detail, with Edit and Delete. After saving, cancelling or deleting, the list redraws with query and filters intact and the view returns to the same item.
- **Typing no longer redraws everything** (September): with 3,069 expenses, redrawing cost 451 ms at the 95th percentile on every key against a budget of 50 ms. The search now runs 150 ms after the last key: twelve keystrokes at 80 ms apart went from 13 redraws to 2.
- **Defects found on the way** (September): the search modal was built twice, so every key launched two searches; closing it left Escape disabled for the rest of the session; highlighting split `&amp;` in half; the investments search button opened two windows on top of each other.

### Try it without registering

- **A "Try the demo" button** (September) on the login and registration screens opens the whole app with realistic fake data. The data lives in the browser tab and disappears when it closes. Nothing is sent to Firebase, and a visitor's session cannot touch a real account. Actions that would use real services (Google Drive, receipt scanning, account export and deletion, password reset) say "Not available in the demo". It is on for everyone since 29 September, and a single device can hide it.
- **Found by running it, not by reading it** (September): three modules fell back to the real `localStorage` and would have left a trace of the demo in a real user's browser.

### A failed save no longer leaves a false screen

The app has no server: when Firestore rejects a write, what remains is what is in memory. If memory had already been updated, the screen showed a result the database never accepted, and the amount went back on reload with no explanation.

- **Write first, then update** (September): fixed in twenty-two operations across open accounts, investment tracking, financings and income edited from the popup. Every case has a regression test seen failing before the fix.
- **A missing method** (September): changing language raised `this.salvaCategorie is not a function` and left the categories normalised in memory but never saved. The method had never existed, and the tests supplied it as a stub.
- **Two October fixes**: a save superseded by the typing delay never resolved, so a button disabled until the save ended stayed stuck until reload; and a failed optimistic income insert removed the wrong entry when another one had landed at the top of the list in the meantime.

### The October interface review

A set of 41 proposals from a mockup was applied to the real app, with identifiers and handlers kept.

- **Navigation**: a profile menu in the header, a four-tab bar on mobile with a "More" sheet, and a switcher that shows one savings chart at a time.
- **Lists**: compact rows with relative dates and icon actions, month arrows and date shortcuts, and delete with "Undo" instead of a confirmation dialog.
- **Defaults**: sections reordered, with two hidden by default for people who have not saved their own preferences. Saved preferences are left as they are.
- **Settings next to what they affect**: payment-method coverage moved from the profile to a gear on the cash section, the accountant documents entry of the tax wizard moved to the Documents tab, and a gear on the country map lets you assign a country to expenses that lack one, in one pass.
- **Charts**: the year comparison covers the same part of both years and ranks the top five categories by combined spend, and the expenses chart gets a monthly budget line. Amounts and percentages are written in Italian notation everywhere.
- **Found and fixed on the way**: Escape did not close two windows; with five or more entries on the same day, the one just added could drop out of the five rows shown after a reload (2 times in 10); after logout and login the investment card buttons did nothing; a card's insight popup opened once per render; the currency breakdown vanished with a single currency even when switched on.

### Accessibility and production-only defects

- **99 violations to 0** (September): an axe-core audit of the login, the tutorial and nine sections in both themes, plus reflow checks at 280, 320 and 375 px. Text colours moved to theme-aware aliases, currency colours darkened, section toggles named, and horizontal scrolling removed on narrow screens.
- **Contrast measured on the render** (September → October): a rewritten checker reads the real design tokens, and a probe measures the rendered page. In October it took the pairs below AA in light theme from 7 and 15 (at 1280 and 375 px) to 0, including budget labels that were nearly invisible.
- **Service worker cached for a year** (September): the hosting rule gave the one file whose job is to change an immutable one-year cache. Measured on the live site and fixed.
- **Mobile header lost in production only** (September): the build emitted two stylesheets in the wrong order, so the base rules beat the media queries. Development looked right.

### Splitting the largest classes

Seventeen oversized classes were split into modules with the suite green at every step. The largest, `investments.js`, went from 1,945 to 521 lines and `documents.js` from 1,501 to 234 (September).

---

## Aggregate metrics across the project's history

| Metric                                           | Value                                            |
| ------------------------------------------------ | ------------------------------------------------ |
| Working sessions                                 | 146 (Jan 2026 → Oct 2026)                        |
| User-facing features introduced                  | 30+                                              |
| Security vulnerabilities closed                  | Multiple XSS + 3 CSV injections + 5 recent HIGH  |
| Silent failures made visible                     | 15+                                              |
| Total tests on 10 October 2026                   | **13,672** across 672 suites, all passing        |
| Global Lines coverage                            | 0% → **98.51%** (measured on 10 October 2026)    |
| Initial bundle                                   | 1.73 MB → 900 KB (-48%)                          |
| Image assets                                     | 6.2 MB → 340 KB (-95%)                           |
| `app.js` reduction                               | 13,150 → 576 lines                               |
| `investments-modals.js` reduction                | 1,439 → 41 lines (barrel)                        |
| Inline styles removed                            | ~540 → ~85 (-84%)                                |
| Hardcoded `rgba()`                               | 388 → 12 (-97%)                                  |
| New pure modules extracted                       | 75+                                              |

---

## What this means for Budgee users today

In ten months Budgee evolved from a working but fragile app into a product that is:

1. **Fast**: initial bundle halved, images down 95%, minified build, full offline.
2. **Secure**: every known XSS vector closed, CSV injection blocked, password complexity rules, UUIDs as IDs, restrictive Firestore rules, auto-logout on inactivity.
3. **Reliable**: errors that used to vanish silently are now visible. A button that does not work tells you why.
4. **Verifiable**: 13,672 tests across 672 suites. Critical functions (login, budgets, investments, exports) are covered above 90%. A future change that breaks something is caught before deploy.
5. **Transparent**: the code is linked from the app's header. Anyone can verify what Budgee does with their data.
6. **Yours**: the whole account downloads as a single archive, the deletion leaves nothing behind, and the one feature that sends data to a third party asks first and can be switched off.
7. **Not about to hit a wall**: transactions are read from and written to monthly documents, and the step that stops the single record from growing is built, tested and waiting to be switched on.
8. **Working to written rules**: the terms of use and the privacy notice can be read before registering, the acceptance stays tied to the version of the text, and a new release does not take over until you say so.

The next development cycle builds on a foundation far better protected by tests and security audits than it was ten months ago, has the step that removes the storage ceiling built and tested, and can take a new language without touching the code.
