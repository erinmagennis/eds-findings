# LIMITATIONS

1. **Scientific fidelity is unmeasured** (FIDELITY.md): no human reader has scored a pre-registered worksheet against source papers.
2. **`id_denotes_entity_rate` is not measured** on this package; `id_resolved_frac` is coverage and ranks builds backwards.
3. **Enum drift is flagged, never coerced**: {"evidence_tier": {"case-report/case-series": 2}}.
4. **Screening uncertainty is carried, not resolved**: 106 papers extracted with `screen_verdict_uncertain=true`; adjudicate before pooling.
5. **Findings per paper is a composition statistic** — compare per `fulltext_status` stratum only (COMPARABILITY.md).
6. **The prompt still carries two POTS-era fields** (`is_covid_associated`, the `Flagged COVID-related` header) that have no meaning for this area; read them as null-equivalent.
7. **Tier 1 (2019-2026) build of eds-findings v0: model-extracted, not human-adjudicated; 1,718 papers shared with pots-findings are referenced via the registry (qa/paper_registry_v1.parquet) and not re-extracted here.**