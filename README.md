# Annual Budget Planner for YNAB

A single-file browser tool for annual financial planning that integrates with [YNAB (You Need A Budget)](https://www.ynab.com/).

YNAB is great for month-to-month budgeting, but limited for planning many months ahead. This tool helps answer questions like: *"Can I afford a big holiday in September given my future spending commitments?"*

## Features

- **Annual Overview** — See your full year's income, fixed expenses, and flexible expenses at a glance
- **Priority Breakdown** — View expenses by priority level (P1-P5) with running remainder to see what's left after each tier
- **Monthly Tracking** — 12-month table showing closing balances and headroom against your float threshold
- **Float Warnings** — Highlights months where your balance drops below your safety threshold
- **Scheduled Transactions** — Automatically includes one-off and monthly recurring transactions
- **CSV Export** — Download your annual plan as a spreadsheet

## Setup

1. Open `index.html` in any modern browser
2. Get your YNAB API token from [YNAB Developer Settings](https://app.ynab.com/settings/developer)
3. Find your Budget ID (it's in the URL when viewing your budget: `app.ynab.com/{budget-id}/budget`)
4. Enter both in the Settings panel and click Save
5. Click **Refresh Data**

## Configuration

| Setting | Description |
|---------|-------------|
| API Token | Your YNAB Personal Access Token |
| Budget ID | The UUID of your YNAB budget |
| Float Threshold | Minimum balance you want to maintain (for warnings) |
| December Balance | Your expected closing balance from the previous year |
| Flexible Prefix | Emoji prefix that marks flexible expense categories (default: 🎯) |

## Category Setup in YNAB

For the tool to work correctly, organize your YNAB categories:

- **Fixed expenses** — Regular categories (rent, utilities, subscriptions)
- **Flexible expenses** — Prefix with the flexible emoji + priority number

Example flexible category names:
```
🎯1 Groceries
🎯2 Entertainment
🎯3 Travel
🎯4 Shopping
🎯5 Misc
```

Priorities 1-5 are included in calculations. Categories with priorities 6-9 are excluded (useful for "someday" items).

## Tabs

- **Overview** — Summary cards + priority breakdown table
- **Monthly** — Detailed 12-month table with headroom column
- **Transactions** — View scheduled transactions by month
- **Projects** — Track flexible expense projects

## Requirements

- Modern web browser (Chrome, Firefox, Safari, Edge)
- YNAB account with API access
- No server, build tools, or installation required

## Privacy

All data stays in your browser. Your API token is stored in localStorage and API calls go directly to YNAB. Nothing is sent to any third-party server.

## License

MIT
