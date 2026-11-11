# Changelog

Earlier versions are archived with their own releases (GitHub / Zenodo).

## Package 1.30.1 (R2026.37, 2026-11-11)

### New address

The site moves to **https://microbiome-repo.colorado.edu/**, a University of Colorado Boulder server. The catalog data are
unchanged from 1.30.0; this release only changes where the site lives and how it is served.

- Canonical links, `robots.txt`, the sitemap, `llms.txt` and `security.txt` now use the new address. The old address, microbiomerepo.com, will become a permanent redirect that keeps the path, so existing links and the
  Zenodo records that cite it keep working.
- The server deploys the newest exact `site-vX.Y.Z` tag of the public site repository. Each release now carries a root
  `version.json` with its site tag, and a post-release check in the site repository confirms the server is serving it.
- A `404.html` page is served for missing addresses. It offers a project's catalog page or a registry lookup when the missing
  address names a project accession.
- d3 and topojson are now served from the site itself with the same Subresource Integrity hashes; only the DuckDB-WASM engine of
  the Samples and Registry explorers still loads from jsDelivr.
- `make verify` now rejects a build that the server would refuse: symlinks, files without an extension, or file types outside
  the server's allow-list.
- `CHANGELOG.md` again lists every public release (1.26.0 onwards). Since 1.28.0 the entries for 1.28.0, 1.29.0, 1.29.1 and
  1.30.0 had been dropped when the file was trimmed to the public history; they are restored from their source files.

## Package 1.30.0 (R2026.36, 2026-11-06)

An integrity release. Package 1.29.1 (R2026.35) stays frozen as the benchmark version; nothing here changes it.

### Sandpiper profiles rebuilt (`scripts/refresh_sandpiper.py`)

- Every Sandpiper table was rebuilt in one pass from Sandpiper **2.0.1** (Zenodo record 21287531, GTDB R232) for the
  **current** catalog samples only. Earlier tables were built for package 1.11.0 and extended in R2026.31. 2.0.1 ships
  *unfilled* coverage; it is converted to filled coverage (each node = its whole subtree). Root coverage matches 2.0.0 for all
  449,912 runs present in both versions.
- 416,961 current samples in 3,136 projects have a profile (51.4 % of 810,820), from 449,912 DNA runs. Against 1.29.1: no
  sample lost, 26 gained. Root coverage changes by more than 1 % for 3 samples and the top genus for 3.
- Removed: 6,235 summary rows of samples retired in earlier releases. Metatranscriptome (RNA) runs no longer enter a community profile.
- QC flags from Sandpiper's per-run metadata (low complexity, read-fraction warning, non-metagenome organism label, ecological
  prediction) are now set for every sample; they were empty for the 83,544 samples added in R2026.31, as were
  `runs_profiled` and `sandpiper_url`. 67,175 profiled samples carry at least one flag.
- `catalog_sandpiper_study_coverage.csv` has one row per current project (it had stale rows for 174 projects with no current
  sample and wrong counts for 278). The PCA was refit: 405,460 samples, 388 genera.
- `catalog_runs.sandpiper_profiled` follows the 2.0.1 run list (registry flags 838,702 → 840,403 runs).

### Release integrity checks (`catalog.release.integrity_checks`, run by `make verify`)

Cross-table checks on current rows: unique sample keys, samples without runs, Sandpiper rows only for current samples with a
flagged profiled run, coverage counts equal to the sample table, propagated values only within a person, no person with two sex
values, ages 0–122 years and dates from 1980 to the build date. Report: `build/integrity_checks.json` (pipeline).

**Identify people by (`study_accession`, `person_key`).** `person_key` is a project's own subject id; 9,577 values recur
across projects.

### Value gates and checked corrections (`catalog.scopes.plausibility`)

Removed values are kept, with the rule that removed them, in the new table **`catalog_rejected_values.parquet`**.
- Gates: age below 0 days or above 122 years.
- Corrections from an Opus 5 reading of 31 projects whose values looked implausible (follow-up over 20 years, impossible ages,
  dates before 1990). Only verdicts with confidence ≥ 0.7 are applied (`plausibility_corrections.csv` in the pipeline):
  - **Person grouping fixed** where a subject id is not a person id: PRJEB55648 (number reused in every village → village +
    number), PRJEB22863 (reused across the NSCLC and RCC cohorts → cohort + number), PRJEB59450 (running number → patient code),
    PRJNA1300398, PRJEB42155 (3 samples), PRJNA419524 (organ donors separated from their recipients), PRJNA1157371
    (`participant_ID` is a leaked spreadsheet row reference → grouping removed).
  - **Ages cleared or corrected** where the field held something else: a submission row counter (PRJNA1138254), days postpartum
    on breast-milk samples (PRJEB74322), the infant's age on the mother's samples (PRJEB83236, 385 samples), the recipient's age
    on FMT donor units (PRJNA1169096), misaligned ages (PRJEB91718), sentinels such as −1 or −957 years (PRJEB32762,
    PRJNA521587) and wrong table rows (PRJEB27661, re-read from the paper).
  - **Dates cleared or corrected**: birth dates entered as collection dates (PRJEB55787), one placeholder date for a whole deposit
    (PRJNA679286), a biobank's collection window (PRJNA1036630), two-digit year typos (PRJNA1031616).
  - Genuine and kept: PRJNA785545 (two donors followed for over 30 years), PRJEB12449 (stool archived in 1985–87),
    PRJNA1303200 (1984–85 cohort specimens), PRJNA471187 (banked serum), PRJNA542578 (a 111-year-old in a centenarian cohort).
  - Not applied (confidence 0.6): PRJEB1220 and PRJEB29373, whose ages need closed-access supplementary tables.
- Country values corrected in 5 projects: PRJEB37731 (the travel attribute had been read; SE → DK), PRJNA1430386 (US → CA),
  PRJNA589866 (HU → RS for one patient), PRJNA624223 (one Hong Kong cohort, CN → HK) and PRJNA644725 (US → BD).

### Per-person sex and country

A person's sex or country is copied to that person's samples that lack it, only where every sample of the person that has the
value agrees (determined_by `person_invariant_v1`; the evidence row names the sample it came from). In 1.29.1 this would add sex
to about 2,100 samples and country to about 600.

### Scope: non-human samples removed

5,528 current samples in 53 projects carried an environmental or animal organism name. Opus 5 read each project:
- **4,766 of them, in 41 projects, leave the catalog**: hospital and household surfaces, air, dust, water, soil, food products,
  mouse / rat / pig / bovine / chicken samples. Also in this group: ex vivo bioreactor cultures of human faeces (PRJNA473225),
  treated like the earlier exclusion of mice given human microbiota. One bacterial culture in PRJEB27661 is removed too.
- **762 are human samples** filed under a wrong organism name (for example human stool filed as "mouse gut metagenome", and
  human breast milk) and are kept.
- 6 negative controls in PRJNA419524 are now marked as controls.

### Interventions: filter I2 audited (`scripts/apply_i2_audit.py`)

Filter I2 (R2026.32) kept intervention codes only for controlled trials. Opus 5 re-read 141 projects whose codes it had removed:
all 121 single-arm before/after projects and a random 20 of the 106 others.
- I2 was mostly right: 125 of 168 codes were routine care that the study only observed.
- 35 codes in 30 projects were real study administrations and are restored: dietary supplements, diets, FMT, phage therapy,
  antibiotics given by the protocol, prebiotics and others. Two of the 20 randomly sampled observational / unknown-design
  projects had real administrations, so about nine more may remain among the 86 not read; they are queued for the next run.
- 23 of the 141 verdicts rest on archive records alone (no paper could be located) and are marked provisional in their notes.

### Site

New page **Atlas › Profile measures**: profiling coverage and why samples have no profile, label-versus-composition agreement
(a project-held-out classifier on genus profiles) with a queue of discordant samples, known-species share by country and body
site, and depth and host-DNA share.

## Package 1.29.1 (R2026.35, 2026-11-03)

### Probiotic audit (`scripts/apply_probiotic_audit.py`)

Before an external comparison with hand-curated metadata, Opus 5 read 131 projects whose probiotic annotation was in doubt:
81 whose title, archive description, short description, design statement or paper titles mention probiotic terms but that had
no probiotic annotation; 25 whose samples carry a probiotic value (arm, `probiotic_exposure` or `medication`) without a
project-level tag; 14 tagged projects whose design had been read as observational or unknown; and 11 projects whose probiotic
code had been set aside (the two earlier readings disagreed, or filter I2 kept codes only for trial designs). Each verdict names
its source and quotes it verbatim; only verdicts with confidence ≥ 0.7 change the catalog.

- 22 project-level interventions added: 20 probiotic, 1 synbiotic, 1 defined live biotherapeutic. Many are single-arm or
  NICU-protocol cohorts — filter I2 (R2026.32) had removed codes outside trial designs, which also dropped real single-arm use.
- 4 probiotic tags removed: 3 projects gave the product only to an in-vitro gut model or to mice, 1 recorded patients' own use.
- `probiotic_exposure` withdrawn for PRJNA701480 (50 samples): the yes / no values mirrored vaginal seeding vs placebo.
- Not changed (13 projects): verdicts below 0.7 confidence or unclear (for example a kefir trial, cohorts with no linked paper).
- Estimated recall of project-level probiotic administration in 1.29.0: 110 of 132 projects (83 %).
- Still open: per-sample arms. About 46 probiotic projects say which samples were in the probiotic arm; for the rest only the
  project is tagged. The `medication` value `probiotic_or_prebiotic` covers both kinds of product.

## Package 1.29.0 (R2026.34, 2026-11-02)

### Who and when: a person and time-point reading (new pass, `scripts/persontime_passes.py`)

Targets: the projects whose source describes repeated sampling but whose samples showed no person with two samples (487,
"repeated sampling, not resolved"), and the longitudinal projects whose repeatedly sampled people had no time axis (599);
647 of them had sample variables that could say who or when.

- Candidates (deterministic): sample attributes that repeat like person identifiers; sample names split into a person part
  and a time part (for example `P01_D7` → `P01` + `D7`), kept only when the person part repeats, each person-and-time pair
  is unique and the time part varies within persons; time variables (attributes, stored time-point labels, name parts,
  and numeric day / week counts); demographic and specimen vocabularies (age, sex, group, tissue, source) never count as a
  person.
- Two independent readings (Sonnet-class, role `rubric`) choose the person and the time variable and give each time value a
  label and its days since the time origin, only when the text or the value states them; a third reading, shown only the
  two disputed variables, settles disagreements (11 projects). Merge rules refuse sample types, body sites, fractions,
  storage or incubation times and bare dates as time axes, and a person variable with more people than the text states or
  whose sex changes within a person.
- Every proposal was checked by Opus 5 (role `adjudicate`) — the person variable against example samples, each time value
  against the text. Labels that only restate a code ("1" → "timepoint 1") are not written.
- Written: **8,728** subject identifiers in **85** projects (1,619 replace one-per-sample identifiers), **21,879** time-point
  labels (12,325 of them decode a stored code, e.g. "A" → "before ablation"), and **17,122** day offsets in **120**
  projects, which give their repeatedly sampled people a time axis.
- Quality: two pilots (40 and 80 projects) and a production audit of 30 projects (Opus 5 reading each project): subject
  identifiers 1,525 / 1,525, decodings 1,131 / 1,131, labels 1,598 / 1,652, offsets 2,383 / 2,453 correct. The wrong rows
  came from three projects: one visit code matched to the wrong visit (withheld), one age used as a time axis for people
  sampled once, and one "W-2" (two weeks before) merged with "W2". The last two patterns are now filtered across all
  outputs (`scripts/apply_persontime_filters.py`; 388 rows in 4 projects withheld) and blocked in the pass itself.
- Cost: $28.75 production + $5.94 pilots.
- Effect in the package (1.28.0 → 1.29.0): longitudinal projects whose repeatedly sampled people have a time axis 698 →
  **828**; "repeated sampling, not resolved" 487 → **402**; people sampled more than once 64,983 → **67,428** (samples
  272,430 → 280,639); projects whose time axis is a mapped time point 54 → 107. Samples with a subject identifier +7,109
  and with a time-point label +9,554 (the rest of the written rows replace or decode existing values).

### Study design: tie-break and re-reading

- The design reading was re-run on the 504 projects whose two readings had given different design types (416) or where a
  reading was missing (88), with the audit's prompt fixes from R2026.33; where the readings still differed, Opus 5 chose
  between them (146 projects: first 75, second 59, neither 12). 12 projects in the whole design table still carry
  "two readings differed" (was 416).
- 449 new design statements, each checked by Opus 5 against its text (303 supported, 146 revised to stated facts only).
- Cost: $24.9 readings, map checks and tie-breaks + $6.0 statement checks.
- Effect in the package: design statements 2,562 → 3,016; per-sample comparison-group values +2,497 and intervention
  values −81 (net), where the re-reading replaced a project's arms.

### New projects

- Registry delta since 2026-10-24: 134 new studies, 19 not seen before, 9 with a human signal; classified by two readings:
  5 admitted (4 in scope, 1 partly; all DNA), 687 specimens. Their study-level passes (cohort statements, interventions,
  design, short description) ran against an intermediate build.
- BioSample harvest falls back to EBI BioSamples for samples released within the last day (ENA answered 404 for 347 of them).

### Pipeline

- `scripts/monthly_intake.py` (select / fold / prep / samples) runs steps 1–4 and the new projects' sample stage in one place.
- Precedence: checked person / time rows now share the infant catalog's first rank and win on confidence. Without this,
  1,182 decoded time-point labels in 8 infant projects (e.g. `BL` → "baseline", `V1` → "birth") lost to the infant
  catalog's parser labels, which keep the raw code only (`tests/test_resolve_precedence.py`).
- Re-runs of the design reading fold in with `scripts/merge_design_rerun.py`; catch-up, design and verification scripts take a
  shard-name tag so a monthly run never overwrites an earlier run's shards.

## Package 1.28.0 (R2026.33, 2026-11-01)

### Study-design reading (new pass, `scripts/design_passes.py`)

Owner request: "understand the overall design of each study and present that to the end user". Targets: every project whose
project page had no design statement (1,729 projects), whose paper compares groups that no sample variable separated (1,123), or
whose trial arms were not recorded per sample (479): 3,067 projects in all.

- Two independent readings (Sonnet-class, role `rubric`) of the best text per project (the generating paper's abstract and
  methods, else abstracts, else the archive record), plus the project's candidate group variables (sample attributes and
  stems of sample names / free-text attributes with 2–40 values). Each reading gives the design type, a one-sentence design
  statement, the compared groups and the variable that separates them with a value → group map.
- Merge: the design type when the readings agree (2,563 projects; otherwise both are kept as "two readings differed"); the
  statement only then. A value → group map is proposed when both readings chose the same variable and agree on the value's
  group (634 projects), with technical controls, body-site keys and over-full groups refused.
- Every proposed map was checked value by value by Opus 5 (role `adjudicate`): 1,542 of 1,873 values confirmed; 517 projects
  kept an assignment covering ≥ 70 % of their specimens with ≥ 2 groups. A production audit (Opus 5, 30 random assigned
  projects) found 75 of 80 values and 4,353 of 4,681 specimens right; the one wrong project used country of recruitment as
  its groups, so gate G1 (`scripts/apply_design_filters.py`) now refuses place-only keys unless both readings call the study a
  comparison of groups and a group quote is verbatim: 17 projects (3,769 specimens) lost their assignment, leaving **500**.
- New per-sample field **`comparison_group`** (56,496 specimens in 500 projects): the study's own comparison group in the paper's wording, route
  R1 from the attribute / name stem. From the same maps: 8,402 new `health_condition` values and 2,910 new `intervention` values
  for specimens that had none (a code only where both readings and the Opus check agreed; specimens with an existing sample-level
  value keep it — 3,716 health-condition and 2,378 intervention disagreements are counted, not applied).
- New study columns in `catalog_studies`: `design_shape_reading`, `design_text_mode`, `design_statement`, `design_groups`
  (reading 1's groups, JSON), `design_group_key`, `design_n_participants_stated`, `design_reading_status`.
- Design statements: the same audit found an unsupported detail in 7 of 30 (most often a sample count given as a participant
  count), so every statement was checked against its text by Opus 5 (`scripts/design_statement_check.py`): 1,353 supported
  as written, 1,209 revised to keep only stated facts, 1 dropped; the site marks them "machine-written, checked".
- Design type (30 audited): 23 right, 4 partly right, 3 wrong — diagnostic-yield studies read as case–control, and tissue or
  site contrasts within one patient group read as group comparisons; both rules are now in the prompt for the next run.
- Cost: $115.40 readings + $13.22 Opus checks of the maps + $42.0 Opus checks of the statements (3,067 projects). Pilot 2 audit (80 projects, Opus 5 reading every assigned
  project): 38 of 39 values and 1,082 of 1,086 specimens correct.

### Site

- The study-design block now leads with the design type from the design reading where no paper reading existed, the
  machine-written design statement (labelled), the groups the source compares, and the comparison_group × body-site matrix.
- Project markers C (cases and healthy controls) and S (same people at several body sites) as in site 1.27.0.1.

## 1.27.0 (R2026.32)

- Study-level passes for the projects added in R2026.30 / R2026.31 (2,318 projects): one reading per project of the generating paper's full text (1,115 projects), else its abstracts (134), else the archive record (1,069), read twice independently. Cohort statements that both readings make for every participant, with a verbatim quote, are committed as route R3 (full text) or R4 (abstract / archive record) determinations with `determined_by = catalog_catchup_v1:*`; they fill sample fields only where no sample-level value exists. Every committed statement was checked a third time (Opus 5) against the passage around its quote; only confirmed statements are kept.
- Interventions, study design (longitudinal schedule, relatedness) and the one-sample-per-person rule for the same projects: `catalog_studies.interventions`, `intervention_design`, `longitudinal_design`, `relatedness_design`; generated `subject_id` values carry `determined_by = gut_subject_unique_v1` and a `parse_note` beginning `generated:` (not generated where the stated number of participants is smaller than the number of specimens). Study-level intervention codes from this pass are kept only for trial designs (randomized, non-randomized controlled, crossover); relatedness only for kinship designs confirmed by the verification reading.
- Supplementary tables for 146 more projects (route R2, `supp_join_v1`), with the same join ladder, column classification and value mapping as R2026.30.
- `catalog_studies.description` holds the archive's full project description (it was cut at 300 characters for the projects added in R2026.30 / R2026.31).
- Body site is a core field: `cov_body_site_code` in `catalog_studies`, counted in `n_fields_with_value`; `unknown_site` counts as not covered.
- Per-sample DNA / RNA flags (`has_dna`, `has_rna`) count only the runs of the sample's own project, and follow the project's resolved nucleic acid where the paper and the rRNA read share overrode a single archive label.
- `releases.csv` carries the Zenodo version DOI of every archived data release.

## Site update 2026-11-01 (tag site-v1.27.0.1; package unchanged)

- **Charts fixed in browsers.** The collection-year and sequencing-depth charts used the SVG class `bar`, which matched the
  page-wide `.bar` rule in `site.css` (width 100 px, height 10 px); browsers apply CSS width/height to SVG rectangles, so every
  column was drawn as a 100 × 10 px strip. Chart classes are now `gc-*`, and the new design matrix uses `ds-*` plus inline
  presentation attributes.
- **Study-design block** at the top of every project page (`site_generator/gen/pages/study_design.py`): the design type
  (read from the paper, from the admission classification, or inferred from the sample variables), one sentence with people,
  groups or arms, repeated sampling, body sites and families, a group × body-site matrix of people, a table naming the field and
  the attributes or texts each design element comes from, and paper-versus-data checks. Interventions and "Sampling design and
  relatedness" now follow it directly; Sources and Samples at a glance come after.
- **Two new project markers** on the Projects table: **C** cases and healthy controls (solid: ≥ 3 samples of each in
  `health_condition`; dotted: the source describes a case–control study but the samples do not separate cases and controls),
  **S** same people at several body sites (solid: ≥ 3 people with samples from two or more sites; dotted: several sites, people
  not identifiable). A legend for all markers sits above the table.

## 1.26.0 (R2026.31)

- One catalog of human-associated microbial communities sequenced by shotgun DNA or RNA sequencing, from every body site and at every age. All projects are in `catalog_studies.parquet`, all samples in `catalog_sample_metadata_wide.parquet` and `catalog_sample_determinations.parquet`, all runs in `catalog_runs.parquet`.
- DNA and RNA data sets are kept apart: `catalog_runs.nucleic_acid`, `catalog_sample_metadata_wide.has_dna` / `has_rna`, `catalog_studies.nucleic_acid` (+ `n_runs_dna`, `n_runs_rna`, `n_samples_dna`, `n_samples_rna`).
- Every sample carries its own `body_site_code`; projects carry `sample_body_sites` and `body_site_primary`.
- Methods and setting read from each project's generating paper (`platform`, `extraction_kit`, `library_kit`, `study_shape`, `n_participants`, `recruitment`, `settlement`, `n_places`, `lifestyle_paper`, `metadata_access_paper`, `n_exposure_items`) and the exposure items behind them (`catalog_exposure_items.parquet`); papers judged to have generated or reused a project's data in `catalog_study_papers.parquet`.
- Amplicon and single-cell data sets are excluded run by run; a sample without any in-scope data set is not in the catalog.
- Table names: every table of the catalog tier is named `catalog_*`; earlier packages stay archived with their own releases (GitHub / Zenodo).

