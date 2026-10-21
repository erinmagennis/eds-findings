# LIMITATIONS

1. **Scientific fidelity is unmeasured** (FIDELITY.md): no human reader has scored a pre-registered worksheet against source papers.
2. **`id_denotes_entity_rate` is not measured** on this package; `id_resolved_frac` is coverage and ranks builds backwards.
3. **Enum drift is flagged, never coerced**: {"evidence_tier": {"systematic-review/meta-analysis": 2}}.
4. **Screening uncertainty is carried, not resolved**: 295 papers extracted with `screen_verdict_uncertain=true`; adjudicate before pooling.
5. **Findings per paper is a composition statistic** — compare per `fulltext_status` stratum only (COMPARABILITY.md).
6. **The prompt still carries two POTS-era fields** (`is_covid_associated`, the `Flagged COVID-related` header) that have no meaning for this area; read them as null-equivalent.
7. **Tier-2 (pre-2019) EDS findings, first build; well-formedness QA only — scientific fidelity of extracted findings has not been human-adjudicated.**