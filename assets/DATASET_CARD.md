# Microbiome Repo — analysis-ready exports, release R2026.44 (data package 1.35.0)

Built by `build_exports.py` on 2026-11-25 from the release tables. These files are release downloads; the website does not read them.

## What is in the samples × genus table

- **Samples:** 379,776 samples from 2,905 projects (2,869 project groups, 282,689 persons). A sample is included when it is in the current release, is a specimen, has a taxonomic profile and carries none of the seven quality flags (`qc_low_depth`, `qc_non_metagenome`, `qc_synthetic`, `qc_rna`, `qc_predicted_ecological`, `qc_no_genus_assigned`, `qc_low_complexity`).
- **Genera:** 7,083 GTDB genera; 23,682,153 non-zero cells. Values are relative abundances (fractions of the profile); the unassigned fraction is left out, so a sample's values sum to at most 1.
- **Layout:** the table is stored as sparse triplets, because 379,776 × 7,083 cells are almost all zero. `ml_samples` is the row axis, `ml_genera` the column axis, `ml_genus_abundance_long` the non-zero cells.

```python
import pandas as pd, scipy.sparse as sp
s = pd.read_parquet('ml_samples_v1.35.0.parquet')
g = pd.read_parquet('ml_genera_v1.35.0.parquet')
t = pd.read_parquet('ml_genus_abundance_long_v1.35.0.parquet')
X = sp.csr_matrix((t.relabund, (t.sample_index, t.genus_index)), shape=(len(s), len(g)))
train, test = s.fold_5 != 0, s.fold_5 == 0      # row i of X is row i of s
```

| Body site | Samples | Projects |
|---|---|---|
| gut_stool | 304,299 | 1,996 |
| (not recorded) | 23,501 | 181 |
| oral | 15,104 | 230 |
| skin | 10,697 | 98 |
| unknown_site | 7,366 | 67 |
| vaginal_urogenital | 5,122 | 85 |
| respiratory_lower | 4,269 | 151 |
| nasal_nasopharyngeal | 4,052 | 74 |
| blood_tissue | 2,466 | 120 |
| other_site | 1,898 | 61 |
| eye_ear | 650 | 23 |
| multi_site | 327 | 6 |
| milk | 25 | 5 |

## Labels

Labels are the release's curated sample fields, unchanged. A missing value means not recorded, not negative.

| Column | Samples with a value | Share |
|---|---|---|
| `body_site_code` | 356,275 | 93.8 % |
| `body_site_class` | 379,776 | 100.0 % |
| `age_category` | 379,776 | 100.0 % |
| `age_at_collection_days` | 99,127 | 26.1 % |
| `sex` | 116,480 | 30.7 % |
| `bmi` | 33,460 | 8.8 % |
| `country` | 356,663 | 93.9 % |
| `lifestyle` | 13,679 | 3.6 % |
| `health_condition` | 189,147 | 49.8 % |
| `comparison_group` | 36,152 | 9.5 % |
| `antibiotic_exposure` | 67,129 | 17.7 % |
| `collection_year` | 310,033 | 81.6 % |

Other columns: `sample_key`, `study_accession`, `project_group`, `person_key`, `person_id`, `root_coverage_sum` (profile depth), `richness_genus`, `shannon_genus`, `n_genera_detected`.

## Fixed leave-project-out splits

- `project_group` joins projects that share participants (the release's study group); otherwise it is the project accession. Use it as the group for leave-one-project-out validation.
- `fold_5` and `fold_10` put every project group into one fold: the first 8 hex digits of `md5(project_group)`, as an integer, modulo 5 or 10. A project group keeps its fold in every later release. Folds are not balanced in size.
- A person can have several samples. `person_key` is the project's own subject label and is unique only within a project; `person_id` (project accession, `|`, `person_key`) identifies a person across the release. For one sample per person, keep the first row per `person_id`.

| fold_5 | Samples | Project groups | Projects |
|---|---|---|---|
| 0 | 67,234 | 538 | 551 |
| 1 | 80,686 | 597 | 602 |
| 2 | 62,283 | 564 | 568 |
| 3 | 92,368 | 607 | 612 |
| 4 | 77,205 | 563 | 572 |

## Taxon × body site × condition prevalence

`taxon_prevalence_body_site_condition_v1.35.0.parquet`: one row per taxon, body site and condition in which the taxon was detected at least once.

- genus: 118,303 rows, 7,083 taxa.
- species: 350,879 rows, 23,122 taxa.
- `health_condition` is the release's condition field (29 values); `(not recorded)` = no condition recorded; `(all)` = all samples of the body site.
- `prevalence` = `n_detected` / `n_samples`, counted over samples, not persons. `n_projects_detected` shows how many projects carry the detections.
- A taxon without a row for a body site and condition was not detected there; the denominators are in `prevalence_denominators_v1.35.0.csv`.
- The same samples as the genus table. Conditions are as recorded by the source projects; a condition group is not a matched case group.

## Replication results as signature rows

Source: the pre-registered replication run of 2026-11-15 on data package 1.34.0 (release R2026.41).

- `replication_signature_taxa_v1.35.0.csv`: 6,933 registered claims (one taxon of one BugSigDB signature) from 367 papers.
- `replication_signatures_v1.35.0.csv`: the same results as 1,074 signature rows with a count per result.
- Every row carries `bsdb_id` and `bugsigdb_url` (the signature's page at BugSigDB), `pmid` and `doi`. Rows without a source link: 0.

| Result | Claims |
|---|---|
| not tested: fewer than three projects for the condition | 3,625 |
| not tested: taxon too rare in the projects | 1,390 |
| not replicated: effect of meaningful size excluded | 738 |
| not replicated: inconclusive | 634 |
| replicated | 495 |
| not replicated: opposite direction | 31 |
| replicated, effect smaller than the smallest effect of interest | 20 |

## Limits

- Profiles come from one profiler and one reference taxonomy; names are GTDB names.
- Detection depends on the profile tables: 0.00 % of species rows and 31.67 % of genus rows of these samples are below a relative abundance of 0.001. Where the species share is near zero the species table has a detection floor that the genus table lacks.
- Sequencing depth differs between projects by orders of magnitude (`root_coverage_sum`); prevalence is not depth-adjusted.
- Labels are incomplete and differ in coverage between projects; see the table above.
- Samples, not persons, are counted; projects with many samples per person weigh more.

## Citation and licence

Cite the release (release id R2026.44, data package 1.35.0) as given on the website's About page, and the upstream sources named there. The licence is the licence of the release.

## Files

| File | Rows | Bytes |
|---|---|---|
| `ml_genus_abundance_long_v1.35.0.parquet` | 23,682,153 | 82,544,497 |
| `ml_genera_v1.35.0.parquet` | 7,083 | 168,087 |
| `ml_samples_v1.35.0.parquet` | 379,776 | 8,610,294 |
| `taxon_prevalence_body_site_condition_v1.35.0.parquet` | 469,182 | 5,254,003 |
| `prevalence_denominators_v1.35.0.csv` | 195 | 6,939 |
| `replication_signature_taxa_v1.35.0.csv` | 6,933 | 3,590,194 |
| `replication_signatures_v1.35.0.csv` | 1,074 | 397,273 |
