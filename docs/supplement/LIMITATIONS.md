# LIMITATIONS

1. **Scientific fidelity is unmeasured** (FIDELITY.md): no human reader has scored a pre-registered worksheet against source papers.
2. **`id_denotes_entity_rate` is not measured** on this package; `id_resolved_frac` is coverage and ranks builds backwards.
3. **Enum drift is flagged, never coerced**: {}.
4. **Screening uncertainty is carried, not resolved**: 0 papers extracted with `screen_verdict_uncertain=true`; adjudicate before pooling.
5. **Findings per paper is a composition statistic** — compare per `fulltext_status` stratum only (COMPARABILITY.md).
6. **The prompt still carries two POTS-era fields** (`is_covid_associated`, the `Flagged COVID-related` header) that have no meaning for this area; read them as null-equivalent.
7. **Nosology supplement: papers PubMed indexes under EDS that the 2017-classification screen excluded (historically classified as EDS, and adjacent heritable connective-tissue disorders); every row carries scope_class; see code/NOSOLOGY.md. Well-formedness QA only; fidelity not human-adjudicated.**