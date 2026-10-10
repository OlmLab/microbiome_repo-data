# Data dictionary

## sample_metadata_wide.parquet
| column | type | meaning |
|---|---|---|
| `sample_key` | string | ENA/BioSample accession (SAMEA/SAMN/SAMD) — primary key |
| `study_accession` | str | BioProject (PRJ…) |
| `secondary_sample` | str | ENA secondary sample accession (ERS/SRS/DRS) |
| `sample_title` | str | submitter sample title |
| `body_site_class` | str | vocabulary (v1.2.1): `primary` — infant gut/stool/meconium/rectal sample; `unknown` — no body-site evidence, kept in body-site scope; `excluded` — non-gut site (skin, oral, milk, vaginal, nasal, blood…) or, since v1.2.1, a non-human host / isolate-genome sample (`exclusion_reason_code`); `linked` — mother/other sample linked to an infant subject, outside body-site scope. Body-site scope = {primary, unknown}; headline scope = `catalog_scope` (age scope ∧ body-site scope) |
| `collection_date` | str | archive collection_date as given |
| `is_gold_heldout` | bool | True if in the curatedMetagenomicData evaluation set |
| `probiotic_exposure` | str | yes | no | unknown |
| `preterm_status` | str | preterm | term | unknown |
| `gestational_age_weeks` | float64 | float weeks at birth |
| `delivery_mode` | str | vaginal | c_section | c_section_elective | c_section_emergency | unknown |
| `feeding_mode` | str | exclusive_breast | mixed | formula | weaned | unknown |
| `antibiotic_exposure` | str | yes | no | unknown (any antibiotics to the infant before/at sampling) |
| `age_at_collection_days` | float64 | integer days postnatal |
| `birth_weight_grams` | float64 | integer grams |
| `country` | str | ISO-3166 alpha-2 |
| `maternal_antibiotics` | str | yes | no | unknown (pregnancy, labour or lactation) |
| `hmo_supplementation` | str | yes | no | unknown |
| `nec_status` | str | yes | no | unknown (necrotising enterocolitis diagnosis) |
| `health_condition` | str | healthy_control | preterm_nicu | nec | sepsis_or_infection | ibd_or_gi_disease | allergy_or_atopy | malnutrition | antibiotic_or_probiotic_trial | other_disease | unknown |
| `multiple_birth` | str | singleton | twin | triplet_or_more | unknown |
| `sibling_in_study` | str | yes | no | unknown |
| `geo_subregion` | str | 'City, Region' free text as given by submitter (never reverse-geocoded) |
| `sex` | str | male | female | unknown |
| `timepoint_label` | str | study's own label (free text) |
| `subject_id` | str | study's own subject identifier (free text) |
| `probiotic_exposure__confidence` | float | confidence (0–1) of `probiotic_exposure` |
| `preterm_status__confidence` | float | confidence (0–1) of `preterm_status` |
| `gestational_age_weeks__confidence` | float | confidence (0–1) of `gestational_age_weeks` |
| `delivery_mode__confidence` | float | confidence (0–1) of `delivery_mode` |
| `feeding_mode__confidence` | float | confidence (0–1) of `feeding_mode` |
| `antibiotic_exposure__confidence` | float | confidence (0–1) of `antibiotic_exposure` |
| `age_at_collection_days__confidence` | float | confidence (0–1) of `age_at_collection_days` |
| `birth_weight_grams__confidence` | float | confidence (0–1) of `birth_weight_grams` |
| `country__confidence` | float | confidence (0–1) of `country` |
| `maternal_antibiotics__confidence` | float | confidence (0–1) of `maternal_antibiotics` |
| `hmo_supplementation__confidence` | float | confidence (0–1) of `hmo_supplementation` |
| `nec_status__confidence` | float | confidence (0–1) of `nec_status` |
| `health_condition__confidence` | float | confidence (0–1) of `health_condition` |
| `multiple_birth__confidence` | float | confidence (0–1) of `multiple_birth` |
| `sibling_in_study__confidence` | float | confidence (0–1) of `sibling_in_study` |
| `geo_subregion__confidence` | float | confidence (0–1) of `geo_subregion` |
| `sex__confidence` | float | confidence (0–1) of `sex` |
| `timepoint_label__confidence` | float | confidence (0–1) of `timepoint_label` |
| `subject_id__confidence` | float | confidence (0–1) of `subject_id` |
| `probiotic_exposure__route` | str | evidence route of `probiotic_exposure` (R1–R4) |
| `preterm_status__route` | str | evidence route of `preterm_status` (R1–R4) |
| `gestational_age_weeks__route` | str | evidence route of `gestational_age_weeks` (R1–R4) |
| `delivery_mode__route` | str | evidence route of `delivery_mode` (R1–R4) |
| `feeding_mode__route` | str | evidence route of `feeding_mode` (R1–R4) |
| `antibiotic_exposure__route` | str | evidence route of `antibiotic_exposure` (R1–R4) |
| `age_at_collection_days__route` | str | evidence route of `age_at_collection_days` (R1–R4) |
| `birth_weight_grams__route` | str | evidence route of `birth_weight_grams` (R1–R4) |
| `country__route` | str | evidence route of `country` (R1–R4) |
| `maternal_antibiotics__route` | str | evidence route of `maternal_antibiotics` (R1–R4) |
| `hmo_supplementation__route` | str | evidence route of `hmo_supplementation` (R1–R4) |
| `nec_status__route` | str | evidence route of `nec_status` (R1–R4) |
| `health_condition__route` | str | evidence route of `health_condition` (R1–R4) |
| `multiple_birth__route` | str | evidence route of `multiple_birth` (R1–R4) |
| `sibling_in_study__route` | str | evidence route of `sibling_in_study` (R1–R4) |
| `geo_subregion__route` | str | evidence route of `geo_subregion` (R1–R4) |
| `sex__route` | str | evidence route of `sex` (R1–R4) |
| `timepoint_label__route` | str | evidence route of `timepoint_label` (R1–R4) |
| `subject_id__route` | str | evidence route of `subject_id` (R1–R4) |
| `subject_key` | str | resolved subject id (study-scoped) |
| `role` | str | infant · mother · other · unknown · adult (age ≥ 18 y) · child (1,101 d – 18 y). `infant` only when evidenced: an attribute/sample-name role, or `age_scope ∈ {infant_evidenced, study_all_infant}`; unevidenced body-site defaults are `unknown` (v1.2) |
| `role_source` | str | what evidences `role`: `attr_role`, `sample_name_token`, `body_site_raw`, `body_site_class=linked`, `age_scope:<value>`, `none` |
| `t_index` | float64 | ordinal timepoint within subject |
| `n_timepoints_subject` | float64 | timepoints for this subject |
| `linked_infant_subject_key` | str | for mother samples: the infant's subject_key |
| `run_accessions` | str | ';'-joined run accessions |
| `n_runs` | int64 | runs for this sample |
| `instrument_model` | str | instrument model(s) |
| `library_layout` | str | PAIRED/SINGLE |
| `read_count_total` | int64 | sum of read_count over runs |
| `study_title` | str | BioProject title |
| `cohort_id` | str | cohort cluster id (COH…) |
| `cohort_name` | str | cohort name |
| `first_public_min` | str | earliest run release date in study |
| `adult_age_flag` | bool | **Rule (v1.2):** True iff `age_at_collection_days` > 1,100 — i.e. the sample carries a committed `age_at_collection_days` determination whose `parse_note` starts with `out_of_scope_adult` (an archive age attribute, R1, that the validator's in-scope check `validate_age` rejected as `age N days outside 0-1100`). The flag is therefore always evidence-linked; the 12 v1.1 samples that carried both an in-scope and an out-of-range age were resolved in favour of the archive attribute (reasons in `value_history.parquet`, `change_stage = auditor_review:B9`) |
| `n_fields_with_value` | int64 | count of populated metadata fields |
| `ena_sample_url` | str | ENA browser link |
| `ena_study_url` | str | ENA browser link |

## sample_determinations.parquet
| column | meaning |
|---|---|
| `sample_key`, `study_accession`, `field_name` | key |
| `field_value` | raw value as found in the source |
| `value_normalized` | value in the controlled vocabulary/unit |
| `confidence` | 0–1 |
| `evidence_source` | label: `sample.attr.<key>`, `run.library_name`, `sample_id_pattern`, `paper.supp.table`, `paper.fulltext.methods`, `paper.abstract`, `study.description` … |
| `evidence_locator` | attribute key / pmcid:file:sheet:column / section |
| `evidence_quote` | ≤12-word verbatim quote |
| `evidence_limited_to_abstract` | 1 when the paper was paywalled |
| `determined_by` | parser or model id |
| `route` | R1–R4 |
| `scope` | sample | subject | group |
| `parse_note` | derivation notes (unit resolution, propagation, supersession, review flags) |
| `group_audit` | keep/downgrade for group statements (Opus audit) |
| `src_track` | pipeline pass that produced the row |
| `release_added` | release in which this row first became visible (`R<YYYY>.<n>` or a pre-numbered package semver `1.0.0`–`1.2.2`); pre-1.3.0 values are reconstructed — see DATA_DICTIONARY 'Release columns' |
| `release_retired` | release that replaced/removed the row; null (empty in CSV) = current row |
| `package_added` | semver of the data package in which the row first appeared |

## Routes
R1 = archive sample attribute or sample-name convention (per sample); R2 = supplementary table row (per sample; 'subject_level_join' in parse_note when copied from a per-subject row); R3 = statement in the paper text applied to a defined group; R4 = abstract/ENA description statement (group; confidence ≤0.5).

## Field vocabularies
| field | values |
|---|---|
| `delivery_mode` | vaginal | c_section | c_section_elective | c_section_emergency | unknown |
| `feeding_mode` | exclusive_breast | mixed | formula | weaned | unknown |
| `preterm_status` | preterm | term | unknown |
| `antibiotic_exposure` | yes | no | unknown (any antibiotics to the infant before/at sampling) |
| `maternal_antibiotics` | yes | no | unknown (pregnancy, labour or lactation) |
| `probiotic_exposure` | yes | no | unknown |
| `hmo_supplementation` | yes | no | unknown |
| `nec_status` | yes | no | unknown (necrotising enterocolitis diagnosis) |
| `age_at_collection_days` | integer days postnatal |
| `gestational_age_weeks` | float weeks at birth |
| `birth_weight_grams` | integer grams |
| `country` | ISO-3166 alpha-2 |
| `sex` | male | female | unknown |
| `timepoint_label` | study's own label (free text) |
| `subject_id` | study's own subject identifier (free text) |
| `health_condition` | healthy_control | preterm_nicu | nec | sepsis_or_infection | ibd_or_gi_disease | allergy_or_atopy | malnutrition | antibiotic_or_probiotic_trial | other_disease | unknown |
| `multiple_birth` | singleton | twin | triplet_or_more | unknown |
| `sibling_in_study` | yes | no | unknown |
| `geo_subregion` | 'City, Region' free text as given by submitter (never reverse-geocoded) |

## study_metadata_wide.parquet
| column | meaning |
|---|---|
| `study_accession`, `study_title`, `n_samples`, `n_runs`, `first_public_min` | archive identity and size |
| `catalog_status`, `triage_verdict`, `decision_stage`, `confidence`, `evidence` | INFANT-catalog inclusion verdict and its evidence (historical; not all-age catalog membership — use `gut_studies.parquet`) |
| `universe_slice` | enumeration channel that found the study |
| `recov_age`, `recov_delivery`, … | recoverability tier predicted before extraction (R1 archive / R2 table / R3 paper / R4 abstract / R0 none) |
| `cov_<field>` | fraction of the study's infant-scope samples with a value |
| `cohort_id`, `cohort_name`, `n_linked_papers`, `linked_pmids` | cohort and literature |
| `n_unique_infants_est` | distinct resolved `subject_key` among `role = infant` samples — populated only when `n_unique_infants_source ∈ {subject_ids, subject_ids_partial}`; NaN otherwise (v1.2; v1.1 wrote 0 or n = n_samples sentinels) |
| `n_unique_infants_source` | `subject_ids` (every infant sample has a resolved subject and subjects repeat), `subject_ids_partial` (subjects resolved on part of the infant samples), `subject_ids_all_distinct` (one subject id per sample — cannot distinguish cross-sectional from unresolved; render "≤ n"), `no_subject_evidence` (render "unknown (≤ n)"), `no_infant_role_samples` |
| `n_unique_infants_upper`, `n_unique_infants_display` | upper bound (number of infant-role samples) and the string the site should render |
| `n_infant_role_samples`, `n_infant_samples_with_subject` | inputs to the above |
| `n_infant_evidenced`, `n_study_all_infant`, `n_age_unknown_mixed_study`, `n_age_unknown_no_study_estimate`, `n_adult_flagged`, `n_non_infant_role`, `n_age_scope_infant` | per-study `age_scope` counts; `n_age_scope_infant` = infant_evidenced + study_all_infant |
| `mixed_age_deposit` | True when more than half of the study's sample rows are `age_unknown_mixed_study` or `adult_flagged` — render a banner |
| `n_samples`, `n_runs`, `n_biosamples` | from `runs.parquet`: runs of the study; distinct BioSamples; `n_samples` = runs for run-unit studies, else BioSamples. `n_sample_rows` = rows of the sample table keyed to this study (differs when a BioSample carries runs from two BioProjects; `n_biosamples_shared_with_other_study`) |
| `longitudinal`, `max_timepoints`, `n_mothers` | subject resolution (infant-role samples) |
| `worklist_rank`, `effort_class` | curation priority |
| `ena_url`, `ncbi_url` | links |

## Sample unit (added after the auditor's run-vs-BioSample finding)
| column | meaning |
|---|---|
| `biosample_accession` | str | always populated: the BioSample (SAMN/SAMEA/SAMD) — equals `sample_key` for `biosample` rows, `parent_biosample` for `run` rows |
| `run_accession` | str | populated only for `run` rows (equals `sample_key`); null otherwise. Sandpiper/run-level joins: `COALESCE(run_accession, runs.run_accession)` |
| `age_scope` | str | `infant_evidenced` · `study_all_infant` · `age_unknown_mixed_study` · `age_unknown_no_study_estimate` · `adult_flagged` · `non_infant_role` — see README "Age scope and roles". Precedence: adult_flagged > non_infant_role > infant_evidenced > study_all_infant (n_infant_samples_est ≥ 0.9 × n_infant_scope_samples) > age_unknown_mixed_study (estimate below 0.9) > age_unknown_no_study_estimate (triage gave no estimate) |
| `age_scope_basis` | str | the concrete evidence behind `age_scope`: `age_at_collection_days<=1100`, `preterm_status`, `gestational_age_weeks`, `subject_has_infant_evidence`, `committed age >1100 d`, `role … from …`, or the study ratio used |
| `sample_unit` | `biosample` (default: one BioSample = one stool), `run` (child row: the submitter registered one BioSample per infant and one run per stool — 6 studies, 521 rows), `biosample_pooled` rows were moved to `parent_biosamples.parquet` in v1.2 — the sample table contains only `biosample` and `run` units |
| `parent_biosample` | for `run` rows, the pooled BioSample they belong to |
| studies.`n_samples` | catalog sample rows (biosample + run units); `n_biosamples` keeps the archive's BioSample count (e.g. PRJNA294605: 158 samples, 11 BioSamples) |

Class-A decision rule (deterministic, `sample_unit_classification.csv`): BioSample text says 'Assembly of N … samples'/'pooled', or a run-keyed supplementary table gives ≥2 distinct ages/timepoints across the BioSample's runs, or NCBI BioSample carries a collection-date range. Technical multi-run BioSamples (lanes, replicates, paired/unpaired, long+short reads; 33 studies) keep the BioSample as unit.


## Confidence (v1.2 clarification)
`confidence` (and every `<field>__confidence`) is an **ordinal reliability tier assigned by the route and engine that produced the value, not a calibrated probability**. Deterministic R1 parsers emit 0.90 (title parser 0.70, unit resolution 0.75–0.85, date arithmetic 0.85, subject propagation 0.70–0.85); R2 table extraction emits 0.65–0.85 (0.75 typical, 0.85 when a paper's own table maps by exact run/sample accession); R3 paper-text group statements 0.25–0.80 (Opus-downgraded statements are capped at 0.40); R4 abstract statements 0.40–0.50. The full tier list is `confidence_tiers.csv` (route × determiner × scope, with the discrete values emitted). The empirical precision of each tier against the curatedMetagenomicData gold join is in `tier_field_precision.csv` (`gold_set = hires` excludes gold rows whose age is an integer number of years; `all` is the full 3,670-sample join; value mapping: cMD c-section subtypes → `c_section`, `No Breastfeeding`/`Exclusively Formula Feeding` → `formula`; age tolerance max(7 d, 10 %); GA ± 1 wk). On that join the tiers are monotone for age (R1 0.90 → 1.00, R1 0.85 date/propagation → 0.70, R2 0.85 → 1.00, R2 0.75 → 0.98) but not for feeding (R2 0.75 → 0.48, R2 0.85 → 0.94), and no gold exists for R4 or for exposure fields — treat the number as a tier label and read the precision table for the field you use.

## parent_biosamples.parquet
The 16 BioSamples that the submitters of 6 deposits registered one-per-infant with one run per stool. Their runs are the catalog's `run` sample units (`n_child_run_units`, `child_run_units`). Same columns as the sample table plus `n_determinations_moved` (their 97 v1.1 determinations are in `value_history.parquet`, status `moved_to_parent_biosamples`). Excluded from every count.

## study_verdict_history.parquet
One row per (study, stage table, replicate). `stage_order` (v1.2.1) follows the stage ontology `stage_rank`/`stage_level`: screen < tier1_rubric < confirm r1–r3 < adjudication < growth_wave (wave order ff, gap, grow, grow_w2, grow_w3, gsa, loose, nm, r3, v3_delta; r1 < r2 < wave adjudication) < growth_wave_merged < biosample_rejudge < human_review (round 1, round 2) < consolidated_triage_v2 < consolidated_final; ties broken by date, replicate, stage. `is_consolidated` marks rows copied from a consolidated table (`consolidated_kind` = final / triage_v2 / growth_wave) — render only the `final` row as the single 'final' line. `verdict_norm` (v1.2.1): Haiku screen `yes` → `uncertain` (screen-pass, not a verdict), `no` → `exclude`, `ERROR` and empty verdicts → null; `verdict_norm_note` explains each mapping. `stage_family` groups tables (haiku_screen, sonnet_rubric_tier1, sonnet_confirm:*, opus_adjudication:*, growth_triage_merged:*, biosample_rejudge, human_review_round1/2, triage_v2_consolidated, catalog_final). `verdict` is the raw label of that stage (include/exclude/unsure/uncertain; the Haiku screen's yes/no; `ERROR` for failed requests); `verdict_norm` maps it to include/exclude/uncertain/None. `is_final` marks the row that equals `universe_studies_all`. `evidence` is the stage's JSON evidence list; `src_artifact`/`src_version_id` name the release-bundle table the row was copied from.

## value_history.parquet
Determinations that are not in `sample_determinations.parquet`. `status`: `superseded` (a higher-precedence value replaced it; `replaced_by` names it), `rejected` (validator or rule rejection; `reason`), `dropped` (group statement failed the Opus checklist audit; `reason` = drop_reason), `recommitted_out_of_scope_adult` (an R1 age the v1.1 validator rejected as out of range that v1.2 commits with `parse_note out_of_scope_adult` to evidence `adult_age_flag`), `not_committed_duplicate` (second out-of-range age attribute on the same sample), `moved_to_parent_biosamples`, `auditor_finding_applied` (study-level auditor findings and what was done). `change_stage` names the pipeline stage that made the change (`auditor_review:B2`/`B9` for v1.2.0; `auditor_review:R3-3` for the v1.2.1 host/isolate rule, statuses `recommitted_out_of_scope_host` and `recommitted_out_of_scope_isolate`, field_name `body_site_class`, evidence source `run.scientific_name` / `run.library_source`).


## Release columns (R2026.1, package 1.3.0)
Fact tables — `sample_determinations.parquet`, `universe_studies_all.parquet`, `study_metadata_wide.parquet`, `cohorts.csv`, `study_paper_links.csv`, `sandpiper_sample_summary.parquet`, `sandpiper_run_qc.parquet`, `sandpiper_top_genera.parquet`, `sandpiper_study_panels.parquet`, `study_metadata_wide.csv` — end with three columns:

| column | dtype | meaning |
|---|---|---|
| `release_added` | string | release in which this row first became visible (`R<YYYY>.<n>` or a pre-numbered package semver `1.0.0`–`1.2.2`); pre-1.3.0 values are reconstructed — see DATA_DICTIONARY 'Release columns' |
| `release_retired` | string \| null | release that replaced/removed the row; null (empty in CSV) = current row |
| `package_added` | string | semver of the data package in which the row first appeared |

Release ids are `R<YYYY>.<n>` (first: `R2026.1` = package 1.3.0) or, for states published before numbering, the package
semver `1.0.0` (first public package, 2026-09-25), `1.1.0` (release v11), `1.2.0`, `1.2.1`, `1.2.2`. "Current" = `release_retired IS NULL`;
in the shipped fact tables every row is current. `sample_metadata_wide` is derived from `sample_determinations` and carries no release columns.

**Reconstruction caveat.** Packages before 1.3.0 carried no release columns, so `release_added` of pre-existing rows was reconstructed:
newest `value_history.change_stage` touching the (sample_key, field_name) or `sample_determinations.src_track`, mapped to the package the
CHANGELOG records for that stage (`sample_unit_fix` → 1.1.0, `auditor_review:B2`/`B9` → 1.2.0, `auditor_review:R3-3` → 1.2.1,
`owner_decision` → 1.2.2). Exact for rows those stages touched (13191 current determinations:
`src_track = sample_unit_fix` → 1.1.0, `src_track = adult_scope_fix` → 1.2.0); every other pre-1.3.0 row is labelled `1.0.0` by assumption
(the first package is not archived as a table, so this cannot be verified row by row). `package_added` equals `release_added` for all
pre-1.3.0 rows. Study verdict rows are all `1.0.0` (no verdict change is dated after the first package); Sandpiper rows are `1.2.0`
(when Sandpiper landed; the v1.2.1 recomputation of existing rows is recorded in the CHANGELOG, not as retire+add).

## sample_determinations_all.parquet (R2026.1)
`sample_determinations` columns + the three release columns + `retired_reason` (string|null; `value_history.reason`) +
`retired_change_stage` (string|null; `value_history.change_stage`). Rows: every current determination (618,898,
`release_retired` null) ∪ every **retired published value** (1,316) reconstructed from `value_history`
rows whose `status` ∈ {`superseded`, `moved_to_parent_biosamples`, `recommitted_out_of_scope_host`, `recommitted_out_of_scope_isolate`}
(`release_retired` = the package of their `change_stage`). value_history rows with status `rejected`, `dropped`, `not_committed_duplicate`,
`recommitted_out_of_scope_adult` (the pre-history of a row that IS current) or `auditor_finding_applied` were never published and are not
rows here. Exactly one current row per (`sample_key`, `field_name`); retired rows may repeat a key. `sample_determinations_superseded.parquet`
(223 rows) is the same set as the `superseded` rows of stages `sample_unit_fix` (75) and `auditor_review:B9` (148) and is kept for the v1.1 layout.

## releases.csv (R2026.1)
One row per release id, oldest first: `release_id`, `package_version`, `release_date`, `data_tag`, `site_tag`, `doi` (empty until Zenodo
mints one), `n_studies_included`, `n_samples`, `n_catalog_scope`, `n_determinations_current`, `sandpiper_version`, `notes_file`. Historical
rows carry only the counts the CHANGELOGs state (empty otherwise — never invented).

## Contribute worklist (R2026.2, package 1.4.0)
`contribute_worklist.csv` — one row per **open** study (`universe_studies_all.triage_verdict ∈ {include, uncertain}` and at least one of the
six worklist fields below 0.5 coverage on its catalog_scope samples; studies with 0 catalog_scope samples are judged on body-site scope
`body_site_class ∈ {primary, unknown}`). Complete studies are not listed. Rules, templates and vocabularies: the pipeline's
`config/contribute.yaml` and `docs/CONTRIBUTE.md`. Read-only: nothing here is a curated value — every column is derived from the tables
of this package and from the recoverability / R2-rescue / supplement-inventory artifacts registered in `config/inputs.json` (group `contribute`).

### contribute_worklist.csv (439 rows)

| column | dtype | meaning |
|---|---|---|
| `rank` | int64 | 1 = highest priority_score (ties: n_samples desc, accession) |
| `study_accession` | string | BioProject accession (universe_studies_all key) |
| `study_title` | string | universe_studies_all.study_title |
| `cohort_id` | string | cohorts.csv id |
| `cohort_name` | string | cohorts.csv name |
| `triage_verdict` | string | `include` | `uncertain` (open studies only) |
| `n_samples` | int64 | sample units of the study (universe_studies_all.n_samples) |
| `n_catalog_scope` | int64 | sample_metadata_wide rows with catalog_scope = True |
| `n_infant_samples_est` | float64 | null | triage estimate of infant samples (universe_studies_all) |
| `missing_fields` | string | ';'-joined short field names (age, delivery, feeding, preterm, antibiotics, probiotic) whose coverage < 0.5 |
| `n_missing_fields` | int64 | count of missing_fields (≥ 1 for every listed study) |
| `coverage_<field>` | float64 | fraction of catalog_scope samples with a value for the field (body-site scope {primary, unknown} when n_catalog_scope = 0); six columns: age, delivery, feeding, preterm, antibiotics, probiotic |
| `best_tier_<field>` | string | best recoverability tier for study × field (`R1` archive attribute · `R2` supplementary table · `R3` paper text · `R4` abstract · `R0` none); six columns |
| `blocker_code` | string | dominant reason the study is open — vocabulary in config/contribute.yaml `blocker_codes` (controlled_access, no_linked_paper, paywalled_abstract_only, tables_unjoinable_need_key, pdf_only_supplement, no_supplement_found, archive_only_uncertain, unitless_age_needs_curator, partial_coverage); decision order `blocker_order` |
| `blocker_detail` | string ≤ 200 | the deciding evidence (counts, access tier, sample-ID forms from RESCUE_REPORT_v2, human-review note) |
| `unlock_text` | string ≤ 200 | imperative 'what would unlock this' sentence from the `unlock_templates` of the blocker (id form / missing fields substituted) |
| `contribution_type` | string | primary ask: per_sample_table | id_key | paper_pointer | age_schedule | verdict_evidence |
| `n_linked_papers` | int64 | rows in study_paper_links for the study |
| `own_data_pmids` | string | ';'-joined PMIDs of the linked papers (empty when none) |
| `n_supp_tables_inventoried` | int64 | supp_inventory members with member_type = table across the linked papers |
| `controlled_access` | bool | study or its cohort is in controlled_access_registry.csv (non-open tier) or flagged controlled in extraction_worklist / universe_studies_all |
| `priority_score` | float64 | Σ over missing fields of weight × (1 − coverage) × log10(n_catalog_scope + 1); weights age 3, delivery 2, feeding 2, preterm 1.5, antibiotics 1, probiotic 0.5 |
| `ena_url` | string | ENA browser URL of the study |
| `ncbi_url` | string | NCBI BioProject URL |
| `issue_url` | string | prefilled GitHub Issue (form catalog-contribution.yml, label contribution; query keys study_accession, contribution_type, release_tag, title) |
| `release_added` | string | release in which this row first became visible (`R<YYYY>.<n>` or a pre-numbered package semver `1.0.0`–`1.2.2`); pre-1.3.0 values are reconstructed — see DATA_DICTIONARY 'Release columns' |
| `release_retired` | string | release that replaced/removed the row; null (empty in CSV) = current row |
| `package_added` | string | semver of the data package in which the row first appeared |

### contribute_worklist_fields.csv (2,634 rows = 6 per worklist study)

| column | dtype | meaning |
|---|---|---|
| `study_accession` | string | BioProject accession |
| `field` | string | age | delivery | feeding | preterm | antibiotics | probiotic (short names of age_at_collection_days, delivery_mode, feeding_mode, preterm_status, antibiotic_exposure, probiotic_exposure) |
| `coverage` | float64 | fraction of in-scope samples with a value (same scope rule as the worklist) |
| `n_with_value` | int64 | in-scope samples with a value |
| `n_catalog_scope` | int64 | catalog_scope samples of the study |
| `best_tier` | string | best recoverability tier R0–R4 for the study × field |
| `blocker_code` | string | `complete` when coverage ≥ 0.5, else the study blocker (only the age field carries unitless_age_needs_curator; other fields then partial_coverage) |
| `evidence` | string ≤ 120 | tier + first recoverability evidence quote/source + note |
| `release_added` | string | release in which this row first became visible (`R<YYYY>.<n>` or a pre-numbered package semver `1.0.0`–`1.2.2`); pre-1.3.0 values are reconstructed — see DATA_DICTIONARY 'Release columns' |
| `release_retired` | string | release that replaced/removed the row; null (empty in CSV) = current row |
| `package_added` | string | semver of the data package in which the row first appeared |

**Blocker codes** (one per study, decision order = listing order for included studies; uncertain studies always `archive_only_uncertain`):
`controlled_access` (study/cohort in the controlled-access registry) · `no_linked_paper` (0 rows in study_paper_links) · `paywalled_abstract_only`
(extraction_worklist access_tier C/D/E) · `tables_unjoinable_need_key` (R2 rescue: supplementary tables keyed by paper-internal names) ·
`pdf_only_supplement` (only PDF/DOCX supplements) · `no_supplement_found` (paper but no inventoried supplement) · `unitless_age_needs_curator`
(age column without unit) · `partial_coverage` (paper + tables processed, fields still missing). Field-level code `complete` = field at/above threshold.
**Contribution types**: `per_sample_table`, `id_key`, `paper_pointer`, `age_schedule`, `verdict_evidence` (dropdown of the GitHub Issue form
`catalog-contribution.yml` that `issue_url` opens prefilled). Rows get `release_retired` when the study becomes complete in a later release.

## Registry tier (R2026.4, package 1.6.0)
The registry is the **outer tier** of the catalog (docs/EXPANSION.md): every ENA study with a human shotgun-metagenome signal, any body site,
any age, classified at STUDY level from ENA study/sample/run metadata (no paper reading). Enumeration slices: S1 `library_source=METAGENOMIC` ×
`WGS|WXS`; S2 misfiled `GENOMIC` on verified human-metagenome taxa; S3 `OTHER|Targeted-Capture|WGA` adjudication (per-slice completeness in
`registry_universe_audit.csv`). Classification stages: `deterministic_prior` (infant-universe verdict carried over), `deterministic_rule`,
`sonnet_x2` (two rubric replicates agree), `opus_adjudicated` (replicates disagreed → adjudication), `pending`. Vocabularies: the pipeline's
`config/vocab/{body_sites,life_stages,assay,population_flags}.yaml`; scope rules: `config/scope.yaml`. The curated infant catalog is the scope
`infant_gut` inside this registry (`in_infant_catalog` mirrors `universe_studies_all.triage_verdict`). The run-level table `registry_runs.parquet`
(all runs of the universe, 46 ENA fields + `found_by`) is attached to the GitHub Release of the data repository as `registry_runs_v<version>.parquet`
(too large for this package). Bitemporal columns follow the package convention (`release_added`, `release_retired`, `package_added`).

### registry_studies.parquet (54,410 rows)

| column | dtype | meaning |
|---|---|---|
| `study_accession` | string | ENA/INSDC BioProject accession (PRJ…); primary key |
| `secondary_study_accession` | string | ENA secondary study accession (ERP/SRP/DRP) |
| `study_title` | string | ENA study title |
| `description_short` | string | ENA study description truncated to 300 characters |
| `center_name` | string | Submitting centre |
| `first_public_min` | string | Earliest run first_public date (ISO) |
| `first_public_max` | string | Latest run first_public date (ISO) |
| `n_runs` | int64 | Runs in the registry universe for this study |
| `n_samples` | int64 | Distinct sample_accession values |
| `n_biosamples` | int64 | Distinct BioSample accessions |
| `library_strategies` | string | JSON dict strategy → run count |
| `library_sources` | string | ';'-joined distinct library_source values |
| `instrument_platforms` | string | ';'-joined distinct instrument_platform values |
| `scientific_names_top` | string | ';'-joined top-5 scientific_name values with run counts, e.g. 'human gut metagenome (120)' |
| `host_tax_ids` | string | ';'-joined distinct host_tax_id values |
| `n_runs_host_9606` | int64 | Runs with host_tax_id 9606 |
| `n_runs_nonhuman_host` | int64 | Runs with a non-9606 host_tax_id |
| `human_signal` | bool | Frame-free sweep human-signal rule outcome (pre-filter) |
| `human_signal_rule` | string | A|B|C|none — which human-signal rule fired |
| `ambiguous` | bool | Human-signal rule tie flag |
| `host_human` | string | Classified host |
| `host_evidence` | string | JSON list of {source, quote} (quote ≤ 12 words; source from the curation-skill label list) |
| `assay` | string | Assay class (config/vocab/assay.yaml) |
| `access` | string | Access tier |
| `body_sites` | string | ';'-joined body-site codes (config/vocab/body_sites.yaml) |
| `body_site_primary` | string | Primary body-site code (multi_site when ≥ 3 strong sites) |
| `body_site_evidence` | string | JSON evidence list |
| `life_stages` | string | ';'-joined life-stage codes (config/vocab/life_stages.yaml) |
| `life_stage_primary` | string | Primary life-stage code (mixed_ages when ≥ 3 strong stages) |
| `life_stage_evidence` | string | JSON evidence list |
| `population_flags` | string | ';'-joined flags (config/vocab/population_flags.yaml) |
| `health_context` | string | ≤ 120 chars free text or null |
| `classification_stage` | string | Cascade stage that produced the classification |
| `classification_confidence` | float64 | 0–1; minimum over components |
| `classification_model` | string | 'deterministic' or the resolved model id of the deciding stage |
| `in_infant_catalog` | string | Infant-catalog verdict |
| `infant_reason_code` | string | Infant triage reason_code (curation-skill vocabulary) or null |
| `scope_memberships` | string | ';'-joined scope ids from config/scope.yaml |
| `universe_slice` | string | Enumeration slice that found the study |
| `release_added` | string | Bitemporal: release id in which the row first appeared |
| `release_retired` | string | Bitemporal: release id in which the row was retired; null = current |
| `package_added` | string | Bitemporal: package semver of first appearance |

### registry_universe_audit.csv

| column | dtype | meaning |
|---|---|---|
| `slice` | string | Slice tag / ENA query label |
| `ena_query` | string | ENA portal query string |
| `ena_count` | int64 | Count returned by the ENA count endpoint |
| `rows_pulled` | int64 | read_run rows actually streamed |
| `n_studies` | int64 | Distinct studies in the slice |
| `completeness` | float64 | rows_pulled / ena_count |
| `pulled_at` | string | ISO timestamp of the pull |

### registry_biosamples.parquet (611,601 rows) — one row per harvested BioSample of a registry study (S2: human_all studies outside the curated infant catalog) — added R2026.5

| column | dtype | meaning |
|---|---|---|
| `sample_accession` | string | BioSample accession (SAMN/SAME/SAMD) as listed in registry_biosample_index; primary key |
| `study_accession` | string | ENA study (BioProject) the sample belongs to |
| `n_attributes` | int64 | Non-placeholder attribute rows harvested for the sample |
| `source` | string | ena_xml | ncbi_biosample — where the attribute record came from |
| `body_site_code` | string | config/vocab/body_sites.yaml code from the normalised body-site attribute; unknown_site when an attribute existed but mapped to no site; null when no body-site attribute |
| `body_site_raw_key` | string | Normalised attribute key the code came from (e.g. host_body_site, isolation_source) |
| `body_site_raw_value` | string | Raw attribute value the code came from |
| `life_stage` | string | config/vocab/life_stages.yaml code from the age attribute (or from age_days); unknown_age when an age attribute existed but was not interpretable; null when none |
| `age_days` | float64 | Age at collection in days when a numeric age with unit was given |
| `age_raw_key` | string | Attribute key the age came from |
| `age_raw_value` | string | Raw age value |
| `sex` | string | female | male | unknown; null when no sex attribute |
| `sex_raw_key` | string | Attribute key the sex came from |
| `sex_raw_value` | string | Raw sex/gender value |
| `country_iso2` | string | ISO-3166-1 alpha-2 from geo_loc_name / country attributes |
| `country_raw_key` | string | Attribute key the country came from |
| `country_raw_value` | string | Raw geographic value |
| `collection_date_raw` | string | Raw collection date attribute |
| `collection_year` | Int64 | Year extracted from collection_date_raw |
| `disease_raw` | string | First non-placeholder disease/health/phenotype attribute value (≤200 chars; NOT normalised — no controlled vocabulary yet) |
| `release_added` | string | Release id in which the row first appeared |
| `release_retired` | string | Release id in which the row was retired (null = current) |
| `package_added` | string | Package version that first carried the row |

### registry_study_papers.parquet (6,398 rows) — one row per study × linked paper (accession mention in Europe PMC and/or publication declared in the NCBI BioProject record); 4,217 human_all studies outside the curated infant catalog — added R2026.5

| column | dtype | meaning |
|---|---|---|
| `study_accession` | str | ENA study (BioProject) accession |
| `paper_id` | str | Europe PMC id (MED:<pmid> | PMC… | PPR… | DOI:…); part of the key |
| `pmid` | str | PubMed id of the linked paper |
| `pmcid` | str | PubMed Central id |
| `doi` | str | DOI |
| `title` | str | Paper title |
| `journal` | str | Journal |
| `year` | Int64 | Publication year |
| `pub_type` | str | Europe PMC publication types |
| `is_open_access` | boolean | Europe PMC open-access flag |
| `in_epmc_fulltext` | object | Full text available in Europe PMC |
| `source` | str | europepmc_mention | bioproject_xml | both — how the paper was linked |
| `match_field` | str | abstract_or_title | fulltext_only — where the accession was mentioned |
| `match_accession` | str | Accession matched (primary PRJ… or secondary ERP/SRP/DRP) |
| `bioproject_declared` | bool | Paper is listed as a publication in the NCBI BioProject record |
| `n_registry_studies` | int64 | How many processed registry studies this paper mentions |
| `relation` | str | data_paper | related | unsure — deterministic link class (see REGISTRY_S2_PAPERS.md §2.3) |
| `method` | str | Rule that produced the relation (det_bioproject_declared 0.9, det_single_mention_abstract 0.75, det_single_mention_fulltext 0.6) |
| `confidence` | float64 | Rule confidence (null for unsure) |
| `contested` | bool | True when the paper mentions ≥ 2 processed studies |
| `contest_reason` | str | multi_study | … |
| `epmc_rank` | float64 | Rank of the paper in the Europe PMC hit list for the study |
| `epmc_hit_count` | float64 | Europe PMC hitCount for the study query |
| `epmc_hits_capped` | object | Hit list truncated at 300 (never true in R2026.5) |
| `body_site_primary` | str | registry_studies.body_site_primary at harvest time (convenience) |
| `release_added` | string | Release id in which the row first appeared |
| `release_retired` | string | Release id in which the row was retired (null = current) |
| `package_added` | string | Package version that first carried the row |

### registry_bioproject_records.parquet (4,217 rows) — one row per processed registry study (NCBI BioProject XML) — added R2026.5

| column | dtype | meaning |
|---|---|---|
| `study_accession` | str | ENA study (BioProject) accession |
| `secondary_study_accession` | str | ENA secondary study accession |
| `found_in_ncbi` | bool | BioProject record found via esearch |
| `bioproject_uid` | str | NCBI BioProject UID |
| `archive` | str | NCBI | EBI | DDBJ |
| `center_id` | str | Submitting center id |
| `center_id_center` | str | Center attribute of center_id |
| `title` | str | Paper title |
| `name` | str | BioProject name |
| `organisation` | str | Submitting organisation (first) |
| `submitter_owner` | str | Submitter/owner organisation |
| `organizations_json` | str | JSON list of organisations (name, abbr, role) |
| `n_publications` | float64 | Publications declared in the record |
| `publication_pmids` | str | ; -joined PMIDs declared |
| `publication_dois` | str | ; -joined DOIs declared |
| `publications_json` | str | JSON list of declared publications |
| `grants_json` | str | JSON list of grants |
| `external_links_json` | str | JSON list of external links |
| `registration_date` | str | ProjectReleaseDate when present (239 studies) |
| `submitted` | str | Submission date |
| `last_update` | str | Last update date |
| `submission_access` | str | public | controlled |
| `data_types` | str | Declared data types |
| `target_material` | str | BioProject target material (eGenome, …) |
| `target_capture` | str | Target capture (eWhole, …) |
| `target_sample_scope` | str | Sample scope (eMultiisolate, eEnvironment, …) |
| `method_type` | str | Method type (eSequencing, …) |
| `relevance_medical` | str | Medical relevance flag |
| `body_site_primary` | str | registry_studies.body_site_primary at harvest time (convenience) |
| `release_added` | string | Release id in which the row first appeared |
| `release_retired` | string | Release id in which the row was retired (null = current) |
| `package_added` | string | Package version that first carried the row |

### registry_authors.parquet (52,110 rows) — one row per study × paper × author (Europe PMC core records of ≤ 5 linked papers per study) — added R2026.5

| column | dtype | meaning |
|---|---|---|
| `study_accession` | str | ENA study (BioProject) accession |
| `author_full_name` | str | Author full name (Europe PMC) |
| `author_display` | str | Surname + initials |
| `author_surname` | str | Surname |
| `author_initials` | str | Initials |
| `is_group` | bool | Collective/group author |
| `source` | str | europepmc_mention | bioproject_xml | both — how the paper was linked |
| `pmid` | str | PubMed id of the linked paper |
| `pmcid` | str | PubMed Central id |
| `doi` | str | DOI |
| `paper_title` | str | Paper title |
| `pub_year` | Int64 | Publication year |
| `position` | int64 | Author position (1 = first); part of the key |
| `n_authors` | int64 | Authors on the paper |
| `is_first` | bool | First author |
| `is_last` | bool | Last author |
| `affiliation` | str | Affiliation string (e-mail addresses removed, rule F7) |
| `orcid` | str | ORCID iD when present |
| `paper_relation` | str | relation of the study×paper link (registry_study_papers) |
| `link_method` | str | method of the link |
| `author_source` | str | europepmc_core |
| `author_key` | str | lower-case surname + initials key for de-duplication |
| `release_added` | string | Release id in which the row first appeared |
| `release_retired` | string | Release id in which the row was retired (null = current) |
| `package_added` | string | Package version that first carried the row |

### gut_sample_determinations.parquet (1,659,128 rows) — one row per sample × field of the gut_all curated scope (current value; precedence infant catalog > R1 > R2 > R3 > R4) — added R2026.7

| column | dtype | meaning |
|---|---|---|
| `sample_key` | string | BioSample accession (or run accession for run-unit samples); with field_name the key |
| `field_name` | string | Field of config/packs/gut.yaml |
| `study_accession` | string | ENA study |
| `field_value` | string | Raw value as found in the source |
| `value_normalized` | string | Normalised value (days for age, vocabulary codes, ISO-2 country, yes/no) |
| `confidence` | float64 | Route/parser confidence tier (R1 0.85–0.9, R2 0.8–0.85, R3 ≤ 0.7, R4 ≤ 0.5) |
| `evidence_source` | string | Labelled source: sample.attr.<key> | paper.supp.<file>[sheet!column] | paper.abstract | study.description | study.title | … |
| `evidence_locator` | string | biosample_attr | PMCID | PMID | accession |
| `evidence_quote` | string | Verbatim quote ≤ 12 words |
| `evidence_limited_to_abstract` | float64 | 1 when the evidence is abstract/description only (R4) |
| `determined_by` | string | Parser / stage id |
| `route` | string | R1 | R2 | R3 | R4 |
| `scope` | string | sample (rows expanded from study_all statements carry 'expanded from study_all' in parse_note) |
| `parse_note` | string | Parser notes |
| `group_audit` | string | Audit note for group statements (infant rows) |
| `src_track` | string | infant_catalog (copied from the curated infant tables) | gut_all_v1 |
| `release_added` | string | Release id in which the row first appeared |
| `release_retired` | string | Release id in which the row was retired (null = current) |
| `package_added` | string | Package version that first carried the row |

### gut_sample_metadata_wide.parquet (579,252 rows) — one row per sample of the gut_all curated scope (one column per field with its route and confidence) — added R2026.7

| column | dtype | meaning |
|---|---|---|
| `sample_key` | string | BioSample accession; primary key |
| `study_accession` | string | ENA study |
| `biosample_accession` | string | BioSample accession |
| `secondary_sample` | string | ERS/SRS/DRS accession (infant rows) |
| `sample_unit` | string | biosample | run |
| `body_site_code` | string | Registry body-site code of the sample (from its attributes) or the infant catalog's class |
| `sample_life_stage` | string | Registry life stage from the sample's age attribute |
| `curated_source` | string | infant_catalog | gut_all_v1 |
| `in_infant_catalog` | bool | Study is an included infant-catalog study |
| `age_at_collection_days` | float64 | Age at collection in days |
| `sex` | string | female | male |
| `bmi` | float64 | Body-mass index kg/m² |
| `country` | string | ISO-3166-1 alpha-2 |
| `health_condition` | string | config/vocab/health_conditions.yaml code |
| `health_condition_detail` | string | Raw health / disease text |
| `antibiotic_exposure` | string | yes | no |
| `subject_id` | string | Subject / participant identifier as given by the source |
| `timepoint_label` | string | Timepoint / visit label as given |
| `age_at_collection_days__confidence` | float64 | Confidence of the committed age_at_collection_days value |
| `age_at_collection_days__route` | string | Route of the committed age_at_collection_days value |
| `sex__confidence` | float64 | Confidence of the committed sex value |
| `sex__route` | string | Route of the committed sex value |
| `bmi__confidence` | float64 | Confidence of the committed bmi value |
| `bmi__route` | string | Route of the committed bmi value |
| `country__confidence` | float64 | Confidence of the committed country value |
| `country__route` | string | Route of the committed country value |
| `health_condition__confidence` | float64 | Confidence of the committed health_condition value |
| `health_condition__route` | string | Route of the committed health_condition value |
| `health_condition_detail__confidence` | float64 | Confidence of the committed health_condition_detail value |
| `health_condition_detail__route` | string | Route of the committed health_condition_detail value |
| `antibiotic_exposure__confidence` | float64 | Confidence of the committed antibiotic_exposure value |
| `antibiotic_exposure__route` | string | Route of the committed antibiotic_exposure value |
| `subject_id__confidence` | float64 | Confidence of the committed subject_id value |
| `subject_id__route` | string | Route of the committed subject_id value |
| `timepoint_label__confidence` | float64 | Confidence of the committed timepoint_label value |
| `timepoint_label__route` | string | Route of the committed timepoint_label value |
| `delivery_mode` | string | Infant-catalog field (only on infant rows) |
| `feeding_mode` | string | Infant-catalog field (only on infant rows) |
| `preterm_status` | string | Infant-catalog field (only on infant rows) |
| `gestational_age_weeks` | string | Infant-catalog field (only on infant rows) |
| `birth_weight_grams` | string | Infant-catalog field (only on infant rows) |
| `maternal_antibiotics` | string | Infant-catalog field (only on infant rows) |
| `probiotic_exposure` | string | Infant-catalog field (only on infant rows) |
| `hmo_supplementation` | string | Infant-catalog field (only on infant rows) |
| `nec_status` | string | Infant-catalog field (only on infant rows) |
| `age_category` | string | neonate | infant | child | adolescent | adult | elderly | unknown (config/packs/gut.yaml age_categories) |
| `age_category_basis` | string | age_at_collection_days | infant_catalog_age_scope | sample_life_stage | study_life_stage | unknown |
| `body_site_class` | string | primary (gut/stool) | unknown | excluded | linked |
| `body_site_basis` | string | sample_attribute | study_single_site | none |
| `infant_scope` | bool | == the infant catalog's catalog_scope rule (reproduces the infant catalog exactly) |
| `n_fields_with_value` | int64 | Pack fields with a committed value |
| `release_added` | string | Release id |
| `release_retired` | string | Release id when retired (null = current) |
| `package_added` | string | Package version |

### gut_studies.parquet (2,837 rows) — one row per study of the gut_all curated scope — registry columns plus curation coverage — added R2026.7

| column | dtype | meaning |
|---|---|---|
| `study_accession` | string | ENA study; primary key |
| `secondary_study_accession` | string | registry_studies column (see registry_studies.parquet) |
| `study_title` | string | registry_studies column (see registry_studies.parquet) |
| `description_short` | string | registry_studies column (see registry_studies.parquet) |
| `center_name` | string | registry_studies column (see registry_studies.parquet) |
| `first_public_min` | string | registry_studies column (see registry_studies.parquet) |
| `first_public_max` | string | registry_studies column (see registry_studies.parquet) |
| `n_runs` | int64 | Runs |
| `n_samples` | int64 | Samples |
| `n_biosamples` | int64 | BioSamples |
| `n_runs_sandpiper` | int64 | Runs with Sandpiper profiles |
| `…registry_studies columns…` |  | all registry_studies columns are carried (classification, evidence, scope memberships, sample roll-ups) |
| `cov_<field>` | float64 | Share of the study's samples with a value for each pack field |
| `n_samples_curated` | int64 | Samples in gut_sample_metadata_wide |
| `age_categories` | string | JSON dict age_category → samples |
| `health_conditions` | string | JSON dict health_condition → samples (top 6) |
| `curated_depth` | string | Routes present among the study's committed values (e.g. R1;R2;R4) |
| `curated_source` | string | infant_catalog | gut_all_v1 |
| `release_added` | string | Release id |
| `release_retired` | string | Release id when retired |
| `package_added` | string | Package version |

### gut_runs.parquet (721,678 rows) — one row per sequencing run of a gut_all catalog study (registry_runs ∩ gut_studies) with the catalog sample_key — added R2026.12 / 1.12.0 — added R2026.12

| column | dtype | meaning |
|---|---|---|
| `run_accession` | string | ENA/SRA run accession (SRR/ERR/DRR) |
| `study_accession` | string | BioProject of the run (a gut_studies row) |
| `sample_accession` | string | BioSample accession(s) of the run (';'-joined when the run pools several) |
| `secondary_sample_accession` | string | SRS/ERS secondary sample accession(s) |
| `experiment_accession` | string | SRX/ERX experiment accession |
| `library_name` | string | Submitter library name |
| `library_strategy` | string | ENA library_strategy (WGS / WXS / OTHER …) |
| `library_source` | string | ENA library_source (METAGENOMIC …) |
| `library_layout` | string | SINGLE | PAIRED |
| `instrument_platform` | string | ENA instrument_platform |
| `instrument_model` | string | ENA instrument_model |
| `read_count` | string | ENA read_count (as delivered by the portal) |
| `base_count` | string | ENA base_count (as delivered by the portal) |
| `first_public` | string | ENA first_public date (YYYY-MM-DD); its year bounds collection_date (R1 parser) |
| `sandpiper_profiled` | bool | Run present in the Sandpiper snapshot (registry_runs_sandpiper) |
| `sample_key` | string | gut_sample_metadata_wide.sample_key of the run's sample (run accession for run-unit samples; null when the BioSample is not a catalog sample, e.g. pooled runs) |

### gut_sandpiper_sample_summary.parquet (339,626 rows) — one row per catalog sample with >= 1 run in the Sandpiper 2.0.0 snapshot (339,626 samples / 2,084 studies) — added R2026.12 — added R2026.12

| column | dtype | meaning |
|---|---|---|
| `sample_key` | str | catalog sample key (gut_sample_metadata_wide) |
| `study_accession` | str | BioProject |
| `n_runs_profiled` | int64 | runs of the sample with a Sandpiper profile |
| `n_runs_total` | int64 | runs of the sample in gut_runs |
| `qc_partial` | bool | 1 = some runs of the sample are unprofiled |
| `root_coverage_sum` | float64 | summed SingleM root coverage over the profiled runs (depth proxy) |
| `richness_genus` | int64 | genera with relative abundance >= 0.001 |
| `shannon_genus` | float64 | Shannon index over genus relative abundances (natural log) |
| `shannon_genus_assigned` | float64 | Shannon over genus-assigned coverage only (unassigned remainder excluded) |
| `top_genus` | str | most abundant GTDB genus |
| `top_genus_relabund` | float64 | its relative abundance (0-1) |
| `unassigned_genus_relabund` | float64 | share of root coverage not resolved to a genus |
| `unassigned_species_relabund` | float64 | share of root coverage not resolved to a species |
| `spf` | float64 | Sandpiper single-copy-marker profile fraction (per_acc_summary) |
| `known_species_fraction` | float64 | fraction of coverage in known species (per_acc_summary) |
| `qc_flags` | str | ';'-joined QC flags (qc_low_depth, qc_low_complexity, qc_non_metagenome, qc_partial, ...) — flagged, never dropped |
| `qc_any_flag` | bool | 1 = at least one QC flag set |
| `qc_low_depth` | bool | root coverage below the depth threshold |
| `qc_low_complexity` | bool | Sandpiper low_complexity flag |
| `qc_readfraction_warning` | bool | Sandpiper read-fraction warning |
| `qc_non_metagenome` | bool | organism prediction not a metagenome (loose rule) |
| `qc_non_metagenome_strict` | bool | organism prediction not a metagenome (strict rule) |
| `qc_synthetic` | bool | Sandpiper synthetic-sample flag |
| `qc_rna` | bool | RNA / non-DNA library flag |
| `qc_predicted_ecological` | bool | Sandpiper host_or_not prediction = ecological |
| `qc_no_genus_assigned` | bool | no coverage resolved to any genus |
| `organism_labels` | str | ';'-joined Sandpiper organism labels of the runs |
| `runs_profiled` | str | ';'-joined profiled run accessions |
| `sandpiper_url` | str | Sandpiper page of the first profiled run |
| `age_category` | str | catalog age category (copied from the wide table) |
| `body_site_class` | str | catalog body-site class |
| `infant_scope` | bool | infant-extension filter flag |
| `taxonomy_db` | str | taxonomy database (GTDB) |
| `taxonomy_version` | str | GTDB release (R232) |
| `sandpiper_version` | str | Sandpiper version (2.0.0) |
| `zenodo_record` | str | Zenodo record of the bulk snapshot (20419175) |

### gut_sandpiper_pca_scores.parquet (335,956 rows) — one row per scored sample of the genus-level CLR-PCA (335,956 samples x 383 genera; root coverage >= 2) — added R2026.12 — added R2026.12

| column | dtype | meaning |
|---|---|---|
| `sample_key` | str | catalog sample key |
| `pc1` | float32 | PCA score, component 1 (16.3 % variance) |
| `pc2` | float32 | component 2 (6.5 %) |
| `pc3` | float32 | component 3 (3.8 %) |
| `pc4` | float32 | component 4 (3.2 %) |
| `pc5` | float32 | component 5 (2.3 %) |
| `study_accession` | str | BioProject |
| `root_coverage_sum` | float64 | summed root coverage (depth proxy) |
| `taxonomy_db` | str | taxonomy database (GTDB) |
| `taxonomy_version` | str | GTDB release (R232) |
| `sandpiper_version` | str | Sandpiper version (2.0.0) |
| `zenodo_record` | str | Zenodo record of the bulk snapshot (20419175) |
| `pca_method` | str | CLR on 383 genera (prevalence >= 1 %), multiplicative pseudocount, randomized SVD |

### gut_sandpiper_pca_loadings.parquet (383 rows) — one row per genus x component loading of the CLR-PCA — added R2026.12 — added R2026.12

| column | dtype | meaning |
|---|---|---|
| `genus` | str | GTDB R232 genus |
| `pc1` | float64 | loading |
| `pc2` | float64 | loading |
| `pc3` | float64 | loading |
| `pc4` | float64 | loading |
| `pc5` | float64 | loading |
| `prevalence_ge_0p1pct` | float64 | share of samples where the genus is >= 0.1 % relative abundance |
| `mean_relabund` | float64 | mean relative abundance over scored samples |
| `clr_mean` | float64 | mean CLR value |
| `taxonomy_db` | str | taxonomy database (GTDB) |
| `taxonomy_version` | str | GTDB release (R232) |
| `sandpiper_version` | str | Sandpiper version (2.0.0) |

### gut_sandpiper_pca_variance.csv (5 rows) — one row per PCA component: explained variance ratio — added R2026.12 — added R2026.12

| column | dtype | meaning |
|---|---|---|
| `pc` | str | component index |
| `explained_variance_ratio` | float64 | share of CLR variance |
| `singular_value` | float64 | singular value |
| `cumulative` | float64 | cumulative explained variance ratio |
| `n_samples` | int64 | samples in the PCA |
| `n_genera` | int64 | genera in the PCA |
| `root_coverage_min` | int64 | minimum root coverage for inclusion |
| `prevalence_min` | float64 | minimum genus prevalence for inclusion |
| `prevalence_detection` | float64 | relative-abundance threshold used for prevalence |
| `pseudocount` | float64 | multiplicative pseudocount before CLR |
| `total_clr_variance` | float64 | total variance of the CLR matrix |

### gut_sandpiper_study_coverage.csv (2,837 rows) — one row per catalog study: Sandpiper join coverage — added R2026.12 — added R2026.12

| column | dtype | meaning |
|---|---|---|
| `study_accession` | str | BioProject |
| `study_title` | str | study title |
| `n_runs` | int64 | runs in gut_runs |
| `n_runs_profiled` | int64 | runs with a profile |
| `n_samples` | int64 | samples |
| `n_infant_scope` | int64 | infant-scope samples |
| `n_samples_profiled` | int64 | samples with >= 1 profiled run |
| `n_samples_partial` | int64 | samples with unprofiled runs |
| `n_low_depth` | int64 | samples flagged low depth |
| `n_low_complexity` | int64 | samples flagged low complexity |
| `n_any_flag` | int64 | samples with any QC flag |
| `root_coverage_median` | float64 | median root coverage |
| `richness_genus_median` | float64 | median genus richness |
| `shannon_genus_median` | float64 | median Shannon |
| `frac_runs_profiled` | float64 | profiled / total runs |
| `frac_samples_profiled` | float64 | profiled / total samples |
| `coverage_class` | str | full | partial | none |
| `top_genus_mode` | str | most frequent top genus |
| `prefix` | str | accession prefix (SRR/ERR/DRR) |
| `sandpiper_version` | str | Sandpiper version (2.0.0) |
| `taxonomy_db` | str | taxonomy database (GTDB) |
| `taxonomy_version` | str | GTDB release (R232) |
| `zenodo_record` | int64 | Zenodo record of the bulk snapshot (20419175) |

### gut_relatedness.parquet (5,858 rows) — one row per family or shared setting with >= 2 catalogued people — added R2026.23 / 1.19.0 — added R2026.23

| column | dtype | meaning |
|---|---|---|
| `study_accession` | str | catalog project |
| `group_kind` | str | family | shared_setting |
| `group_type` | str | family, or the shared setting type |
| `group_id` | str | family_id / shared_setting_id as given in the source |
| `n_people` | int64 | distinct people (person_key) |
| `n_samples` | int64 | catalog samples |
| `roles` | str | JSON {family_role: n_people} |
| `motif` | str | family motif, e.g. mother + child×2 |
| `people` | str | ';'-joined person keys |
| `sample_keys` | str | ';'-joined sample keys |

### gut_relations.parquet (1,015 rows) — one row per explicit pairwise relation stated by the authors — added R2026.23 / 1.19.0 — added R2026.23

| column | dtype | meaning |
|---|---|---|
| `study_accession` | str | catalog project |
| `subject_a` | str | person (subject id or label) |
| `subject_b` | str | related person |
| `relation` | str | donor_of | twin_of | twin_mz_of | twin_dz_of | partner_of | roommate_of | mother_of | father_of | sibling_of | household_member_of | ... |
| `evidence_source` | str | where the relation is stated |
| `evidence_quote` | str | verbatim quote |
| `confidence` | float64 | 0-1 |
| `sample_key_a` | str | a sample of subject_a when known |
| `notes` | str | free text |

## Sandpiper columns (added v1.2.0)
Source: SingleM community profiles from Sandpiper 2.0.0 (Woodcroft et al. 2025, *Nat Biotechnol*; Zenodo record 20419175, CC-BY), taxonomy GTDB R232. Profiles are keyed by run; for a catalog sample with several profiled runs the filled coverage per taxon is **summed across runs and then normalised** (never averaged). Every `*_ra` column is a **fraction of prokaryotic (Bacteria + Archaea) coverage** — approximately a cell proportion, not a read fraction; not comparable with MetaPhlAn or 16S numbers.

| column | meaning |
|---|---|
| `sp_profiled` | True when at least one run of the sample has a Sandpiper profile |
| `sandpiper_url` | `https://sandpiper.qut.edu.au/run/<run>` — the RANDOM-selection profiled run with the highest root coverage (v1.2.1) |
| `sp_n_runs_total`, `sp_n_runs_profiled`, `sp_partial` | run counts; `sp_partial` = some runs unprofiled |
| `sp_spf` | prokaryotic read fraction (%), metagenome-size-weighted over runs |
| `sp_known_species_fraction` | % of prokaryotic coverage assigned to a named GTDB species |
| `sp_root_coverage` | summed root coverage (× genome equivalents); `sp_low_depth` = root < 2× (bars hidden) |
| `sp_top_genus`, `sp_top_genus_ra` | most abundant genus-level bin (may be `unassigned_at_genus`) |
| `sp_ra_g_Bifidobacterium` | GTDB `g__Bifidobacterium` (stable genus) |
| `sp_ra_f_Bacteroidaceae`, `sp_ra_g_Bacteroides`, `sp_ra_g_Phocaeicola` | GTDB moved *B. vulgatus/dorei/plebeius/coprocola* to *Phocaeicola*; the family ≈ NCBI-sense *Bacteroides* |
| `sp_ra_enterobacterales_core` | Σ of six GTDB genera incl. their alphabetic-suffix genera (`^g__<Name>(_[A-Z]+)?$`): Escherichia, Klebsiella, Enterobacter(+_B,_D), Citrobacter(+_A = amalonaticus/farmeri, _B = koseri, _C, _D), Salmonella, Serratia(+_B,_G) (GTDB `f__Enterobacteriaceae` is broader than NCBI's, so the family is not used) |
| `sp_ra_g_Escherichia`, `sp_ra_g_Klebsiella`, `sp_ra_f_Lachnospiraceae`, `sp_ra_f_Lactobacillaceae` (GTDB splits *Lactobacillus*), `sp_ra_g_Streptococcus`, `sp_ra_g_Staphylococcus`, `sp_ra_g_Enterococcus`, `sp_ra_g_Veillonella`, `sp_ra_g_Clostridioides` (*C. difficile*) | indicator taxa |
| `sp_ra_unassigned_genus` | coverage not resolved to a genus (novel fraction; never renormalised away) |
| `sp_shannon_genus`, `sp_n_genera_ge1pct` | Shannon (ln) over genus bins incl. the unassigned bin; named genera ≥ 1 % |
| `sp_flag_low_complexity` | Sandpiper low-complexity rule — expected for Bifidobacterium-/Enterobacterales-dominated neonatal stool; display only, never triage |
| `sp_nonmeta_class`, `sp_flag_non_metagenome`, `sp_flag_synthetic`, `sp_flag_rna`, `sp_flag_readfraction_warning` | see **sandpiper_flag_table.csv** (single source for field, site label, definition, level; `sandpiper_flag_definitions.json` is generated from it). `sp_flag_non_metagenome` = `sp_nonmeta_class ∈ {named_nonhuman_host, named_microbe_or_other}` over the sample's runs (v1.2.1) |
| `sp_run_concordance_bc`, `sp_runs_discordant` | max pairwise genus-level Bray–Curtis between the sample's runs; discordant = BC > 0.5 (human-review hint, not a verdict) |
| `taxonomy_db`, `taxonomy_version`, `sandpiper_version` | GTDB / R232 / 2.0.0 on every profiled row |

### GTDB alphabetic-suffix genera (v1.2.1, R3-1)
Every genus indicator matches `^g__<Name>(_[A-Z]+)?$`, i.e. the GTDB genus **and** its alphabetic-suffix split genera, so that the NCBI-sense genus is covered. `gtdb_to_ncbi_note` per indicator:

| indicator | GTDB taxa summed | gtdb_to_ncbi_note |
|---|---|---|
| `sp_ra_g_Bifidobacterium` | g__Bifidobacterium | single GTDB genus; equals NCBI Bifidobacterium |
| `sp_ra_g_Bacteroides` | g__Bacteroides, g__Bacteroides_D, g__Bacteroides_E, g__Bacteroides_F | Bacteroides_D/_E/_F are NCBI Bacteroides species split by GTDB (e.g. B. pectinophilus) |
| `sp_ra_g_Phocaeicola` | g__Phocaeicola, g__Phocaeicola_A | Phocaeicola_A added; the family f__Bacteroidaceae ≈ NCBI-sense Bacteroides |
| `sp_ra_enterobacterales_core` | g__Citrobacter, g__Citrobacter_A, g__Citrobacter_B, g__Citrobacter_C, g__Citrobacter_D, g__Enterobacter, g__Enterobacter_B, g__Enterobacter_D, g__Escherichia, g__Klebsiella, g__Salmonella, g__Serratia, g__Serratia_B, g__Serratia_G | includes Citrobacter_A (amalonaticus/farmeri), Citrobacter_B (koseri), Enterobacter_B/_D, Serratia_B/_G; Escherichia includes NCBI Shigella |
| `sp_ra_g_Escherichia` | g__Escherichia | GTDB Escherichia includes NCBI Shigella |
| `sp_ra_g_Klebsiella` | g__Klebsiella | single GTDB genus |
| `sp_ra_g_Streptococcus` | g__Streptococcus | single GTDB genus in this dataset |
| `sp_ra_g_Staphylococcus` | g__Staphylococcus | single GTDB genus in this dataset |
| `sp_ra_g_Enterococcus` | g__Enterococcus, g__Enterococcus_A, g__Enterococcus_B, g__Enterococcus_C, g__Enterococcus_D, g__Enterococcus_E, g__Enterococcus_F, g__Enterococcus_G, g__Enterococcus_H, g__Enterococcus_I, g__Enterococcus_J, g__Enterococcus_K, g__Enterococcus_L | includes GTDB Enterococcus_A (avium/raffinosus/gilvus), _B (faecium/lactis/hirae/durans), _C (asini/dispar), _D (gallinarum/casseliflavus), _E (cecorum), _F … _L; plain g__Enterococcus = E. faecalis group |
| `sp_ra_g_Veillonella` | g__Veillonella, g__Veillonella_A | includes Veillonella_A (seminalis/ratti and unnamed species) |
| `sp_ra_g_Clostridioides` | g__Clostridioides | single GTDB genus (C. difficile) |

Placeholder genera: GTDB R232 contains alphanumeric placeholder genera without a Latin name, e.g. `g__ECMA0423` (Enterobacteriaceae; one species, detected in ~34 % of profiled samples at ~0.25 % mean RA) and `g__G047199095`. They are real GTDB genera, appear in `sandpiper_top_genera.parquet` and in study panels under their placeholder name, and are **not** counted in any named indicator column. Bifidobacterium species shares (`share_within_bifidobacterium`, off-package table) are normalised over **named species only**; in ~20 % of samples > 10 % of genus coverage is unresolved at species rank, so shares overstate named species — use `sp_ra_unassigned_species` as the denominator caveat.

`<field>__scope` (16 coverage fields): scope of the winning determination — `sample`, `subject`, `biosample` (propagated from a parent BioSample) or `group` (R3/R4 statement applied to a defined group). Apply the README reading rule to `group`-scope values.

Study columns `sp_*`: `sp_frac_samples_profiled` = profiled rows / `n_sample_rows` (one denominator, v1.2.1); `sp_median_*` are computed over **panel-scope** rows (`catalog_scope` ∧ profiled ∧ NOT `sp_low_depth`) and are null with `sp_panel_scope` giving the fallback label when a study has none; `sp_frac_runs_flagged` counts runs whose organism label is a non-human host or a named microbe, or synthetic / RNA (the routine "Homo sapiens" host label is *not* counted). `sp_auditor_attention`/`sp_auditor_reason`: the Sandpiper track's hint that a study deserves a human look (never a verdict).

## Author columns (added v1.2.0)
`authors.parquet`: one row per study × author × paper (`author_display`, `author_surname`, `author_initials`, `is_group`, `source` ∈ linked_paper / bioproject_publication / ena_study_xref, `pmid`, `doi`, `position`, `n_authors`, `is_first`, `is_last`, `paper_relation`, `catalog_status`). `study_authors_summary.csv`: per screened study `first_author`, `last_author`, `n_authors`, `n_papers`, `organisations` (ENA centre / broker and NCBI BioProject owner names, `;`-joined). Names are string-matched from Europe PMC `authorString`; the same person may appear under several initial variants and no disambiguation was attempted.

## sandpiper_run_qc.parquet (documented v1.2.1)
One row per run of the 389 included studies (174,022 rows). Keys: `run_accession`, `study_accession`, `sample_accession` (BioSample), `catalog_sample_key` (sample-table key), `sample_unit` (`biosample` / `run` — v1.2.0 wrote the retired value `biosample_pooled` for the 521 run-unit runs; renamed in v1.2.1 so a join on `sample_unit = 'run'` works).

| column | meaning |
|---|---|
| `in_sandpiper` | True when the run has a profile in the Zenodo snapshot |
| `sp_miss_reason` | why not (precedence v1.2.1: strategy → platform → access → horizon → size → snapshot gap): `profiled` · `non_wgs_strategy` (library_strategy in {AMPLICON, Targeted-Capture, WXS} — the strategies with zero profiled runs in the snapshot; OTHER and WGA are profiled by Sandpiper and are not miss reasons) · `non_illumina_platform` · `controlled_access_study` · `published_after_snapshot_horizon` · `unknown_size` (ENA base_count ≤ 0, so size cannot be judged) · `small_run_lt_100Mbp` · `ena_illumina_not_in_snapshot` / `sra_illumina_not_in_snapshot` / `ddbj_illumina_not_in_snapshot` (eligible by every rule but absent from the snapshot) |
| `library_strategy`, `library_source`, `instrument_platform`, `base_count`, `first_public`, `organism` | ENA run/sample fields used by the rules |
| `sp_root_coverage`, `sp_species_coverage` | root / species-level coverage (genome equivalents) |
| `top1_order_fraction`, `top3_order_fraction` | share of coverage in the top 1 / top 3 orders (low-complexity inputs) |
| `sp_spf`, `sp_known_species_fraction` | prokaryotic read fraction (%), known-species fraction (%) |
| `bacterial_archaeal_bases`, `metagenome_size`, `average_bacterial_archaeal_genome_size` | Sandpiper size estimates (bases) |
| `sp_warning_present` | Sandpiper emitted a warning for the run |
| `sp_prediction`, `sp_host_or_not` | Sandpiper ecological prediction and host-associated call |
| `sp_nonmeta_class` | organism-label class: `not_flagged` · `named_human_host` · `named_nonhuman_host` · `named_microbe_or_other` (see sandpiper_flag_table.csv) |
| `sp_flag_*`, `sp_low_depth`, `sp_predicted_ecological`, `sp_low_known_species_high_depth` | run-level flags — every one is defined in **sandpiper_flag_table.csv** |
| `sandpiper_url`, `sandpiper_version`, `taxonomy_db`, `taxonomy_version`, `zenodo_record` | provenance (Sandpiper 2.0.0 outputs, Zenodo 20419175, GTDB R232; the live Sandpiper site may show a newer version) |

## sandpiper_study_panels.parquet (v2, v1.2.1)
Mean relative abundance per study over **panel-scope rows** = `catalog_scope` ∧ `sp_profiled` ∧ NOT `sp_low_depth` (top-12 phyla, top-15 genera, plus the `unassigned_at_*` bins with `rank_order = 0`). Columns: `study_accession`, `rank`, `taxon`, `mean_rel_abundance`, `n_samples_panel` (rows averaged; asserted equal to the recomputation from the shipped sample table), `rank_order`, `n_samples_study` (rows of the study), `n_catalog_scope`, `n_profiled`, `n_profiled_catalog_scope`, `n_profiled_age_infant` / `n_profiled_age_adult` / `n_profiled_age_unknown_or_other` (age_scope breakdown of all profiled rows, for the caption), `n_low_depth_excluded_from_panel`, `frac_samples_profiled`, `panel_scope`, `panel_definition`, `taxonomy_db`, `taxonomy_version`. Studies without panel-scope rows have **no rows** here; their fallback label is in `sandpiper_study_panel_status.csv` and in `study_metadata_wide.sp_panel_scope`. The v1.2.0 merge artefacts `n_samples_x`/`n_samples_y` are gone.

## sandpiper_flag_table.csv (v1.2.1)
Single source for every Sandpiper flag: `field`, `level` (run / sample / study), `site_label`, `definition`, `used`. `sandpiper_flag_definitions.json` is generated from it; the GlobDB domain-only flags are kept with `used = false` (the catalog uses GTDB profiles only).

## organisations.parquet (v2, v1.2.1) and study_authors_summary.csv
One row per study × organisation token × source (`bioproject_organization` = NCBI BioProject Organization, `submitter_center` = ENA center_name, `ena_broker`). v1.2.1 adds `not_org_reason` (`submission_id` for `^SUB\d+( \| SUB\d+)*$`, `email_or_handle` for tokens containing `@`, `placeholder` for n/a / None / '.', `lowercase_username` for single all-lowercase tokens such as `hpsun`), `is_organisation` (not_org_reason is null; such rows get `org_type = not_an_organisation`) and `display_eligible` (is_organisation AND, for PRJNA studies with an NCBI BioProject organisation, not an ENA center_name). `organisations_index.json`, `study_authors_summary.organisations` and `study_metadata_wide.organisations` are built from `display_eligible` rows only; a check asserts that no published organisation string matches `SUB\d{6,}` or contains `@`. Single lowercase tokens include some genuine company/institute acronyms (e.g. novogene, rivm) — they stay in the parquet for owner whitelisting but are not displayed.

`authors.parquet` (v2): `author_key` folds names with a transliteration map (ø→o, æ→ae, ß→ss, ł→l, đ→d, İ→i, Ð→d, Þ→th) before NFKD stripping, and folds initials too; the group regex includes office / production / editorial; `name_needs_check` flags no-initials multi-token person names for manual review.

## Complete column reference (generated from the parquet schema; the build fails on an undocumented column)
### sample_metadata_wide.parquet (147 columns)
| column | dtype | description |
|---|---|---|
| `sample_key` | str | primary key: BioSample accession for `biosample` units, run accession for `run` units |
| `study_accession` | str | BioProject accession |
| `secondary_sample` | str | ENA secondary sample accession |
| `sample_title` | str | submitter sample title |
| `body_site_class` | str | body-site scope class — vocabulary: `primary` (infant gut/stool/meconium/rectal), `unknown` (no body-site evidence; kept in body-site scope), `excluded` (non-gut site: skin, oral, milk, vaginal, nasal…; or, since v1.2.1, non-human host / isolate genome — see `exclusion_reason_code`), `linked` (mother/other sample linked to an infant subject; out of body-site scope) |
| `collection_date` | str | archive collection_date as given |
| `is_gold_heldout` | bool | True if in the curatedMetagenomicData evaluation set |
| `probiotic_exposure` | str | metadata field `probiotic_exposure` (see Field vocabularies) |
| `preterm_status` | str | metadata field `preterm_status` (see Field vocabularies) |
| `gestational_age_weeks` | float64 | metadata field `gestational_age_weeks` (see Field vocabularies) |
| `delivery_mode` | str | metadata field `delivery_mode` (see Field vocabularies) |
| `feeding_mode` | str | metadata field `feeding_mode` (see Field vocabularies) |
| `antibiotic_exposure` | str | metadata field `antibiotic_exposure` (see Field vocabularies) |
| `age_at_collection_days` | float64 | metadata field `age_at_collection_days` (see Field vocabularies) |
| `birth_weight_grams` | float64 | metadata field `birth_weight_grams` (see Field vocabularies) |
| `country` | str | metadata field `country` (see Field vocabularies) |
| `maternal_antibiotics` | str | metadata field `maternal_antibiotics` (see Field vocabularies) |
| `hmo_supplementation` | str | metadata field `hmo_supplementation` (see Field vocabularies) |
| `nec_status` | str | metadata field `nec_status` (see Field vocabularies) |
| `health_condition` | str | metadata field `health_condition` (see Field vocabularies) |
| `multiple_birth` | str | metadata field `multiple_birth` (see Field vocabularies) |
| `sibling_in_study` | str | metadata field `sibling_in_study` (see Field vocabularies) |
| `geo_subregion` | str | metadata field `geo_subregion` (see Field vocabularies) |
| `sex` | str | metadata field `sex` (see Field vocabularies) |
| `timepoint_label` | str | metadata field `timepoint_label` (see Field vocabularies) |
| `subject_id` | str | metadata field `subject_id` (see Field vocabularies) |
| `probiotic_exposure__confidence` | float64 | confidence of `probiotic_exposure` (0–1) |
| `preterm_status__confidence` | float64 | confidence of `preterm_status` (0–1) |
| `gestational_age_weeks__confidence` | float64 | confidence of `gestational_age_weeks` (0–1) |
| `delivery_mode__confidence` | float64 | confidence of `delivery_mode` (0–1) |
| `feeding_mode__confidence` | float64 | confidence of `feeding_mode` (0–1) |
| `antibiotic_exposure__confidence` | float64 | confidence of `antibiotic_exposure` (0–1) |
| `age_at_collection_days__confidence` | float64 | confidence of `age_at_collection_days` (0–1) |
| `birth_weight_grams__confidence` | float64 | confidence of `birth_weight_grams` (0–1) |
| `country__confidence` | float64 | confidence of `country` (0–1) |
| `maternal_antibiotics__confidence` | float64 | confidence of `maternal_antibiotics` (0–1) |
| `hmo_supplementation__confidence` | float64 | confidence of `hmo_supplementation` (0–1) |
| `nec_status__confidence` | float64 | confidence of `nec_status` (0–1) |
| `health_condition__confidence` | float64 | confidence of `health_condition` (0–1) |
| `multiple_birth__confidence` | float64 | confidence of `multiple_birth` (0–1) |
| `sibling_in_study__confidence` | float64 | confidence of `sibling_in_study` (0–1) |
| `geo_subregion__confidence` | float64 | confidence of `geo_subregion` (0–1) |
| `sex__confidence` | float64 | confidence of `sex` (0–1) |
| `timepoint_label__confidence` | float64 | confidence of `timepoint_label` (0–1) |
| `subject_id__confidence` | float64 | confidence of `subject_id` (0–1) |
| `probiotic_exposure__route` | str | route of `probiotic_exposure` (R1–R4, see Routes) |
| `preterm_status__route` | str | route of `preterm_status` (R1–R4, see Routes) |
| `gestational_age_weeks__route` | str | route of `gestational_age_weeks` (R1–R4, see Routes) |
| `delivery_mode__route` | str | route of `delivery_mode` (R1–R4, see Routes) |
| `feeding_mode__route` | str | route of `feeding_mode` (R1–R4, see Routes) |
| `antibiotic_exposure__route` | str | route of `antibiotic_exposure` (R1–R4, see Routes) |
| `age_at_collection_days__route` | str | route of `age_at_collection_days` (R1–R4, see Routes) |
| `birth_weight_grams__route` | str | route of `birth_weight_grams` (R1–R4, see Routes) |
| `country__route` | str | route of `country` (R1–R4, see Routes) |
| `maternal_antibiotics__route` | str | route of `maternal_antibiotics` (R1–R4, see Routes) |
| `hmo_supplementation__route` | str | route of `hmo_supplementation` (R1–R4, see Routes) |
| `nec_status__route` | str | route of `nec_status` (R1–R4, see Routes) |
| `health_condition__route` | str | route of `health_condition` (R1–R4, see Routes) |
| `multiple_birth__route` | str | route of `multiple_birth` (R1–R4, see Routes) |
| `sibling_in_study__route` | str | route of `sibling_in_study` (R1–R4, see Routes) |
| `geo_subregion__route` | str | route of `geo_subregion` (R1–R4, see Routes) |
| `sex__route` | str | route of `sex` (R1–R4, see Routes) |
| `timepoint_label__route` | str | route of `timepoint_label` (R1–R4, see Routes) |
| `subject_id__route` | str | route of `subject_id` (R1–R4, see Routes) |
| `subject_key` | str | resolved subject id (study-scoped) |
| `role` | str | infant · mother · other · unknown · adult · child (see Sample unit section) |
| `t_index` | float64 | timepoint index within subject |
| `n_timepoints_subject` | float64 | timepoints of the subject |
| `linked_infant_subject_key` | str | infant subject a mother/other sample is linked to |
| `run_accessions` | str | `;`-joined run accessions of the sample |
| `n_runs` | float64 | number of runs |
| `instrument_model` | str | instrument model (top) |
| `library_layout` | str | PAIRED/SINGLE |
| `read_count_total` | float64 | summed read_count over runs |
| `study_title` | str | BioProject title |
| `cohort_id` | str | cohort id (cohorts.csv) |
| `cohort_name` | str | cohort name |
| `first_public_min` | str | earliest first_public of the runs |
| `adult_age_flag` | bool | True when a committed age is > 1,100 d (see `age_scope = adult_flagged`) |
| `n_fields_with_value` | int64 | count of the 19 fields with a value |
| `ena_sample_url` | str | ENA browser link |
| `ena_study_url` | str | ENA study link |
| `sample_unit` | str | `biosample` or `run` (see Sample unit section) |
| `parent_biosample` | str | parent BioSample of a `run` unit |
| `biosample_accession` | str | BioSample accession (always populated) |
| `run_accession` | str | run accession for `run` units, else null |
| `age_scope` | str | age-scope class (see Sample unit section). Footnote: `adult_flagged` means "over-age flagged" — any committed age > 1,100 d, which includes children aged 3–18 y (`role = child`), not only adults |
| `age_scope_basis` | str | evidence behind age_scope |
| `role_source` | str | evidence behind role |
| `sp_profiled` | bool | True when at least one run has a Sandpiper profile |
| `sandpiper_url` | str | link to the representative profiled run: RANDOM-selection run with the highest root coverage (v1.2.1; v1.2.0 used the alphabetically first run) |
| `sp_n_runs_total` | int64 | runs of the sample (populated for every row since v1.2.1) |
| `sp_n_runs_profiled` | int64 | profiled runs (0 when unprofiled) |
| `sp_partial` | bool | True when 0 < profiled runs < total runs; False for unprofiled rows |
| `sp_spf` | float64 | prokaryotic read fraction (%) |
| `sp_known_species_fraction` | float64 | % of prokaryotic coverage assigned to a named GTDB species |
| `sp_root_coverage` | float64 | summed root coverage (genome equivalents) |
| `sp_low_depth` | boolean | root coverage < 2 (excluded from panels/medians) |
| `sp_top_genus` | str | most abundant genus bin |
| `sp_top_genus_ra` | float64 | its relative abundance |
| `sp_ra_g_Bifidobacterium` | float64 | GTDB g__Bifidobacterium (no suffix genera in R232) |
| `sp_ra_f_Bacteroidaceae` | float64 | GTDB f__Bacteroidaceae |
| `sp_ra_g_Bacteroides` | float64 | GTDB g__Bacteroides + suffix genera Bacteroides_D/_E/_F |
| `sp_ra_g_Phocaeicola` | float64 | GTDB g__Phocaeicola + Phocaeicola_A |
| `sp_ra_enterobacterales_core` | float64 | Σ of GTDB Escherichia, Klebsiella, Enterobacter(+_B,_D), Citrobacter(+_A,_B,_C,_D), Salmonella, Serratia(+_B,_G) |
| `sp_ra_g_Escherichia` | float64 | GTDB g__Escherichia (includes NCBI Shigella) |
| `sp_ra_g_Klebsiella` | float64 | GTDB g__Klebsiella |
| `sp_ra_f_Lachnospiraceae` | float64 | GTDB f__Lachnospiraceae |
| `sp_ra_f_Lactobacillaceae` | float64 | GTDB f__Lactobacillaceae (GTDB splits NCBI Lactobacillus into many genera) |
| `sp_ra_g_Streptococcus` | float64 | GTDB g__Streptococcus |
| `sp_ra_g_Staphylococcus` | float64 | GTDB g__Staphylococcus |
| `sp_ra_g_Enterococcus` | float64 | GTDB g__Enterococcus (= E. faecalis group) + Enterococcus_A (avium/raffinosus/gilvus) + _B (faecium/lactis/hirae/durans) + _C … _L |
| `sp_ra_g_Veillonella` | float64 | GTDB g__Veillonella + Veillonella_A |
| `sp_ra_g_Clostridioides` | float64 | GTDB g__Clostridioides (C. difficile) |
| `sp_ra_unassigned_genus` | float64 | coverage not resolved to a genus |
| `sp_shannon_genus` | float64 | Shannon (ln) over genus bins incl. unassigned |
| `sp_n_genera_ge1pct` | float64 | named genera ≥ 1 % |
| `sp_flag_low_complexity` | boolean | see sandpiper_flag_table.csv |
| `sp_flag_non_metagenome` | bool | see sandpiper_flag_table.csv (derived from sp_nonmeta_class since v1.2.1) |
| `sp_flag_synthetic` | boolean | see sandpiper_flag_table.csv |
| `sp_flag_rna` | boolean | see sandpiper_flag_table.csv |
| `sp_flag_readfraction_warning` | boolean | see sandpiper_flag_table.csv |
| `sp_run_concordance_bc` | float64 | max pairwise genus Bray–Curtis between runs |
| `sp_runs_discordant` | boolean | BC > 0.5 |
| `taxonomy_db` | str | GTDB |
| `taxonomy_version` | str | R232 |
| `sandpiper_version` | str | 2.0.0 (Zenodo snapshot 20419175; the live Sandpiper site may show a newer version) |
| `age_at_collection_days__scope` | string | scope of the winning `age_at_collection_days` determination: sample / subject / biosample / group |
| `antibiotic_exposure__scope` | string | scope of the winning `antibiotic_exposure` determination: sample / subject / biosample / group |
| `birth_weight_grams__scope` | string | scope of the winning `birth_weight_grams` determination: sample / subject / biosample / group |
| `country__scope` | string | scope of the winning `country` determination: sample / subject / biosample / group |
| `delivery_mode__scope` | string | scope of the winning `delivery_mode` determination: sample / subject / biosample / group |
| `feeding_mode__scope` | string | scope of the winning `feeding_mode` determination: sample / subject / biosample / group |
| `geo_subregion__scope` | string | scope of the winning `geo_subregion` determination: sample / subject / biosample / group |
| `gestational_age_weeks__scope` | string | scope of the winning `gestational_age_weeks` determination: sample / subject / biosample / group |
| `health_condition__scope` | string | scope of the winning `health_condition` determination: sample / subject / biosample / group |
| `hmo_supplementation__scope` | string | scope of the winning `hmo_supplementation` determination: sample / subject / biosample / group |
| `maternal_antibiotics__scope` | string | scope of the winning `maternal_antibiotics` determination: sample / subject / biosample / group |
| `multiple_birth__scope` | string | scope of the winning `multiple_birth` determination: sample / subject / biosample / group |
| `nec_status__scope` | string | scope of the winning `nec_status` determination: sample / subject / biosample / group |
| `preterm_status__scope` | string | scope of the winning `preterm_status` determination: sample / subject / biosample / group |
| `probiotic_exposure__scope` | string | scope of the winning `probiotic_exposure` determination: sample / subject / biosample / group |
| `sibling_in_study__scope` | string | scope of the winning `sibling_in_study` determination: sample / subject / biosample / group |
| `exclusion_reason_code` | object | controlled reason code when a deterministic sample rule set `body_site_class = excluded` in v1.2.1: `host_nonhuman` (ENA scientific_name/tax_id is a non-human animal) or `assay_isolate_genome` (all runs GENOMIC with a named microbe organism); null otherwise. Rows are logged in value_history (change_stage `auditor_review:R3-3`) |
| `sp_nonmeta_class` | str | any-run organism-label class over ALL runs of the sample (see sandpiper_flag_table.csv): not_flagged · named_human_host · named_nonhuman_host · named_microbe_or_other |
| `catalog_scope` | bool | **headline scope (v1.2.1)** = `age_scope ∈ {infant_evidenced, study_all_infant}` AND `body_site_class ∈ {primary, unknown}`; the intersection used for headline counts (71,795 rows) and for Sandpiper study panels/medians |
| `panel_scope` | bool | True when the row enters its study's Sandpiper panel/medians: `catalog_scope AND sp_profiled AND NOT sp_low_depth` |

### study_metadata_wide.parquet (120 columns)
| column | dtype | description |
|---|---|---|
| `study_accession` | str | BioProject accession (key) |
| `triage_verdict` | str | include/exclude/uncertain from the infant triage cascade. INFANT-CATALOG verdict (historical, 2026-09 infant screen) — NOT membership in the all-age catalog: a study can be `excluded` here (e.g. age_adult_only) and still be in `gut_studies`. Catalog membership = presence in `gut_studies.parquet`. |
| `outcome` | str | contract outcome of the final verdict row |
| `confidence` | float64 | confidence of the final verdict |
| `reason_code` | str | controlled exclusion reason of the INFANT screen (null for included); age_* codes are infant-age reasons, not catalog exclusions |
| `evidence` | str | JSON evidence list of the final verdict |
| `model` | str | model/stage that produced the final verdict |
| `decision_stage` | str | stage table of the final verdict |
| `note` | str | free-text note of the final verdict |
| `n_infant_samples_est` | float64 | triage estimate of infant samples |
| `body_site_call` | str | triage body-site call for the study |
| `slot` | str | verdict slot name |
| `validator_ok` | object | validator result on the verdict row |
| `validator_msg` | str | validator message |
| `study_title` | str | BioProject title |
| `n_samples` | int64 | from runs.parquet: distinct BioSamples with runs registered under this study (runs for run-unit studies). Sums to 154,268 over studies because 62 BioSamples carry runs from two BioProjects; use `n_sample_rows` for rows in the sample table |
| `n_runs` | int64 | runs registered under the study |
| `first_public_min` | str | earliest first_public |
| `sig_infant_hit` | bool | enumeration infant signal hit |
| `sig_infant_title` | bool | infant term in title |
| `screen_haiku` | str | Haiku screen label (yes/no) |
| `linked_infant_paper` | bool | a linked paper is infant-scoped |
| `universe_slice` | str | enumeration channel |
| `catalog_status` | str | included / excluded / human_review in the INFANT catalog. INFANT-CATALOG verdict (historical, 2026-09 infant screen) — NOT membership in the all-age catalog: a study can be `excluded` here (e.g. age_adult_only) and still be in `gut_studies`. Catalog membership = presence in `gut_studies.parquet`. |
| `assessed_downstream` | bool | reached the extraction stage |
| `recov_age` | str | recoverability tier for age (R0–R4) |
| `recov_antibiotics` | str | recoverability tier for antibiotics (R0–R4) |
| `recov_delivery` | str | recoverability tier for delivery (R0–R4) |
| `recov_feeding` | str | recoverability tier for feeding (R0–R4) |
| `recov_preterm` | str | recoverability tier for preterm (R0–R4) |
| `recov_probiotic` | str | recoverability tier for probiotic (R0–R4) |
| `cohort_id` | str | cohort id |
| `cohort_name` | str | cohort name |
| `cohort_crosswalk_names` | str | alternative cohort names |
| `n_biosample_infant_flagged` | int64 | BioSamples flagged infant by archive attributes at triage |
| `n_linked_papers` | int64 | linked papers |
| `linked_pmids` | str | `;`-joined PMIDs |
| `median_gbp` | float64 | median bases per run (Gbp) |
| `median_read_len` | float64 | median nominal read length |
| `paired_share` | float64 | fraction of PAIRED runs |
| `instrument_model_top` | str | most frequent instrument model |
| `generation_top` | str | sequencer generation of instrument_model_top |
| `timepoint_class` | str | cross_sectional / longitudinal heuristic from sample titles |
| `country_top` | str | most frequent country |
| `worklist_rank` | float64 | curation priority rank |
| `expected_field_yield` | float64 | predicted number of recoverable fields |
| `effort_class` | str | curation effort class |
| `controlled_access` | object | True when the study is controlled-access (dbGaP/EGA) |
| `n_biosamples` | int64 | BioSample-unit rows of the study in the sample table (v1.2.1: parents of run units no longer counted here; see n_run_units, n_parent_biosamples) |
| `sample_unit` | str | dominant sample unit of the study: biosample / run |
| `cov_probiotic_exposure` | float64 | fraction of catalog-scope-eligible rows (n_infant_scope_samples denominator) with a `probiotic_exposure` value |
| `cov_preterm_status` | float64 | fraction of catalog-scope-eligible rows (n_infant_scope_samples denominator) with a `preterm_status` value |
| `cov_gestational_age_weeks` | float64 | fraction of catalog-scope-eligible rows (n_infant_scope_samples denominator) with a `gestational_age_weeks` value |
| `cov_delivery_mode` | float64 | fraction of catalog-scope-eligible rows (n_infant_scope_samples denominator) with a `delivery_mode` value |
| `cov_feeding_mode` | float64 | fraction of catalog-scope-eligible rows (n_infant_scope_samples denominator) with a `feeding_mode` value |
| `cov_antibiotic_exposure` | float64 | fraction of catalog-scope-eligible rows (n_infant_scope_samples denominator) with a `antibiotic_exposure` value |
| `cov_age_at_collection_days` | float64 | fraction of catalog-scope-eligible rows (n_infant_scope_samples denominator) with a `age_at_collection_days` value |
| `cov_birth_weight_grams` | float64 | fraction of catalog-scope-eligible rows (n_infant_scope_samples denominator) with a `birth_weight_grams` value |
| `cov_country` | float64 | fraction of catalog-scope-eligible rows (n_infant_scope_samples denominator) with a `country` value |
| `cov_maternal_antibiotics` | float64 | fraction of catalog-scope-eligible rows (n_infant_scope_samples denominator) with a `maternal_antibiotics` value |
| `cov_hmo_supplementation` | float64 | fraction of catalog-scope-eligible rows (n_infant_scope_samples denominator) with a `hmo_supplementation` value |
| `cov_nec_status` | float64 | fraction of catalog-scope-eligible rows (n_infant_scope_samples denominator) with a `nec_status` value |
| `cov_health_condition` | float64 | fraction of catalog-scope-eligible rows (n_infant_scope_samples denominator) with a `health_condition` value |
| `cov_multiple_birth` | float64 | fraction of catalog-scope-eligible rows (n_infant_scope_samples denominator) with a `multiple_birth` value |
| `cov_sibling_in_study` | float64 | fraction of catalog-scope-eligible rows (n_infant_scope_samples denominator) with a `sibling_in_study` value |
| `cov_geo_subregion` | float64 | fraction of catalog-scope-eligible rows (n_infant_scope_samples denominator) with a `geo_subregion` value |
| `n_unique_infants_est` | float64 | distinct infant subjects (see Sample unit section) |
| `max_timepoints` | float64 | max timepoints per infant subject |
| `longitudinal` | bool | True when max_timepoints > 1 |
| `n_mothers` | int64 | mother-role subjects |
| `n_infant_scope_samples` | int64 | **body-site scope** count: rows with body_site_class ∈ {primary, unknown} (name kept for compatibility; not age-evidenced) |
| `ena_url` | str | ENA link |
| `ncbi_url` | str | NCBI link |
| `n_age_scope_infant` | int64 | rows with age_scope ∈ {infant_evidenced, study_all_infant} |
| `n_adult_flagged` | int64 | rows with age_scope = adult_flagged (over-age, includes children) |
| `n_age_unknown_mixed_study` | int64 | rows age_unknown_mixed_study |
| `n_age_unknown_no_study_estimate` | int64 | rows age_unknown_no_study_estimate |
| `n_infant_evidenced` | int64 | rows infant_evidenced |
| `n_non_infant_role` | int64 | rows non_infant_role |
| `n_study_all_infant` | int64 | rows study_all_infant |
| `mixed_age_deposit` | bool | > 50 % of rows are age_unknown_mixed_study or adult_flagged |
| `n_infant_role_samples` | int64 | rows with role = infant |
| `n_infant_samples_with_subject` | int64 | infant rows with a resolved subject |
| `n_unique_infants_source` | str | source class of the unique-infant estimate |
| `n_unique_infants_upper` | float64 | upper bound |
| `n_unique_infants_display` | str | string to render |
| `n_sample_rows` | int64 | rows of the sample table keyed to this study |
| `n_biosamples_shared_with_other_study` | int64 | BioSamples of this study also registered under another BioProject |
| `sp_n_samples_profiled` | int64 | sample rows with a profile |
| `sp_frac_samples_profiled` | float64 | sp_n_samples_profiled / n_sample_rows (single denominator since v1.2.1) |
| `sp_median_bifidobacterium_ra` | float64 | median sp_ra_g_Bifidobacterium over panel-scope rows (catalog_scope ∧ profiled ∧ NOT low depth); null when the study has none |
| `sp_median_shannon_genus` | float64 | median Shannon over panel-scope rows |
| `sp_n_runs_profiled` | int64 | profiled runs registered under this study |
| `sp_frac_runs_profiled` | float64 | sp_n_runs_profiled / n_runs |
| `sp_coverage_class` | str | none (no profiled rows) · partial · full (every row profiled); defined on sample rows since v1.2.1 |
| `sp_frac_runs_flagged` | float64 | see sandpiper_flag_table.csv |
| `sp_frac_low_complexity_profiled` | float64 | see sandpiper_flag_table.csv |
| `sp_frac_low_depth_profiled` | float64 | see sandpiper_flag_table.csv |
| `sp_median_spf` | float64 | median prokaryotic read fraction over panel-scope rows |
| `sp_median_known_species_fraction` | float64 | median known-species fraction over panel-scope rows |
| `sp_auditor_attention` | bool | see sandpiper_flag_table.csv |
| `sp_auditor_reason` | str | reason text for sp_auditor_attention |
| `first_author` | str | first author of the primary linked paper |
| `last_author` | str | last author |
| `n_authors` | int64 | distinct author keys over linked papers |
| `n_author_papers` | int64 | papers contributing authors |
| `organisations` | str | `;`-joined organisation names (v1.2.1: submission ids, e-mail addresses, placeholders and single lowercase tokens removed; NCBI BioProject Organization preferred over ENA center_name for PRJNA studies) |
| `sp_median_scope` | str | scope label of the medians (same vocabulary as sp_panel_scope) |
| `sp_n_samples_panel` | int64 | rows in the panel scope |
| `sp_panel_scope` | str | `catalog_scope_adequate_depth` (panel shown) · `fallback:no_catalog_scope_samples` · `fallback:no_age_evidenced_profiled_samples` · `fallback:all_age_evidenced_profiled_samples_low_depth` · `no_profiled_samples` — render the fallback text instead of a panel |
| `sp_coverage_note` | str | set when rows are profiled only through runs registered under a sibling BioProject |
| `n_catalog_scope` | int64 | rows with catalog_scope = True (age scope ∧ body-site scope) — the headline denominator |
| `n_excluded_host_or_isolate` | int64 | rows set to body_site_class = excluded by the v1.2.1 host/isolate rule (exclusion_reason_code not null) |
| `n_body_site_excluded` | int64 | rows with body_site_class = excluded |
| `n_run_units` | int64 | run-unit rows (one BioSample per infant, one run per stool) |
| `n_parent_biosamples` | int64 | parent BioSamples of the run units (listed in parent_biosamples.parquet, counted nowhere else) |
| `shared_biosample_note` | object | human-readable note for the five studies sharing BioSamples (F6) |
| `release_added` | string | release in which this row first became visible (`R<YYYY>.<n>` or a pre-numbered package semver `1.0.0`–`1.2.2`); pre-1.3.0 values are reconstructed — see DATA_DICTIONARY 'Release columns' |
| `release_retired` | string | release that replaced/removed the row; null (empty in CSV) = current row |
| `package_added` | string | semver of the data package in which the row first appeared |
