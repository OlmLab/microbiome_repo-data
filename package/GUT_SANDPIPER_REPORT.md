# GUT_SANDPIPER_REPORT — Sandpiper profiles for the whole gut catalog + interactive PCA (R2026.12 prep)

Inputs: Sandpiper 2.0.0 bulk GTDB R232 profiles (Zenodo record 20419175, `sandpiper2.0.0.gtdb.csv.gz`), `sandpiper2.0.0.per_acc_summary.csv.gz`, `gut_runs.parquet` (721,678 runs of the 2,837 catalog studies), package 1.11.0 (`gut_sample_metadata_wide.parquet`, 579,252 samples). All numbers below are read from the build logs and output tables (`work/sp/filter_log.json`, `work/sp/build_log.json`).

## 1. Streaming pass over the bulk file
One pass with `filter_gut.py` (same semantics as `src/catalog/sandpiper/filter_bulk.py`; pyarrow CSV reader, 64 MB blocks, 16 zstd parquet parts).

| quantity | value |
|---|---|
| rows scanned | 281,588,385 |
| rows kept (profiled gut runs) | 76,546,223 |
| runs expected (sandpiper_profiled = True) | 372,911 |
| runs found in bulk | 372,911 |
| runs missing from bulk | 0 |
| wall time | 52.7 s |

Rows per rank in the kept set (0 = Root … 7 = species): 0: 372,911, 1: 623,321, 2: 2,365,479, 3: 3,419,840, 4: 5,477,004, 5: 9,418,268, 6: 23,170,193, 7: 31,699,207. Unfilled coverage (filled(node) − Σ filled(children)) was derived for every node; rows with unfilled < −0.02 (rounding): 0.

## 2. Join to catalog samples
372,911 profiled runs → 369,957 carry a `sample_key`; **2,954 profiled runs have no sample_key in gut_runs.parquet** (their biosample is not a catalog sample in package 1.11.0; e.g. ERR209550/PRJEB1220, ERR719072/PRJEB8094) and are excluded from every sample table. Result: **339,626 profiled samples** in **2,084 studies** (study_accession from the wide table; 2,081 by the runs table). Aggregation to the sample = SUM of filled coverage over the sample's profiled runs, normalised by the summed root coverage — identical to the root-coverage-weighted mean of per-run relative abundance. Per-rank relative abundances sum to 1 per sample (genus min/max 1.000000/1.000000; species 1.000000/1.000000).

### Coverage by accession prefix
| prefix   |   studies |   studies_profiled |   runs |   runs_profiled |   samples |   samples_profiled |   frac_runs |   frac_samples |
|:---------|----------:|-------------------:|-------:|----------------:|----------:|-------------------:|------------:|---------------:|
| PRJDA    |         1 |                  1 |     80 |              60 |        80 |                 60 |       0.75  |          0.75  |
| PRJDB    |        95 |                 56 |  11029 |            5837 |     10387 |               5566 |       0.529 |          0.536 |
| PRJEB    |       708 |                498 | 299828 |          143848 |    235720 |             128371 |       0.48  |          0.545 |
| PRJNA    |      2033 |               1529 | 410741 |          223166 |    333065 |             205629 |       0.543 |          0.617 |

Totals: 2,084 of 2,837 studies have ≥ 1 profiled sample; 372,911 of 721,678 runs (51.7%); 339,626 of 579,252 samples (58.6%). Study coverage classes (`gut_sandpiper_study_coverage.csv`, frac_samples_profiled): majority 1,371, none 753, complete 568, partial 145 (complete ≥ 95 %, majority ≥ 50 %, partial > 0, none = 0).

### Coverage by age category (wide table)
| age_category   |   n_samples |   n_profiled |   frac_profiled |
|:---------------|------------:|-------------:|----------------:|
| adolescent     |        5853 |         3226 |           0.551 |
| adult          |      229779 |       140152 |           0.61  |
| child          |       26268 |        16698 |           0.636 |
| elderly        |       30826 |        21411 |           0.695 |
| infant         |       66200 |        34673 |           0.524 |
| neonate        |       14663 |         9611 |           0.655 |
| unknown        |      205663 |       113855 |           0.554 |

## 3. QC flags per sample (flagged, never dropped)
| flag                     |   n_samples |   pct | definition                                                                                                                           |
|:-------------------------|------------:|------:|:-------------------------------------------------------------------------------------------------------------------------------------|
| qc_low_depth             |        2051 |  0.6  | summed root coverage < 2 genome equivalents                                                                                          |
| qc_low_complexity        |       11306 |  3.33 | Sandpiper low_complexity = yes on any run                                                                                            |
| qc_readfraction_warning  |        3564 |  1.05 | Sandpiper read-fraction warning on any run                                                                                           |
| qc_non_metagenome        |        1754 |  0.52 | organism label names a single non-human species on any run (strict rule minus 'Homo sapiens'; approximates the v1.2.1 curated class) |
| qc_non_metagenome_strict |       16030 |  4.72 | Sandpiper strict rule incl. 'Homo sapiens' (kept for reproducibility)                                                                |
| qc_synthetic             |         169 |  0.05 | organism contains synthetic/simulat or library_source = SYNTHETIC                                                                    |
| qc_rna                   |           0 |  0    | RNA library strategy/source (strict)                                                                                                 |
| qc_predicted_ecological  |        3795 |  1.12 | Sandpiper host_or_not = ecological on any run                                                                                        |
| qc_partial               |        9697 |  2.86 | n_runs_profiled < n_runs_total                                                                                                       |
| qc_no_genus_assigned     |        2234 |  0.66 | no coverage reaches genus level (richness 0)                                                                                         |
| qc_any_flag              |       41947 | 12.35 | any of the above                                                                                                                     |

Multi-run samples: 11,865 samples have > 1 profiled run (max 321). `qc_flags` is the semicolon-joined list; the boolean columns are also shipped.

## 4. Tables
| file | rows | bytes | content |
|---|---|---|---|
| gut_sandpiper_sample_genus.parquet | 22,380,090 | 185,885,848 | long: sample_key, genus (GTDB `g__`), lineage, relabund, coverage — all genera + `unassigned_at_genus` (8,916 genera) |
| gut_sandpiper_sample_species.parquet | 20,477,008 | 199,447,330 | long, species with relabund ≥ 0.001 + `unassigned_at_species` (25,946 species) |
| gut_sandpiper_sample_summary.parquet | 339,626 | 24,478,106 | sample_key, study_accession, n_runs_profiled, n_runs_total, root_coverage_sum, richness_genus (relabund ≥ 0.001), shannon_genus (incl. unassigned bin) and shannon_genus_assigned, top_genus, top_genus_relabund, unassigned shares, spf, known_species_fraction, qc_flags + booleans, runs, sandpiper_url |
| gut_sandpiper_study_coverage.csv | 2,837 | 568,468 | per study: runs/samples profiled, fractions, coverage_class, flag counts, medians, top_genus_mode |
| gut_sandpiper_pca_scores.parquet | 335,956 | 13,024,317 | sample_key, pc1..pc5 (float32), study_accession, root_coverage_sum |
| gut_sandpiper_pca_loadings.parquet | 383 | 37,707 | genus, pc1..pc5, prevalence, mean_relabund, clr_mean |
| gut_sandpiper_pca_variance.csv | 5 | 531 | explained_variance_ratio, singular_value, method parameters |
All carry taxonomy_db = GTDB, taxonomy_version = R232, sandpiper_version = 2.0.0, zenodo_record = 20419175. The run-level table with unfilled coverage (gut_sandpiper_profiles_runs.parquet, 76,546,223 rows, 490,237,969 bytes) was built but is not shipped as an artifact: it is regenerated in ≈ 1 min by `filter_gut.py` + `build_gut_tables.py` (both saved).

## 5. PCA
Matrix: samples with summed root coverage ≥ 2 genome equivalents (336,952); genera detected (relabund ≥ 0.001) in ≥ 1 % of those samples: **383 of 8,916**; 996 samples contain none of the kept genera and are not scored → **335,956 scored samples** (2,073 studies). Kept genera hold a median 0.934 (5th percentile 0.681) of a sample's root coverage; the unassigned remainder is excluded and the kept genera re-closed to 1. Zeros replaced by multiplicative replacement with δ = 1e-05 (non-zeros scaled by 1 − n_zeros·δ), CLR = log x − mean(log x) per sample, columns centred, randomized SVD (sklearn `randomized_svd`, 5 components, 30 oversamples, 7 power iterations, seed 0). Sign convention: the largest-|loading| genus of each axis is positive.

Explained variance ratio: PC1 0.1626, PC2 0.0653, PC3 0.0381, PC4 0.0320, PC5 0.0228 (cumulative 0.3207).

Top loadings (genus, weight):
- **PC1** + g__Faecalibacterium 0.186, g__Roseburia 0.179, g__Gemmiger 0.174, g__Alistipes 0.174, g__Vescimonas 0.156, g__Fusicatenibacter 0.152, g__Blautia_A 0.150, g__Lachnospira 0.149; − g__Escherichia -0.110, g__Veillonella -0.109, g__Klebsiella -0.095, g__Enterococcus -0.088, g__ECMA0423 -0.083, g__Staphylococcus -0.080, g__Rothia -0.075, g__Enterobacter -0.063
- **PC2** + g__Enterocloster 0.204, g__Flavonifractor 0.189, g__Mediterraneibacter 0.170, g__Anaerostipes 0.162, g__Eggerthella 0.157, g__Blautia 0.155, g__Bacteroides 0.151, g__Blautia_A 0.144; − g__CAG-170 -0.175, g__Prevotella -0.175, g__Hominicoprocola -0.154, g__Faecousia -0.137, g__Coprococcus -0.130, g__Vescimonas -0.128, g__CAG-177 -0.126, g__UBA11524 -0.124
- **PC3** + g__Alistipes 0.215, g__Bacteroides 0.186, g__Akkermansia 0.180, g__Parabacteroides 0.165, g__Phocaeicola 0.148, g__Ruthenibacterium 0.142, g__Oscillibacter 0.128, g__Lawsonibacter 0.124; − g__Streptococcus -0.199, g__Dorea_A -0.168, g__Prevotella -0.155, g__Dorea -0.148, g__Oliverpabstia -0.136, g__Bifidobacterium -0.133, g__Veillonella -0.133, g__Anaerobutyricum -0.131
- **PC4** + g__Prevotella 0.319, g__Phocaeicola 0.215, g__Pilosibacter 0.176, g__Sutterella 0.172, g__Parabacteroides 0.166, g__Bacteroides 0.165, g__Lachnospira 0.133, g__Faecalibacterium 0.131; − g__Hominenteromicrobium -0.151, g__Lentihominibacter -0.143, g__Anaerotardibacter -0.143, g__Evtepia -0.135, g__Adlercreutzia -0.129, g__Akkermansia -0.127, g__Bifidobacterium -0.117, g__Anaerobutyricum -0.113
- **PC5** + g__Prevotella 0.292, g__Collinsella 0.214, g__Escherichia 0.171, g__Bifidobacterium 0.170, g__Holdemanella 0.160, g__Mediterraneibacter 0.158, g__CAG-177 0.145, g__Ruthenibacterium 0.137; − g__Butyribacter -0.181, g__Eubacterium_F -0.148, g__Hominilimicola -0.133, g__Anthropogastromicrobium -0.129, g__Hominisplanchenecus -0.128, g__Hominimerdicola -0.126, g__Hominiventricola -0.118, g__Brotaphodocola -0.114

PC1 opposes the diverse adult-type community (Faecalibacterium, Roseburia, Gemmiger, Alistipes) to facultative-anaerobe-dominated communities (Escherichia, Veillonella, Klebsiella, Enterococcus, Staphylococcus) — the infant/neonatal flank in the figure. PC2 opposes Enterocloster/Flavonifractor/Mediterraneibacter/Eggerthella to Prevotella/CAG-170/Coprococcus.

### Check against PCoA (20,000-sample random subsample, seed 0, same 383-genus closure)
| comparison | Procrustes disparity (2 axes) | Procrustes disparity (5 axes) | abs r axis 1 | abs r axis 2 | abs r diagonal (1–5) |
|---|---|---|---|---|---|
| CLR-PCA vs Hellinger PCoA | 0.3907 | 0.3803 | 0.857 | 0.585 (best match PCo2) | [0.857, 0.585, 0.439, 0.556, 0.043] |
| CLR-PCA vs Bray-Curtis PCoA | 0.4187 | 0.4117 | 0.851 | 0.521 (best match PCo5) | [0.851, 0.512, 0.285, 0.455, 0.012] |
| Hellinger vs Bray-Curtis PCoA | 0.0607 | 0.0267 | 0.992 | 0.934 | [0.992, 0.934, 0.922, 0.959, 0.968] |

Hellinger PCoA explained variance 0.147, 0.097, 0.081, 0.054, 0.043; Bray-Curtis PCoA 0.160, 0.112, 0.092, 0.061, 0.051. The first axis is shared by all three ordinations (|r| ≈ 0.85–0.86 between CLR-PCA PC1 and PCo1); the second CLR axis agrees only moderately with the abundance-based PCo2 (|r| 0.59 Hellinger, 0.52 Bray-Curtis), as expected because CLR weights low-abundance genera that Hellinger/Bray-Curtis down-weight. The two abundance-based ordinations agree closely with each other (Procrustes 0.06). Read PC2–PC5 as CLR-specific structure.

## 6. Interactive page (site_generator/gen/pages/pca.py, templates/atlas_pca.html, static/pca.js, static/pca.css, tests/test_pca_page.py)
`build(env, render, ctx, out_dir)` renders `atlas/pca.html` (root `../`, nav `atlas`) and writes `data/atlas/`:

| file | bytes |
|---|---|
| pca_points.bin (Float32 n × 5, row-major) | 6,719,120 |
| pca_codes.bin (Uint16 study + Uint8 age_category, country, health_condition, body_site_class, top_genus[, lifestyle]) | 2,351,692 |
| pca_meta.json (labels, counts, sample keys, explained variance, loadings, study titles) | 5,122,093 |
| total | 14,192,905 (13.54 MiB, cap 15 MB enforced by an assert) |

Points on the page: **335,956** (2,073 studies). Features: PC axis selectors (PC1–PC5), colour-by (age_category, country top 15 + other, health_condition top 15 + other, body_site_class, top_genus top 15 + other, study top 20 + other, lifestyle when the wide table has the column — 1.11.0 does not, so the page built here has no lifestyle option; the harness covers both cases), alpha scaling with the number of visible points, hover tooltip (sample_key → samples/index.html?sample=, study → studies/<acc>.html, age category, country, condition, top genus), study-highlight text box with datalist (drawn on top in red), box selection (checkbox or shift-drag) reporting count / study breakdown and an "open selection in sample explorer" button (samples/index.html?study=<acc>) when the selection is one study, legend click to isolate a category, wheel zoom / drag pan, URL state (x, y, color, study), explained-variance caption, top-loading genera panel, and the binary layout documented in the page footer. Canvas2D only; no external libraries. `pca.js` passes a JavaScriptCore syntax check; `python -m pytest tests/test_pca_page.py` → 3 passed (500-sample synthetic input; asserts files, byte layout vs meta, selectors, PC options, footer layout text, lifestyle optional). build_site.py is untouched — the root wires the page in (`from pages import pca; pca.build(env, render, ctx, out)` with ctx = wide, studies + the PCA/summary tables or `pkg` dir) and adds the Atlas nav entry; `static/pca.css` is picked up by the existing static copy loop.

## 7. Deviations / caveats
- 2,954 profiled runs without a sample_key in gut_runs.parquet are excluded from sample tables (listed in `sp/gut_runs_profiled` join; not a data loss in the bulk pass).
- `qc_non_metagenome` approximates the shipped v1.2.1 curated class (strict rule minus the 'Homo sapiens' label); the shipped infant tables used a hand-curated organism-class table that was not among this task's inputs.
- The PCoA check uses a 20,000-sample subsample (by design); Bray-Curtis PCoA eigenpairs via Lanczos on the Gower-centred matrix.
- Lifestyle colouring is implemented but inactive against package 1.11.0 (no `lifestyle` column yet).
- The run-level table is not shipped as an artifact (490 MB; regenerable in ≈ 1 min with the saved scripts).
