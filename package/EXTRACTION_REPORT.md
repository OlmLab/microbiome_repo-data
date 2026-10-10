# EXTRACTION REPORT — per-sample metadata for the Infant Gut Shotgun-Metagenome Catalog
Date 2026-09-25 (v3, final passes + field extension) · plan artifact 8c115402 (5 phases) · scope: 389 included studies, 153,701 samples, 174,022 runs.

## 1. What was produced
* `sample_determinations.parquet` — **606,023 determinations** (one per sample × field) over 150,544 samples in 379 studies; columns follow `catalog_schema.sql::sample_determinations` plus `route` (R1–R4), `scope` (sample|group), `group_audit`. Every row passes `validate_row()` (labelled ≤12-word verbatim quote, controlled vocabularies).
* `samples.parquet` (153,701; body-site class, gold flag) and `sample_attributes.parquet` (7,477,170 long-format archive attributes, 2,280 distinct keys; ENA sample XML 6.05M rows, NCBI BioSample 170k rows for the 10,034 SAMN records ENA lacks; 71 samples have no attributes anywhere).
* `sample_subjects.parquet` / `subjects.parquet` / `study_subject_summary.csv` — subject and timepoint resolution: 190/389 studies resolve subjects; 34,185 infant subjects, 1,538 mothers (2,162 mother samples linked to an infant; 3,411 unlinked); 92 longitudinal studies; median 1 sample per infant (mean 2.6). `cohorts_v4.csv` recomputes unique_infants_est for 174 cohorts.
* `infant_catalog.sqlite` (189 MB) — studies, samples, runs, sample_determinations, study_triage, cohorts, paper_study_links loaded per `catalog_schema.sql`.
* Evidence/QC tables: `sample_determination_candidates.parquet` (all 774,628 route candidates), `sample_determination_conflicts.parquet` + `conflicts_adjudication_log.parquet`, `group_statement_audit.csv`, `sample_determinations_dropped_group_audit.parquet`, `sample_determinations_rejected.parquet`, `extraction_audit.md`, `audit_300.parquet`, `audit_group_100.parquet`, `PILOT_REPORT.md`, `PHASE0_REPORT.md`.

## 2. Method (four evidence routes, fixed precedence)
| Route | Evidence | Engine | Rows in final table |
|---|---|---|---:|
| R1 archive attributes + sample-name conventions | ENA/NCBI BioSample attributes; DOL/week/month tokens in titles | deterministic parsers; Haiku only to normalise 466 distinct free-text strings | 379,038 |
| R2 supplementary tables | per-sample rows keyed by run/sample accession, library name, title (pooled re-analysis tables accepted on ≥20 exact accession matches) | openpyxl/csv parsing; Haiku column classification; one source table per study × field | 83,676 |
| R3 paper text (group scope) | Methods/Results statements with an explicit mapping (all / timepoint label / pattern) | Sonnet, two replicates per paper, union; deterministic expansion | 31,821 |
| R4 abstract / ENA description (group scope) | cohort-wide statements only; confidence ≤0.5; `evidence_limited_to_abstract=1` | Haiku | 23,095 |

Merge: R1 > R2 > R3 > R4 per sample × field; cross-route disagreements (3,096 sample-level, 76 patterns) adjudicated once per pattern by Opus (536 values changed, mostly R2 winning over submitter-level country/sex). Group statements pass a consistency filter against per-sample values (drop when >20 % disagreement on ≥5 samples) and a threshold filter; **all 359 distinct group statements were then audited by Opus with a failure-mode checklist: 186 kept, 60 downgraded (confidence 0.4, review flag), 113 dropped (17,165 rows)**.

## 3. Coverage (infant-scope samples = primary + unknown body site, n = 146,126)
| Field | samples with a value | share | studies |
|---|---:|---:|---:|
| Probiotic exposure | 7,939 | 5.4% | 25 |
| Preterm status | 33,164 | 22.7% | 126 |
| Gestational age | 12,127 | 8.3% | 44 |
| Delivery mode | 27,798 | 19.0% | 76 |
| Feeding mode | 10,458 | 7.2% | 75 |
| Antibiotic exposure | 20,410 | 14.0% | 77 |
| Age at collection | 49,016 | 33.5% | 159 |
| Birth weight | 9,993 | 6.8% | 37 |
| Country | 135,930 | 93.0% | 363 |
| Maternal antibiotics | 6,879 | 4.7% | 40 |
| HMO supplementation | 1,256 | 0.9% | 4 |
| NEC status | 5,153 | 3.5% | 32 |

Age at collection is recoverable for **34%** of infant-scope samples (49,016); 123 studies have age for ≥80 % of their samples, 135 studies have no field beyond country. Per-study coverage is bimodal (`field_coverage.png`): a field is recoverable for essentially every sample of a study or for none, as the recoverability tiering predicted. Exposures (antibiotics, probiotics, HMO, NEC) are below 15 %.

## 4. Accuracy
**Gold** = 3,670 curatedMetagenomicData infant samples (18 studies) matched to sample keys, never read during extraction. Age tolerance max(7 d, 10 %); GA ±1 wk; "hires" excludes 172 gold rows with integer-year ages.
| Field | gold n | coverage | precision | recall |
|---|---:|---:|---:|---:|
| age_days | 3346 | 0.952 | 0.994 | 0.947 |
| born_method | 2546 | 0.894 | 0.995 | 0.890 |
| feeding_practice | 693 | 0.641 | 0.896 | 0.574 |
| premature | 1998 | 0.961 | 1.000 | 0.961 |
| antibiotics_current_use | 2534 | 0.905 | 0.867 | 0.785 |
| gestational_age | 1998 | 0.262 | 1.000 | 0.262 |

Route precision on gold (hi-res): R2 age 1.000 (n=2,192), R1 age 0.981 (n=994), R2 delivery 0.995, R2 preterm 1.000, R3 preterm 1.000 (n=191), R2 feeding 0.896, R2 antibiotics 0.867. The antibiotic gap is definitional (tables record *any* exposure; cMD records *current use*) — PILOT_REPORT §3; feeding disagreements are cMD's `No Breastfeeding` vs table categories.

**Blind audit** (Sonnet, 300 stratified determinations, two replicates, quote-only): κ 0.79 (3-class) / 0.91 (binary); supported rate R2 0.93, R4 0.83, R1 0.77, R3 0.61 *before* the group-statement gate. The gate removed the dominant R3/R4 failure modes (eligibility criteria read as observations, "healthy" read as term, timepoint lists read as one age, care setting read as exposure, summary words, thresholds). Remaining R1 weaknesses: parent-questionnaire attributes (American Gut `antibiotic_history`), range midpoints, `host_age` given in gestational weeks.

## 5. Costs
| Stage | tokens |
|---|---:|
| Phase 0 Sample frame (Haiku value normalisation) | 127,343 |
| Phase 1 Pilot (Haiku/Sonnet/Opus) | 1,370,707 |
| Phase 2 R2 workers ×3 (Haiku) | 247,774 |
| Phase 2 R3 workers ×9 (Sonnet ×2 replicates) | 10,634,863 |
| Phase 2 R4 worker (Haiku + Sonnet gate) | 647,869 |
| in-frame R4 gap (111 studies, Haiku) | 626,504 |
| in-frame Opus conflict adjudication (76 patterns) | 39,831 |
| Phase 3 QC (Sonnet blind audit ×2 + group checklist) | 214,903 |
| in-frame Opus group-statement audit (359) | 114,069 |

**Extraction phase: 14,023,863 tokens (~14.0M); whole pipeline ≈ 42.2M.** 13 + 5 leaf workers; every LLM fan-out dispatched from the root. Two R2 workers and the R4 worker reported estimated (not measured) token counts after in-run counters were lost to script crashes.

## 6. Limitations and open items
* **R2 ID-mapping residue.** 77 R1/R2-tier studies (41,906 samples; TEDDY PRJNA400115 12,266, American Gut, PRJEB46788, PRJEB45799, PRJEB49383, PRJNA398089 …) have per-sample tables keyed by identifiers that do not match archive accessions/titles. A rescue pass matching table IDs against any archive attribute value was written (`r2_rescue_map.py`); two runs were attempted: the first was lost to a model outage before running; the second reached one study (TEDDY: 4 mapped tables, all gene-abundance matrices without metadata columns → 0 determinations) before Europe PMC stalled and its time-box expired (`RESCUE_REPORT.md`, `rescue_table_mapping.csv`). The 76 remaining studies are the single largest open item; `RESCUE_REPORT.md` lists each with the sample-ID form its tables use. Re-run `r2_rescue_map.py` + `run_rescue_gate.py` from the cached supplementary ZIPs when the network is healthy.
* 10,958 samples carry unitless numeric ages that no rule or paper fixed (`r1_rejected`); 4 pilot studies need a curator (PRJNA300541, PRJNA379120, PRJNA489090, PRJNA716780).
* R3 read at most two papers per study and one ≤46k-char chunk; 3+-paper studies (PRJNA524703, PRJEB12669, PRJNA557731 …) were not read exhaustively. 2,691 R4 candidates shadowed by dropped R3 rows were not promoted (unaudited).
* R4 covers 97 + 111 studies without open-access full text on abstract/description only; 22 of the 111 have no linked paper at all. Paywalled-paper policy applied (abstract + archive only).
* PDF/DOCX supplementary tables were not parsed (23 in one slice alone).
* Gold covers age/delivery/feeding/preterm/antibiotics/GA only; probiotic, HMO, NEC, birth weight, maternal antibiotics have no external truth set (blind-audit only; HMO n=4, 3 flagged).
* Mother–infant linking is partial (3,411 mother samples unlinked); 199 studies expose no subject identifier.

## 7. Rules added to `infant-curation-rules` (this phase)
Pooled exact-accession tables count as evidence (cap 0.75); ranges/thresholds/timepoint lists are not values; boolean breastfeeding columns do not set feeding_mode; group statements need the consistency check and must not come from eligibility criteria, care setting, site policy, "healthy", summary words, maternal pregnancy weeks or single cases; unitless ages resolve only by sibling attribute, unique admissible unit or paper quote; parent questionnaires are respondent history; `antibiotic_exposure` = any antibiotics before/at sampling (record `antibiotic_current` separately when a source distinguishes).


## 8. Recovery round 2 (2026-09-25) — six methods, results
| Method | Rows kept after merge | Samples gaining ≥1 field | Notes / deviations |
|---|---:|---:|---|
| R2 ID-mapping rescue (attribute inverted index; 77 studies) | 1,569 | ~1,000 | 78 new tables in 20 studies (orig 31 / attr_norm 29 / attr_exact 17 / composite 1); **57 of 68 studies with tables still yield nothing — their tables are keyed by identifiers absent from the archive** (TEDDY subject ids, LUMC ids, censored ids) or are abundance/statistics tables; PMC6358638 ZIP not cached; ID forms per study in `RESCUE_REPORT_v2.md` |
| PDF/DOCX supplements (136 files, 65 studies) | 732 | ~300 | 81 files parsed, 386 tables kept, only 6 mapped (4 studies). **Found a bug in the R2 gate: sample accessions and library names were dropped as 'generic' IDs** — fixed (`r2_supp_extract_v2.py`); 17 previously inventoried XLSX tables became mappable (+1,382 rows, 5 studies) |
| Unitless ages (49 studies, 17,462 samples) | 1,853 | 1,853 | U1 1,337 / U2 307 / U3 209; **10,305 samples are adults/>36 mo** (mothers, American Gut) → `adult_age_rows.parquet`; PRJEB108678: 44 prior rows were years-converted months — corrected; PRJNA398089 host_age is not an age; PRJNA1274040 `age` is a visit index; 9 studies unresolved |
| Multi-paper cohorts (93 studies) | 2,850 | 2,546 | Token cap reached after 45/93 studies (82 of 1,908 paper-chunks read); 102 statements → 55 rule-dropped, 24 Opus-audited (9 keep / 10 downgrade / 5 drop); 738 rows supersede weaker R3/R4 rows; 48 studies (34k infant samples) not read |
| Subject propagation (deterministic) | 2,242 | 1,665 | Per-infant-constant fields copied across 11,892 multi-sample infants; 308 subjects with inconsistent sex/GA excluded |
| Date arithmetic (collection − birth date) | 78 | 78 | Only 2 studies carry both dates; agreement with existing ages 0.938 (n=227) |
| Shadowed R4 promotion | 0 | 0 | All 5 shadowed statements were the same failure modes Opus had already dropped |
| Exposure truth set | — | — | **Not built: the curation request was refused by a content-safety filter** (sub-agent failed before any work). No external truth exists for probiotic/HMO/NEC/birth-weight/maternal-antibiotic fields; they remain blind-audit only |

Coverage change (infant-scope samples, n = 146,126):
| Field | before | after | Δ samples | share |
|---|---:|---:|---:|---:|
| Probiotic exposure | 7,939 | 8,113 | +174 | 5.6% |
| Preterm status | 33,164 | 33,645 | +481 | 23.0% |
| Gestational age | 12,127 | 12,587 | +460 | 8.6% |
| Delivery mode | 27,798 | 28,472 | +674 | 19.5% |
| Feeding mode | 10,458 | 10,505 | +47 | 7.2% |
| Antibiotic exposure | 20,410 | 20,533 | +123 | 14.1% |
| Age at collection | 49,016 | 53,149 | +4,133 | 36.4% |
| Birth weight | 9,993 | 10,200 | +207 | 7.0% |
| Country | 135,930 | 136,139 | +209 | 93.2% |
| Maternal antibiotics | 6,879 | 7,467 | +588 | 5.1% |
| HMO supplementation | 1,256 | 1,256 | +0 | 0.9% |
| NEC status | 5,153 | 5,153 | +0 | 3.5% |

Gold after merge (hi-res): age precision 0.993 / recall 0.949 (was 0.994 / 0.947); other fields unchanged. Tokens this round: 2,700,912. **Whole pipeline ≈ 44.9M.**

Structural changes delivered: `r2_supp_extract_v2.py` (attribute-inverted-index gate default, study_keys fix, numeric-ID plausibility), Methods-first chunking for R3, skill rules for unit resolution and adult-age routing. The remaining ceiling is structural, not methodological: ~42k samples sit in studies whose per-sample tables cannot be joined to archive records without submitter keys, and 34k samples' extra papers were not read for budget reasons (a second multi-paper pass with the same recipe would finish them).


## 9. Final passes (2026-09-25) — multi-paper pass 2, R2 re-run with the fixed gate, field extension
| Pass | Rows kept | Result |
|---|---:|---|
| Multi-paper pass 2 (48 studies) | 1,714 | 32 of 48 studies read (budget); 1,712 rows confirm existing R3 values exactly, 2 new pairs — the prose route is saturated. 8 studies (626 samples) still unread |
| R2 full re-run, v2 gate (219 studies with tables; 7,363 study×table pairs) | 9,449 | 386 newly accepted tables in 104 studies; 4,757 samples gain ≥1 field (age +651); 170 studies have no PMC-linked table at all; 2 ZIPs unretrievable; 1,978 rows removed by a post-hoc misclassification filter |
| Field extension (R1 + R2) | 70,522 | New fields: geo_subregion 47,016 samples / 174 studies; health_condition 9,680 / 33; multiple_birth 4,792 / 18; sibling_in_study 3,924 / 10 (3,130 candidate sibling rows withheld — cannot verify distinct infants). No reverse-geocoding; region codes kept as given |

Final table: **606,023 determinations**, 150,677 samples, 381 studies. Gold (hi-res): age 0.991 / 0.951, delivery 0.995 / 0.932, preterm 1.000 / 0.961, feeding 0.891 / 0.577, antibiotics 0.867 / 0.820, GA 1.000 / 0.264.

Coverage (infant-scope samples, n = 146,126):
| Field | samples | share | studies |
|---|---:|---:|---:|
| Probiotic exposure | 8,308 | 5.7% | 24 |
| Preterm status | 33,704 | 23.1% | 116 |
| Gestational age | 12,791 | 8.8% | 48 |
| Delivery mode | 29,149 | 19.9% | 87 |
| Feeding mode | 11,828 | 8.1% | 72 |
| Antibiotic exposure | 21,187 | 14.5% | 65 |
| Age at collection | 53,802 | 36.8% | 171 |
| Birth weight | 10,371 | 7.1% | 37 |
| Country | 136,139 | 93.2% | 363 |
| Maternal antibiotics | 8,397 | 5.7% | 38 |
| HMO supplementation | 1,256 | 0.9% | 3 |
| NEC status | 5,173 | 3.5% | 20 |
| Health condition | 9,680 | 6.6% | 32 |
| Multiple birth | 4,792 | 3.3% | 18 |
| Sibling in study | 3,924 | 2.7% | 10 |
| Geo subregion | 47,016 | 32.2% | 172 |

Tokens these passes: 2,358,996. **Whole pipeline ≈ 47.3M.** Published as data_package_v1.zip (README, DATA_DICTIONARY, wide tables, notebook) and the GitHub Pages site.
