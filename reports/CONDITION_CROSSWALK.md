# CONDITION_CROSSWALK — POTS corpus ↔ EDS corpus condition buckets (draft v0, 2026-10-21; for approval)

Both corpora use `condition` as the **population the paper studied**. The enums differ in grain: POTS has one EDS bucket,
EDS has five. Nothing is relabelled by this document — it defines how the connections layer JOINs the two tables and
is written down before any cross-corpus number is reported.

## Measured basis

POTS `EDS_hypermobility`: 13,034 rows / 1,818 papers (2019–2026). Its written definition (CONDITION_BUCKET_DEFINITIONS.md v1)
covers EDS of any type, HSD, and Beighton-assessed hypermobility without a diagnosis. Keyword presence in finding text + quote + title
(rows may match several; lower bound on each):

| sub-population wording | rows |
|---|---:|
| hEDS / hypermobile type / type III | 2,759 |
| vascular EDS / COL3A1 / type IV | 2,111 |
| HSD / JHS / BJHS / hypermobility spectrum | 1,979 |
| generalized joint hypermobility / Beighton only | 1,371 |
| classical EDS / COL5A | 717 |

So the POTS bucket is a *union* of all five EDS buckets, and about a sixth of its rows are vascular EDS — a population with
nothing to do with POTS/dysautonomia pathophysiology but drawn in by the EDS query theme. 1,632 of its 1,818 papers are the
papers the EDS registry records as `referenced` (shared, not re-extracted).

## Proposed crosswalk

| POTS bucket | EDS bucket(s) | join rule |
|---|---|---|
| `EDS_hypermobility` | `hEDS` ∪ `HSD` ∪ `hypermobility_unspecified` ∪ `EDS_unspecified_or_mixed` ∪ `EDS_other_type` | **coarse join** (default): POTS bucket ↔ any EDS bucket. Used for recall-style questions ("is this entity reported in EDS populations at all?") |
| `EDS_hypermobility` (rows whose text names a sub-population) | the named EDS bucket | **fine join** (opt-in): a POTS row is assigned a provisional EDS bucket by the keyword rule above, carried as `eds_bucket_provisional` with the matching span — model-free, auditable, never written into the POTS table |
| `POTS`, `dysautonomia`, `MCAS`, `IBS_gut`, `small_fiber_neuropathy`, `fibromyalgia`, `ME_CFS`, `long_COVID`, `migraine`, `autism` | — (no EDS counterpart) | EDS rows about these comorbidities live under their EDS population bucket with `symptom_correlate` / `anatomical_system`; the connection is on **entity**, not bucket |
| `general` | `general` | joinable; both mean "no listed population studied" |
| `multiple` | `multiple` | NOT joinable as a bucket — "multiple" means different things in each corpus; join on entity only |

## Decisions requested

1. **Default join = coarse.** Fine join only when a question needs it, and then the provisional bucket is reported as provisional.
2. **Vascular EDS rows in the POTS corpus** (2,111 rows): exclude from dysautonomia-oriented connection queries by default (they are vEDS,
   not hypermobility), but keep them for the EDS-side claim graph. Alternative: leave them in and let the fine join sort them.
3. **Era.** POTS is 2019–2026; EDS Tier 2 is 1946–2018. Every cross-corpus comparison carries `year_min/year_max` of both sides; a disagreement
   between a 1985 EDS claim and a 2023 POTS claim is reported as a cross-era difference, not a contradiction, unless both sides have post-2017 support.
4. **hEDS strictness.** EDS `hEDS` admits pre-2017 "EDS type III" cohorts; POTS rows naming "hEDS" are 2019+ and therefore 2017-criteria. For a
   strict-hEDS comparison, filter EDS `hEDS` to year ≥ 2017 (362 of 1,055 Tier-2 hEDS rows; all Tier-1 rows).
5. **Nosology supplement rows** (`scope_class ≠ eds_2017`) are excluded from the crosswalk by default; they join only on entity.

## What this unblocks

With 1–5 approved, the connections ledger per CONNECTIONS_DESIGN.md can be computed from claims_v1: shared claims (same entity, direction, reference
frame, joinable bucket), discordant claims, and entity-only connections — each with support on both sides and the era span.
