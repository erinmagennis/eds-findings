# eds-findings

Machine-extracted research findings from the Ehlers-Danlos syndromes and hypermobility spectrum disorder literature,
published 1946 to 2026, with a verbatim source quote on every row. 24,200 findings from 3,558 papers, released as
four self-contained data packages. Built with [condition-findings-builder](https://github.com/erinmagennis/condition-findings-builder).
Project index: [condition-findings](https://github.com/erinmagennis/condition-findings).

This is machine-extracted research information, not medical advice. Read and cite the original paper before relying
on a finding.

## Packages

| package | what it covers | findings | papers | status |
|---|---|---:|---:|---|
| `eds-findings-v0.1` | 2019 to 2026 | 7,101 | 679 | verifier PASS |
| `eds-findings-tier2-v0.1` | 1946 to 2018 (the search window opened in 1936; no extractable paper predates 1946) | 15,860 | 2,605 | verifier PASS |
| `eds-findings-nosology-supplement-v0` | papers indexed under EDS in MeSH that the screen excluded: historical EDS types later reclassified, adjacent heritable connective tissue disorders. Every row carries `scope_class` | 343 | 45 | verifier PASS |
| `eds-findings-tier2-recovery-v0` | pre-2019 papers whose abstract had to be recovered from OpenAlex; rows are flagged `screen_verdict_uncertain` | 896 | 229 | verifier PASS |

Each package has a `README.md`, `docs/` (methods, data dictionary, limitations), `qa/`, `CHECKSUMS.sha256` and the code
that built it, and `code/verify_package.py --self-test` re-checks it end to end. The package tarballs are not stored in
this repository; `packages/<name>/` holds each package's README, MANIFEST, CITATION and checksums, and `ARTIFACTS.md`
says where the tarballs are.

The diagnosis itself changed in 1997 (Villefranche) and 2017 (international classification). Every row carries
`criteria_era` and the EDS type as the paper stated it; `docs/NOSOLOGY.md` in the packages explains how the old and new
names map and when they should not be pooled.

## What has been checked, and what has not

Checked by machine, with negative controls:
- every quote traces to its paper (Tier 2 fresh-fetch census: 1,414 of 1,420 rows, wrong-paper control 0 of 400)
- every entity identifier comes from an authority lookup on the entity name (exact label or authority synonym);
  names that did not match were left without an identifier, never guessed
- counts conserve across rosters, registry and packages; packages rebuild byte-identically
- extraction tier agrees with the publication type recorded by Europe PMC on 98.85% of rows

Not yet checked: whether each finding states what its paper found. A 200-finding fidelity sample and its worksheet
are in `audits/`; the human read is open. Until it is done, scientific fidelity is unmeasured and marker claims
should not be read off these tables.

## Layout

- `packages/` package metadata per release
- `docs/` package documentation per tier
- `qa/` QA outputs, paper registry reports, independent tests
- `scope/` screening lists and verdicts
- `audits/` fidelity sample, calibration report, exclusion audit, synonym candidates
- `claims/` cross-corpus claim graph over EDS and POTS (machine-derived; waits on the human reads)
- `data/` findings tables, rosters, registry, resolution cache
- `reports/` build reports and the resume specification

## Citation and licence

Derived structured data: CC BY 4.0. Verbatim quotes remain the copyright of their authors and publishers and are not
licensed by this project. Each package carries a `CITATION.cff`.

Credit: Erin Magennis.
