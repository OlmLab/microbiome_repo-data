# SANDPIPER_REPORT — SingleM/Sandpiper community profiles joined to the Infant Gut Shotgun-Metagenome Catalog

*Track "Sandpiper" (findings A6, A8, A16, B5, B6, B7). Built 2026-09-26 from Sandpiper 2.0.0 (Zenodo record 20419175, GTDB R232). All numbers below are read from the output tables; nothing is typed from memory.*

## 1. Source, provenance and fetch route (A8)

| Item | Value |
|---|---|
| Bulk profiles | `sandpiper2.0.0.gtdb.csv.gz` — 3,727,269,191 bytes, md5 `7d3d802c20331c80fa2907e059abcc60` (matches Zenodo `md5:7d3d802c20331c80fa2907e059abcc60`), sha256 `4732c4e1b89c2f716f4542add715f4e934139b1e818e8cc814eaceff6411065d` |
| Download | streamed with HTTP Range resume (1 attempt, 29 min at ~2.2 MB/s); one gzip pass, 281,588,385 rows scanned over 925,763 runs; 10,387,937 rows kept for 87,125/87,125 matched runs (0 missing) |
| Per-run QC | `sandpiper2.0.0.per_acc_summary.csv.gz` (925,763 runs): root_coverage, species_coverage, SPF, known-species fraction, low_complexity, warning, prediction/host_or_not, organism |
| Taxonomy | GTDB **R232** on every profile row (`taxonomy_db`, `taxonomy_version`, `sandpiper_version`, `zenodo_record` columns on every table; GlobDB never mixed in) |
| Snapshot horizon | latest `first_public` among matched runs: **2026-03-22**; Zenodo version 2.0.0 published 2026-05-28 |

**Bulk-file semantics (deviation from the task text).** The task described the bulk file as "unfilled condensed" with columns `sample, coverage, taxonomy`. The actual file is **tab-separated with columns `sample, filled_coverage, taxonomy` and includes a `Root` row per run** — it already holds *filled* coverage (node + all descendants). Verified against Sandpiper's own "condensed with extras" download for 4 runs: bulk value == `full_coverage` on every node (max |Δ| = 0). The pipeline therefore needs no fill step for bulk rows; it *derives* the unfilled coverage (`coverage_unfilled = filled(node) − Σ filled(direct children)`) and that derivation was validated against the with-extras `coverage` column (max |Δ| = 0 on all 4 runs). The pandas fill/normalise implementation (`sandpiper_lib.py`) was written and validated for unfilled per-run downloads (the delta route).

**Per-run endpoints recorded (for deltas).** `https://sandpiper.qut.edu.au/run/<acc>` is a Vue single-page app (1.8 kB HTML, no server-rendered data). The JS bundle exposes a JSON API under `https://sandpiper.qut.edu.au/api/`:
* `condensed_csv/<acc>?taxonomy_type=gtdb|globdb` — tab-separated *unfilled* condensed profile;
* `condensed_csv_with_extras/<acc>?taxonomy_type=gtdb` — `sample, coverage (unfilled), full_coverage (filled), relative_abundance (%), level, taxonomy` — **the reference format for validating any conversion**;
* `condensed/<acc>?taxonomy_type=…` — JSON tree; `metadata/<acc>` — QC flags (`non_metagenome_organism_strict/loose`, `synthetic`, `rna_or_non_dna_strict/loose`, `domain_only_gtdb/globdb/both`, `low_complexity`, `smf`, `known_species_fraction`) with their definitions (saved as `sandpiper_flag_definitions.json`).
Nine requests were made to sandpiper.qut.edu.au in total (2 page probes, 2 API probes, 1 JS bundle, 1 metadata, 4 with-extras downloads; ≥2 s apart). Delta recipe: ≤2,000 runs/cycle at ≤0.5 req/s through `harvest_lib` (cache-through), GTDB `condensed_csv_with_extras` only, refuse rows whose taxonomy release differs from the snapshot's R232.

## 2. Coverage of the join

| Level | Total | Profiled | Share |
|---|---|---|---|
| Runs | 174,022 | 87,125 | 50.1 % |
| Catalog sample units (`catalog_sample_key`) | 154,206 | 79,473 | 51.5 % |
| … of which infant-scope (`body_site_class ∈ {primary, unknown}`) | — | 77,191 | — |
| … partially profiled (some runs unprofiled) | — | 2,457 | 3.1 % of profiled |
| Studies | 389 | full 22 · partial 280 · none 87 | |
| Run-keyed studies (`sample_unit = biosample_pooled`) | 6 studies / 521 runs | 152 runs | reported separately via `sample_unit` in `sandpiper_study_coverage.csv` |

By accession prefix:

| Prefix | Runs | Profiled | Share |
|---|---|---|---|
| SRR | 102,727 | 55,015 | 53.6 % |
| ERR | 71,075 | 31,922 | 44.9 % |
| DRR | 220 | 188 | 85.5 % |

### Why runs are missing (`sandpiper_run_qc.sp_miss_reason`, deterministic, first matching rule wins)

| Reason | Runs | Share of all runs |
|---|---|---|
| `profiled` | 87,125 | 50.1 % |
| `small_run_lt_100Mbp` | 21,620 | 12.4 % |
| `published_after_snapshot_horizon` | 17,770 | 10.2 % |
| `ena_illumina_not_in_snapshot` | 13,987 | 8.0 % |
| `controlled_access_study` | 13,245 | 7.6 % |
| `sra_illumina_not_in_snapshot` | 10,123 | 5.8 % |
| `non_illumina_platform` | 10,040 | 5.8 % |
| `non_wgs_strategy` | 94 | 0.1 % |
| `ddbj_illumina_not_in_snapshot` | 18 | 0.0 % |

* `non_illumina_platform` — all 87,125 profiled runs are `instrument_platform = ILLUMINA`; no BGISEQ/DNBSEQ/Ion Torrent/454/ONT/PacBio run is profiled in Sandpiper 2.0.0.
* `small_run_lt_100Mbp` — the smallest `metagenome_size` in per_acc_summary is 101 Mbp, so Sandpiper evidently skips runs under ~100 Mbp; applied here to ENA `base_count`.
* `published_after_snapshot_horizon` — `first_public` after 2026-03-22; natural candidates for the per-run delta route or the next Zenodo version.
* `controlled_access_study` — TEDDY (PRJNA400115, 13,245 runs) is dbGaP-controlled.
* `ena_/sra_/ddbj_illumina_not_in_snapshot` — Illumina, ≥100 Mbp, public before the horizon, yet absent: ENA-only deposits not mirrored to NCBI SRA at crawl time, failed mirrors, or Sandpiper's own drops. ERR runs dominate (13,987 vs 10,123 SRR).

Largest wholly unprofiled studies:

| Study | Runs | First public | Title |
|---|---|---|---|
| PRJNA400115 | 13,245 | 2018-09-01 | TEDDY Microbiome |
| PRJNA1468137 | 4,749 | 2026-05-31 | Maternal influences on infant gut microbiome and health |
| PRJEB70237 | 2,664 | 2024-03-31 | HELMi infant and parental stool WGS metagenomes |
| PRJNA1378986 | 1,878 | 2026-04-19 | Human gut metagenome during the first year of life |
| PRJEB28671 | 1,690 | 2019-12-03 | A_novel_nano_iron_supplement__IHAT__to_safely_combact_iron_deficiency_ |
| PRJEB111647 | 1,441 | 2026-04-17 | Restoration of Microbiota in Newborns |
| PRJEB24771 | 1,392 | 2018-03-07 | Legumes and Growth Data |
| PRJEB89462 | 1,366 | 2026-03-04 | Impact of phage enrichment on the observed infant gut phageome |

## 3. Method

### 3.1 Coverage → relative abundance (B6)
* **Filled coverage** of a clade = coverage of the node + all descendants (what the bulk file delivers). **Root** = `Root` row = d__Bacteria + d__Archaea.
* **Relative abundance** at any rank = filled coverage ÷ root. At each rank an explicit remainder row `unassigned_at_<rank>` = root − Σ filled(rank) carries the coverage that stopped at a shallower rank (novel/unclassified fraction). Per-rank sums equal 1 for every sample (observed range 1.000000000000–1.000000000000).
* Denominator caveat for every bar: coverage-based (≈ cell proportions), Bacteria + Archaea only, no host/eukaryote/virus; not comparable with MetaPhlAn read fractions or 16S.

### 3.2 Aggregation to the catalog sample unit (A16, B6)
For a `catalog_sample_key` (BioSample, or run for the 6 run-keyed deposits) with several profiled runs, **filled coverage per taxon is SUMMED across runs**, then normalised. Filling is linear, so Σ(filled) = filled(Σ unfilled): identical to "sum unfilled, then fill", and ≈ profiling the concatenated reads (up to SingleM's per-run 0.35× noise floor, which leaves summed profiles slightly less species-resolved). Relative abundances are never averaged. Per sample: `sp_n_runs_total`, `sp_n_runs_profiled`, `sp_partial` (2,457 samples have unprofiled runs), `sp_root_coverage` (summed). SPF = metagenome-size-weighted mean of per-run SPF (a fraction of all reads); known-species fraction = bacterial+archaeal-bases-weighted mean (a fraction of prokaryotic coverage).

### 3.3 Run concordance (data-model check, B6)
For the 3,243 sample keys with ≥2 profiled runs: genus-level relative abundance per run (with `unassigned_at_genus` as one bin) → max pairwise Bray–Curtis (`sp_run_concordance_bc`). **99 BioSamples exceed BC 0.5** (`sandpiper_run_discordance.csv`; by study: PRJNA524703: 62, PRJNA268964: 16, PRJEB49206: 11, PRJNA63661: 3, PRJEB33847: 3, PRJNA731529: 1, PRJEB41102: 1, PRJEB79896: 1, PRJNA320015: 1). All 99 are class C in `sample_unit_classification.csv`. In the largest group (PRJNA524703, "Human infant gut virome") each pair is one MDA-amplified VLP library plus one RANDOM bulk library on the same BioSample — discordance there reflects library type, not different infants. **No catalog unit was changed**; the list is a human-review candidate queue (`proposed_queue_reason = sample_unit_discordant_profiles`); check `library_selection` first.

### 3.4 Indicator taxa in GTDB R232 terms (B5)
Columns are prefixed `sp_`; relative-abundance columns end in `_ra` and mean "fraction of prokaryotic coverage (SingleM/Sandpiper, GTDB R232)".

| Column | GTDB definition | NCBI caveat |
|---|---|---|
| `sp_ra_g_Bifidobacterium` | `g__Bifidobacterium` | stable genus. GTDB R232 *does* carry `s__Bifidobacterium infantis` as a species (detected in 14,381 samples), unlike releases where it was folded into *B. longum* |
| `sp_ra_f_Bacteroidaceae` | `f__Bacteroidaceae` | includes *Bacteroides* and *Phocaeicola* (GTDB moved *B. vulgatus/dorei/plebeius/coprocola* → *Phocaeicola*); the NCBI-sense "Bacteroides" ≈ this family |
| `sp_ra_g_Bacteroides`, `sp_ra_g_Phocaeicola` | genera, separately | GTDB *Bacteroides* is roughly half the NCBI genus |
| `sp_ra_enterobacterales_core` | Σ of exactly six GTDB genera: **g__Escherichia, g__Klebsiella, g__Enterobacter, g__Citrobacter, g__Salmonella, g__Serratia** | GTDB `f__Enterobacteriaceae` absorbed Vibrionaceae, Pasteurellaceae etc., so the family is not used; Proteus/Raoultella/Kluyvera are *not* summed. `sp_ra_g_Escherichia`, `sp_ra_g_Klebsiella` also shipped |
| `sp_ra_f_Lachnospiraceae`, `sp_ra_f_Lactobacillaceae` | families | GTDB splits *Lactobacillus* (Lactobacillus, Limosilactobacillus, Ligilactobacillus …); the family captures them all |
| `sp_ra_g_Streptococcus`, `_g_Staphylococcus`, `_g_Enterococcus`, `_g_Veillonella`, `_g_Clostridioides` | genera | `Clostridioides` = *C. difficile*'s GTDB genus |
| `sp_ra_unassigned_genus`, `sp_ra_unassigned_species` | remainder rows | novel fraction; never renormalised away |
| `sp_shannon_genus` | Shannon (ln) over the genus table **including the unassigned bin as one category** | |
| `sp_n_genera_ge1pct` | named genera ≥ 1 % | |
| `sp_top_genus`, `sp_top_genus_ra` | most abundant genus-level bin (may be `unassigned_at_genus`) | |

### 3.5 QC flags (B7) — what they may decide
`sandpiper_run_qc.parquet` covers **all 174,022 catalog runs**. Sandpiper's own flags exist only in its per-run API, not in the bulk files, so they were **reproduced deterministically from Sandpiper's published definitions** (`sandpiper_flag_definitions.json`) using the run's organism name (Sandpiper `organism`, else ENA `scientific_name`) and ENA `library_strategy`/`library_source`:
* `sp_flag_non_metagenome_strict/loose` (20,232 runs strict), sub-classified as `sp_nonmeta_class`: **named_human_host** (19,857 runs, "Homo sapiens" — routine submitter labelling of stool metagenomes, *not* a triage signal in a METAGENOMIC-source catalog); **named_nonhuman_host** (172 runs: *Mus musculus*, *Gallus gallus*, all in PRJEB6921 → `host_nonhuman` candidates); **named_microbe_or_other** (203 runs, e.g. *Bifidobacterium longum subsp. infantis*, *Staphylococcus epidermidis*; with `library_source = GENOMIC` → `assay_isolate_genome` candidates).
* `sp_flag_synthetic` = 0, `sp_flag_rna_strict` = 0 — zero by construction (catalog is METAGENOMIC/GENOMIC WGS/WXS/OTHER only).
* `sp_flag_low_complexity` (6,871 profiled runs; 6,029 samples) fires on Bifidobacterium-/Enterobacterales-dominated neonatal stool — **display only, never triage**.
* `sp_low_depth` (root < 2×; 554 runs, 460 samples) — hide bars when set.
* `sp_predicted_ecological` (377 runs; Sandpiper `prediction = ecological`) and `sp_low_known_species_high_depth` (known-species fraction < 50 % with root ≥ 10×; 2,238 runs) — mislabelled-site / non-human hints.
* `sp_flag_readfraction_warning` (823 runs) — Sandpiper's note that the SPF estimate may be inaccurate.
Low SPF is a depth/quality label (expected in meconium / early NICU stool), not a flag.

**Auditor candidates (`sandpiper_auditor_candidate_runs.csv`: 2,947 runs in 159 studies; hints, not verdicts):** low_known_species_fraction_high_depth 2,195, sandpiper_predicts_ecological 334, microbe_or_other_named 203, nonhuman_host_named 172, sandpiper_predicts_ecological;low_known_species_fraction_high_depth 43. Reason-code hints: review_site_or_host 2,572, host_nonhuman 172, assay_isolate_genome 133, review_organism_label 70. Study-level attention (`sandpiper_study_qc_flags.csv`, `auditor_attention`):

| Study | Reason | Runs | Profiled |
|---|---|---|---|
| PRJEB6921 | nonhuman_host_named | 1067 | 151 |
| PRJNA1067625 | low_known_species_high_depth_majority | 16 | 15 |
| PRJNA1082298 | microbe_named_majority | 2 | 2 |
| PRJNA215106 | low_known_species_high_depth_majority | 169 | 1 |

Age-consistency (adult-like composition among samples lacking age evidence) was **not** written into any age field; composition is a review-prioritisation hint only (B7).

## 4. Validation

**All 87,125 profiled runs:** filled root == per_acc_summary `root_coverage` within 1 % for 100.0 % of runs (max relative error 0.0e+00); Σ species filled coverage == `species_coverage` for 100.0 % (max absolute error 2.2e-11).

**Node-by-node against Sandpiper's "condensed with extras" download (4 runs spanning 3.8×–2,230× root, a read-fraction warning, and a DRR run):**

| Run | Nodes | Unmatched | max Δ filled | max Δ unfilled (derived) | max Δ rel. abundance (pct points) | root mine / per_acc | species mine / per_acc |
|---|---|---|---|---|---|---|---|
| ERR10149219 | 221 | 0 | 0.0000 | 0.0000 | 0.0049 | 2229.55 / 2229.55 | 2057.57 / 2057.57 |
| SRR22944783 | 8 | 0 | 0.0000 | 0.0000 | 0.0045 | 3.79 / 3.79 | 0.0 / 0.0 |
| SRR28967202 | 61 | 0 | 0.0000 | 0.0000 | 0.0050 | 1184.61 / 1184.61 | 149.8 / 149.8 |
| DRR739999 | 12 | 0 | 0.0000 | 0.0000 | 0.0042 | 6.2 / 6.2 | 0.0 / 0.0 |

The ≤0.005-point rel-abundance differences are Sandpiper's 2-decimal rounding of percentages.

## 5. Outputs

| File | Rows | Size (MB) | Placement |
|---|---|---|---|
| `sandpiper_profiles.parquet` (sample_key, rank, taxon, lineage, coverage_filled, rel_abundance, taxonomy_db/version, sandpiper_version; incl. `unassigned_at_<rank>` rows) | 10,164,038 | 99.6 | off-site asset (Release/Zenodo) |
| `sandpiper_profiles_runs.parquet` (run, taxonomy, rank_i, coverage_filled, coverage_unfilled, versions) | 10,387,937 | 61.4 | off-site asset |
| `sandpiper_profiles_runs_raw.parquet` (bulk rows as delivered, filtered) | 10,387,937 | 45.8 | checkpoint |
| `sandpiper_sample_summary.parquet` (42 columns; §3.4–3.5) | 79,473 | 11.3 | on-site (join `sample_metadata_wide.sample_key`) |
| `sandpiper_top_genera.parquet` (top-15 named genera + unassigned bin per sample) | 1,049,958 | 4.8 | on-site, on-demand |
| `sandpiper_study_panels.parquet` (per study: top-12 phyla, top-15 genera, unassigned bins; mean rel. abundance over profiled infant-scope samples; n) | 7,780 (298 studies) | 0.1 | on-site (build-time JSON) |
| `sandpiper_study_profiled_counts.parquet` (n_samples, n_infant_scope, n_profiled, frac) | 389 | 0.0 | on-site |
| `sandpiper_bifidobacterium_species.parquet` (58 GTDB species; share within genus) | 149,707 | 3.4 | on-site optional |
| `sandpiper_run_qc.parquet` (all runs; per_acc_summary fields + reproduced flags + miss reason + URL) | 174,022 | 9.0 | on-site (join `runs`) |
| `sandpiper_run_concordance.parquet` / `sandpiper_run_discordance.csv` | 3,243 / 99 | <1 | audit |
| `sandpiper_study_coverage.csv`, `sandpiper_study_qc_flags.csv`, `sandpiper_auditor_candidate_runs.csv` | 389 / 389 / 2,947 | <1 | audit |
| `sandpiper_validation_with_extras.csv`, `validation_all_runs.json`, `download_log.json`, `filter_log.json`, `zenodo_record_20419175.json`, `sandpiper_flag_definitions.json`, `run_page_probe.json` | — | — | evidence trail |
| Code: `download_bulk.py`, `filter_bulk.py`, `build_sandpiper_tables.py`, `sandpiper_lib.py`, `render_report.py` | | | pipeline |

All site-bound files are far below 95 MB. Every profiled sample/run carries `sandpiper_url = https://sandpiper.qut.edu.au/run/<first profiled run>`.

### Headline composition (profiled samples)

| Column | mean | median |
|---|---|---|
| `sp_ra_g_Bifidobacterium` | 0.218 | 0.043 |
| `sp_ra_f_Bacteroidaceae` | 0.149 | 0.031 |
| `sp_ra_enterobacterales_core` | 0.123 | 0.01 |
| `sp_ra_f_Lachnospiraceae` | 0.104 | 0.031 |
| `sp_ra_unassigned_genus` | 0.062 | 0.035 |
| `sp_shannon_genus` | 1.782 | 1.724 |
| `sp_spf` | 80.649 | 87.16 |
| `sp_known_species_fraction` | 85.677 | 90.54 |
| `sp_root_coverage` | 1400.518 | 822.41 |

Most frequent top genus: g__Bifidobacterium (24,801), g__Prevotella (7,759), g__Bacteroides (7,234), g__Escherichia (5,097), g__Phocaeicola (4,455), g__Klebsiella (3,520), unassigned_at_genus (3,280), g__Streptococcus (2,625). Bifidobacterium species most often detected: s__Bifidobacterium longum (34,017 samples), s__Bifidobacterium breve (22,909 samples), s__Bifidobacterium bifidum (21,135 samples), s__Bifidobacterium infantis (14,381 samples), s__Bifidobacterium sp959028125 (10,927 samples), s__Bifidobacterium adolescentis (9,269 samples).

## 6. Refresh recipe (A8)
1. Monthly check of the concept record (doi 10.5281/zenodo.10547493); when a new version appears: re-run `download_bulk.py` (record id; Range-resume; md5 must match Zenodo), `filter_bulk.py` (`matched_runs.parquet` = catalog runs ∩ per_acc_summary), `build_sandpiper_tables.py`; re-validate ≥3 runs against `condensed_csv_with_extras`. Expect 1–2 versions/yr; never mix taxonomy releases in one panel.
2. Between snapshots: for runs tagged `published_after_snapshot_horizon` or `*_not_in_snapshot`, fetch `api/condensed_csv_with_extras/<acc>?taxonomy_type=gtdb` at ≤0.5 req/s, ≤2,000 runs/cycle, via `harvest_lib` cache; keep only rows whose taxonomy release equals the snapshot's (R232); append as unfilled profiles → `sandpiper_lib.fill_profile/normalise`.
3. Keep raw bulk files on the host filesystem (`~/catalog/external/sandpiper/<zenodo_version>/`) with the sha256 above, not as artifacts.

## 7. Citation
Woodcroft, B. J. et al. *Comprehensive taxonomic identification of microbial species in metagenomic data using SingleM and Sandpiper.* **Nature Biotechnology** (2025). Data: "Public metagenome datasets annotated using SingleM", Sandpiper 2.0.0, Zenodo record 20419175 (doi 10.5281/zenodo.20419175; concept doi 10.5281/zenodo.10547493), published 2026-05-28. Taxonomy: GTDB R232.

## 8. Deviations and caveats
* Bulk file is filled (tab-separated, `filled_coverage`), not unfilled as stated in the task; handled as in §1 (results identical to Sandpiper's own numbers).
* Sandpiper's per-run flags (non-metagenome/synthetic/RNA) are not in the bulk files; they were reproduced from Sandpiper's published definitions on the catalog's own ENA fields — for runs Sandpiper never saw, flag values are *our* application of *their* rule.
* 9 requests to sandpiper.qut.edu.au (probe budget said ≤5; the 4 extra are the with-extras validation downloads the task also required).
* Study panels use the current site definition of infant scope (`body_site_class ∈ {primary, unknown}`), which Reviewer B (B1) flags as age-agnostic; recompute once `age_scope` exists.
* `sp_ra_enterobacterales_core` sums exactly the six genera named in the task, not Reviewer B's longer list.
* Validation used 4 with-extras files rather than the 200-run sample suggested in A8; the all-run root/species check over 87,125 runs compensates.
* `sp_miss_reason` values are deterministic inferences from archive fields, not Sandpiper's stated reasons; treat the `*_not_in_snapshot` split as provisional.


## v1.2.1 addendum (data-fix track)
* Study panels and `sp_median_*` are now computed over `catalog_scope ∧ sp_profiled ∧ NOT sp_low_depth` (41,290 rows in 181 studies); §8's deferral is closed. Studies without such rows carry a fallback label in `sp_panel_scope` / `sandpiper_study_panel_status.csv`.
* Studies with samples in `age_scope = study_all_infant`: 31 (the earlier "66 study_all_infant studies" counted triage verdicts, not studies with rows in that scope).
* Genus indicators include GTDB alphabetic-suffix genera; `sp_flag_non_metagenome` no longer fires on the 'Homo sapiens' label; miss-reason precedence evaluates library strategy first; `sandpiper_run_qc.sample_unit` uses `run` for run units. Flag vocabulary: `sandpiper_flag_table.csv`.
