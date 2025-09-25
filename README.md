# Industrial Engineering Offline Dashboard

An offline-first industrial engineering dashboard delivered as a single `index.html` file. The application runs entirely in the browser, ships with all dependencies embedded locally, and persists data in the user's browser storage so it can operate without any network connectivity.

## Key capabilities
- **Modular KPI coverage** for OEE, MTTR, work orders, training compliance, CAPA adherence, safety incidents, and safety walks.
- **Global filters** (date range, line, shift, status) that persist across sessions and scope the calculations and visualisations for every module.
- **Offline data interchange** via locally generated Excel templates, transactional imports with validation, and filtered exports powered by bundled SheetJS utilities.
- **Local persistence and backups** using `localStorage`, JSON backup/restore, reset to factory defaults, and audit logs for imports, exports, and backups.
- **Responsive layout** with sidebar navigation, KPI tiles, Plotly charts (served locally), and detailed tables optimised for desktop and large tablet form factors.

## Architecture overview
- **Presentation**: Vanilla HTML/CSS/JS packaged in `index.html`. All scripts and styles are embedded so the app runs from disk without a build step.
- **Charts**: Local Plotly bundle (`assets/plotly-2.27.0.min.js`) operating in offline mode.
- **Excel handling**: Local SheetJS build (`assets/xlsx.full.min.js`) for template generation, imports, and exports.
- **Persistence**: Application data, logs, and cached filter preferences are stored in the browser's `localStorage` under the key `ie-dashboard-data`.
- **Seeding**: Deterministic sample data is created on first run to showcase each module and can be regenerated through the reset workflow.

## Data model
All core entities are represented as arrays inside the persisted JSON payload:

| Collection | Purpose | Primary fields |
|------------|---------|----------------|
| `lines` | Production line metadata | `id`, `name`, `area`, `is_active` |
| `shifts` | Shift definitions | `id`, `name`, `start_time`, `end_time` |
| `production_runs` | Run history for OEE | `line_id`, `date`, `shift_id`, `planned_minutes`, `good_count`, `total_count`, `ideal_cycle_time_sec` |
| `downtime_events` | Planned/unplanned downtime events | `line_id`, `start_ts`, `end_ts`, `reason_code`, `category`, `description`, `wo_id` |
| `maintenance_work_orders` | Work order lifecycle | `id`, `asset_id`, `line_id`, `request_ts`, `start_ts`, `end_ts`, `due_date`, `status`, `type`, `priority` |
| `maintenance_repairs` | Repair detail for MTTR | `wo_id`, `repair_start_ts`, `repair_end_ts`, `technician`, `failure_code` |
| `training_assignments` | Individual training requirements | `employee_id`, `course_code`, `due_date`, `completed_date`, `status` |
| `capa_records` | Corrective actions | `title`, `owner`, `due_date`, `closed_date`, `status`, `severity`, `root_cause` |
| `safety_incidents` | Safety incident log | `date`, `line_id`, `type`, `severity`, `lost_time_hours`, `status`, `corrective_actions` |
| `safety_walks` | Safety walk tracking | `scheduled_date`, `completed_date`, `owner`, `area`, `status`, `findings_count`, `actions_open` |
| `hours_worked` | Optional hours input for TRIR | `date`, `line_id`, `hours` |
| `import_log`, `export_log`, `backup_log`, `error_log` | Audit and diagnostic entries | timestamped text records |

## KPI calculations
- **Overall Equipment Effectiveness (OEE)**: Availability × Performance × Quality with unplanned downtime mapped per line/day.
- **Mean Time to Repair (MTTR)**: Average repair minutes from `repair_start_ts` to `repair_end_ts`, grouped by work order and line.
- **Work Order Due Date Adherence**: On-time percentage for closed work orders, overdue counts, and detailed lateness tracking.
- **Training Compliance**: On-time completion rate, overdue assignments, and compliance snapshot as of the selected date.
- **CAPA Adherence**: On-time closure rate, overdue CAPAs, severity distribution, and lateness statistics.
- **Safety Incident Metrics**: Monthly rollups, TRIR (when hours are provided), incident categorisation, and open corrective actions.
- **Safety Walk Adherence**: On-time completion rate, overdue walks, owner/area breakdowns, and action follow-up counts.

## Excel templates and interchange
Each collection has a versioned template with header validation. Generate templates from **Admin → Excel Templates**; imports reconcile IDs, normalise dates/numerics, and log results. Exports honour the active filters and reuse the template column ordering. When no rows match the filters, a header-only workbook is created so recipients can still see the expected schema.

## Using the dashboard offline
1. Open `index.html` in any modern desktop browser (Chrome, Edge, Firefox, or Chromium-based offline shell).
2. The app seeds sample data on first run and persists it automatically; subsequent launches resume from the saved state.
3. Adjust the global filters in the header. Selections are remembered across sessions and immediately applied to every module.
4. Navigate via the sidebar to explore KPI-specific dashboards. Tiles summarise performance, Plotly charts show trends, and detailed tables offer drill-downs.
5. Manage data from **Admin**:
   - Generate Excel templates or filtered exports.
   - Import updated spreadsheets (all parsing happens locally).
   - Create or restore JSON backups.
   - Reset to factory defaults when you need fresh sample data.

## Validation and QA
- Disconnect your network adapter and reload the dashboard to confirm zero external calls (all scripts/styles are local).
- Exercise each module after bulk imports to verify KPI calculations adjust immediately.
- Use the admin logs to confirm every import/export/backup event is captured for audit.
- Optional smoke test: clear browser storage and reopen `index.html` to validate deterministic seeding and reset workflows.

## Maintenance tips
- Periodically export a JSON backup and store it on an offline drive.
- Regenerate Excel templates after schema changes and share the updated workbooks with operators.
- To distribute updates, ship the latest `index.html` together with the `assets/` directory so recipients always have the matching Plotly and SheetJS bundles.
