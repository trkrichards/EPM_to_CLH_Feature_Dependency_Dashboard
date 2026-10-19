# EPM to CLH: features needed by database

A dashboard that shows why building one feature doesn't move EPM databases to CLH. A database can migrate only when every feature it relies on exists in CLH, and most databases need several.

## What it shows

- **Executive summary:** the three headline statements, key figures, build order, the choices behind the numbers and open questions. Covers the full scope with no filters.
- **Headline metrics:** databases that depend on at least one of the six features CLH does not have yet, databases that depend on two or more, and the most common need.
- **Features needed:** one card per feature with the number of databases that need it. Select one or more features to list those databases ("any selected" or "all selected").
- **Database list and export:** appears once at least one feature is selected. Searchable, sorted by number of features needed, and exportable to CSV (includes contract value, learners and admins).

Filters: country and client type.

## Repository layout

```
index.html                 Dashboard page (HTML, CSS, JavaScript)
data/dashboard-data.js     Generated data file loaded by index.html
assets/fonts/              Elsevier brand web fonts (Tiempos Text, National 2)
scripts/build_data.py      Rebuilds data/dashboard-data.js from the Client Directory workbook
requirements.txt           Python dependency for the build script
```

## Viewing the dashboard

Open `index.html` in a browser. No server or build step is needed.

## Updating the data

1. Save the latest Client Directory workbook locally. Don't commit it (it's in `.gitignore`).
2. Run:

   ```
   pip install -r requirements.txt
   python scripts/build_data.py "path/to/Client Directory.xlsx"
   ```

3. Commit the updated `data/dashboard-data.js`.

Columns are found by header name, so added or moved columns don't break the build. If a header is renamed, update `HEADERS` in the script.

## Scope and method

- **Unit:** one record per CMX ID (one row per database). Rows without a valid CMX ID are left out.
- **In scope:** databases still to migrate. If the main directory tab has Excel filters set (for example Phase excluding Cancel/Remove, and Migration Status excluding Complete, Net New, TTP Cutover and NA), the build script reads and applies those filters. Otherwise it excludes Migration Status of Complete, Net New and TTP Cutover; Phase of Cancel/Remove, Cancelled, Internal, NA or Data Moved to CLH; Migration Status of NA; databases marked Canceled under CLH account status or Change Type; and client types Workforce Development, Internal MCS Site and all Connect types.
- **Features:** Multi-database (Org Picker), HR Download, Self-registration link, SCORM Uploader, LinkedContent / Linked Tests (combined) and CK Nursing (subscription and Skills link combined). A feature counts as needed when its column is "Yes". Multi-database uses the "Multi-database (Org Picker)" column, not "Multiple Databases (Old Def)".

To change features or scope rules, edit `GATES`, `EXCLUDE_PHASE`, `EXCLUDE_STATUS` and `EXCLUDE_CLIENT_TYPE` in `scripts/build_data.py`.

## Data sensitivity

`data/dashboard-data.js` contains account names, IDs, contract values and learner counts. Keep this repository **private**. The fonts in `assets/fonts/` are licensed Elsevier brand fonts; keep them in internal or private repositories only.

## Contact

Tessa Richards, Customer Readiness Manager (t.richards@elsevier.com)
