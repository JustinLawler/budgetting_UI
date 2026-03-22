# Initial App Specification

Build a single static HTML file called index.html — no build tools, no npm, no server required. It opens directly in a browser. All logic in vanilla JavaScript. Use Pico CSS (https://cdn.jsdelivr.net/npm/@picocss/pico@2/css/pico.min.css) for base styling.

## What this app does
It's a personal annual financial planning tool that pulls data from the YNAB API and displays a month-by-month table for the current year showing income, expenses, and running balances.

---

## SETTINGS (stored in localStorage)

Show a settings panel (collapsible) at the top of the page with these fields:
- YNAB API Token (text input, masked)
- YNAB Budget ID (text input)
- Float Threshold in euros (number input, e.g. 6500)
- December Closing Balance in euros (number input — this is the carry-in to January)
- Flexible expense emoji prefix (text input, default value "🔵")

Add a "Save Settings" button that persists all values to localStorage.
Add a "Refresh Data" button (outside the settings panel, prominent) that triggers a fresh fetch from the YNAB API.

On load, populate the settings fields from localStorage if values exist.

---

## YNAB API INTEGRATION

Base URL: https://api.ynab.com/v1
Auth: Bearer token in Authorization header.

Fetch the following on Refresh:

1. GET /budgets/{budget_id}/months — fetch all months for the current year (Jan–Dec). Each month object contains:
   - month (date string e.g. "2026-01-01")
   - income (actual income received that month)
   - budgeted (total budgeted that month)
   - categories (array) — each has: name, budgeted, activity

2. GET /budgets/{budget_id}/transactions — fetch all transactions for the current year to get actual flexible spend for past months.

3. GET /budgets/{budget_id}/scheduled_transactions — fetch all scheduled transactions to get:
   - Future income (scheduled transactions with positive amount)
   - Future flexible expenses (scheduled transactions whose category name starts with the flexible emoji prefix, e.g. "🔵")

All YNAB amounts are in milliunits (integer). Divide by 1000 to get euros. Negative milliunits = outflow (expense). Positive = inflow (income).

---

## PAST VS FUTURE BOUNDARY

The boundary is the last day of the previous month. So if today is 15th March, then:
- January and February are PAST months → use YNAB actuals
- March onwards are CURRENT/FUTURE months → use estimates

---

## MONTHLY CALCULATIONS

For each month Jan–Dec, calculate these values:

### Income
- Past months: use the YNAB month's `income` field (actuals)
- Current/future months: sum of scheduled income transactions falling in that month (positive amount transactions)

### Fixed Expenses
- A "fixed" category is any category whose name does NOT start with the flexible emoji prefix
- Past months: sum of `budgeted` amounts across all fixed categories for that month
- Current month: use current month's budgeted amount if already set in YNAB (budgeted > 0), otherwise use prior month's budgeted amount
- Future months: use prior month's budgeted amount (i.e. the most recent past month's fixed total)

### Flexible Expenses
- A "flexible" category is any category whose name starts with the flexible emoji prefix (e.g. "🔵")
- Past months: sum of actual `activity` amounts (negative = spend, make it positive for display) for flexible categories
- Current/future months: sum of scheduled transactions whose category name starts with the emoji prefix, falling in that month

### Available to Budget
= prior month's closing balance + this month's income
- For January: prior month's closing balance = December Closing Balance from settings

### Closing Balance
= available to budget − fixed expenses − flexible expenses

### Float Warning
Flag a month if closing balance < float threshold (from settings)

---

## THE TABLE

Display a full-width table with these columns:
- Month (e.g. "Jan 2026")
- Income (€)
- Fixed Expenses (€)
- Flexible Expenses (€)
- Available to Budget (€)
- Closing Balance (€)

Formatting:
- All euro amounts formatted as €X,XXX (no decimals needed)
- Past months: render with slightly muted styling (e.g. reduced opacity) to distinguish from current/future
- Current month: highlight row with a subtle background
- Any month where closing balance < float threshold: highlight entire row in red (use a soft red background, not aggressive)
- Negative numbers should display in red text

---

## STATUS BAR

Below the Refresh button, show a one-line status bar:
- When data is loaded: "X months below float threshold" (or "All months healthy ✓" if none)
- While loading: "Fetching from YNAB..."
- On error: show the error message clearly

---

## CODE QUALITY

- Keep all monthly calculation logic in a single clearly-named function called `calculateMonthlyRows(data, settings)` that takes raw YNAB data and settings, and returns an array of 12 row objects
- Add clear comments above each calculation step inside that function explaining what it's doing and why
- Name all other functions clearly (e.g. `fetchYnabData`, `renderTable`, `loadSettings`, `saveSettings`)
- Keep API calls in a separate section clearly marked with a comment block
- Handle the case where settings are missing — show a prompt to complete settings rather than a broken table

---

## LAYOUT

Top to bottom:
1. Page title: "Annual Budget Planner"
2. Settings panel (collapsible, collapsed by default if settings already saved)
3. Refresh button + status bar
4. The table
