# DATA_MODEL_FIX_REPORT — data package v1.2 (release v12), 2026-09-26

Implements Reviewer B findings B1, B2, B3, B8, B9, B10, B14, B15 deterministically (no LLM calls; 0 tokens). Inputs: data_package_v1(.1) tables, the v11 release bundle, the superseded/rejected/dropped/adult-age/r1_rejected tables, human-review rounds, the 56 triage-stage tables in the artifact store, `gold_extract.parquet` (B8 precision table only). Every new determination row passed `curation_kernel_ext.validate_row`.

## Before → after
| Item | v1.1 | v1.2 | Change |
|---|---:|---:|---|
| sample table rows | 154,222 | 154,206 | 16 `biosample_pooled` parents → `parent_biosamples.parquet` [B2] |
| determinations | 609,584 | 618,898 | −97 parent-row determinations, −23 superseded in-scope ages, −125 superseded title-parser ages, +9,559 out-of-scope adult ages [B9] |
| `adult_age_flag = True` | 9,567 | 9,559 | now ≡ `age_at_collection_days > 1100`, every flag backed by a determination row (`parse_note out_of_scope_adult`) [B9]; 8 flags withdrawn (PRJEB14941 `.infant.` samples carrying the pair's maternal age) |
| flagged samples with an in-scope age too | 12 | 0 | conflicts resolved for the archive attribute; losers in `value_history` |
| flagged samples with `role = infant` | 7,397 | 0 | roles `adult` (5,877) / `child` (1,512) |
| `role = infant` | 138,363 | 72,848 | unevidenced body-site defaults → `unknown` (65,813) [B1] |
| studies with `n_unique_infants_est` 0 or = n_samples | 225 | 0 | NaN + `n_unique_infants_source` / `_display` [B10] |
| always-populated `biosample_accession`, nullable `run_accession` | – | yes | [B15] |
| `study_verdict_history.parquet` | – | 32,231 rows / 9,767 studies / 56 stage tables | [B3] |
| `value_history.parquet` | – | 45,591 rows | superseded 223 · rejected 19,012 · dropped 17,165 · recommitted_out_of_scope_adult 8,353 · not_committed_duplicate 738 · moved_to_parent_biosamples 97 · auditor_finding_applied 3 [B3] |
| `confidence_tiers.csv` / `tier_field_precision.csv` | – | 29 tiers / 49 rows | [B8] |

## B1 — age_scope (sample level)
* `infant_evidenced`: 67,598
* `age_unknown_no_study_estimate`: 49,556
* `age_unknown_mixed_study`: 14,410
* `adult_flagged`: 9,559
* `study_all_infant`: 7,097
* `non_infant_role`: 5,986

Rule (precedence order): `adult_flagged` (committed age > 1,100 d) > `non_infant_role` (mother/other from `attr_role`, `sample_name_token`, `body_site_raw`, `body_site_class=linked`) > `infant_evidenced` (age ≤ 1,100 d, `preterm_status` or `gestational_age_weeks` on the sample — 53,669 / 13,110 / 112 — or on another sample of the same resolved subject, 707) > `study_all_infant` (triage `n_infant_samples_est ≥ 0.9 × n_infant_scope_samples`; 66 studies) > `age_unknown_mixed_study` (estimate < 0.9; the 0.9–0.99 band is empty so 0.5 and 0.9 give the same partition) > `age_unknown_no_study_estimate` (253 studies where triage gave no numeric estimate — TEDDY, MOMmy, Codiversification, HMP2-IBD… — labelled unknown, **not** all-infant, because their triage quotes describe mixed cohorts; this sixth value and `non_infant_role` extend Reviewer B's four-value list).
Headline coverage now uses `age_scope ∈ {infant_evidenced, study_all_infant}` (n = 74,695): age ≤ 1,100 d on 53,669 (71.9%), preterm 32,426 (43.4%), delivery 23,473 (31.4%), antibiotics 13,027 (17.4%), feeding 11,842 (15.9%). Exposure values on adults: probiotic 3,534 of 8,989, antibiotics 4,970 of 22,718.
Mixed-age deposits (`mixed_age_deposit`, > 50 % of rows mixed/adult): 61 studies, 23,403 rows. Largest:
| study_accession   | study_title                                                                                                    |   n_sample_rows |   n_infant_evidenced |   n_age_unknown_mixed_study |   n_adult_flagged |   n_infant_samples_est |
|:------------------|:---------------------------------------------------------------------------------------------------------------|----------------:|---------------------:|----------------------------:|------------------:|-----------------------:|
| PRJNA1468137      | Maternal influences on infant gut microbiome and health                                                        |            4749 |                    0 |                        4577 |                 0 |                   2200 |
| PRJEB11419        | American Gut Project                                                                                           |            7024 |                   79 |                        2362 |              4583 |                     39 |
| PRJNA1257550      | Role of gut microbiota in eczema development                                                                   |            1737 |                    0 |                        1737 |                 0 |                   1000 |
| PRJNA695570       | Human gut metagenomes of healthy mothers and their children                                                    |            1767 |                  763 |                         536 |               468 |                    600 |
| PRJNA1356541      | Metagenomic strain tracking among children in urban and rural communities in low- and middle-income countries. |             511 |                    0 |                         511 |                 0 |                    150 |
| PRJNA1081952      | BRISC microbiome sub study                                                                                     |            1422 |                  940 |                         482 |                 0 |                   1000 |
| PRJNA1274946      | Siblings are Reservoirs for Microbial Metabolic Processes that Influence Allergy Development                   |             353 |                    0 |                         353 |                 0 |                    150 |
| PRJNA1273620      | Maternal-Prenatal Signature Underlying Offspring Atopic Dermatitis Susceptibility                              |             325 |                    0 |                         325 |                 0 |                    150 |

## B9 — adult_age_flag rule and the 12 contradictions
All 10,305 rows of `adult_age_rows.parquet` (R1 `r1_unit_resolution_v2`, 9,567 samples) were re-validated: `validate_row` ok for all; `validate_age(value_normalized,'days')` returned `age N days outside 0-1100` for all (that message is now in `parse_note`). One row per sample committed (738 second attributes → `not_committed_duplicate`). Investigation of the 12 contradictions showed **all 12 are maternal samples or maternal ages**: PRJEB14941 is a pregnancy cohort ("microbiota at multiple body sites during pregnancy…", Tanzania) whose `age` attribute is the mother's age and whose `dob` attribute is the infant's date of birth attached to every sample of the pair — the 5 (+11 outside infant scope) date-arithmetic ages 0–126 d sit on maternal visit samples (titles without the `.infant.` token); PRJNA716780's six `CTRM*` mother samples had a supplementary-table age of 30–36 read as months; PRJNA695570 SAMN17618835 has host_age 1388 d (R1) vs a pooled-table 1095 d (R2), R1 wins by precedence. Two further deterministic corrections in PRJEB14941 followed from the same evidence (declared beyond the reviewer's list): 125 title-parser `birth → 0 d` rows on maternal birth-visit samples superseded; 8 out-of-range ages on `.infant.` samples rejected because the value equals the mother's `age` on the same pair code. **Open auditor item:** PRJEB14941 still has 67 `.infant.` title-parser ages (`birth → 0 d`, plausible) and 62 date-arithmetic ages on `.infant.` samples (plausible); the 751 former `role = infant` rows of this study are now mostly `unknown`.

## B10 — n_unique_infants_source
| n_unique_infants_source   |   studies |
|:--------------------------|----------:|
| no_infant_role_samples    |       152 |
| no_subject_evidence       |        97 |
| subject_ids               |        75 |
| subject_ids_all_distinct  |        37 |
| subject_ids_partial       |        28 |

## B2 / B15 / B14 — counts reconciled from the tables
* Sample table 154,206 rows = 153,701 distinct BioSamples (153,685 BioSample units + 521 run units under 16 parents). Site index must use this table's length (154,206), not 154,222.
* Per-study `n_samples`/`n_runs` now come from `runs.parquet` (sum 154,268 / 174,022); 62 BioSamples carry runs from two BioProjects, so the per-study sum exceeds the sample-table row count by 62; `n_sample_rows` gives the table count. Only PRJEB49206 changed vs v1.1 (419/457 → 439/477; runs.parquet is the shipped truth).
* Age covered: 54,105 body-site infant-scope samples with age ≤ 1,100 d (v1.1 README 53,802; site 54,150 — the site counted the 16 parent rows and rows since superseded, the README predates the run-unit fix). Age-scope figure: 53,669.
* Gold recall: 22 cMD BioProjects, 18 included, of which 1 by the human-review pass → 17/22 automated, 18/22 final. Both numbers are now stated in the README.
* Multi-run: 57 class-C + 6 class-A studies in `sample_unit_classification_by_study.csv`; the 61 − 57 = 4 studies with multi-run BioSamples in the sample table (PRJNA1019702, PRJNA1198101, PRJNA613032, PRJNA613054) are cross-study BioSamples (runs from a sibling BioProject on the same BioSample), added as class `X_cross_study_biosample`; the dictionary's "33 technical" count is withdrawn.
* hires vs full gold: `extraction_gold_eval_hires.csv` (pipeline evaluator; 3,498 hi-res rows) remains the headline; `tier_field_precision.csv` re-derives both sets with an explicit, documented value mapping and gives (hires / all):
| field                  |   ('n_covered', 'all') |   ('n_covered', 'hires') |   ('precision', 'all') |   ('precision', 'hires') |   ('recall', 'all') |   ('recall', 'hires') |
|:-----------------------|-----------------------:|-------------------------:|-----------------------:|-------------------------:|--------------------:|----------------------:|
| age_at_collection_days |                   3370 |                     3236 |                  0.971 |                    0.989 |               0.93  |                 0.948 |
| antibiotic_exposure    |                   2414 |                     2395 |                  0.868 |                    0.867 |               0.792 |                 0.819 |
| delivery_mode          |                   2445 |                     2426 |                  0.995 |                    0.995 |               0.907 |                 0.932 |
| feeding_mode           |                    525 |                      520 |                  0.796 |                    0.804 |               0.577 |                 0.592 |
| gestational_age_weeks  |                    551 |                      551 |                  1     |                    1     |               0.272 |                 0.272 |
| preterm_status         |                   1950 |                     1949 |                  1     |                    1     |               0.961 |                 0.961 |
  The re-derived hires set has 3,530 rows (integer-year `age_years` rule applied to gold rows without `infant_age_days`) vs the evaluator's 3,498, and feeding precision differs (0.80 vs 0.89) because the evaluator's feeding mapping is not in the package; treat the tier table as internally consistent but not identical to the shipped evaluator — **provisional until the evaluator code is added to the repo**.

## B8 — confidence tiers
`confidence_tiers.csv`: 29 route × determiner × scope tiers; discrete values per tier. Precision by tier (hires, age): R1 0.90 → 1.000 (n=194), R1 0.85 (date arithmetic / subject propagation) → 0.700 (n=70), R1 0.75 → 1.000 (n=763), R2 0.85 → 1.000 (n=1,519), R2 0.75 → 0.980 (n=683); feeding R2 0.75 → 0.480 (n=152) vs R2 0.85 → 0.938 (n=368); R3 preterm 0.80 → 1.000 (n=193). No gold for R4 or exposure fields. Dictionary paragraph added.

## B3 — histories
`study_verdict_history.parquet`: 32,231 rows from 56 tables (haiku screen 3,978; Sonnet rubric tier-1 960; Sonnet confirm parts/replicates; Opus adjudication 00/01/linked/rescue/gap/grow/w2/w3/ff/nm/loose/r3/gsa; growth_triage merged outcomes; BioSample re-judge 13; human review 157 + 60; triage_v2 consolidated 9,579; final 9,579). 138 of 389 included studies carry ≥ 2 distinct stage verdicts (include/exclude/uncertain) — the site's "Decision history" panel has content for them. `value_history.parquet`: 45,591 rows (statuses above); r1_rejected rows that v1.2 committed are marked `recommitted_out_of_scope_adult` with `replaced_by` pointing at the current row.

## Checks (all pass)
{
 "wide_rows": 154206,
 "subj_rows": 154206,
 "no_pooled_in_wide": true,
 "wide_vs_det_mismatches": {
  "probiotic_exposure": 0,
  "preterm_status": 0,
  "gestational_age_weeks": 0,
  "delivery_mode": 0,
  "feeding_mode": 0,
  "antibiotic_exposure": 0,
  "age_at_collection_days": 0,
  "birth_weight_grams": 0,
  "country": 0,
  "maternal_antibiotics": 0,
  "hmo_supplementation": 0,
  "nec_status": 0,
  "health_condition": 0,
  "multiple_birth": 0,
  "sibling_in_study": 0,
  "geo_subregion": 0,
  "sex": 0,
  "timepoint_label": 0,
  "subject_id": 0
 },
 "adult_flag_equals_age_gt_1100": true,
 "adult_flag_rows_have_out_of_scope_note": true,
 "infant_role_all_evidenced": true,
 "role_infant_unevidenced": 0,
 "age_scope_infant_has_no_adult": 0,
 "biosample_accession_all_SAM": true,
 "run_accession_only_on_run_units": true,
 "det_unique_sample_field": true,
 "det_keys_in_wide": true,
 "study_n_sample_rows_sum": 154206,
 "study_n_samples_sum": 154268,
 "study_n_runs_sum": 174022,
 "n_unique_infants_est_zero_or_sentinel": 0,
 "value_history_rows": 45591,
 "study_verdict_history_rows": 32231,
 "history_covers_all_catalog_studies": true,
 "non_age_det_rows_identical": true,
 "new_adult_rows_validate_row_ok": 9559
}

## Not done / declared deviations
* age_scope vocabulary extended by two values (`age_unknown_no_study_estimate`, `non_infant_role`).
* Two PRJEB14941 corrections beyond the listed findings (125 superseded title-parser rows, 8 rejected adult rows), both from sample_id_pattern evidence.
* Site templates/generator not touched (out of this track); the package now supplies `n_unique_infants_display`, `mixed_age_deposit`, `age_scope`, `parent_biosamples`, and the two history tables for the site track.
* Reviewer B's cohort-clustering re-adjudication (B10 second part, ~50k tokens) not done — LLM work excluded from this deterministic track.
* Gold precision tiers are a re-derivation with a documented mapping; the shipped evaluator CSV is unchanged.
