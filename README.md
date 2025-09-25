# Offline Industrial Engineering Dashboard

This repository packages a complete industrial engineering performance dashboard inside a single, offline-ready `index.html`. The application bundles all required JavaScript libraries and sample data, so teams can review KPIs, import/export Excel workbooks, and manage backups without internet connectivity or additional tooling.

## Features

- **Modular KPI coverage** – Overview tiles and dedicated pages for OEE, MTTR, maintenance work orders, training compliance, CAPA adherence, safety incidents, and safety walks.
- **Excel interoperability** – One-click template downloads, validated imports, and exports for both raw tables and KPI rollups powered by the embedded SheetJS library.
- **Local persistence** – All data, configuration, and activity logs are stored in browser `localStorage`, including automated sample data seeding for evaluation.
- **Offline audit tooling** – Built-in validation checks confirm that the dashboard serves all assets locally and avoids external network calls.
- **Backup & restore** – JSON backups, restores, and factory resets keep the experience self-contained for offline deployments.

## Getting Started

1. Clone or download this repository to an offline-capable machine.
2. Open `index.html` in any modern Chromium, Firefox, or Edge browser (double-click the file or use `File → Open`).
3. Interact with the sidebar to switch between KPI modules, apply the global filters, and review seeded demo data.

> The app seeds approximately one month of realistic sample records on the first launch. All data is stored locally, so subsequent visits load the same workspace unless you reset or clear the browser storage.

## Data Management Workflows

- **Excel templates:** Navigate to **Admin → Excel Templates** and download the pre-validated workbook for each domain.
- **Importing data:** Use the **Import Data** panel to upload edited templates. Imports validate required columns, normalize lookups, and update existing IDs in place.
- **Exporting tables and KPIs:** Dedicated buttons provide filtered raw table exports and KPI summaries (OEE, MTTR, adherence metrics, safety) as XLSX workbooks.
- **Backups:** The Admin page offers JSON backup creation, restore, and a “Reset to Defaults” action that reseeds the demo dataset.

## Offline Validation

The Admin page includes an **Offline Validation** card. Clicking **Run Offline Audit** executes the following checks and logs the outcome:

- Confirms the browser reports an offline state (`navigator.onLine === false`).
- Verifies that all script and stylesheet references originate from the local package (no CDNs).
- Scans runtime network resources to ensure no external URLs were fetched while the dashboard is open.

Audit results are displayed immediately and recorded under the Validation log so you can maintain evidence of offline compliance.

## Resetting or Updating Data

- **Factory reset:** Use the Admin reset button to clear local storage and rebuild with demo data.
- **Manual clear:** Clearing site data from browser settings or deleting `localStorage` for the file origin achieves the same effect.
- **Versioned schema:** The app stores a schema version (`STORAGE_VERSION`). When this value changes, the loader rehydrates missing structures automatically using default definitions.

## Packaging Notes

For distribution on locked-down networks, copy the repository contents (including the `assets/` directory) to removable media or integrate `index.html` into a kiosk/desktop wrapper such as PyInstaller + PyWebView or Electron. No additional dependencies are required once the files are on the target machine.

## Troubleshooting

- If imports fail, review the error log in the Admin page for detailed messages (missing columns, invalid dates, etc.).
- Use the Validation log to monitor successful template downloads, exports, backups, and offline audits.
- To refresh the UI after restoring a backup or editing data outside the app, reload the page—global filters and layouts will rebuild automatically.
