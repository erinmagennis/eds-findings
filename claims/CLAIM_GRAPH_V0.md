# Claim graph v0 — POTS + EDS (2026-10-21)

**What it is.** Findings rows aggregated into *claims*: (corpus, canonical entity, condition bucket, reference frame, direction).
Reference frame is the extractor's `direction_reference` (disease-state vs intervention) — without it, treatment effects
("histamine down after antihistamine") masquerade as contradictions of disease-state findings ("histamine up in MCAS").
Adding it removed 431 of 1,734 apparent contradictions. Support = distinct papers. Contradiction = up and down asserted by
disjoint paper sets within the same cell.

**Numbers (from `claim_graph_v0_summary.json`).** 105,298 rows with an entity (89,381 POTS / 15,917 EDS) → 44,974 claims;
40,196 rest on one paper, 2,744 on two, 1,244 on 3–4, 548 on 5–9, 242 on 10+. 1,303 entity×condition×reference cells are
contradicted (1,177 POTS / 126 EDS); 272 with ≥2 papers on each side, 111 with ≥3. 1,048 cells pit an effect against a null.
822 canonical entities appear in both corpora; of 603 entity pairs with a majority disease-state direction in each corpus,
465 agree and 138 disagree.

**What it is for.** (1) Human reading goes where claims rest: `first_reads_v0.csv` lists 75 findings — one up and one down
row for each of the 25 most-contradicted disease-state cells, and one row for each of the 25 best-supported claims.
(2) `cross_corpus_entities_v0.csv` is the first concrete input to the connections layer. (3) The pre-registered marker rule
(≥3 independent papers, ≥1 above case-series, no unresolved contradiction, top claims human-read) can be evaluated
against `claims_v0.parquet` once the human reads exist.

**Limits of v0.** Canonicalization is a punctuation/case skeleton (43,736 → 40,321 strings): `HR`/`heart rate`,
`IL-6`/`interleukin-6` are not merged, so support is a lower bound and some contradictions are split across spellings.
Condition buckets are not crosswalked between corpora. A contradiction may be real disagreement, a population or assay
difference, or an extraction error — only the read decides. Nothing here is a fidelity measurement.
