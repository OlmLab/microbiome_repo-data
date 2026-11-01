# Changelog

Earlier versions are archived with their own releases (GitHub / Zenodo).

## 1.27.0 (R2026.32)

- Study-level passes for the projects added in R2026.30 / R2026.31 (2,318 projects): one reading per project of the generating paper's full text (1,115 projects), else its abstracts (134), else the archive record (1,069), read twice independently. Cohort statements that both readings make for every participant, with a verbatim quote, are committed as route R3 (full text) or R4 (abstract / archive record) determinations with `determined_by = catalog_catchup_v1:*`; they fill sample fields only where no sample-level value exists. Every committed statement was checked a third time (Opus 5) against the passage around its quote; only confirmed statements are kept.
- Interventions, study design (longitudinal schedule, relatedness) and the one-sample-per-person rule for the same projects: `catalog_studies.interventions`, `intervention_design`, `longitudinal_design`, `relatedness_design`; generated `subject_id` values carry `determined_by = gut_subject_unique_v1` and a `parse_note` beginning `generated:` (not generated where the stated number of participants is smaller than the number of specimens). Study-level intervention codes from this pass are kept only for trial designs (randomized, non-randomized controlled, crossover); relatedness only for kinship designs confirmed by the verification reading.
- Supplementary tables for 146 more projects (route R2, `supp_join_v1`), with the same join ladder, column classification and value mapping as R2026.30.
- `catalog_studies.description` holds the archive's full project description (it was cut at 300 characters for the projects added in R2026.30 / R2026.31).
- Body site is a core field: `cov_body_site_code` in `catalog_studies`, counted in `n_fields_with_value`; `unknown_site` counts as not covered.
- Per-sample DNA / RNA flags (`has_dna`, `has_rna`) count only the runs of the sample's own project, and follow the project's resolved nucleic acid where the paper and the rRNA read share overrode a single archive label.
- `releases.csv` carries the Zenodo version DOI of every archived data release.

# Changelog

Earlier versions are archived with their own releases (GitHub / Zenodo).

## 1.26.0 (R2026.31)

- One catalog of human-associated microbial communities sequenced by shotgun DNA or RNA sequencing, from every body site and at every age. All projects are in `catalog_studies.parquet`, all samples in `catalog_sample_metadata_wide.parquet` and `catalog_sample_determinations.parquet`, all runs in `catalog_runs.parquet`.
- DNA and RNA data sets are kept apart: `catalog_runs.nucleic_acid`, `catalog_sample_metadata_wide.has_dna` / `has_rna`, `catalog_studies.nucleic_acid` (+ `n_runs_dna`, `n_runs_rna`, `n_samples_dna`, `n_samples_rna`).
- Every sample carries its own `body_site_code`; projects carry `sample_body_sites` and `body_site_primary`.
- Methods and setting read from each project's generating paper (`platform`, `extraction_kit`, `library_kit`, `study_shape`, `n_participants`, `recruitment`, `settlement`, `n_places`, `lifestyle_paper`, `metadata_access_paper`, `n_exposure_items`) and the exposure items behind them (`catalog_exposure_items.parquet`); papers judged to have generated or reused a project's data in `catalog_study_papers.parquet`.
- Amplicon and single-cell data sets are excluded run by run; a sample without any in-scope data set is not in the catalog.
- Table names: every table of the catalog tier is named `catalog_*`; earlier packages stay archived with their own releases (GitHub / Zenodo).

# Changelog

Earlier versions are archived with their own releases (GitHub / Zenodo).

