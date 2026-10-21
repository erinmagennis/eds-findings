# Claim graph v1 — authority-id merging (2026-10-21)

**Change from v0.** Entity strings that resolved to the same authority identifier are one entity. Only
**authority-verified** ids are used: in POTS v1.1 that is `ontology_id_verification == authority_verified`
(5,727 rows); the 16,662 `unverified_model_emitted` ids are ignored. EDS ids are all authority-resolved by the
resolution stage. A verified id is propagated to spellings that share the same case/punctuation skeleton as a
resolved spelling. 21,268 of 105,298 entity rows are now keyed by a verified id; 709 ids merge more than one
spelling (`id_merges_v1.csv`, with a `token_disjoint` column marking abbreviation/synonym pairs such as
PGE2 / prostaglandin E2 for audit).

**What the first attempt found.** Using *all* POTS ids produced 454 merges whose spellings shared no token —
e.g. CHEBI:145994 carried by remdesivir, pembrolizumab and nirmatrelvir-ritonavir; CHEBI:135729 by midostaurin and
zoledronic acid. Every one came from an `unverified_model_emitted` id. The POTS package already flags those rows;
this is the concrete demonstration of why they must never drive a join. Restricting to verified ids removed all
454.

**Numbers.** 44,873 claims (v0 44,974): 1 paper 39,082 · 2 papers 2,726 · 3–4 1,260 · 5–9 557 · 10+ 248 — the
shape is unchanged because most entity rows have no verified id, so most spellings still stand alone. 1,313
contradicted cells (273 with ≥2 papers each side); 817 entities in both corpora. `support_frac_of_condition_papers`
gives corpus-relative support (supporting papers / papers in that corpus × condition cell): the top EDS claim,
joint hypermobility ↑ in hypermobility_unspecified, rests on 86 papers = 16.8% of that cell's papers.

**Still open.** Unresolved spellings ('HR' vs 'heart rate' where neither resolved) remain separate; the next merge
evidence would be the POTS `entity_vocabulary_crosstier.parquet` lookup table (planted-control validated) and a
reviewed synonym list — never string similarity alone. Condition buckets are still not crosswalked between corpora.
