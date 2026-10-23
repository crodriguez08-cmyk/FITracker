# FI Aging Tracker (GitHub Pages)

## Files

- `index.html` is the dashboard. It runs entirely in the browser.
- `data/latest-report.csv` is the daily Salesforce report. Power Automate writes it here. It is not included here, because your flow creates it.
- `data/history.json` holds the daily snapshots used for the change numbers and the History tab.

## Setup

1. Create a repository (private if the data is sensitive) and upload `index.html` to the root, plus the `data/` folder with `history.json`.
2. In the repository, go to Settings, then Pages. Choose "Deploy from a branch", branch `main`, folder `/ (root)`. Private repositories need a GitHub plan that supports Pages.
3. Your Power Automate flow overwrites `data/latest-report.csv` each morning. Reload the page to see the new data.

## Power Automate flow (outline)

1. Trigger: Recurrence (for example, weekdays at 7:00 AM), or "When a file is created" in the SharePoint folder.
2. Get file content from SharePoint for the daily report.
3. Write it to GitHub at `data/latest-report.csv`. Use the GitHub connector's create-or-update file action if your environment has it. Otherwise use an HTTP action with a `PUT` to `https://api.github.com/repos/OWNER/REPO/contents/data/latest-report.csv`, a personal access token with repository contents write access, the base64 file content, and the current file `sha` when updating an existing file.

## Column names

The page finds columns by their header names (case and punctuation don't matter). It expects headers close to these: Fulfillment Item Name, Account Name, Current Function, Status, Fulfillment Item Owner, Fulfillment Item Created Date, Function Aging Days, Design Review Date, Domain/URL, Client is currently using DBE, Login Information Acquired, and CSM Coach Name. The "Report columns found" panel at the bottom of the page lists each column it matched and flags any it could not find.

## Known limits

- `history.json` is not updated automatically. Today's numbers are always live from the report, but the History tab and the "vs yesterday" numbers only grow when someone adds a row to `history.json`.
- The owner list is built into the page. A new owner needs an edit to `index.html`.
