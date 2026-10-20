# EPM to CLH: features needed by database

A dashboard that shows why building one feature doesn't move EPM databases to CLH. A database can migrate only when every feature it relies on exists in CLH, and most databases need several.

## What it shows

1. **Executive summary:** why it matters, headline statements, what this means, and decisions needed.
2. **At a glance:** key counts; contract value, learner and admin totals; a breakdown of databases that need review; and how the number of databases able to migrate grows with each build step.
3. **Build simulator:** click features to mark them built and see databases, contract value and learners released, plus a bar of features still missing per database.
4. **Features needed:** one card per feature; select features (any or all) to list the databases that need them.
5. **Databases by selected feature:** the filtered list, with CSV export.
6. **Databases sorted by features needed:** every database, rolled up by account (ECR ID). All VISN (VA network) databases form one group regardless of ECR or CMX ID. Shows contract value, learners and admins (summed for each group). Opens sorted by Multi-database (Org Picker) count; click any column to re-sort; click a group to see its databases. CSV export lists every database.
7. **How we counted:** the scope and counting rules, plus a glossary of terms.
8. **Why we count databases, not customers:** why EPM databases can't simply be translated into customers.

## Editing text in the browser

1. Open the dashboard and click **Edit text** (bottom right).
2. Click any heading, hint or paragraph and type your changes. Numbers, cards and tables come from the data and can't be edited this way.
3. Click **Save edited copy**. Your browser downloads an updated HTML file.
4. Replace `index.html` (or the single-file dashboard) with the downloaded file, then commit and push. If you edit the single-file version, ask for it to be converted back to `index.html` so the data stays in `data/dashboard-data.js`.

Edits aren't saved until you download the copy; reloading the page discards them.

## Repository layout

```
index.html                     Dashboard page (HTML, CSS, JavaScript)
data/dashboard-data.js         Generated data file loaded by index.html
assets/fonts/                  Elsevier brand web fonts (Tiempos Text, National 2)
assets/favicon.svg             Browser tab icon
scripts/build_data.py          Rebuilds data/dashboard-data.js from the Client Directory workbook
requirements.txt               Python dependency for the build script
.github/workflows/pages.yml    Publishes the site to GitHub Pages on push to main
.nojekyll                      Tells GitHub Pages to serve files as-is
robots.txt                     Asks search engines not to index the site
standalone/                    Single-file copy of the dashboard (data, fonts and icon built in) for emailing or opening offline
```

## Hosting on GitHub Pages

**Before you publish:** the data file contains account names, IDs, contract values and learner counts. On most GitHub plans a Pages site is public even if the repository is private. Use GitHub Enterprise with Pages access control (private Pages), or confirm with your data owner that the content can be public. See "Data sensitivity" below.

1. Create a **private** repository and push the contents of this folder to the `main` branch.
2. In the repository, go to **Settings → Pages → Build and deployment** and set **Source** to **GitHub Actions**.
3. Push to `main` (or run the workflow from the **Actions** tab). The workflow publishes the site and shows the URL when it finishes.
4. If your organization supports it, set Pages **visibility** to **Private** so only signed-in members can view it.

Alternative without Actions: **Settings → Pages → Source: Deploy from a branch → `main` / root**.

You can also open `index.html` directly in a browser; no server or build step is needed.

## Updating the data

1. Save the latest Client Directory workbook locally. Don't commit it (workbooks are in `.gitignore`).
2. Run:

   ```
   pip install -r requirements.txt
   python scripts/build_data.py "path/to/Client Directory.xlsx"
   ```

3. Commit and push the updated `data/dashboard-data.js`. The Pages workflow republishes automatically.

Columns are found by header name, so added or moved columns don't break the build. If a header is renamed, update `HEADERS` in the script.

## Scope and method

- **Unit:** one record per CMX ID (one row per database). Rows without a valid CMX ID are left out.
- **Workbook filters:** if the main directory tab has Excel filters set, the build script reads and applies them. Otherwise it uses `EXCLUDE_PHASE` and `EXCLUDE_STATUS`, which mirror those filters (Phase: Cancel/Remove, Cancelled, Internal, NA, Data Moved to CLH, DirectCourse; Migration Status: Complete, NA, Net New, TTP Cutover, Handoff to AM).
- **Also excluded:** databases marked Canceled under CLH account status or Change Type, and client types Workforce Development, Internal MCS Site and all Connect types (`EXCLUDE_CLIENT_TYPE`).
- **Features:** Multi-database (Org Picker), HR Download, Self-registration link, SCORM Uploader, LinkedContent / Linked Tests (combined) and CK Nursing (subscription and Skills link combined). A feature counts as needed when its column is "Yes".
- **Build order:** steps are chosen to release the most databases at each step. Multi-database (Org Picker) is built together with HR Download because it depends on it (set in `DEP` in `index.html`).
- **Needs review breakdown:** uses Country, SSO Eligibility and Mosby Skills to show which databases rely on international support or complex/Connect SSO, which may be ready, and which have incomplete records.

## Data sensitivity

- Keep the repository **private**.
- Don't publish to a public Pages site without approval from your data owner. If the site must be public, remove account names, IDs and contract values in `scripts/build_data.py` first.
- The fonts in `assets/fonts/` are licensed Elsevier brand fonts. Keep them in internal or private repositories only.

## Contact

Tessa Richards, Customer Readiness Manager (t.richards@elsevier.com)
