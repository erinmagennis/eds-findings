# LIMITATIONS

1. **Scientific fidelity is unmeasured** (FIDELITY.md): no human reader has scored a pre-registered worksheet against source papers.
2. **`id_denotes_entity_rate` is not measured** on this package; `id_resolved_frac` is coverage and ranks builds backwards.
3. **Enum drift is flagged, never coerced**: {}.
4. **Screening uncertainty is carried, not resolved**: 229 papers extracted with `screen_verdict_uncertain=true`; adjudicate before pooling.
5. **Findings per paper is a composition statistic** — compare per `fulltext_status` stratum only (COMPARABILITY.md).
6. **The prompt still carries two POTS-era fields** (`is_covid_associated`, the `Flagged COVID-related` header) that have no meaning for this area; read them as null-equivalent.
7. **Tier-2 recovery: 323 pre-2019 EDS papers with no abstract in Europe PMC/PubMed whose abstract was recovered from OpenAlex; NOT screened (abstracts did not exist at screen time) — every row carries screen_verdict_uncertain=true. Well-formedness QA only; fidelity not human-adjudicated.**