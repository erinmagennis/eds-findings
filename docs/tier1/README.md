# eds-findings-v0 — Ehlers-Danlos syndromes (all 2017-classification types) and hypermobility spectrum disorder

> **Tier 1 (2019-2026) build of eds-findings v0: model-extracted, not human-adjudicated; 1,718 papers shared with pots-findings are referenced via the registry (qa/paper_registry_v1.parquet) and not re-extracted here.**

**7,101 machine-extracted findings from 679 papers** (Tier 1 — EDS/HSD core literature 2019-2026 (papers not already extracted in pots-findings); chunks [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29]; years 2019–2026; source stream Europe PMC; extraction model `claude-sonnet-5`).

## Read this before the data

**Scientific fidelity is unmeasured.** Every check in `qa/` verifies well-formedness — that a quote is verbatim in its source, that an identifier denotes the named entity, that counts conserve. Nothing here tests whether a finding correctly interprets its paper. Every row is a pointer to a paper worth reading, not a fact to cite.

`condition` semantics: **population** — `condition` names the patient POPULATION the paper studied. A collagen-biology paper with no EDS/HSD cohort is `general` even if it informs EDS; a paper that carries an EDS/HSD label without studying that population is a bucket violation, recorded in the additive annotations table and never corrected in place. Definitions version: eds dry-run v1 (2026-09-30); EDS_hypermobility inherited from CONDITION_BUCKET_DEFINITIONS.md v1 and split (full text in `docs/METHODS.md`).

## Measured numbers (each recomputed at build time and re-derived by `code/verify_package.py`)

| metric | numerator | denominator | value | basis |
|---|---|---|---|---|
| papers yielding >= 1 finding | 679 | 766 | 88.64% | papers with >=1 finding / papers in the frozen roster (all dispatched) |
| findings / paper (overall) | 7101 | 679 | 10.46 | pooled; see strata below — composition drives this number |
| findings / paper — abstract-only | 2215 | 391 | 5.66 | per fulltext_status stratum |
| findings / paper — retrieved-fulltext | 4886 | 288 | 16.97 | per fulltext_status stratum |
| rows with a resolved entity id | 1079 | 7101 | 15.2% | COVERAGE, not correctness (id_denotes_entity_rate not yet measured) |
| rows with >= 1 verified HPO id | 3236 | 7101 | 45.57% | exact label or authority-asserted synonym, OLS4 |
| rows with HGNC id | 988 | 1041 | 94.91% | of rows carrying a gene_marker; MyGene symbol lookup |
| number_in_quote false | 327 | 3859 | 8.47% | of rows carrying a number; an open question, never auto-fixed |

### Quote traceability census (with wrong-paper negative control)

| stratum | checked | traceable | rate | no source | control false-positives | control verdict |
|---|---|---|---|---|---|---|
| abstract-only | 2215 | 2210 | 99.77% | 0 | 0/400 (0.0%) | matcher discriminates |
| full-text (archive-backed) | 4886 | 4859 | 99.45% | 0 | 0/400 (0.0%) | matcher discriminates |
| blocked (fetch failed; extracted from title+abstract) | 0 | 0 | — | 0 | — | NO CONTROL POSSIBLE (<2 papers with source text in this stratum) - rate reported WITHOUT a control; do not cite it alone |

Coverage: 7101 of 7101 quote-bearing rows (100.0%) could be checked against retained source text; 7069 traced (99.55% of checkable).

### Invariants — 8/9 pass — **declared failures below, not repaired**

Chunks in the frozen roster with NO rows in the data: [17] (expected chunks 30, chunks with rows 29). The conservation invariant fails on purpose for these; the basis for each is in `scope/`.

| check | result | detail |
|---|---|---|
| schema_completeness | ok | {"missing": []} |
| conservation | **FAIL** | {"absent_chunks": [17], "over_allotment": []} |
| monotonicity | ok | {"rows": 7101, "papers": 679, "prior_rows": 0, "prior_papers": 0} |
| no_model_emitted_ids | ok | {"violations": {}} |
| id_provenance | ok | {"id_without_source": {}} |
| enum_drift | ok | {"flagged": {"evidence_tier": {"case-report/case-series": 2}}, "note": "flagged for adjudication, never auto-remapped"} |
| duplicates | ok | {"fully_identical_rows": 0, "loose_key_groups": 22, "note": "loose>full is expected and NOT a defect; only fully-identical rows are duplicates"} |
| hpo_multivalue | ok | {"mismatch": 0, "n_with_ids": 3236, "n_multi": 1031} |
| authority_prefix_match_v4 | ok | {"mismatches": {}, "n_mismatched_rows": 0, "n_mismatched_ids": 0, "n_rows_checked": 4315, "n_ids_checked": 5973, "n_undeclared_tag_rows": 0, "n_minting_column_d |

### Screening (two arms)

1463 papers screened; final verdicts {"in_scope": 738, "off_topic": 569, "uncertain": 156}; LLM off-topic calls against a rule-arm anchor flagged for adjudication (extracted, `screen_verdict_uncertain=true`): 50; instrument alarm: True (see `qa/screen_summary.json`, `scope/`).

## Flags (rows kept, never deleted)

| flag | count |
|---|---|
| query_contamination | {"rows": 9, "papers": 2} |
| screen_verdict_uncertain | {"rows": 926, "papers": 106} |
| number_in_quote_true | 3532 |
| number_in_quote_false | 327 |
| number_in_quote_null_no_number | 3242 |

## Files

`data/` findings (parquet + csv.gz) and the frozen roster/chunk map · `docs/` this file, DATA_DICTIONARY, METHODS, LIMITATIONS, COMPARABILITY · `qa/` invariants, quote census, screening summary, gate log, package_stats · `scope/` screening report and flagged/excluded lists · `code/` the pipeline generation that produced the rows, rendered prompt and scope, config, verifier · `MANIFEST.json`, `CHECKSUMS.sha256`.

Verify: `python code/verify_package.py --self-test` (re-derives every number above from `data/`, then plants regressions and asserts each is caught).

## Licence

Data: CC-BY-4.0. Code: MIT. The verbatim `provenance_quote` excerpts are the publishers'/authors' copyright and are not covered by either licence.