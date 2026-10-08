# Connections layer between `pots-findings` and `eds-findings` — design note (v0, 2026-10-08)

Status: design only. Nothing here is built; the numbers are measured on the two corpora as they stand today
(pots-findings v1.1: 148,186 findings / 11,519 papers; eds-findings Tier 1 raw merge: 7,101 findings / 679 papers,
identifier resolution still running). Every connection described below is a *candidate* for human adjudication,
not a result.

## 1. What already connects the two corpora (measured)

| link | count | basis |
|---|---|---|
| papers in both universes | 1,941 | EDS Tier-5 universe ∩ POTS corpus (2019–2026 window: 62.8%) |
| …extracted in POTS with findings, referenced (not re-extracted) in EDS | 1,718 | registry `reference_only=True` |
| …dispatched in POTS with no findings, re-extracted in EDS chunks 25–29 | 223 → 88 survived the screen → 49 yielded | `qa/build_state.json`, roster v3 |
| POTS rows in the `EDS_hypermobility` bucket | 13,034 rows / 1,818 papers | POTS `condition` contains `EDS_hypermobility` |
| entity names shared (normalised string equality only) | 694 of 2,150 EDS entities (32.3%); 328 also appear in POTS `EDS_hypermobility` rows | lower-cased exact match — a floor, not a measurement of concept overlap |
| gene markers shared | 108 of 571 EDS / 6,965 POTS | upper-cased exact match; EDS `gene_marker` carries HGVS variant strings (e.g. `ADAMTS2 c.769C>T; p.Arg257Ter`), so the true overlap is higher once variants are collapsed to genes |

What this says: the paper-level link is already explicit and complete (the registry). The *finding*-level link does not
exist yet, and string equality is the wrong instrument for it — 68% of EDS entity names have no exact POTS twin, and most
of that is lexical variation, not biology.

## 2. The object to build: an entity × population membership ledger

One table, both corpora, one row per (canonical entity, population, corpus, finding_id):

| column | meaning | source |
|---|---|---|
| `entity_canonical_id` | ontology id after resolution (HGNC for genes — variants collapsed; HPO/UBERON/CHEBI/CL/UniProt otherwise); unresolved names kept under a `name:` pseudo-id | stage-5 resolver, both corpora (POTS Tiers 1–3 have model-emitted ids: mark `id_provenance`) |
| `population` | the *studied population*, one of a shared vocabulary that maps both enums: POTS buckets (POTS, ME_CFS, long_COVID, EDS_hypermobility, MCAS, dysautonomia, SFN, autism, migraine, IBS_gut, fibromyalgia) and EDS buckets (hEDS, HSD, EDS_other_type + `eds_type`, EDS_unspecified_or_mixed, hypermobility_unspecified) | `condition` + `eds_type`; `multiple`/`general` rows carry the finding's own population list, not the bucket |
| `direction`, `finding_type`, `evidence_tier`, `n_participants` | as extracted | both corpora |
| `provenance_quote`, `pmid`, `corpus`, `tier`, `prompt_ref` | traceability | both corpora + registry |
| `membership_status` | `adjudicated` / `model_only` | human adjudication table (empty at v0) |

**Why population, not bucket.** The POTS bucket `EDS_hypermobility` and the EDS buckets `hEDS`/`HSD`/`EDS_other_type` are the
same axis at two granularities (the registry's open question). The ledger stores the finer label where it exists and a mapping
`EDS_hypermobility → {hEDS, HSD, hypermobility_unspecified, EDS_unspecified_or_mixed}` so queries can be run at either level —
without rewriting either corpus.

## 3. Connection classes the ledger makes queryable

1. **Overlap (same entity, same population, both corpora)** — e.g. a marker reported for hEDS in an EDS-extracted paper and for `EDS_hypermobility` in a POTS-extracted paper. These are the cheapest to adjudicate: two independent extractions of (often) different papers agreeing or disagreeing on direction.
2. **Bridge (same entity, different populations)** — an entity found in hEDS *and* in POTS/MCAS/ME-CFS rows. This is the question the user actually asked ("connections where they overlap"). Report as a contingency table entity × population with counts of papers (not findings) and the direction split; rank by number of *distinct papers* on both sides, never by finding count.
3. **Speculative (entity in one corpus only, population adjacent)** — EDS-only entities (e.g., of the 21 EDS-causal genes in `config/eds.yaml`, those with an EDS-extracted row and no POTS row at gene-symbol level: none — 20 of the 21 appear in eds-findings and every one of those also appears in pots-findings at symbol level (PYCR1 is absent from both); at symbol level 116 of 188 EDS gene symbols have a POTS twin) that have no POTS row at all. These are *hypotheses* by construction; the ledger marks them `speculative` and lists what evidence would move them (a POTS/dysautonomia paper measuring the same entity).
4. **Contradictions** — same entity, same population, opposite `direction`. Surface, do not resolve.

## 4. Controls (so the layer can be trusted before anyone reads a result from it)

- **Lexical-collision control:** sample 200 shared entity names and have a reviewer mark whether the two corpora mean the same concept (expected failure mode: generic terms — "collagen", "pain", "heart rate").
- **Population-leak control:** `eds_type` currently leaks onto 283 non-`EDS_other_type` rows and POTS `multiple`/`general` rows carry mixed populations. Rows with ambiguous population go to the ledger as `population=unresolved` and are excluded from bridge counts until adjudicated.
- **Negative control for bridges:** shuffle population labels within corpus and recompute the bridge table; any entity whose observed bridge count is not above the shuffled 95th percentile is not reported.
- **Direction-agreement control:** for overlap rows, the agreement rate between corpora on `direction` is a (weak) fidelity signal; report it with its base.

## 5. What waits for human adjudication (and what does not)

Does not wait: building the ledger, the canonicalisation (resolver), the contingency tables, the controls, and the ranked
candidate lists — all mechanical, all traceable to quotes.

Waits for the user's ~10–15 h: (a) the membership adjudication of the ~50 highest-ranked bridge entities (both quotes side by side,
one decision each); (b) the population mapping decision between the two enums (one meeting, written down in `config/`);
(c) any marker-evidence ledger or shortlist — explicitly deferred per the 2026-10-07 decisions: a shortlist drawn from
unadjudicated model output would be ~50,000 unverified rows on top of 148,000.

## 6. Build order (when approved)

1. Finish stage-5 resolution for eds-findings (running) and apply the same resolver to the POTS `EDS_hypermobility` rows (13,034 rows; the POTS Tier-4 rows already carry authority ids).
2. Variant→gene collapse for `gene_marker` (HGVS regex + HGNC lookup); emit a mapping file with every collapse listed.
3. Population vocabulary file + the two bucket mappings, config-gated.
4. Ledger build + the four controls + ranked candidate tables (papers-based), shipped as one artifact set with a README that leads with the controls.
5. Adjudication sheet for the top 50 bridges; everything else stays `model_only`.
