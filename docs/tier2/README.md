# eds-findings-tier2-v0 — Ehlers-Danlos syndromes (all 2017-classification types) and hypermobility spectrum disorder

> **Tier-2 (pre-2019) EDS findings, first build; well-formedness QA only — scientific fidelity of extracted findings has not been human-adjudicated.**

**15,860 machine-extracted findings from 2,605 papers** (Tier 2 — EDS/HSD literature 1936-2018 (pre-2019; no overlap with pots-findings); chunks [30, 36, 37, 39, 40, 41, 42, 43, 44, 45, 46, 47, 48, 49, 50, 51, 52, 53, 54, 55, 56, 57, 58, 59, 60, 61, 62, 63, 64, 65, 66, 67, 68, 69, 70, 71, 72, 73, 74, 75, 76, 77, 78, 79, 80, 81, 82, 83, 84, 85, 86, 87, 88, 89, 90, 91, 92, 93, 94, 95, 96, 97, 98, 99, 100, 101, 102, 103, 104, 105, 106, 107, 108, 109, 110, 111, 112, 113, 114, 115, 116, 117, 118, 119, 120, 121, 122, 123, 124, 125, 126, 127, 128, 129, 130, 131, 132, 133, 134, 135, 136, 137]; years 1946–2018; source stream Europe PMC; extraction model `claude-sonnet-5`).

## Read this before the data

**Scientific fidelity is unmeasured.** Every check in `qa/` verifies well-formedness — that a quote is verbatim in its source, that an identifier denotes the named entity, that counts conserve. Nothing here tests whether a finding correctly interprets its paper. Every row is a pointer to a paper worth reading, not a fact to cite.

`condition` semantics: **population** — `condition` names the patient POPULATION the paper studied. A collagen-biology paper with no EDS/HSD cohort is `general` even if it informs EDS; a paper that carries an EDS/HSD label without studying that population is a bucket violation, recorded in the additive annotations table and never corrected in place. Definitions version: eds dry-run v1 (2026-09-30); EDS_hypermobility inherited from CONDITION_BUCKET_DEFINITIONS.md v1 and split (full text in `docs/METHODS.md`).

## Measured numbers (each recomputed at build time and re-derived by `code/verify_package.py`)

| metric | numerator | denominator | value | basis |
|---|---|---|---|---|
| papers yielding >= 1 finding | 2605 | 2734 | 95.28% | papers with >=1 finding / papers in the frozen roster (all dispatched) |
| findings / paper (overall) | 15860 | 2605 | 6.09 | pooled; see strata below — composition drives this number |
| findings / paper — abstract-only | 11508 | 2337 | 4.92 | per fulltext_status stratum |
| findings / paper — retrieved-fulltext | 4352 | 268 | 16.24 | per fulltext_status stratum |
| rows with a resolved entity id | 3134 | 15860 | 19.76% | COVERAGE, not correctness (id_denotes_entity_rate not yet measured) |
| rows with >= 1 verified HPO id | 7317 | 15860 | 46.13% | exact label or authority-asserted synonym, OLS4 |
| rows with HGNC id | 2110 | 2225 | 94.83% | of rows carrying a gene_marker; MyGene symbol lookup |
| number_in_quote false | 608 | 5903 | 10.3% | of rows carrying a number; an open question, never auto-fixed |

### Quote traceability census (with wrong-paper negative control)

| stratum | checked | traceable | rate | no source | control false-positives | control verdict |
|---|---|---|---|---|---|---|
| abstract-only | 11507 | 11487 | 99.83% | 1 | 0/400 (0.0%) | matcher discriminates |
| full-text (archive-backed) | 4352 | 4322 | 99.31% | 0 | 0/400 (0.0%) | matcher discriminates |
| blocked (fetch failed; extracted from title+abstract) | 0 | 0 | — | 0 | — | NO CONTROL POSSIBLE (<2 papers with source text in this stratum) - rate reported WITHOUT a control; do not cite it alone |

Coverage: 15859 of 15860 quote-bearing rows (99.99%) could be checked against retained source text; 15809 traced (99.68% of checkable).

### Invariants — 9/9 pass

| check | result | detail |
|---|---|---|
| schema_completeness | ok | {"missing": []} |
| conservation | ok | {"absent_chunks": [], "over_allotment": []} |
| monotonicity | ok | {"rows": 15860, "papers": 2605, "prior_rows": 0, "prior_papers": 0} |
| no_model_emitted_ids | ok | {"violations": {}} |
| id_provenance | ok | {"id_without_source": {}} |
| enum_drift | ok | {"flagged": {"evidence_tier": {"systematic-review/meta-analysis": 2}}, "note": "flagged for adjudication, never auto-remapped"} |
| duplicates | ok | {"fully_identical_rows": 0, "loose_key_groups": 46, "note": "loose>full is expected and NOT a defect; only fully-identical rows are duplicates"} |
| hpo_multivalue | ok | {"mismatch": 0, "n_with_ids": 7317, "n_multi": 2407} |
| authority_prefix_match_v4 | ok | {"mismatches": {}, "n_mismatched_rows": 0, "n_mismatched_ids": 0, "n_rows_checked": 10451, "n_ids_checked": 14441, "n_undeclared_tag_rows": 0, "n_minting_column |

### Screening (two arms)

3859 papers screened; final verdicts {"in_scope": 2394, "uncertain": 1188, "off_topic": 277} (pass 1); after pass 2 (population check, rule: pass-1 two-arm reconcile; pass-2 population check over pass-1 uncertain; exclude only where pass-1 LLM off_topic AND pass-2 no listed population (verbatim spans) or both pass-1 arms agree; rest extracted and flagged): {"in_scope": 2394, "off_topic": 1125, "uncertain": 340}; LLM off-topic calls against a rule-arm anchor flagged for adjudication (extracted, `screen_verdict_uncertain=true`): 910; instrument alarm: True (see `qa/screen_summary.json`, `scope/`).

## Flags (rows kept, never deleted)

| flag | count |
|---|---|
| query_contamination | {"rows": 40, "papers": 8} |
| screen_verdict_uncertain | {"rows": 1527, "papers": 295} |
| number_in_quote_true | 5295 |
| number_in_quote_false | 608 |
| number_in_quote_null_no_number | 9957 |

## Files

`data/` findings (parquet + csv.gz) and the frozen roster/chunk map · `docs/` this file, DATA_DICTIONARY, METHODS, LIMITATIONS, COMPARABILITY · `qa/` invariants, quote census, screening summary, gate log, package_stats · `scope/` screening report and flagged/excluded lists · `code/` the pipeline generation that produced the rows, rendered prompt and scope, config, verifier · `MANIFEST.json`, `CHECKSUMS.sha256`.

Verify: `python code/verify_package.py --self-test` (re-derives every number above from `data/`, then plants regressions and asserts each is caught).

## Licence

Data: CC-BY-4.0. Code: MIT. The verbatim `provenance_quote` excerpts are the publishers'/authors' copyright and are not covered by either licence.