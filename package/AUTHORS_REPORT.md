# AUTHOR INDEX — Infant Gut Shotgun-Metagenome Catalog

Deterministic build (no LLM) of an author/organisation index for all **9,579 screened studies**
(389 included, 52 human_review, 9,138 excluded). Every name comes from a fetched record
(Europe PMC `authorString`, NCBI BioProject XML) or an existing catalog table; nothing was typed from memory.
Wall time ≈ 30.7 min; 495 HTTP calls (NCBI 199, EBI 296), all cached in `harvest_cache/`.

## 1. Sources
| source | how | studies reached |
|---|---|---|
| `linked_paper` | paper↔study link tables (paper_study_links + new_paper_study_links 1/2, `sentinel_no_evidence` rows dropped) ∪ `catalog_studies.linked_pmids` → 1467 (study, paper) pairs; authors from `papers_raw.authorString`; 86 pairs whose paper was not in papers_raw resolved via Europe PMC (`EXT_ID:<id> AND SRC:MED/PPR`, resultType=core) | 607 |
| `bioproject_publication` | NCBI BioProject XML (`esearch db=bioproject <acc>[PRJA]` ×100 → `efetch` ×200) → `<Publication id=…>` refs (955 refs on 762 projects: {'pmid': 708, 'doi': 237, 'pmcid': 7, 'ppr': 3}); resolved via Europe PMC by PMID / DOI / PMCID / PPR id (938 resolved; 15 fell back to the NCBI `StructuredCitation/AuthorSet` names; 2 refs carry no author list at all: [('PRJNA1139951', '10.2139/ssrn.4960739'), ('PRJEB52881', '35559516')]) | 734 |
| `ena_study_xref` | ENA browser XML `STUDY_LINKS/XREF_LINK DB=PUBMED` scanned for all 8316 studies that had no author from the two sources above → 2 hits (1 preprint resolved; PMID 35559516 is not in Europe PMC) | 1 |
| organisations | ENA `center_name` (universe table + ENA portal `study` search for 614 missing; 510 studies have a blank centre in ENA), ENA `broker_name`, NCBI BioProject `Submission/Organization` (role owner/participant) | 9,579 |

| source                 |   rows |   studies |   authors |   papers |
|:-----------------------|-------:|----------:|----------:|---------:|
| bioproject_publication |  11536 |       734 |      7696 |      842 |
| ena_study_xref         |     45 |         1 |        45 |        1 |
| linked_paper           |  16786 |       607 |      3964 |      526 |

Studies with authors from both `linked_paper` and `bioproject_publication`: 78.

## 2. Coverage by catalog status
| catalog_status   |   organisation only |   ≥1 author |   All |
|:-----------------|--------------------:|------------:|------:|
| excluded         |                8170 |         968 |  9138 |
| human_review     |                  42 |          10 |    52 |
| included         |                 103 |         286 |   389 |
| All              |                8315 |        1264 |  9579 |

v1.2.1 correction: 9,513 of 9,579 studies have at least one displayable organisation (66 have only a submission id / username / placeholder: 3 included, 63 excluded). The original statement below is superseded — Every study has at least one organisation (submitter centre or BioProject owner), so no study is
without any searchable entity. **103 included studies (26%) have no author at all** —
their BioProject carries no publication, no paper was linked, and ENA has no PubMed xref. These are the
priority for a future literature back-link pass (title/accession search in Europe PMC full text), which
is outside this deterministic track.

## 3. Counts
* Author rows: **28,367** (dedup on study + display name + PMID); distinct author keys **10,965**, of which 36 are consortium/group names (`is_group=True`, e.g. "RESONANCE Consortium").
* Distinct papers behind the rows: 1,367; max authors on one paper 89; 120 (study, paper) pairs have ≥25 authors.
* Organisation rows: 18,888 over 9,886 distinct raw keys; **v1.2.1 correction:** 6,143 rows / 6,079 keys are not organisations (SRA submission ids `SUB…` 6,033, single lowercase usernames 93, placeholders 9, e-mail addresses 8); 3,747 displayable organisation keys remain after preferring the BioProject Organization over the ENA centre for PRJNA studies (`organisations.parquet` columns `not_org_reason`, `is_organisation`, `display_eligible`).
* Most-connected authors (studies): 
| author | studies |
|---|---|
| Ventura M | 244 |
| Lugli GA | 244 |
| Mancabelli L | 243 |
| Turroni F | 243 |
| Tarracchini C | 241 |
| Milani C | 238 |
| van Sinderen D | 215 |
| Fontana F | 212 |
| Alessandri G | 210 |
| Viappiani A | 188 |
  (the Parma/Ventura group registers one BioProject per isolate genome — 240+ excluded single-genome projects.)

## 4. PMIDs / papers newly resolved
`new_papers_from_bioproject.parquet`: **881** Europe PMC records not present in papers_raw
({'bioproject_publication': 805, 'linked_paper': 75, 'ena_study_xref': 1}; sources {'MED': 848, 'PPR': 32, 'PMC': 1}), with authorString, title, journal, year, DOI, abstract and the
study accessions they are registered for. Of the 955 BioProject publication refs only 55 were already in papers_raw —
BioProject-registered publications are largely orthogonal to the literature-harvest channel (most belong to excluded, non-infant projects).

## 5. Failures / rate limits
* NCBI: 96 esearch + 96 efetch calls, 1 efetch batch failed on first pass (100 records) and was refetched successfully; no HTTP 429.
  4 accessions are not in NCBI BioProject at all: ['PRJEB79367', 'PRJEB90452', 'PRJNA1163982', 'PRJNA448876'] (2 recent PRJEB not yet mirrored, 2 PRJNA suppressed/withdrawn) — organisation only from ENA.
* Europe PMC: 0 failed batches; 3 PMIDs registered on BioProjects (38298144, 34145447, 35559516) are absent from Europe PMC — NCBI structured-citation names used where present.
* 17 of 955 publication refs could not be resolved in Europe PMC (mostly MDPI *Sci*, Frontiers marine/plant, SSRN, Aquaculture Reports DOIs not indexed) — 15 used NCBI structured-citation authors, 2 have no authors.

## 6. Caveats
* **String names only, no ORCID.** Author identity = `author_key` = transliterated (ø→o, æ→ae, ß→ss, ł→l, đ→d, İ→i, Ð→d, Þ→th; v1.2.1) then ASCII-folded lowercase "surname initials" (e.g. `van sinderen d`). Homonyms are NOT disambiguated: 1,075 of 8,181 surnames (recomputed from `authors.parquet` v2, v1.2.1) occur with more than one initials set, and common East-Asian surname+initial combinations (e.g. `wang y`, `zhang j`) certainly merge distinct people. The site should present the index as "possible matches" and let the researcher confirm by study.
* 26 keys have >1 display spelling (accent/hyphen variants collapsed by the folding); the most frequent spelling is shown.
* Europe PMC `authorString` truncation ("et al."): **0** affected in papers_raw and **0** in the new records — both were fetched with `resultType=core`, which returns the full string (max observed 89 authors). The lite-endpoint truncation caveat therefore does not apply to this build.
* Initials parsing: 9 non-group rows have no initials (single-token names such as Indonesian mononyms); position/is_first/is_last follow the Europe PMC author order; NCBI structured citations may be incomplete or ordered differently.
* `linked_paper` rows inherit the link `relation` (own_data / reused_public_data / unsure / cited_only) and `contested` status — a "reused_public_data" author did not necessarily generate the deposit. `bioproject_publication` rows come from the submitter's own registration and are the strongest ownership signal.
* Organisation names are as deposited (case and abbreviation variants, e.g. "UNIVERSITY OF SOUTH FLORIDA"); only ASCII-folded lowercase keys are used for grouping — no institution normalisation.

## 7. Deliverables
* `authors.parquet` — 28,367 rows: study_accession, author_display, author_surname, author_initials, author_key, is_group, source, pmid, pmcid, doi, paper_title, pub_year, position, n_authors, is_first, is_last, paper_relation, link_method, catalog_status, reason_code, study_title.
* `organisations.parquet` — 18,888 rows: study_accession, organisation, abbr, role, org_type, source, organisation_key, catalog_status.
* `authors_index.json` (6.3 MB, single file, <15 MB) — {author_key: {display, group, n_studies, studies:[{acc, status, reason_code, title_short, pmid, src(L/B/E), pos}]}}.
* `organisations_index.json` (1.4 MB) — same shape for organisations.
* `study_authors_summary.csv` — one row per study: n_authors, first_author, last_author, n_papers, n_linked_papers, n_bioproject_pubs, organisations, has_any_author, has_any_organisation, in_ncbi_bioproject.
* `new_papers_from_bioproject.parquet`, `bioproject_records.parquet` (9,575 records: accession, title, publications JSON, publication_pmids, grants, external_links, organizations JSON, submitter_owner, center_id, submission dates), `ena_study_xrefs.csv`, `harvest_bioproject_authors.py`, `bioproject_fetch_log.json`.
