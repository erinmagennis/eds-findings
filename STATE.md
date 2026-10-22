# STATE — POTS and EDS literature-findings corpora (2026-10-21)

For anyone receiving these files. Every number below is read from a saved QA file inside the package it describes; the package's own `code/verify_package.py --self-test` re-derives the headline numbers from its data.

## What these are

Findings extracted from the biomedical literature by a pinned language model (`claude-sonnet-5`) under a fixed prompt, one row per finding, each row carrying a **verbatim provenance quote** from the paper, the paper's PMID, a condition (population) bucket, direction, evidence tier, and — where an authority confirmed it — ontology identifiers (UniProt, ChEBI, HPO, UBERON, CL, NCBITaxon) with the authority's own label. Two corpora, built with the same method repository (`condition-findings-builder`):

- **pots-findings v1.1** — 148,186 findings / 11,519 papers (2019–2026), 16 condition buckets (POTS, ME/CFS, long COVID, MCAS, dysautonomia, SFN, fibromyalgia, migraine, IBS, autism, EDS/hypermobility, …); five packages (t1 36,067 rows, t2 70,441, t3 26,032, t4 15,646). **v1.2 in preparation** (see "Known defects").
- **eds-findings** — four packages, 24,200 findings / 3,558 papers, no paper in more than one package; combined in `eds_findings_all_tiers.parquet`:

| package | findings | papers | quote census (traceable / checked; wrong-paper controls false positives) | verifier | sha256 | note |
|---|---:|---:|---|---|---|---|
| eds-findings-v0.1 (Tier 1, 2019–2026) | 7,101 | 679 | 7,069/7,101 (99.55%), controls 0/800 | PASS | `d673753a…2453` | screened; data identical to v0 |
| eds-findings-tier2-v0.1 (Tier 2, 1946–2018) | 15,860 | 2,605 | 15,809/15,859 (99.68%), controls 0/800 | PASS | `8e456d53…f880` | screened; data identical to v0 |
| eds-findings-nosology-supplement-v0 | 343 | 45 | 342/342 (100.00%), controls 0/342 | PASS | `f7de48d1…1427` | scope_class on every row; historical/adjacent |
| eds-findings-tier2-recovery-v0 | 896 | 229 | 853/896 (95.20%), controls 0/400 | PASS | `231f11c3…ebeb` | UNSCREENED (abstracts recovered from OpenAlex); every row screen_verdict_uncertain |

Shared papers: 1,718 EDS-relevant papers already extracted in pots-findings are referenced from the EDS registry, never re-extracted. Registry v4 (12/12 invariants): EDS papers extracted 3,558 · referenced 1,718 · excluded by screen 1,641 · not extractable (no text anywhere) 1,308 · dispatched, no yield 318.

Recall: of the 4,142 papers PubMed indexes under "Ehlers-Danlos Syndrome"[MeSH], 4,140 are in the EDS registry or the POTS corpus. The binding limit is text availability, not discovery.

## What has been verified — and what has not

**Verified (well-formedness), for every package:** quotes trace to their paper's own text (census above; EDS Tier 2 also 1,414/1,420 against freshly fetched text); identifiers come from authorities with exact-label or authority-synonym agreement, never fuzzy; row and paper counts conserve against the frozen roster; no duplicate rows; packages rebuild byte-identically from their inputs; every package passes its self-test (19 headline numbers re-derived, 6 planted regressions caught).

**Measured without any model in the loop (whole corpora):** evidence tier incompatible with the paper's indexed publication type — POTS 0.97% of rows, EDS 1.15%; numbers in `quantitative_detail` absent from the retained source — EDS ≤2.2% (upper bound); direction asserted with no comparison language in the quote — POTS 14.4%, EDS 23.1% of directional rows (upper bound).

**NOT verified: scientific fidelity** — whether a finding faithfully states what the paper found. A 200-finding human audit per corpus (10 questions, pre-registered thresholds in `FIDELITY.md`) is open; the judge-model calibration is done (EDS: 2 of 10 questions machine-extendable now; POTS: 3), and the human worksheets are blank. **Until they are scored, read these tables as provenance-traced extractions, not as established findings, and do not derive marker claims from them.**

## Known defects and how they are handled

- **POTS v1.1 carries 16,662 rows with model-emitted identifiers no authority confirmed** (`ontology_id_verification = unverified_model_emitted`). They are wrong often enough to corrupt joins (one ChEBI id shared by three unrelated drugs). **Do not join on them.** v1.2 re-resolves every one against its authority and nulls those that fail; the benchmark of how often model-emitted ids are right ships alongside.
- EDS `eds_type` values appear on 1,038 rows outside `EDS_other_type`; 2 rows carry an out-of-enum evidence tier; both kept and declared.
- Pre-1980 EDS literature is thin because its text is not available (1,308 papers remain title-only).
- 848 EDS Tier-2 exclusions rest on one model confirming another; a 100-paper human audit is open.

## Nosology
`condition` follows the 2017 International Classification of the EDS. Papers written under earlier classifications (Berlin 1988, Villefranche 1997) and adjacent heritable connective-tissue disorders are included in the supplement package with a `scope_class`; historical type numerals are mapped in `NOSOLOGY.md` and preserved verbatim in quotes.

## Analysis layers (derived; not part of any package)
Claim graph v1: 44,873 claims across both corpora (40,095 on one paper; 243 on 10+); 1,313 contradicted cells, of which 178 have at least one pair of opposing papers matched on comparator, population, design and era; 817 entities appear in both corpora. Condition crosswalk decisions recorded in `CONDITION_CROSSWALK.md`.

## How to verify a package
```
tar -xzf <package>.tar.gz && cd <package>
shasum -a 256 -c CHECKSUMS.sha256
python code/verify_package.py --self-test
```

## Cite
Magennis, E. (2026). *<package name>* [Data set]. See `CITATION.cff` in each package. Method: `condition-findings-builder` (sibling repository).
