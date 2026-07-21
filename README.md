# Budgee - Personal Finance Manager

<div align="center">

**Take control of your finances, stress-free**

[Open the app](https://financial-management-by-bonn.web.app) · Installable as a PWA · Cloud-synced · Free

[![Italiano](https://img.shields.io/badge/Leggi_in_Italiano-009246?style=for-the-badge)](./README_IT.md)

</div>

---

## What Budgee is

Budgee is a Progressive Web App (PWA) for personal finance. You track expenses and income, set monthly budgets, follow your investments and loans, store financial documents on Google Drive and set savings goals - from any device, with real-time cloud sync.

It was built for people who keep their finances in a notebook or a messy spreadsheet and want something easier without giving up on detail.

![Budgee main dashboard](./docs/screenshots/en/01-expenses-dashboard.png)

*Monthly overview: income, expenses, savings rate and deductible totals on a single page.*

---

## See it in action

A full walkthrough: login, expenses dashboard, adding an expense, income, savings, adding and cashing out an investment, and budgets.

![Budgee demo](./docs/media/budgee-demo.gif)


---

## What you get

- Everything in one app: expenses, income, monthly budgets, investments, loans, open accounts, savings goals, deductible expenses and documents
- No subscription, no premium tier, no ads
- Runs on smartphone, tablet and desktop; you can install it from the browser and it behaves like a native app
- Cloud sync via Firebase, so the same data follows you across devices
- Works offline; anything you add without connection syncs back when you reconnect
- Quick to start: register and you are in
- Pattern detection on your spending, with practical suggestions
- Multi-currency: EUR, USD, GBP and PLN with automatic conversion

---

## Getting started

### 1. Open the app

Go to [financial-management-by-bonn.web.app](https://financial-management-by-bonn.web.app) from any browser (Chrome, Safari, Firefox, Edge).

### 2. Create your account

Tap **Register**, type your email and a password. You will receive a verification email; click the link to activate the account.

### 3. Install on your device (optional)

On mobile, the browser will suggest "Add to Home Screen". Accept and Budgee will show up like a native app. On desktop, look for the install icon in the address bar.

### 4. Add your first transactions

Start by adding a few expenses or income entries from the current month. Each transaction needs an amount, a category and a date.

### 5. Set your budgets

Open the **Budget** tab and set spending limits for the categories you want to monitor. Budgee will track your progress in real time.

---

## How to use Budgee

### Expenses and income

The core of the app. For every transaction you can set:

- Amount and currency (EUR, USD, GBP, PLN)
- Category, chosen from hierarchical categories you can customise
- Subcategory for more detail
- A free-text description
- Date and payment method (cash, digital, bank transfer, crypto, direct debit, check)
- Link to a loan instalment or to an investment contribution
- Tax-deductible flag for end-of-year reporting
- Recurring frequency (daily, weekly, monthly, yearly)

Other things you can do:

- Import transactions from an `.xlsx` file in bulk
- Export your data to CSV for backup or external use
- Search across transactions by date, category, amount, keyword, payment method or linked item
- Read live statistics: daily average, projected month-end total, highest-spending day
- Open monthly, weekly and yearly trend charts and category distributions

![Expense calendar heatmap and monthly trend](./docs/screenshots/en/02-calendar-trend.png)

*The daily calendar uses colour intensity to mark the days with higher spending; the trend chart below shows how spending builds up day by day.*

### Budget

Set a monthly spending limit for each category. Budgee shows:

- How much you spent compared to the limit, with progress bars
- A forecast of where you are headed by the end of the month
- A one-click way to copy last month's budget, so you do not re-enter limits every month

Budget categories work in a hierarchy: macro-category first, then sub-categories. You decide how granular to go.

![Budget overview with progress bars per category](./docs/screenshots/en/04-budget-progress.png)

*Hierarchical budgets per macro and sub-category, with progress bars and alerts when you go over.*

### Savings

A dedicated section that derives your savings from income minus expenses:

- Monthly trend chart over time
- Cumulative savings line
- Saving rate (what fraction of your income you actually keep)
- Best and worst months at a glance
- Pattern detection with short suggestions

![Savings analysis and monthly trend](./docs/screenshots/en/10-savings-trend.png)

*Monthly savings trend with year-to-date totals and saving rate.*

### Investments

Your portfolio in one place:

- Asset types: bonds, deposit accounts, stocks, mutual funds, ETFs, cryptocurrencies, real estate and more
- Per-investment details: invested amount, subscription date, interest rate, maturity date, expected gross/net return, actual return, free-text notes
- Time-based progress bars on how much is left until maturity
- Link dividends, interest or rental income to the right investment
- Link expense payments to track additional capital contributions
- Portfolio totals: invested capital, expected returns, actual returns, average interest rate, next maturity date
- Multi-filter search

![Investments portfolio with progress and expected returns](./docs/screenshots/en/06-investments-list.png)

*Each card shows invested amount, expected return, interest rate, maturity progress and quick actions for capital movements.*

### Loans and financing

Track every form of debt:

- Types: home mortgages, car loans, personal loans, student loans, phone financing, degree redemption and more
- Per-loan details: loan amount, start and end dates, total instalments, instalment amount, interest rate, instalments paid so far, total paid, remaining balance, notes
- Progress bars on how close you are to closing the loan
- Link expense entries to instalments to track real payments
- Record paid instalments or the cumulative paid amount
- Portfolio totals: total borrowed, total to pay (with interest), already paid, remaining, average monthly instalment, average progress
- Search and a detail view with full payment history and amortization schedule

![Loan tracking with payment progress](./docs/screenshots/en/08-financings-list.png)

*Mortgages and loans with instalment progress, total paid, remaining balance and interest rate.*

### Documents

Google Drive integration to keep your paperwork organized:

- 27+ predefined folders for payslips, invoices, deductible expenses, medical reports, investment documents, loan documents, contracts, insurance, tax documents, warranties, bank statements, credit cards, bills, taxes, real estate, vehicles, pension, donations, education, cryptocurrencies, condo fees, legal expenses, veterinary expenses, average balances and miscellaneous
- Folders organized per year automatically (2024, 2025, ...)
- Choose which folders you want to see
- Quick links that open the folders straight in Google Drive
- OAuth 2.0 authentication, no password sharing
- Folder names translated based on your language
- Folder preferences saved in the cloud and synced

### Recurring transactions

Automation for repeating expenses and income:

- Frequencies: daily, weekly (with day of week), monthly (first day, last day or a specific day), yearly
- Each occurrence has to be confirmed before it lands in your records, so nothing is added behind your back
- Edit the amount, description and payment method; delete a single occurrence or all future ones; check the confirmation history
- Cloud sync via Firestore
- Typical uses: rent, salary, subscriptions, utilities, insurance, loan instalments

### Insights and reports

The data view:

- Calendar heatmap with spending intensity for every day of the month
- Sankey diagram of where your money flows across categories
- Cumulative trend charts for expenses, income and savings over time
- Pattern detection on spending behaviour and anomalies
- Comparison between the current period and previous ones
- Tailored suggestions based on your habits
- Search with filters on category, period, amount, keywords
- Custom reports for any time range, with proportional budget calculations

![Category analysis with macro and sub-category breakdown](./docs/screenshots/en/11-categories.png)

*Category analysis ranks spending by macro-category, with drill-down to sub-categories and percentage share.*

### Open accounts

Track money you owe and money owed to you:

- Account types: debt or credit
- Per-account details: name of the person or supplier, type, initial amount, current balance, opening date, notes
- Payment history with date and amount for each instalment
- Accounts automatically marked closed when the balance hits zero
- Consolidated view in one list, or separated by active/closed
- Linked transactions: every payment associated with the account
- Totals: credits, debts, net balance
- Search by name, type, amount range or date
- CSV export

### Savings goals

Set and follow concrete targets:

- Goal types: one-off with a deadline, or ongoing
- Per-goal details: target amount, saved so far, deadline, progress percentage
- Colour-coded progress bars and completion markers
- Track how much of your liquid balance is allocated to goals
- Create, edit, archive or delete a goal at any time
- Automatic monthly amount needed to hit the deadline
- Priority by importance and deadline
- Visual confirmation when a goal is completed
- Archive view for past goals

### Tax-deductible expenses

A dedicated section for tax season:

- Flag expenses as deductible as you record them
- View deductibles grouped by tax year
- Quick access to current and previous year
- Drill down to any past year
- Add deductibles that do not have a matching expense (extras)
- Breakdown by category, to see what contributes most
- Yearly total
- Export-ready for tax preparation
- Recurring deductibles auto-flagged
- Track deductible investment contributions too

### Languages

Italian and English. You can switch from the settings at any time. Interface, categories, charts, statistics and document folder names follow your language.

### Light and dark theme

Choose light or dark mode from the header. The setting is saved and synced.

<div align="center">
<table>
<tr>
<td><img src="./docs/screenshots/en/01-expenses-dashboard.png" alt="Light mode" width="420"></td>
<td><img src="./docs/screenshots/en/16-dark-mode.png" alt="Dark mode" width="420"></td>
</tr>
<tr>
<td align="center"><em>Light mode</em></td>
<td align="center"><em>Dark mode</em></td>
</tr>
</table>
</div>

### Built for mobile

Budgee is designed mobile-first: every section adapts to small screens with bottom-tab navigation and compact layouts. Install it from your browser to launch it like a native app.

<div align="center">
<img src="./docs/screenshots/en/14-mobile-expenses.png" alt="Mobile expenses view" width="280">
&nbsp;&nbsp;&nbsp;
<img src="./docs/screenshots/en/15-mobile-calendar.png" alt="Mobile spending calendar" width="280">
</div>

### Interactive tutorial

First-time users go through a guided tour that explains the main features.

---

## Privacy and security

Your financial data is sensitive, and Budgee treats it that way:

- Each user can only access their own data, enforced at the database level
- All connections use HTTPS; data is stored with AES-256 encryption
- Accounts must verify their email before use
- Passwords must be at least 8 characters, with uppercase, lowercase and numbers
- No tracking, no ads; Budgee does not sell or share your data
- Data cached locally for offline use is synced back over an encrypted channel

For the detailed inventory of security measures, see the [security documentation](./SECURITY.md).

---

## Under the hood

Budgee is a Progressive Web App (PWA) built with web standards:

| Component | Technology |
|-----------|-----------|
| Frontend | Vanilla JavaScript (ES6+ modules), HTML5, CSS3 with custom properties |
| Architecture | Modular, feature-based; event delegation; explicit lifecycle |
| Charts | Chart.js |
| Backend | Firebase (Firestore, Authentication, Cloud Functions, Hosting) |
| Documents | Google Drive API with OAuth 2.0 |
| Notifications | Telegram Bot API |
| Offline | Service Worker with Network-First caching |
| Import/Export | SheetJS (xlsx) for Excel, JSZip for compressed exports |
| Security | Content Security Policy, HTTPS enforcement, input sanitization, Firestore rules |

---

## Feature checklist

- Expense and income tracking with categories, subcategories and payment methods
- Budget management with real-time monitoring and alerts
- Savings analysis with automatic calculations and trend chart
- Investment portfolio with returns and maturity dates
- Loan management with payment tracking and progress visualization
- Open accounts for credits and debts
- Savings goals with progress and deadline
- Tax-deductible expenses organized per year
- Document management with Google Drive integration
- Recurring transactions with flexible scheduling
- Search across all data types
- Spending insights with pattern detection
- Multi-currency: EUR, USD, GBP, PLN
- Multi-language: Italian, English
- Light and dark theme
- Offline mode with automatic sync
- Installable as a PWA on any device
- Excel import and export
- Cloud sync
- Interactive tutorial

---

## License

This project is proprietary. The web app is free for personal use. See [LICENSE](./LICENSE) for details.

---

## Feedback and support

If you have feedback, suggestions or a bug to report, get in touch.

Read the [feedback guide](./FEEDBACK.md) for how to:

- Report bugs in a way that is actually useful
- Request new features
- Share your experience
- Ask for support

Quick contact: andreabonacci95@protonmail.com

---

## Author

Built by Andrea Bonacci - [github.com/AndreaBonn](https://github.com/AndreaBonn)

---

## Support the project

Budgee is free to use. If it helps you keep your finances under control and you want to give something back, you can leave a tip via PayPal. The amount is up to you and it is entirely optional.

<div align="center">

[![Donate with PayPal](https://img.shields.io/badge/Donate-PayPal-00457C?logo=paypal&logoColor=white&style=for-the-badge)](https://paypal.me/AndreaBonacci19)

</div>

---

<div align="center">

*The source code is private, but the app is free to use.*

*If Budgee turned out to be useful for you, a star on the repository is appreciated.*

**© 2025-2026 Andrea Bonacci**

</div>
