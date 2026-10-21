# eds-findings-nosology-supplement-v0 — Ehlers-Danlos syndromes (all 2017-classification types) and hypermobility spectrum disorder

> **Nosology supplement: papers PubMed indexes under EDS that the 2017-classification screen excluded (historically classified as EDS, and adjacent heritable connective-tissue disorders); every row carries scope_class; see code/NOSOLOGY.md. Well-formedness QA only; fidelity not human-adjudicated.**

**343 machine-extracted findings from 45 papers** (Nosology supplement — papers PubMed indexes under EDS that the 2017-classification screen excluded: historically classified as EDS (reclassified, e.g. occipital horn syndrome / EDS IX) and adjacent heritable connective-tissue disorders; 1970-2026; each row carries scope_class; chunks [150, 151]; years 1977–2025; source stream Europe PMC; extraction model `claude-sonnet-5`).

## Read this before the data

**Scientific fidelity is unmeasured.** Every check in `qa/` verifies well-formedness — that a quote is verbatim in its source, that an identifier denotes the named entity, that counts conserve. Nothing here tests whether a finding correctly interprets its paper. Every row is a pointer to a paper worth reading, not a fact to cite.

`condition` semantics: **population** — `condition` names the patient POPULATION the paper studied. A collagen-biology paper with no EDS/HSD cohort is `general` even if it informs EDS; a paper that carries an EDS/HSD label without studying that population is a bucket violation, recorded in the additive annotations table and never corrected in place. Definitions version: eds dry-run v1 (2026-09-30); EDS_hypermobility inherited from CONDITION_BUCKET_DEFINITIONS.md v1 and split (full text in `docs/METHODS.md`).

## Measured numbers (each recomputed at build time and re-derived by `code/verify_package.py`)

| metric | numerator | denominator | value | basis |
|---|---|---|---|---|
| papers yielding >= 1 finding | 45 | 53 | 84.91% | papers with >=1 finding / papers in the frozen roster (all dispatched) |
| findings / paper (overall) | 343 | 45 | 7.62 | pooled; see strata below — composition drives this number |
| findings / paper — abstract-only | 204 | 37 | 5.51 | per fulltext_status stratum |
| findings / paper — retrieved-fulltext | 139 | 8 | 17.38 | per fulltext_status stratum |
| rows with a resolved entity id | 54 | 343 | 15.74% | COVERAGE, not correctness (id_denotes_entity_rate not yet measured) |
| rows with >= 1 verified HPO id | 100 | 343 | 29.15% | exact label or authority-asserted synonym, OLS4 |
| rows with HGNC id | 82 | 92 | 89.13% | of rows carrying a gene_marker; MyGene symbol lookup |
| number_in_quote false | 13 | 102 | 12.75% | of rows carrying a number; an open question, never auto-fixed |

### Quote traceability census (with wrong-paper negative control)

| stratum | checked | traceable | rate | no source | control false-positives | control verdict |
|---|---|---|---|---|---|---|
| abstract-only | 203 | 203 | 100.0% | 1 | 0/203 (0.0%) | matcher discriminates |
| full-text (archive-backed) | 139 | 139 | 100.0% | 0 | 0/139 (0.0%) | matcher discriminates |
| blocked (fetch failed; extracted from title+abstract) | 0 | 0 | — | 0 | — | NO CONTROL POSSIBLE (<2 papers with source text in this stratum) - rate reported WITHOUT a control; do not cite it alone |

Coverage: 342 of 343 quote-bearing rows (99.71%) could be checked against retained source text; 342 traced (100.0% of checkable).

### Invariants — 9/9 pass

| check | result | detail |
|---|---|---|
| schema_completeness | ok | {"missing": []} |
| conservation | ok | {"absent_chunks": [], "over_allotment": []} |
| monotonicity | ok | {"rows": 343, "papers": 45, "prior_rows": 0, "prior_papers": 0} |
| no_model_emitted_ids | ok | {"violations": {}} |
| id_provenance | ok | {"id_without_source": {}} |
| enum_drift | ok | {"flagged": {}, "note": "flagged for adjudication, never auto-remapped"} |
| duplicates | ok | {"fully_identical_rows": 0, "loose_key_groups": 2, "note": "loose>full is expected and NOT a defect; only fully-identical rows are duplicates"} |
| hpo_multivalue | ok | {"mismatch": 0, "n_with_ids": 100, "n_multi": 58} |
| authority_prefix_match_v4 | ok | {"mismatches": {}, "n_mismatched_rows": 0, "n_mismatched_ids": 0, "n_rows_checked": 154, "n_ids_checked": 271, "n_undeclared_tag_rows": 0, "n_minting_column_dis |

## Flags (rows kept, never deleted)

| flag | count |
|---|---|
| query_contamination | {"rows": 0, "papers": 0} |
| screen_verdict_uncertain | {"rows": 0, "papers": 0} |
| number_in_quote_true | 89 |
| number_in_quote_false | 13 |
| number_in_quote_null_no_number | 241 |

## Files

`data/` findings (parquet + csv.gz) and the frozen roster/chunk map · `docs/` this file, DATA_DICTIONARY, METHODS, LIMITATIONS, COMPARABILITY · `qa/` invariants, quote census, screening summary, gate log, package_stats · `scope/` screening report and flagged/excluded lists · `code/` the pipeline generation that produced the rows, rendered prompt and scope, config, verifier · `MANIFEST.json`, `CHECKSUMS.sha256`.

Verify: `python code/verify_package.py --self-test` (re-derives every number above from `data/`, then plants regressions and asserts each is caught).

## Licence

Data: CC-BY-4.0. Code: MIT. The verbatim `provenance_quote` excerpts are the publishers'/authors' copyright and are not covered by either licence.