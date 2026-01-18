# 💰 Budgee (PWA)

<div align="center">

**Web App for Personal Finance Management**

🌐 [**Try Live**](https://financial-management-by-bonn.web.app) • 📱 Installable • ☁️ Cloud Sync

</div>

---

## ℹ️ Description

**Budgee** is a comprehensive progressive web application for personal finance management. Built with **HTML5, CSS3, and vanilla JavaScript**, it uses **Firebase** for the backend and **Chart.js** for interactive visualizations.

The app offers a modern and intuitive user experience, with a focus on **performance**, **responsive design**, and **offline-first functionality**.

---

## ✨ Key Features

### 💰 Complete Finance Management
- 💸 **Expense and Income Tracking** - Record all transactions with hierarchical categories
- 📈 **Investment Portfolio** - Manage bonds, stocks, ETFs, crypto, and real estate
- 💳 **Loan Management** - Track mortgages, car loans, and personal financing
- 📁 **Document Organization** - Google Drive integration with 25+ predefined folders
- 📊 **Savings Analysis** - Dedicated tab with cumulative growth and saving rate trends
- 🔄 **Recurring Transactions** - Automate subscriptions, salaries, and recurring bills

### 📊 Advanced Analytics
- 📈 **Interactive Charts** - Heatmaps, trends, Sankey diagrams, and cumulative views
- 🎯 **Budget Management** - Set monthly limits and monitor spending in real-time
- 🤖 **Smart Insights** - Get personalized suggestions based on your habits
- 🔍 **Advanced Search** - Find any transaction with multiple filters
- 📑 **Custom Reports** - Generate reports for any date range with proportional budgets

### 🌐 Modern Experience
- 🌍 **Multi-language** - Available in English 🇬🇧 and Italian 🇮🇹
- 💱 **Multi-currency** - Support for EUR € and PLN zł with automatic conversion
- 📱 **PWA** - Install as a native app on any device
- ☁️ **Cloud Sync** - Your data synced across all devices with Firebase
- 🔒 **Secure** - Firebase Authentication and Firestore security rules

### 📊 Interactive Visualizations

#### Calendar Heatmap
Analyze the distribution of your expenses and income by day. Click on each section to see transaction details.

#### Cumulative Trend Charts
Monitor monthly and yearly trends of your finances with line charts showing cumulative growth.

#### Combined Chart
Compare income vs expenses over time. Click on a point to see all transactions for that day with:
- 💰 Total income
- 💸 Total expenses
- 📊 Daily balance
- 📝 Detailed transaction list

#### Complete Savings Tab
An entire section dedicated to analyzing your savings:

**Advanced Charts:**
- **Monthly Savings Trend** - View income, expenses, and savings for each month with combined chart (lines + bars)
- **Cumulative Savings** - Monitor the progressive growth of your wealth with dynamic filled area (green/red)
- **Saving Rate Trend** - Analyze monthly savings percentage with dynamic colors based on performance

**Interactive Statistics:**
- 💰 **Total Saved** (clickable) - Shows complete breakdown of total income vs total expenses
- 📊 **Average Saving Rate** - Average savings percentage relative to period income
- 🏆 **Best Month** (clickable) - Identifies the month with highest savings and displays complete details
- ⚠️ **Worst Month** (clickable) - Identifies the month with lowest savings to understand where to improve

**Smart Insights:**
- Automatic analysis of savings patterns
- Savings rate evaluation (Excellent ≥20%, Good ≥10%, Low ≥0%)
- Trend comparison between consecutive months
- Personalized suggestions to optimize savings

**Flexible Time Filters:**
- Current month
- Last 3, 6, or 12 months
- Year to date (default)
- All available period
- Custom range with specific dates

### 🎯 Budget and Monitoring

- **Monthly Budgets** - Set spending limits for each category
- **Copy Previous Month Budget** - Quickly resume budgets from the previous month with selective selection
- **Real-time Monitoring** - View usage percentages with colored bars
- **Automatic Alerts** - Notifications when you exceed your budget
- **Smart Forecasts** - Estimate end-of-month spending based on current trends
- **Proportional Budgets** - Automatic calculation for custom periods

### 🔄 Recurring Transactions

- **Recurring Expenses** - Schedule repeating expenses (Netflix subscriptions, rent, bills, gym)
- **Recurring Income** - Schedule repeating income (salary, freelance, rental income)
- **Flexible Frequencies** - Daily, weekly (choose day), monthly (first/last/specific day), yearly
- **Manual Confirmation** - Each recurring transaction requires your confirmation before being recorded
- **Complete Management** - Edit amount, description, and payment method for future occurrences
- **Smart Deletion** - Skip individual payments or delete all future occurrences while keeping history
- **Elegant Modals** - Premium interface without native alerts, with smooth animations and modern design

### 📈 Investment Management

- **Multiple Types** - Bonds, deposit accounts, stocks, funds, ETFs, crypto, real estate
- **Complete Tracking** - Invested amount, interest rate, subscription/maturity dates
- **Returns** - Monitor expected return (gross/net) and actual return
- **Progress Bar** - Visualize time elapsed until maturity
- **Income Linking** - Associate received dividends and interest to investments
- **Aggregate Statistics** - Total invested, total returns, average rate

### 💳 Loan Management

- **Multiple Types** - Mortgage, car/personal/student loans, phone financing, degree redemption
- **Complete Tracking** - Loan amount, number of installments, installment amount, interest rate
- **Payment Monitoring** - Installments paid, total amount paid, remaining to pay
- **Progress Bar** - Visualize payment progress
- **Expense Linking** - Associate installment payments to expenses
- **Aggregate Statistics** - Total loaned, to pay, paid, remaining, average installment

### 📁 Document Organization

- **Google Drive Integration** - Secure access via OAuth 2.0
- **25+ Predefined Folders** - Payslips, invoices, deductible expenses, medical reports, contracts, insurance, bills, bank statements, tax documents, and more
- **Year Folders** - Automatic organization by year (2024, 2025, etc.)
- **Customization** - Choose which folders to display
- **Direct Access** - Quick links to open folders on Google Drive
- **Preference Sync** - Your choices saved and synchronized

### 🤖 Smart Insights

The automatic analysis engine helps you:
- 📈 Identify patterns in your spending, saving, and investment habits
- 🔍 Discover the most used categories and optimize distribution
- 📅 Compare different periods (current month vs previous, year over year)
- 📊 **Custom Period Reports** - Generate reports for specific date ranges with proportional budgets
- 💸 **Money Flow (Sankey Diagram)** - Visualize how your money moves
- 💡 Receive personalized suggestions to optimize your finances
- 🎯 Analyze investment performance and financing costs

### 🌍 Multilingual

Fully translated interface in:
- 🇮🇹 **Italian**
- 🇬🇧 **English**

Instant language switching with automatic translation of all categories, charts, and messages.

### 📱 Progressive Web App

- **Installable** - Add to home screen as a native app
- **Responsive Design** - Optimized for mobile, tablet, and desktop
- **Performance** - Fast loading and smooth animations

---

## 🛠️ Technologies Used

### Frontend
- **HTML5, CSS3, JavaScript** - Vanilla JS for maximum performance
- **Chart.js** - Library for interactive and responsive charts
- **PWA APIs** - Service Worker, Web App Manifest, Cache API

### Backend & Storage
- **Firebase Firestore** - Real-time NoSQL database
- **Firebase Authentication** - Secure authentication system
- **Firebase Hosting** - Fast and reliable hosting with HTTPS
- **Firebase Cloud Functions** - Serverless functions for Telegram

### Integrations
- **Google Drive API** - Document storage and organization
- **Google Identity Services** - OAuth 2.0 authentication
- **Telegram Bot API** - Notifications and automated reports
- **SheetJS (xlsx)** - Excel file import
- **JSZip** - Data export in ZIP archives

### Architecture
- **Single Page Application** - Smooth navigation without reload
- **Offline First** - Local persistence with cloud synchronization
- **Responsive Design** - Mobile-first approach
- **Real-time Sync** - Instant updates across all devices

---

## 🚀 How It Works

### 1. Registration and Login
Create an account with email and password. Verify your email to access.

### 2. Create Your Profile
Set your name, default currency, and preferred language.

### 3. Record Transactions
Add expenses and income with just a few clicks. Each transaction includes:
- Amount and currency
- Category and subcategory
- Description (optional)
- Date and payment method
- Link to investments/loans (optional)

### 4. Manage Investments
Add your investments to the portfolio:
- Enter name, type, and invested amount
- Specify interest rate and dates
- Link income (dividends/interest) to investments
- Monitor expected and actual returns

### 5. Monitor Loans
Keep track of loans and mortgages:
- Record loan amount, number of installments, and installment amount
- Link installment payments to expenses
- View progress and remaining to pay
- Monitor total interest

### 6. Organize Documents
Connect Google Drive to store documents:
- Sign in with your Google account
- The app automatically creates 25+ organized folders
- Customize which folders to display
- Quickly access your financial documents

### 7. View Charts
Charts update automatically with each new transaction. Click on any element to see details.

### 8. Analyze Savings
Go to the "Savings" tab to:
- View monthly trend of income, expenses, and savings
- Monitor cumulative wealth growth
- Analyze monthly savings percentage
- Click on statistics for in-depth details
- Receive automatic insights on your savings patterns

### 9. Set Budgets
Define monthly limits for each category and monitor usage in real-time. Use the "Copy Previous Month Budget" feature to quickly copy budgets from last month.

### 10. Generate Custom Reports
Create reports for specific periods (e.g., from October 15 to November 20). The system automatically calculates proportional budgets based on the actual days of each month included in the period.

### 11. Configure Recurring Transactions
Schedule expenses and income that repeat:
- **Create a recurrence**: Check "🔄 Recurring Expense/Income" and choose the frequency
- **Confirm payments**: Receive notifications when it's time to confirm a recurring transaction
- **Manage recurrences**: Click "📋 Manage Recurring" to edit or delete future schedules

**Practical examples:**
- 💰 Monthly salary (last day of month)
- 🏠 Rent (first day of month)
- 📺 Netflix (15th of each month)
- 🏋️ Gym (every Monday)
- 💼 Fixed freelance client (first of month)
- 💳 Mortgage payment (linked to financing)

### 12. Receive Insights
The system automatically analyzes your habits and provides personalized suggestions. Visualize money flow with the Sankey diagram to understand where your money goes.

---

## 🎯 Try the App Now!

<div align="center">

### 👉 [**OPEN BUDGEE**](https://financial-management-by-bonn.web.app) 👈

**No installation required • Works on all devices • Cloud-synced data**

[![Try Now](https://img.shields.io/badge/🚀_START_FREE-4285F4?style=for-the-badge&logo=google-chrome&logoColor=white&labelColor=1a73e8)](https://financial-management-by-bonn.web.app)

</div>

---

## 📬 Contact

For more information, collaborations, or feedback:

📧 **Email:** [andreabonacci95@protonmail.com](mailto:andreabonacci95@protonmail.com)

---

<div align="center">

**The source code is private, but you can test the application via the link above.**

**© 2025 Andrea Bonacci - All rights reserved**

</div>
