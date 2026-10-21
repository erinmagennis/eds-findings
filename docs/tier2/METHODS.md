# METHODS

Method repository: `condition-corpus-builder` (pipeline generation: Tier-4 code made condition-agnostic; config `config/eds_tier2.yaml`).

**Extraction model (literal id): `claude-sonnet-5`** — max_tokens full text 32000, abstract 8000, screen 2000.

## Pinned parameters

| parameter | value |
|---|---|
| papers_per_chunk | 50 |
| fetch_pacing_s | 1.5 |
| fetch_attempts | 6 |
| n_inflight | 5 |
| n_parts_default | 1 |
| max_attempts | 3 |
| worker_max_s | 3000 |
| collect_slice_s | 120 |
| min_yield | 0.6 |
| retry_under_yield | False |
| stall_min_s | 1500 |
| warn_blocked | 0.05 |
| max_blocked | 0.35 |
| min_ft_chars | 2000 |
| ft_input_char_cap | 120000 |
| llm_concurrency | 8 |
| max_fulltext_per_unit | 12 |
| watchdog_resplit | True |

Deviations from the calibrated block (validator warnings at load): pinned.fetch_pacing_s=1.5 deviates from the calibrated value 0.9; pinned.n_inflight=5 deviates from the calibrated value 3; pinned.n_parts_default=1 deviates from the calibrated value 2; pinned.n_inflight > 3: Europe PMC rate-limits on aggregate concurrency; ~8.5 req/s produced a 92% blocked rate.

## Stages run

1. **Draw** (pipeline/draw.py): Europe PMC, one query per theme ['heds_hsd', 'eds_other_types', 'eds_general', 'gjh'], years 1936–2018, SRC:MED, HAS_ABSTRACT:y. Universe None unique PMIDs; contamination-regex flagged None (flag, not exclusion). 
2. **Screen** (pipeline/screen.py): rule arm = config.screen.rule_anchors; LLM arm = rendered scope (`code/scope_rendered.txt`) + title/abstract, verbatim span required, `claude-sonnet-5`, max_tokens 2000; reconciled with helpers.screen_reconcile.
3. **Extract** (pipeline/pipeline.py via pipeline/driver.py): names only, ids forbidden in the prompt and stripped by the parser guard; `unescape_source_text=True` (entities decoded twice, inline tags stripped) BEFORE the model read the text; `retain_source_text=true` — the exact text handed to the model is archived per chunk.
4. **Resolve** (pipeline/resolver.py + pipeline.ground_findings_df): every identifier derived from the model's NAME against the authority (OLS4 CHEBI/HP/CL/UBERON/NCBITaxon, UniProt, MyGene), exact label or authority-asserted synonym only, minting ontology recorded.
5. **QA** (pipeline/invariants.py, invariants_ext.py, quote_census.py with quote_trace.quote_traceability): invariants after merge; quote census over BOTH strata against the retained archive, each with a wrong-paper negative control.

## Gate log (per work unit, from the driver)

| chunk | part | ok | reason | yield | findings | blocked_frac | guard_violations | attempt |
|---|---|---|---|---|---|---|---|---|
| 68 | [0, 1] | True | ok | 1.0 | 97 | 0 | 0 | 1 |
| 69 | [0, 1] | True | ok | 0.931 | 133 | 0 | 0 | 1 |
| 70 | [0, 1] | True | ok | 1.0 | 102 | 0 | 0 | 1 |
| 71 | [0, 1] | True | ok | 1.0 | 112 | 0 | 0 | 1 |
| 67 | [0, 1] | True | ok | 1.0 | 123 | 0 | 0 | 1 |
| 73 | [0, 1] | True | ok | 0.9355 | 136 | 0 | 0 | 1 |
| 74 | [0, 1] | True | ok | 0.9091 | 94 | 0 | 0 | 1 |
| 75 | [0, 1] | True | ok | 1.0 | 86 | 0 | 0 | 1 |
| 72 | [0, 1] | True | ok | 0.9091 | 148 | 0 | 0 | 1 |
| 76 | [0, 1] | True | ok | 1.0 | 151 | 0 | 0 | 1 |
| 82 | [0, 1] | True | ok | 1.0 | 122 | 0 | 0 | 1 |
| 81 | [0, 1] | True | ok | 0.931 | 152 | 0 | 0 | 1 |
| 83 | [0, 1] | True | ok | 0.9583 | 134 | 0 | 0 | 1 |
| 84 | [0, 1] | True | ok | 0.9688 | 174 | 0 | 0 | 1 |
| 85 | [0, 1] | True | ok | 0.9677 | 202 | 0 | 0 | 1 |
| 86 | [0, 1] | True | ok | 0.9697 | 152 | 0 | 0 | 1 |
| 87 | [0, 1] | True | ok | 1.0 | 148 | 0 | 0 | 1 |
| 88 | [0, 1] | True | ok | 0.8667 | 140 | 0 | 0 | 1 |
| 90 | [0, 1] | True | ok | 0.9655 | 158 | 0 | 0 | 1 |
| 91 | [0, 1] | True | ok | 0.9032 | 143 | 0 | 0 | 1 |
| 89 | [0, 1] | True | ok | 0.9722 | 198 | 0 | 0 | 1 |
| 92 | [0, 1] | True | ok | 0.9655 | 169 | 0 | 0 | 1 |
| 93 | [0, 1] | True | ok | 0.9333 | 153 | 0 | 0 | 1 |
| 95 | [0, 1] | True | ok | 1.0 | 136 | 0 | 0 | 1 |
| 94 | [0, 1] | True | ok | 0.9375 | 155 | 0 | 0 | 1 |
| 96 | [0, 1] | True | ok | 0.9583 | 120 | 0 | 0 | 1 |
| 105 | [0, 1] | True | ok | 0.96 | 99 | 0 | 0 | 1 |
| 102 | [0, 1] | True | ok | 0.9231 | 155 | 0 | 0 | 1 |
| 103 | [0, 1] | True | ok | 1.0 | 157 | 0 | 0 | 1 |
| 116 | [0, 1] | True | ok | 0.9429 | 217 | 0 | 0 | 1 |
| 118 | [0, 2] | True | ok | 0.8947 | 184 | 0 | 0 | 1 |
| 115 | [0, 1] | True | ok | 0.9412 | 226 | 0 | 0 | 1 |
| 112 | [0, 1] | True | ok | 0.8824 | 273 | 0 | 0 | 1 |
| 118 | [1, 2] | True | ok | 0.9444 | 107 | 0 | 0 | 1 |
| 117 | [0, 1] | True | ok | 1.0 | 335 | 0 | 0 | 1 |
| 120 | [0, 1] | True | ok | 0.875 | 194 | 0 | 0 | 1 |
| 121 | [0, 2] | True | ok | 0.8333 | 151 | 0 | 0 | 1 |
| 119 | [0, 1] | True | ok | 0.9677 | 225 | 0 | 0 | 1 |
| 121 | [1, 2] | True | ok | 0.9444 | 155 | 0 | 0 | 1 |
| 123 | [0, 1] | True | ok | 1.0 | 225 | 0 | 0 | 1 |
| 122 | [0, 1] | True | ok | 0.9688 | 265 | 0 | 0 | 1 |
| 131 | [0, 1] | True | ok | 0.875 | 182 | 0 | 0 | 1 |
| 128 | [0, 1] | True | ok | 0.925 | 293 | 0 | 0 | 1 |
| 129 | [0, 1] | True | ok | 0.9118 | 249 | 0 | 0 | 1 |
| 137 | [0, 1] | True | ok | 0.8889 | 112 | 0 | 0 | 1 |

## Bucket definitions

Semantics: population — `condition` names the patient POPULATION the paper studied. A collagen-biology paper with no EDS/HSD cohort is `general` even if it informs EDS; a paper that carries an EDS/HSD label without studying that population is a bucket violation, recorded in the additive annotations table and never corrected in place.

- **hEDS**: Hypermobile Ehlers-Danlos syndrome as the studied population: cohorts diagnosed under the 2017 hEDS criteria, and pre-2017 cohorts the paper labels "EDS hypermobility type" / "EDS type III". Includes hEDS comorbid with POTS, MCAS or GI dysmotility when hEDS is the specific subject. Nearest exclusions: hypermobility spectrum disorder goes to `HSD`; a cohort pooling hEDS and HSD without separate reporting goes to `multiple`; "joint hypermobility syndrome" cohorts without an EDS label go to `hypermobility_unspecified`.
- **HSD**: Hypermobility spectrum disorder (generalized, historical, localized or peripheral HSD, 2017 framework) as the studied population. Nearest exclusions: hEDS goes to `hEDS`; a pooled "hEDS/HSD" cohort goes to `multiple`; Beighton-assessed hypermobility without an HSD diagnosis goes to `hypermobility_unspecified`.
- **EDS_other_type**: Any 2017-classification EDS type OTHER than hypermobile, as the studied population, with the type named in `eds_type`: classical (cEDS), classical-like (clEDS), cardiac-valvular (cvEDS), vascular (vEDS), arthrochalasia (aEDS), dermatosparaxis (dEDS), kyphoscoliotic (kEDS), brittle cornea syndrome (BCS), spondylodysplastic (spEDS), musculocontractural (mcEDS), myopathic (mEDS), periodontal (pEDS), or a newer molecularly defined type. Includes single-family and case reports of a named type. Nearest exclusions: hypermobile EDS goes to `hEDS`; a cohort of mixed or unstated EDS type goes to `EDS_unspecified_or_mixed`.
- **EDS_unspecified_or_mixed**: An Ehlers-Danlos cohort whose type is not stated, or that pools several EDS types without reporting them separately (e.g. registry, survey or claims-code "EDS" cohorts). Nearest exclusions: a single named type goes to `hEDS` or `EDS_other_type`; a cohort that ALSO includes HSD or GJH as a separately described group goes to `multiple`.
- **hypermobility_unspecified**: Generalized or joint hypermobility assessed by Beighton score or an equivalent instrument WITHOUT an EDS or HSD diagnosis, including pre-2017 "joint hypermobility syndrome" (JHS/BJHS) cohorts not labelled EDS, and hypermobility studied as a trait in athletes, dancers or general-population samples. Inherited from CONDITION_BUCKET_DEFINITIONS.md v1 (EDS_hypermobility: "generalized joint hypermobility assessed by Beighton score WITHOUT an EDS diagnosis"). Nearest exclusion: orthopaedic single-joint laxity/instability with no hypermobility-as-condition question is `general` (and usually topical drift).
- **multiple**: Several listed conditions are subjects of the paper and reported as distinct groups — the typical case is a pooled or side-by-side hEDS and HSD cohort, or an EDS-types comparison that includes hEDS.
- **general**: The correct answer when no listed condition is the paper's subject: connective-tissue or collagen biology without an EDS/HSD cohort, other heritable connective-tissue disorders (Marfan, Loeys-Dietz, osteogenesis imperfecta) studied alone, and comparison groups. A catch-all by design; "too vague" is not a defect.