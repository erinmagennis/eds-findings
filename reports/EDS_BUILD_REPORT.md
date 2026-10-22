# eds-findings v0 — build report (Tier 1, 2019–2026)

Built 2026-10-07/08 with `condition-findings-builder` v13 (`config/eds.yaml`), model `claude-sonnet-5`.
Package: `eds-findings-v0.tar.gz` (111 files, 8.00 MB compressed / 10.45 MB unpacked; measured on the shipped artifact c8926a5d) — verifier PASS (19/19 headline numbers re-derived, 6/6 planted regressions, 109/109 checksums verified).
**Every number below is well-formedness. Scientific fidelity is unmeasured** (no human scoring; `id_denotes_entity_rate` not measured).

## 1. Deviations from the approved plan — read first

| # | what happened | consequence | what changed |
|---|---|---|---|
| D1 | **Wave 2, first attempt (2026-10-07 12:07–15:09Z): a system-wide stall.** All six in-flight workers ran >3,000 s and saved nothing (one was a 7-paper chunk); the per-unit watchdog killed each, re-split it and re-dispatched into the same stall — twice; then one transient artifact-store query failure killed the controller. The orchestrator was in long waits and did not look at the build for ~3 h. **Cause (identified 2026-10-08 when the two orphaned workers were collected): the Claude sign-in expired mid-wave — workers failed with "No credentials available for Anthropic API" / "session is no longer valid", so every model call hung regardless of unit size.** | ~3 h wall clock and the in-flight partial work of 8 units lost. **No completed findings lost** (chunks 0–11 were already in the store). | `driver.py`: system-stall breaker (no unit completes for max(1500 s, 2.5× longest completed unit) and every in-flight unit is older than that → stop workers, defer units, never re-dispatch into the stall); store-query retry (4×, 20 s); `dispatch_deadline` (no new unit after the user's window end − margin). Controller runs one wave per call with the ledger saved to the store between waves. Tests 69/69 under both configs. Supervision cadence: ≤15-min checks. |
| D2 | **Pilot done-detection collision.** The dry run's `eds_chunk_000_part*of3_*` (2026-09-30, 75-paper roster) satisfied done-detection for the full build's chunk 0, which was skipped. | Caught at the pilot gate review; chunk 0 extracted afterwards. | `artifacts.build_after` cutoff in config, forwarded to every store query (test `build_after_cutoff`). **Any gather of `eds_chunk_*` files must apply it.** |
| D3 | **Pilot chunks changed** from (15, 0) to (8, 0): chunk 15 shrank to 7 papers after screening. | Cost model refit on 4 units spanning both ends (23 and 2 full texts). | — |
| D4 | **Roster v3 differs from v2**: 569 screen-excluded and 128 title-only (no abstract in Europe PMC or PubMed, no full text expected) papers removed; chunk ids frozen from v2 (sizes 6–47). 15 title-only papers *with* full text expected were kept. | 766 papers extracted instead of 1,463; projection fell from 15.0M to 7.3M output tokens. | `plan_parts` caps parts at ⌈papers/25⌉ so screened-down chunks run as one unit. |
| D5 | **Yield gate false alarms.** Chunk 17 (6 papers: 5 abbreviation collisions — HED = handheld electronic device / human equivalent dose / hybrid experimental design / HCC Early Detection Strategy — plus one erratum) correctly yielded 0 three times. Chunks 25–29 hold the 223 POTS no-yield papers by construction; chunk 25 passed on attempt 3 (0.60), chunk 26 failed three times (0.41–0.47) and is shipped from its last attempt. | ~0.6M tokens on retries that could not have changed the outcome. | Wave 5 ran with `min_yield` 0.40 for chunks 27–29 (recorded in `qa/build_state.json`). Open: the gate needs a per-chunk prior (screen-uncertain share, sibling no-yield share). |
| D6 | **Retry versions.** Where a unit was retried, the workers saved same-named files as separate artifacts; the build takes the latest per filename (`qa/unit_files_used.csv` lists the exact version ids). | Chunk 26 ships 166 findings (attempt 3) rather than 220 (attempt 2). | Rule written into the README and resume note. |
| D7 | **Pass-2 population check is a model judging a model.** 485/541 "no listed population" verdicts and the 385 resulting exclusions were produced by `claude-sonnet-5` over `claude-sonnet-5` screen output, with verbatim-span checks (519/541) but no human sample. | Exclusions are auditable (`scope/excluded_pmids_v3.csv` carries both spans) but not adjudicated. | A 100-paper human sample of the exclusions is the first adjudication task to schedule. |
| D8 | Workspace sweep on 2026-10-08 lost the pilot and wave 0–1 per-unit gate logs; totals survive in `qa/build_state.json` and the units' own stats remain in their worker frames. | `qa/gate_log.json` covers waves 2–5 only (27 units + retries). | Ledger now saved to the store after every wave. |

## 2. What was built

| | |
|---|---|
| universe (Tier-5 draw, 1936–2026) | 8,455 PMIDs; 3,093 in 2019–2026 |
| shared with pots-findings | 1,941 (62.8% of the window): 1,718 with POTS findings → **referenced, not re-extracted**; 223 dispatched in POTS without yield → re-rostered here (chunks 25–29) |
| roster v1 → v2 → v3 | 1,240 new papers → +223 = 1,463 → −569 screen-excluded −128 title-only = **766 papers / 30 chunks** (308 full text expected) |
| two-pass screen | pass 1 (two arms): in_scope 738 / uncertain 541 / off_topic 184; pass 2 (population check over the 541): 485 no / 53 yes / 3 errors; final: in_scope 738 / uncertain 156 (132 rostered, extracted + flagged) / off_topic 569 |
| extraction | pilot (chunks 8, 0) + 6 waves; 45 planned units, 27 + retries in waves 2–5; unit wall median 602 s, max 1,435 s; blocked papers 3 |
| output | **7,101 findings / 679 papers** (88.6% of rostered; full-text papers 93.8%, abstract-only 85.2%, screen-uncertain 80.3%, POTS no-yield chunks 55.7%) |

Rows by `condition` (population): EDS_other_type 2,410 (296 papers) · general 1,200 (199) · hypermobility_unspecified 1,141 (185) · multiple 978 (151) · EDS_unspecified_or_mixed 646 (163) · hEDS 529 (94) · HSD 197 (30).
`eds_type` on EDS_other_type rows: vascular 806 · classical 427 · brittle cornea syndrome 311 · not specified 287 · classical-like 195 · kyphoscoliotic 177 · other named 118 · musculocontractural 111 · spondylodysplastic 92 · periodontal 65 · dermatosparaxis 36 · myopathic 24 · cardiac-valvular 23 · arthrochalasia 20 (+1 spelling variant "arthrochalasis", flagged).
Finding types: mechanism/biomarker 2,355 · comorbidity 1,474 · treatment/intervention 1,231 · symptom-burden 1,027 · diagnostic/screening 1,014. Evidence tiers: cross-sectional 1,382 · case-report 1,294 · review/expert-opinion 1,210 · case-series 1,026 · cohort 1,017 · mechanistic 567 · case-control 521 · meta-analysis 43 · RCT 39 · 2 rows outside the enum (`case-report/case-series`, flagged not coerced).

## 3. QA (well-formedness)

| check | result |
|---|---|
| invariants (package) | 8/9 — conservation FAILS on chunk 17 (declared; the roster chunk has no rows). Builder and verifier now measure conservation against the *frozen roster's* chunk list (`expected_chunk_ids`), not the data's — before this fix an absent chunk could never fail. |
| quote census (archive-first, 3 strata) | 7,069/7,101 traceable = **99.55%**; abstract-only 2,210/2,215 (99.77%), full text 4,859/4,886 (99.45%); negative control 0/400 and 0/400 false positives; blocked stratum empty. Untraceable rows are mostly ellipsis-joined quotes. |
| identifier resolution (stage 5, OLS4/UniProt/MyGene) | 5,364 lookups, 0 errors, 50 min; cache 5,728 entries. Entity rows with an authority id 1,079/4,681 (23.1%): anatomical 43.5%, chemical 65.2%, cell type 38.0%, gene/protein via UniProt (see `qa/resolve_report.json`). HPO verified on 3,236/5,592 rows with phenotype terms (57.9%); 865/3,734 distinct terms resolve. HGNC on 988/1,041 gene-marker rows (94.9%). Worker vs stage agreement 189/189; stage-only 890. |
| guard / parse | 0 guard violations, 0 parse failures across all shipped units |
| registry v1 | 22,805 rows; 12/12 invariants; eds-findings: referenced 1,718 · extracted 679 · dispatched_no_yield 87 · excluded_screen 569 · not_extractable 128 · planned (pre-2019, deferred) 5,362 |

## 4. Spend vs projection

| stage | projected | measured |
|---|---|---|
| pilot (chunks 8, 0; 75 papers) | — | 716,680 output tokens; fit: 18,024 / full-text paper, 5,326 / abstract paper (dry-run model 22,600 / 5,500) |
| remaining 28 chunks (691 papers, 283 full text) | 7,273,800 | 8,045,638 (**+10.6%**, under the 20% stop line; includes 0.6M of yield-gate retries) |
| by wave | w0 1.92M · w1 1.82M · w2 1.12M · w3 0.91M · w4 0.94M · w5 0.56M | w0 1.93M · w1 1.95M · w2 1.29M · w3 1.00M · w4 1.49M · w5 0.39M |
| extraction total | ≈8.0M | **8,762,318** |
| screen + population check | — | ≈270k (6 screen units ≈45k each) + 93k |
| wall clock | — | extraction ≈6.5 h of productive controller time plus the 3-h stall; resolution 50 min |

## 5. Shared-vs-new accounting (the connections question)

- Paper level: complete and explicit in the registry — 1,718 referenced, 223 → 88 → 49 re-extracted here after POTS yielded nothing on them.
- Finding level: **not built**. Lexical overlap only: 694/2,150 EDS entity names (32.3%) have an exact POTS twin, 328 of them in POTS `EDS_hypermobility` rows; 108/571 raw gene-marker strings; after collapsing HGVS variant strings to symbols, 116/188 EDS gene symbols have a POTS twin and all 20 config EDS-causal genes present in eds-findings also appear in pots-findings. See `CONNECTIONS_DESIGN.md` for the ledger design, controls and what waits for adjudication.

## 6. Known gaps carried forward

1. No human fidelity scoring; pass-2 exclusions unadjudicated (D7).
2. `eds_type` leaks onto 283 non-`EDS_other_type` rows (prompt-instruction violation; flagged).
3. Yield gate lacks a per-chunk prior (D5); watchdog re-split on stall replaced by the breaker but the two still share a threshold.
4. All-or-nothing worker save: a killed unit saves nothing (bounded now by the dispatch deadline, not removed).
5. POTS-era `is_covid` prompt fields; double full-text fetch; POTS templates unused by the generic builder; `screen_instrument_alarm` degenerate on anchor rosters (documented in `qa/screen_summary.json`).
6. Pre-2019 EDS literature (5,362 universe papers) deferred.
7. Pilot / wave 0–1 unit logs lost (D8).

## 7. Artifacts

`eds-findings-v0.tar.gz` · `eds_findings_resolved.parquet` (7,101 rows) · `eds_findings_merged_raw.parquet` · `paper_registry_v1.parquet` + `registry_v1_report.json` · `eds_tier1_roster_v3.parquet` + excluded / not-extractable lists · `screen_eds_tier1_final.csv` · `build_state.json`, `wave_2..5.json`, gate logs · `quote_census.json`, `invariants.json`, `resolve_report.json`, `resolution_cache.parquet` · `CONNECTIONS_DESIGN.md` · `condition-findings-builder.tar.gz` v13 · `EDS_RESUME.md`.
