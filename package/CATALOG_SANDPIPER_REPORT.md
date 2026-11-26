# CATALOG_SANDPIPER_REPORT — Sandpiper 2.0.1 profiles for the current catalog samples

How the profiles were joined to the catalog samples. Inputs: `sandpiper2.0.1.gtdb.csv.gz` (GTDB R232, Zenodo record 21287531), `sandpiper2.0.1.per_acc_summary.csv.gz`, `catalog_runs.parquet` and `catalog_sample_metadata_wide.parquet`. Only current samples (release_retired null) are used: 813,356 of 873,877 wide-table rows. Of the 933,965 DNA runs of those samples, 448,844 have a profile.

## 1. Coverage

**415,757 of 813,356 current samples (51.1 %) in 3,135 of 5,086 projects; 448,844 of 970,388 runs (46.3 %).** Profiled runs without per-run metadata: 0.

| prefix   |   projects |   projects_profiled |   runs |   runs_profiled |   samples |   samples_profiled |
|:---------|-----------:|--------------------:|-------:|----------------:|----------:|-------------------:|
| PRJDA    |          2 |                   1 |     81 |              60 |        81 |                 60 |
| PRJDB    |        189 |                  81 |  26009 |            6474 |     25473 |               6450 |
| PRJEB    |       1105 |                 698 | 367061 |          172588 |    296008 |             157363 |
| PRJNA    |       3786 |                2352 | 576849 |          269449 |    491406 |             251611 |
| SRP00    |          2 |                   1 |     16 |               7 |        16 |                  7 |
| SRP01    |          1 |                   1 |      3 |               3 |         3 |                  3 |
| SRP05    |          1 |                   1 |    369 |             263 |       369 |                263 |

Coverage classes (frac_samples_profiled; complete ≥ 0.95, majority ≥ 0.5, partial > 0): none 1,951, majority 1,876, complete 787, partial 472.

By age category:

| age_category   |   n_samples |   n_profiled |   frac_profiled |
|:---------------|------------:|-------------:|----------------:|
| adolescent     |        7245 |         4081 |           0.563 |
| adult          |      288183 |       166712 |           0.578 |
| child          |       35605 |        20343 |           0.571 |
| elderly        |       38332 |        23946 |           0.625 |
| infant         |       75974 |        42292 |           0.557 |
| neonate        |       16691 |        11384 |           0.682 |
| unknown        |      351326 |       146999 |           0.418 |

## 2. QC flags per sample (flagged, never dropped; computed for every sample)

| flag | n_samples | pct |
|---|---|---|
| qc_low_depth | 6,375 | 1.5 % |
| qc_low_complexity | 24,371 | 5.9 % |
| qc_readfraction_warning | 6,558 | 1.6 % |
| qc_non_metagenome | 3,783 | 0.9 % |
| qc_non_metagenome_strict | 23,465 | 5.6 % |
| qc_synthetic | 124 | 0.0 % |
| qc_rna | 0 | 0.0 % |
| qc_predicted_ecological | 4,174 | 1.0 % |
| qc_partial | 11,273 | 2.7 % |
| qc_no_genus_assigned | 6,898 | 1.7 % |
| qc_any_flag | 66,898 | 16.1 % |

Definitions as in the 1.x report: low_depth = summed root coverage < 2; low_complexity / readfraction_warning / predicted_ecological (host_or_not = ecological) = on any run; non_metagenome = organism names a single non-human species on any run (strict rule minus 'Homo sapiens'); non_metagenome_strict includes 'Homo sapiens'; synthetic = organism synthetic/simulat or library_source SYNTHETIC; rna = RNA library strategy/source; partial = n_runs_profiled < n_runs_total (DNA runs); no_genus_assigned = richness 0.

Three caveats for anyone using these columns: the per-run flags are this resource's application of Sandpiper's rules, not Sandpiper's own output; the `sp_miss_reason` values are inferences from the run metadata, not statements by Sandpiper; and the relative abundances are fractions of prokaryotic coverage, not of all reads.

## 3. PCA

Matrix: 409,382 samples with root coverage ≥ 2; 386 genera detected (≥ 0.001) in ≥ 1 % of them; 5,064 samples contain none of the kept genera → **404,318 scored samples**. Kept genera hold a median 0.932 (5th percentile 0.562) of a sample's root coverage. Method unchanged from 1.x.

Explained variance ratio: PC1 0.1991, PC2 0.0609, PC3 0.0386, PC4 0.0303, PC5 0.0279 (cumulative 0.3567).

- **PC1** + g__Faecalibacterium 0.195, g__Roseburia 0.186, g__Alistipes 0.175, g__Blautia_A 0.174, g__Gemmiger 0.168, g__Phocaeicola 0.160; − g__Veillonella -0.102, g__Rothia -0.095, g__Corynebacterium -0.089, g__Staphylococcus -0.085, g__Neisseria -0.082
- **PC2** + g__Enterocloster 0.199, g__Flavonifractor 0.191, g__Mediterraneibacter 0.171, g__Eggerthella 0.165, g__Bacteroides 0.157, g__Blautia 0.153; − g__CAG-170 -0.182, g__Prevotella -0.177, g__Hominicoprocola -0.167, g__Vescimonas -0.140, g__Faecousia -0.140
- **PC3** + g__Prevotella 0.257, g__Streptococcus 0.237, g__Veillonella 0.178, g__Haemophilus_D 0.176, g__Pauljensenia 0.172, g__Rothia 0.148; − g__Akkermansia -0.132, g__Bacteroides -0.115, g__Escherichia -0.111, g__Alistipes -0.106, g__Ruthenibacterium -0.104
- **PC4** + g__Prevotella 0.272, g__Phocaeicola 0.247, g__Bacteroides 0.217, g__Parabacteroides 0.210, g__Alistipes 0.176, g__Oscillibacter 0.141; − g__Bifidobacterium -0.180, g__Anaerobutyricum -0.145, g__Clostridium -0.140, g__Oliverpabstia -0.127, g__Lentihominibacter -0.127
- **PC5** + g__Escherichia 0.318, g__Bifidobacterium 0.244, g__Prevotella 0.189, g__ECMA0423 0.161, g__Klebsiella 0.160, g__Holdemanella 0.141; − g__Adlercreutzia -0.121, g__Neisseria -0.121, g__Alistipes -0.121, g__Actinomyces -0.118, g__Akkermansia -0.118
