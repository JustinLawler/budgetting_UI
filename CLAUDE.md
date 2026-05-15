# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Single-file personal annual financial planning tool that integrates with the YNAB (You Need A Budget) API. No build tools, npm, or server required — opens directly in a browser.

Purpose of this tool is to budget many months in advance. YNAB is very limited on its future budgetting functionality. I want to be able to see for instance can I afford a big or a small holiday in September given the future spending commitments I already have. 


## Development

To test changes, open `index.html` directly in a browser. No build step required.


## Elements / Concepts
- Data source - transactions (past and scheduled future) all come from YNAB. E.g.
    - Future expenses could be car insurance in September, holiday expenses spread out over multiple months (book flights in Feb, book hotel in April, dining out expenses in July)
    - Future expected income (monthly, extra like bonuses)
- Transactions are categorized as per YNAB categories 
- There are fixed and flexible categories:
    - fixed will be expected to be the same month-per-month
    - Flexible may have zero for many months, but then a big expense in 5 months time. 
    - Flexible expenses have an icon at the start of the category name
- Categories have priorities - p1 -> p5
    - priority number comes after the flexible icon



## Architecture

**Single file (`index.html`)** containing:
- Pico CSS (CDN) for base styling
- Vanilla JavaScript with no external dependencies
- LocalStorage for settings persistence

**Key JavaScript sections** (marked with comment blocks):
- `SETTINGS MANAGEMENT` — load/save from localStorage
- `YNAB API CALLS` — fetch months, transactions, scheduled transactions
- `MONTHLY CALCULATIONS` — all budget math lives in `calculateMonthlyRows(data, settings)`
- `RENDERING` — table, summary cards, CSV export
- `MAIN REFRESH FLOW` — orchestrates fetch → calculate → render

**Data flow:**
1. User configures API token, Budget ID, thresholds in settings panel
2. On "Refresh Data", app fetches from YNAB API (3 parallel requests)
3. `calculateMonthlyRows()` processes raw data into 12 row objects
4. Render functions display summary cards and table

**YNAB API specifics:**
- Base URL: `https://api.ynab.com/v1`
- All amounts in milliunits (divide by 1000 for euros)
- Negative amounts = outflows, positive = inflows
- Categories prefixed with flexible emoji (default 🔵) are treated as flexible expenses

**Scheduled transaction handling:**
- `never` (one-off): included in the month of `date_next`
- `monthly`: expanded to all months from `date_next` through December
- Other frequencies (weekly, yearly, etc.): skipped with a warning displayed to user

**Past vs Future boundary:**
- Months before current month use YNAB actuals
- Current/future months use scheduled transactions for estimates
