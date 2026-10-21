# LIMITATIONS

1. **Scientific fidelity is unmeasured** (FIDELITY.md): no human reader has scored a pre-registered worksheet against source papers.
2. **`id_denotes_entity_rate` is not measured** on this package; `id_resolved_frac` is coverage and ranks builds backwards.
3. **Enum drift is flagged, never coerced**: {"evidence_tier": {"systematic-review/meta-analysis": 2}}.
4. **Screening uncertainty is carried, not resolved**: 295 papers extracted with `screen_verdict_uncertain=true`; adjudicate before pooling.
5. **Findings per paper is a composition statistic** — compare per `fulltext_status` stratum only (COMPARABILITY.md).
6. **The prompt still carries two POTS-era fields** (`is_covid_associated`, the `Flagged COVID-related` header) that have no meaning for this area; read them as null-equivalent.
7. **EDS findings, Tier 2 (pre-2019), v0.1 = v0 data unchanged plus: scope_class column (all rows eds_2017), NOSOLOGY.md (condition follows the 2017 International Classification of the EDS; historical type numerals are mapped in NOSOLOGY.md and preserved verbatim in quotes; reclassified and adjacent disorders are in the nosology-supplement package), registry v4, MeSH recall check, extra verification passes (byte-identical rebuild, fresh-fetch census, id correspondence), fidelity-judge calibration and blank audit worksheets. Well-formedness QA only; scientific fidelity has not been human-adjudicated (audit open).**