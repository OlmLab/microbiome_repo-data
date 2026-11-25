# Microbiome Repo — data package v1.34.2 (2026-11-24)

**One catalog, two tiers, all ages.** The **catalog** tables (`catalog_*`) are the curated tier: human-associated microbial communities sequenced by shotgun DNA or RNA sequencing, from every body site and at every age — **5,086 projects, 813,356 samples, 4,814,518 sample × field values** — with body site, age at collection, sex, BMI, country, health condition (controlled vocabulary), antibiotic exposure, subject and timepoint recovered from the archive record, from the supplementary tables of linked papers, from the paper text and from abstracts; every value carries a verbatim evidence quote (≤ 12 words), the source it came from and a confidence. Each run is labelled DNA or RNA (`catalog_runs.nucleic_acid`), each sample says which data sets it has (`has_dna`, `has_rna`), and each project carries the nucleic acid of its data sets (`catalog_studies.nucleic_acid`). Coverage over all catalog samples: age 24%, sex 31%, country 89%, health condition 45%, antibiotic exposure 14%. The **registry** tables (`registry_*`) list every screened public study in ENA/SRA/DDBJ with a human-associated metagenome signal — 64,369 studies — with archive-only classification (host, UBERON-anchored body sites, life stages, assay, access) and the harvested BioSample tier (637,397 BioSamples with normalised site / age / sex / country).

**Start here.** `catalog_studies.parquet` (one row per project: body sites, DNA / RNA, papers, methods and setting read from the paper, per-field coverage, age-category and health-condition mix, curated depth) → `catalog_sample_metadata_wide.parquet` (one row per sample: every field with `__route` / `__confidence`, `body_site_code`, `age_category` + basis, `has_dna` / `has_rna`) → `catalog_sample_determinations.parquet` (the evidence behind every value) and `catalog_runs.parquet` (every run). `registry_studies.parquet` is the registry; `registry_runs_v1.34.2.parquet` (every screened run) ships as a GitHub Release asset.

**Birth and feeding fields.** The birth-cohort projects carry a deeper curation (delivery mode, feeding mode, preterm status, gestational age, birth weight, maternal antibiotics, probiotics, HMO supplementation, NEC). Those rows are in the `catalog_*` tables (`curated_source = infant_catalog`; `infant_scope = true` selects them: 66,355 samples), and the deeper tables of that curation (`sample_metadata_wide`, `sample_determinations`, `study_metadata_wide`, `cohorts`, `universe_studies_all`, `sandpiper_*`, …) are in the package as well.

## Files
| File | Rows | What it is |
|---|---:|---|
| `catalog_studies.parquet` | 5,389 | Catalog projects: body sites (sample_body_sites, body_site_primary), nucleic acid of the data sets (nucleic_acid, n_runs_dna / n_runs_rna, n_samples_dna / n_samples_rna), methods and setting read from the paper (platform, extraction_kit, library_kit, study_shape, n_participants, recruitment, settlement), registry columns + per-field coverage (cov_<field> for every curated field), age-category / health-condition distributions, sequencing summary (n_runs_total, Gbp per run, instrument models, layouts, Sandpiper share; 1.12.0), curated depth and source |
| `catalog_sample_metadata_wide.parquet` | 873,877 | Catalog samples, one row per sample: body_site_code / body_site_class / body_site_basis, has_dna / has_rna, age category, age, sex, BMI, country, health condition, antibiotic exposure, subject, timepoint, collection date, detailed location (region / locality / site), latitude / longitude, lifestyle (+ route / confidence per field), infant-only fields, body-site class, `infant_scope` (== the infant catalog's catalog_scope filter) |
| `catalog_sample_determinations.parquet` | 5,326,776 | **Catalog determinations**: one row per sample × field for every catalog sample (every body site, all ages) — value, normalised value, route (R1 archive attribute / R2 supplementary table / R3 paper prose / R4 abstract), confidence, verbatim evidence quote and source; infant-catalog rows copied verbatim (src_track infant_catalog) |
| `catalog_runs.parquet` | 997,068 | **Catalog runs**: one row per sequencing run of a catalog sample — nucleic_acid (dna / rna) and rrna_read_share where measured, accessions, library strategy / source / layout, instrument, read and base counts, first_public, sandpiper_profiled and the catalog sample_key (run-unit samples keyed by run accession) |
| `catalog_study_papers.parquet` | 7,826 | project × paper judged by the link audit to have generated (own_data) or reused (reuse) the project's data |
| `catalog_exposure_items.parquet` | 883 | exposure item read from a project's paper |
| `catalog_sample_determinations_conflicts.parquet` | 184,012 | Competing values not committed: one row per sample × field × alternative value where sources disagree, with its evidence (the committed value is in `catalog_sample_determinations`) |
| `catalog_sandpiper_sample_summary.parquet` | 415,757 | **Sandpiper community profiles for the whole catalog**: one row per catalog sample with a SingleM/Sandpiper 2.0.0 profile (GTDB R232) — profiled runs, depth proxy, genus richness, Shannon, top genus, QC flags; the genus- and species-level long tables (catalog_sandpiper_sample_genus / _species) ship as release assets in parts |
| `catalog_sandpiper_pca_scores.parquet` | 404,318 | Genus-level CLR-PCA scores (pc1-pc5) of every profiled sample with root coverage >= 2 — the data behind Insights › PCA of community profiles |
| `catalog_sandpiper_pca_loadings.parquet` | 386 | Genus loadings of the same PCA |
| `catalog_sandpiper_pca_variance.csv` | 5 | Explained variance ratio per component |
| `catalog_sandpiper_study_coverage.csv` | 5,086 | Sandpiper join coverage per catalog study |
| `catalog_relatedness.parquet` | 6,540 | **Relatedness groups**: one row per family or shared setting (household, village / community, tribe, daycare / school, care home, ward, unit) with at least two catalogued people — study, group kind / type / id, people (person keys), samples, role counts and the family motif (e.g. mother + father + child) |
| `catalog_relations.parquet` | 1,122 | **Explicit relations**: pairwise links stated by authors that are not expressible as a family id — FMT donor_of, twin_of / twin_mz_of / twin_dz_of, partner_of, roommate_of, mother_of, sibling_of — with evidence source, quote and confidence |
| `catalog_pass_coverage.csv` | 5,086 | **Annotation pass coverage**: one row per catalog project — whether each applicable pass ran (full text, abstract, interventions, deep per-sample, supplementary tables, subject_id, study design); the release fails when a pass never ran without a recorded exception |
| `catalog_metadata_contradictions.parquet` | 8,840 | **Self-contradictory metadata**: one row per withheld sample × field — two sample-level sources with incompatible values (sex, country, antibiotics yes/no, age > max(30 d, 20 %), BMI > 2, healthy_control vs a disease) or one subject id whose samples disagree on sex; value_a / source_a / quote_a and value_b / source_b / quote_b, handling (the value is not published) and a note. Project pages show a red ! badge |
| `catalog_sandpiper_study_panels.parquet` | 37,267 | Composition panel per project: mean relative abundance of the top genera / phyla over the profiled samples of the project's most-profiled body site (`panel_body_site`) |
| `CATALOG_SANDPIPER_REPORT.md` |  | How the catalog samples were joined to Sandpiper profiles: streaming pass, join coverage, QC flags, tables, PCA |
| `catalog_rejected_values.parquet` | 3,081 | Values taken out by the plausibility gates and checked corrections: the original determination row with `rejection_rule` and `rejection_reason` (ages below 0 or above 122 years, and corrections checked against the source text) |
| `catalog_sandpiper_unprofiled.parquet` | 397,599 | current catalog sample without a Sandpiper community profile, with the reason where it can be known |
| `catalog_sandpiper_gap_by_cause.csv` | 10 | reason a current sample has no Sandpiper profile (summary of catalog_sandpiper_unprofiled) |
| `sample_metadata_wide.parquet` / `.csv.gz` | 153,238 | Birth-cohort projects only — for the whole catalog use `catalog_sample_metadata_wide.parquet`. One row per sample: identifiers, body-site class, every metadata field with `<field>__confidence` and `<field>__route`, subject/timepoint, run accessions, cohort, links |
| `sample_determinations.parquet` | 619,647 | Long form: one row per sample × field with `evidence_source`, `evidence_locator`, `evidence_quote` (≤12 words verbatim), `route`, `scope`, `confidence` |
| `study_metadata_wide.parquet` / `.csv` | 389 | One row per included study (BioProject): title, counts, triage evidence, recoverability tiers, per-field coverage (`cov_*`), cohort, links |
| `runs.parquet` | 174,172 | Run → sample → study with library/instrument fields (join key into ENA/SRA) |
| `sample_subjects.parquet` | 154,356 | Subject and timepoint resolution per sample (`subject_key`, `role` infant/mother/other, `t_index`) |
| `cohorts.csv` | 373 | Cohort clusters (studies + papers sharing a cohort) with unique-infant estimates |
| `study_paper_links.csv` | 973 | Study ↔ paper links with PMID/PMCID/DOI and link method |
| `sandpiper_flag_table.csv` | 20 | Single source for every Sandpiper flag (field, level, site label, definition, used) |
| `sandpiper_study_panel_status.csv` | 389 | Per-study panel scope label and n breakdown (age_scope of profiled rows) |
| `sandpiper_auditor_candidate_runs.csv` | 375 | Runs whose organism label is a non-human host or a named microbe, with the v1.2.1 action taken |
| `review_genomic_only_samples.csv`, `review_named_microbe_metagenomic_samples.csv`, `review_amplicon_runs.csv` | 3158 / 70 / 39 | Owner review lists (library_source GENOMIC on every run; METAGENOMIC runs with a named-microbe organism; AMPLICON-strategy runs) |
| `universe_studies_all.parquet` | 9,581 | Every screened study with verdict, reason code and evidence |
| `parent_biosamples.parquet` | 16 | Parent BioSamples of the run-level rows (provenance only; excluded from every count) |
| `study_verdict_history.parquet` | 32,233 | Every classification a screened study passed through (rule, rubric, automated re-readings, re-judgement of the BioSamples, human review, final) with confidence, evidence and where it came from |
| `value_history.parquet` | 46,587 | Determinations that are not current: superseded, rejected, dropped by the group-statement audit, duplicates, moved parent rows, auditor findings — each with `status`, `reason`, `replaced_by` |
| `sample_determinations_superseded.parquet` | 223 | Subset of the above kept in the v1.1 layout (run-level supersessions + v1.2 age corrections) |
| `confidence_tiers.csv` | 29 | The engine confidence tiers: route × determiner × scope with the fixed confidence values they emit |
| `tier_field_precision.csv` | 49 | Empirical precision of each route × confidence tier per field against the cMD gold join (hi-res and full sets) |
| `sample_unit_classification_by_study.csv` | 67 | Per-study sample-unit class (A run-keyed, C technical multi-run, X cross-study BioSample) |
| `human_review_queue.csv` | 52 | Studies the pipeline could not decide, with the reason |
| `field_coverage_summary.csv`, `study_field_coverage_matrix.csv` | | Coverage per field and per study × field |
| `extraction_gold_eval_hires.csv` | 6 | Precision/recall vs curatedMetagenomicData |
| `sample_determinations_all.parquet` | 620,963 | Current **and retired** determinations: `sample_determinations` columns + `release_added`, `release_retired` (null = current), `package_added`, `retired_reason`, `retired_change_stage` — the per-field value timeline |
| `releases.csv` | 46 | Release registry: one row per release id (package version, date, tags, DOI, headline counts, notes file) |
| `RELEASE_NOTES_R2026.1.md` | | Generated diff report vs the previous package (studies/samples, coverage, verdict flips, findings, Sandpiper, gold, schema) |
| `contribute_worklist.csv` | 439 | Community-contribution worklist: one row per OPEN included/uncertain study (≥ 1 of the six fields age/delivery/feeding/preterm/antibiotics/probiotic below 0.5 coverage on catalog_scope), ranked by priority_score, with blocker_code, unlock_text and a prefilled contribution Issue URL |
| `contribute_worklist_fields.csv` | 2,634 | Study × field detail of the worklist (coverage, best recoverability tier, field-level blocker, evidence) |
| `registry_studies.parquet` | 64,369 | **Registry tier**: one row per ENA study of the human shotgun-metagenome universe (all body sites, all ages) — host_human, assay, body_sites/life_stages (config/vocab codes with UBERON anchors), population flags, access, evidence rows, classification_stage, infant-catalog verdict, scope memberships |
| `registry_universe_audit.csv` | 35 | Registry enumeration audit: per ENA slice the archive count, rows pulled and completeness |
| `REGISTRY_REPORT.md` | | Registry build report (universe counts per slice, host / assay / site / stage facets, scope sizes, deviations) |
| `registry_biosamples.parquet` | 637,397 | **Registry sample tier**: one row per harvested BioSample of the human_all registry studies outside the curated infant catalog — body site, life stage / age_days, sex, country, collection year normalised to the vocabularies (normalised by an automated check of the distinct attribute pairs), raw key/value provenance, disease text unnormalised |
| `registry_study_papers.parquet` | 6,998 | Registry study × paper links from Europe PMC accession mentions and NCBI BioProject declared publications (deterministic relation classes) |
| `registry_bioproject_records.parquet` | 4,217 | NCBI BioProject record per registry study: organisation, submitter, declared publications, dates, data types |
| `registry_authors.parquet` | 52,110 | Authors (with ORCID / affiliation where present) of ≤ 5 linked papers per registry study, Europe PMC core records |
| `DATA_DICTIONARY.md` | | Every column, every vocabulary |
| `getting_started.ipynb` | | Load, filter, join, plot |

## Release model
Releases are numbered `R<YYYY>.<n>` (`VERSION.json.release_id`). Every fact table
(`sample_determinations`, `universe_studies_all`, `study_metadata_wide`, `cohorts`, `study_paper_links`, `sandpiper_*`)
carries three trailing columns `release_added`, `release_retired` (null = current) and `package_added`; `sample_metadata_wide`
is derived and has none. `sample_determinations_all.parquet` adds the retired determinations (1,316 rows,
reconstructed from `value_history`) so a per-field value timeline is one `ORDER BY release_added` query; `releases.csv` is the
registry of every release. **Caveat:** packages before 1.3.0 had no release columns, so `release_added` for pre-existing rows is
reconstructed from `value_history.change_stage` / `src_track` and is exact only for rows those stages touched (adult-age
recommits, run-level rows, retired values); every other pre-1.3.0 row is labelled `1.0.0` by assumption. Full rules:
`DATA_DICTIONARY.md`, 'Release columns'.

## How to read a value
Every value says where it came from and how reliable it is. It comes from one of four sources: the **archive record** (the sample's own attribute or a sample-name convention, per sample), a **supplementary table** of a linked paper (per sample; `subject_level_join` in `parse_note` when the row was per subject), the **paper text** (a statement about a defined group) or the **abstract** (a statement in the abstract or in the archive's project description; confidence ≤ 0.5). The archive record wins over the supplementary table, then the paper text, then the abstract; the `route` column carries these four as the codes `R1`, `R2`, `R3` and `R4`. Confidence is an ordinal reliability tier fixed by the route/engine that produced the value (`confidence_tiers.csv`), **not a calibrated probability**; its empirical precision per tier and field against the gold set is in `tier_field_precision.csv` (see DATA_DICTIONARY). For most analyses: use archive-record and supplementary-table values at any confidence, and paper-text and abstract values only when `confidence ≥ 0.5` — those are group-level statements applied to samples. `parse_note` explains derivations (unit resolution, subject propagation, date arithmetic). Rows that are no longer current (superseded, rejected, dropped by the group-statement audit) are in `value_history.parquet` with the reason and what replaced them; study-level verdict changes are in `study_verdict_history.parquet`.

## Provenance and licence
Built with an LLM-assisted pipeline and deterministic parsers; every value links to its source, its verbatim evidence quote and a confidence, and the undecidable residue went through two rounds of automated review of the paper text. Full methods: the Methods and 'Where values come from' pages of the site. The working reports of this resource's earlier birth-cohort phase (`CATALOG_REPORT.md`, `EXTRACTION_REPORT.md`, `SANDPIPER_REPORT.md`, `AUTHORS_REPORT.md`, `DATA_MODEL_FIX_REPORT.md`) are files in this package; they are not pages of the site. Licence: CC-BY-4.0 for the curated tables (archive metadata remains under INSDC terms). Cite: Microbiome Repo, release R2026.43 (data package 1.34.2), Olm Lab at the University of Colorado Boulder, 2026-11-24. DOI 10.5281/zenodo.23087712.

## Birth and feeding fields (earlier birth-cohort curation)
Nine further fields — delivery mode, feeding mode, preterm status, gestational age, birth weight, maternal
antibiotics, probiotic exposure, HMO supplementation and NEC status — were curated for the birth-cohort projects
before the catalog covered every age. Their values are in the `catalog_*` tables (`curated_source = infant_catalog`);
`infant_scope = true` selects those samples. The deeper tables of that curation (`sample_metadata_wide`,
`sample_determinations`, `study_metadata_wide`, `cohorts`, `sandpiper_*`, …) are in this package and every column of
them, including `age_scope` (what is known about a sample's age, and the filter their headline coverage is computed
on), is documented in `DATA_DICTIONARY.md`.

Read those tables with these caveats:

* An age is recoverable for a minority of their samples, and per project it is all-or-nothing: some projects'
  supplementary tables are keyed by identifiers that are absent from the archive and can only be joined with the
  submitter's help.
* `antibiotic_exposure` means *any* antibiotics before or at sampling; sources that record only current use disagree
  with that definition.
* `feeding_mode` categories differ between sources.
* Probiotic exposure, HMO supplementation, NEC status, birth weight and maternal antibiotics have no external truth
  set; they were audited by a blind automated re-reading only.
* Projects that sampled several age groups carry many of the exposure values on adults — filter on `age_scope`
  before using those fields.
* Sandpiper relative abundances (`*_ra`) are fractions of prokaryotic coverage, not of all reads, and the per-run QC
  flags are this resource's application of Sandpiper's rules, not Sandpiper's own output.
* Some projects have no author on record in the author index.
