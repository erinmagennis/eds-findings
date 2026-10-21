# Contradictions typed by factor — v1 (2026-10-21)

Every contradicted cell in claim graph v1 (1,313 cells: same corpus, entity, condition bucket and reference frame, with *up* asserted by some papers and *down* by others) is decomposed into (up paper, down paper) pairs — 15,016 pairs after removing papers that assert both directions. For each pair the extractor-recorded **comparator**, **population age group**, **study design** and **era** (pre-1997 / 1997–2016 / 2017+) are compared.

| pair outcome | pairs |
|---|---:|
| matched — every stated factor agrees | 479 |
| explained — at least one stated factor differs | 10,345 |
| undetermined — a factor unstated on one side, none differs | 4,192 |

| cell type | cells |
|---|---:|
| genuine-disagreement candidate (≥1 matched pair) | 178 |
| explained by factors (every pair differs) | 556 |
| mostly explained (no matched pair; some undetermined) | 334 |
| undetermined (factors unstated) | 245 |

Which factor differs most often across pairs: population 9,953 · design 8,122 · comparator 5,241 · era 2,248. Only 3.2% of opposing-direction paper pairs match on every recorded factor — most apparent contradictions dissolve into "measured something different". The 178 candidate cells, ranked by matched pairs in `contradictions_typed_v1.csv` (with one matched pair cited per cell), are where the literature may actually disagree with itself: those are the research questions.

**What this is not.** The factors are the extractor's own fields and are unaudited; the comparator and population vocabularies are coarse ('other patient group', 'adult'); and a matched pair can still differ in assay, tissue or outcome definition that the row does not record. The 25 human-read pairs in `first_reads_v1_pairs.csv` are the calibration set: if their `contradiction_class` agrees with this typing, the typing can be trusted for the rest; if not, the vocabulary the reader used in `note` becomes the next typing scheme.
