# CATALOG_SANDPIPER_REPORT — Sandpiper 2.0.1 profiles for the current catalog samples

Built by `scripts/refresh_sandpiper.py` (one pass, all tables rebuilt). Inputs: `sandpiper2.0.1.gtdb.csv.gz` (GTDB R232, Zenodo record 21287531), `sandpiper2.0.1.per_acc_summary.csv.gz`, `catalog_runs.parquet` and `catalog_sample_metadata_wide.parquet` from `build/package`. Only current samples (release_retired null) are used: 805,008 of 865,529 wide-table rows.

## 1. Streaming pass

| quantity | value |
|---|---|
| bulk rows scanned | 238,306,693 |
| rows kept | 66,736,432 |
| candidate runs (DNA runs of current samples) | 925,273 |
| runs with a profile | 448,290 |
| coverage format in the bulk file | unfilled (converted to filled) |
| wall time | 85.1 s |

Unfilled coverage rows below −0.02: 0. Genus sum-check (min, max, n): [0.9999999999999989, 1.000000000000001, 415393].

## 2. Coverage

**415,393 of 805,008 current samples (51.6 %) in 3,130 of 5,050 projects; 448,290 of 961,694 runs (46.6 %).** Profiled runs without per-run metadata: 0.

| prefix   |   projects |   projects_profiled |   runs |   runs_profiled |   samples |   samples_profiled |
|:---------|-----------:|--------------------:|-------:|----------------:|----------:|-------------------:|
| PRJDA    |          2 |                   1 |     81 |              60 |        81 |                 60 |
| PRJDB    |        177 |                  81 |  19812 |            6474 |     19393 |               6450 |
| PRJEB    |       1100 |                 698 | 366858 |          172588 |    295805 |             157363 |
| PRJNA    |       3767 |                2347 | 574555 |          268895 |    489341 |             251247 |
| SRP00    |          2 |                   1 |     16 |               7 |        16 |                  7 |
| SRP01    |          1 |                   1 |      3 |               3 |         3 |                  3 |
| SRP05    |          1 |                   1 |    369 |             263 |       369 |                263 |

Coverage classes (frac_samples_profiled; complete ≥ 0.95, majority ≥ 0.5, partial > 0): none 1,920, majority 1,873, complete 785, partial 472.

By age category:

| age_category   |   n_samples |   n_profiled |   frac_profiled |
|:---------------|------------:|-------------:|----------------:|
| adolescent     |        7172 |         4018 |           0.56  |
| adult          |      287941 |       166439 |           0.578 |
| child          |       35551 |        20312 |           0.571 |
| elderly        |       38278 |        23944 |           0.626 |
| infant         |       75850 |        42218 |           0.557 |
| neonate        |       16692 |        11385 |           0.682 |
| unknown        |      343524 |       147077 |           0.428 |

## 3. QC flags per sample (flagged, never dropped; now computed for every sample)

| flag | n_samples | pct |
|---|---|---|
| qc_low_depth | 6,375 | 1.5 % |
| qc_low_complexity | 24,360 | 5.9 % |
| qc_readfraction_warning | 6,558 | 1.6 % |
| qc_non_metagenome | 3,709 | 0.9 % |
| qc_non_metagenome_strict | 23,391 | 5.6 % |
| qc_synthetic | 124 | 0.0 % |
| qc_rna | 0 | 0.0 % |
| qc_predicted_ecological | 4,171 | 1.0 % |
| qc_partial | 11,243 | 2.7 % |
| qc_no_genus_assigned | 6,898 | 1.7 % |
| qc_any_flag | 66,793 | 16.1 % |

Definitions as in the 1.x report: low_depth = summed root coverage < 2; low_complexity / readfraction_warning / predicted_ecological (host_or_not = ecological) = on any run; non_metagenome = organism names a single non-human species on any run (strict rule minus 'Homo sapiens'); non_metagenome_strict includes 'Homo sapiens'; synthetic = organism synthetic/simulat or library_source SYNTHETIC; rna = RNA library strategy/source; partial = n_runs_profiled < n_runs_total (DNA runs); no_genus_assigned = richness 0.

## 4. PCA

Matrix: 409,018 samples with root coverage ≥ 2; 386 genera detected (≥ 0.001) in ≥ 1 % of them; 5,064 samples contain none of the kept genera → **403,954 scored samples**. Kept genera hold a median 0.932 (5th percentile 0.562) of a sample's root coverage. Method unchanged from 1.x.

Explained variance ratio: PC1 0.1991, PC2 0.0609, PC3 0.0386, PC4 0.0303, PC5 0.0279 (cumulative 0.3568).

- **PC1** + g__Faecalibacterium 0.195, g__Roseburia 0.186, g__Alistipes 0.175, g__Blautia_A 0.174, g__Gemmiger 0.168, g__Phocaeicola 0.160; − g__Veillonella -0.102, g__Rothia -0.095, g__Corynebacterium -0.089, g__Staphylococcus -0.085, g__Neisseria -0.082
- **PC2** + g__Enterocloster 0.199, g__Flavonifractor 0.191, g__Mediterraneibacter 0.171, g__Eggerthella 0.166, g__Bacteroides 0.157, g__Blautia 0.153; − g__CAG-170 -0.182, g__Prevotella -0.177, g__Hominicoprocola -0.167, g__Vescimonas -0.140, g__Faecousia -0.140
- **PC3** + g__Prevotella 0.257, g__Streptococcus 0.237, g__Veillonella 0.178, g__Haemophilus_D 0.176, g__Pauljensenia 0.172, g__Rothia 0.148; − g__Akkermansia -0.132, g__Bacteroides -0.115, g__Escherichia -0.111, g__Alistipes -0.106, g__Ruthenibacterium -0.104
- **PC4** + g__Prevotella 0.270, g__Phocaeicola 0.247, g__Bacteroides 0.217, g__Parabacteroides 0.210, g__Alistipes 0.177, g__Oscillibacter 0.142; − g__Bifidobacterium -0.181, g__Anaerobutyricum -0.144, g__Clostridium -0.141, g__Oliverpabstia -0.127, g__Lentihominibacter -0.126
- **PC5** + g__Escherichia 0.317, g__Bifidobacterium 0.243, g__Prevotella 0.191, g__ECMA0423 0.160, g__Klebsiella 0.160, g__Holdemanella 0.141; − g__Adlercreutzia -0.122, g__Neisseria -0.120, g__Alistipes -0.119, g__Actinomyces -0.118, g__Akkermansia -0.118

## 5. Changes against the previous tables

| quantity | value |
|---|---|
| previous_rows | 415,393 |
| previous_rows_retired_samples | 0 |
| previous_versions | {'2.0.1': 415393} |
| samples_new | 0 |
| samples_lost | 0 |
| root_changed_gt1pct | 0 |
| top_genus_changed | 0 |
| compared | 415,393 |

## 6. Registry run flags

`registry_runs_sandpiper.parquet`: 2,042,964 runs; sandpiper_profiled 843,049 → 843,049 (run list of `sandpiper2.0.1.per_acc_summary.csv.gz`).
