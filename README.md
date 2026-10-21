# eds-findings — EDS/HSD literature findings corpus (working repository)

Grounded, provenance-traced findings extracted from the Ehlers-Danlos / hypermobility literature, built with the
`condition-corpus-builder` method repo (sibling folder). Two tiers:

| tier | window | package | findings | papers | status |
|---|---|---|---:|---:|---|
| 1 | 2019–2026 | eds-findings-v0 | 7,101 | 679 | shipped 2026-10-08; verifier PASS |
| 2 | 1936–2018 (extractable 1946–2018) | eds-findings-tier2-v0 | 15,860 | 2,605 | shipped 2026-10-21; verifier PASS |

Combined table: `data/eds_findings_all_tiers.parquet` (22,961 rows / 3,284 papers, `tier` column). Paper registry across
EDS and POTS: `data/paper_registry_v2.parquet` (shared papers are referenced, never re-extracted).

**What is and is not claimed.** All QA to date is well-formedness: quotes trace to their papers (census 99.55% / 99.68%,
fresh-fetch 99.58%, wrong-paper controls 0%), identifiers come from authorities with exact-label or authority-synonym
agreement, counts conserve, packages rebuild byte-identically. Whether findings faithfully state what the papers found
is **not yet measured** — the fidelity audit is open (`audits/`, see `reports/EDS_TIER2_BUILD_REPORT.md` §7b) and awaits
human scoring. Do not read marker claims off these tables before that.

Layout: `reports/` build reports, design notes, resume spec · `qa/tier{1,2}/` QA outputs · `scope/tier{1,2}/` screening
lists · `docs/tier{1,2}/` package docs · `packages/` package README/MANIFEST/CHECKSUMS (tarballs in the artifact store,
see `ARTIFACTS.md`) · `data/` findings tables, rosters, registry, resolution cache · `audits/` fidelity and exclusion
worksheets. Reproduce a package: `condition-corpus-builder/build_packages.py --area-config config/eds*.yaml …`
(exact commands in the reports).
