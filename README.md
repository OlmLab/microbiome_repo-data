# infant-gut-catalog-data

Published data tables of the **Infant Gut Shotgun-Metagenome Catalog** (https://microbiomerepo.com/).

* `package/` — the current data package (one file per table, ≤50 MB each; `VERSION.json` lists every file with its sha256).
* `audit/findings/` — applied auditor/owner findings (CSV, one file per date).
* Tags `data-vX.Y.Z` — every tagged version is built into `data_package_vX.Y.Z.zip` + `infant_catalog_vX.Y.Z.sqlite` by
  `.github/workflows/release.yml` and attached to the GitHub Release of that tag.

Code and documentation: https://github.com/OlmLab/catalog-pipeline (docs/RUNBOOK.md, docs/DATA_LAYOUT.md).
Licence: curated tables CC-BY-4.0; archive-derived fields retain their INSDC terms.
