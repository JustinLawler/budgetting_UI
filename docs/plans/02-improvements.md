# Improvements

We have a working index.html — a single-file YNAB annual budget planner.
Do not rewrite it from scratch. Make the following improvements to the existing file.

---

## 1. STATUS BAR IMPROVEMENTS

The current status bar shows a simple one-liner. Expand it to show:
- Total income expected for the year (sum of all 12 months)
- Total fixed expenses for the year
- Total flexible expenses for the year
- Net position for the year (total income − total fixed − total flexible)
- Count of months below float threshold, e.g. "⚠ 3 months below float" or "✓ All months healthy"

Display these as a clean summary row of cards/chips above the table, not as a single line of text.

---

## 2. ROW HIGHLIGHTING — REFINEMENTS

- Past months: muted styling is fine but make sure text remains fully readable (don't go below 70% opacity)
- Current month: add a left border accent (3px solid) in a neutral blue to make it easy to spot at a glance
- Months below float threshold: soft red background on the entire row + the closing balance cell gets bold red text
- Add a subtle top border between the past/future boundary row to visually separate history from estimates

---

## 3. FLOAT THRESHOLD INDICATOR

In the Closing Balance column header, add a small note showing the float threshold value, e.g.:
"Closing Balance (floor: €6,500)"
So the user always knows what they're being warned against.

---

## 4. COLUMN: FLOAT HEADROOM

Add a new column after Closing Balance called "Headroom".
Value = closing balance − float threshold.
- If positive: show in green (e.g. "+€2,400")
- If negative: show in red (e.g. "−€800")
- If within 20% above the float threshold: show in amber as a soft warning

---

## 5. TOOLTIPS ON KEY CELLS

Add a tooltip (HTML title attribute is fine, no need for a custom tooltip library) to the following cells explaining where the number came from:
- Income cell: "Actual YNAB income" (past) or "Estimated from scheduled transactions" (future)
- Fixed expenses cell: "Actual budgeted amount" (past/current) or "Based on [month]'s budgeted amount" (future, name the source month)
- Available to Budget cell: "Prior closing balance (€X) + this month's income (€Y)"

---

## 6. SETTINGS PANEL — SMALL IMPROVEMENTS

- Add a "Test Connection" button next to the API token field.
  On click: call GET /budgets (YNAB API) and show either "✓ Connected — found X budgets" or the error.
- After saving settings, collapse the settings panel automatically.
- Add a small "Last refreshed: [time]" label next to the Refresh button, updated each time data is fetched.

---

## 7. EDGE CASE HANDLING

Handle these gracefully (show a clear message in the relevant table cell, not a broken/blank value):

- Missing month data from YNAB API (e.g. a month with no budget set up yet)
  → show "—" in fixed/flexible cells, carry forward prior closing balance unchanged
- Scheduled transaction with no category assigned
  → skip it silently (don't crash, don't include in flexible total)
- API rate limit hit (YNAB allows 200 requests/hour)
  → show "Rate limit reached — please wait a moment and refresh again"
- API token invalid or expired
  → show "Invalid API token — please check settings" with a link/button to open settings panel
- Budget ID not found
  → show "Budget not found — please check your Budget ID in settings"

---

## 8. PRINT / EXPORT

Add a small "Export CSV" button above the table (right-aligned).
On click: generate a CSV of the 12-month table (all columns) and trigger a download as "ynab-annual-plan-2026.csv".
No libraries needed — use a plain Blob download.

---

## CODE QUALITY — MAINTAIN

- Keep all calculation logic inside calculateMonthlyRows() — do not move any maths into the render layer
- Add comments for any new logic added, matching the style of existing comments
- Do not introduce any new external dependencies beyond what's already in the file
