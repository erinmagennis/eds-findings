# DATA_DICTIONARY

Primary table `data/eds-findings_findings.parquet` (7101 rows x 94 columns). Columns marked *harmonization* were APPENDED at packaging time from pipeline columns for the verifier contract; no pipeline column was mutated.

| column | fill % | description |
|---|---:|---|
| `entity` | 65.92 | Marker, gene, protein, metabolite or measure the finding is about. Free text as the model wrote it. Null on 40%+ of rows by design — symptom, phenotype and quality-of-life findings name no entity. |
| `entity_kind` | 80.47 | Entity type (gene, protein, chemical/metabolite, …). **Tiers 3-4 only** — null means NOT PRESENT in Tiers 1-2. |
| `gene_marker` | 14.66 | Gene symbol as written in the source. |
| `direction` | 100.0 | Direction of the effect (increased / decreased / unchanged / etc.). |
| `direction_reference` | 100.0 | What the direction is relative to (controls, baseline, another group). |
| `finding_text` | 100.0 | The extracted finding, one sentence, as the model rendered it. |
| `pathway_or_mechanism` | 21.77 | Free-text mechanism. Prose, not an ontology term — only 14 of 147 Tier-2 mechanism strings were real ontology terms, which is why `pathway_id` was dropped. |
| `quantitative_detail` | 55.54 | Numeric detail as stated in the source. |
| `effect_size` | 30.07 | Effect size as stated. Not harmonized across studies. |
| `causal_or_correlational` | 100.0 | Whether the source claims causation or correlation. |
| `evidence_tier` | 100.0 | Study design. Pinned enum; drift is flagged, never coerced — see the README. |
| `functional_impact` | 25.95 | Stated functional consequence. |
| `condition` | 100.0 | What the finding is ABOUT. Distinct from `source_literature` (which literature it came from). |
| `eds_type` | 37.92 | subtype named when condition=EDS_other_type |
| `population_age_group` | 100.0 | Age group studied. |
| `sex_hormonal` | 100.0 | Sex or hormonal-status stratification. |
| `anatomical_system` | 100.0 | Body system. |
| `is_covid_associated` | 100.0 | Whether the finding concerns a COVID-associated population. Semantics differ subtly by tier — see comparability report. |
| `finding_type` | 100.0 | Kind of finding (biomarker, mechanism, treatment response, prevalence, …). |
| `treatment_class` | 17.81 | Treatment class where the finding concerns one. |
| `symptom_correlate` | 59.34 | Symptom the finding correlates with. |
| `phenotype_terms` | 78.75 | Atomic clinical terms split out of the raw symptom string. |
| `validated_instrument` | 20.21 | Named validated instrument (COMPASS-31, DSQ-SF, Beighton, …). |
| `adverse_event_note` | 6.63 | Adverse event mentioned alongside the finding. |
| `certainty` | 100.0 | The extractor's own certainty judgment. Never overwritten. |
| `cohort_registry` | 10.22 | Registry the cohort came from. |
| `cohort_name` | 9.55 | Named cohort. |
| `dataset_accession` | 0.45 | Dataset accession where stated. 0.7% filled — papers rarely state one. |
| `relation_type` | 100.0 | Relation type for subject-object findings. |
| `relation_subject` | 100.0 | Relation subject. |
| `relation_object` | 100.0 | Relation object. |
| `provenance_quote` | 100.0 | **The verbatim source span the finding was drawn from.** The verification handle for the whole dataset. Present on every row. See LICENSE for terms — this column is not CC BY. |
| `provenance_section` | 100.0 | Which section of the paper the quote came from. |
| `null_or_failed_result` | 99.79 | Set where the finding is a null or failed result — deliberately retained, since these are systematically under-published. |
| `contradicts_finding_ref` | 3.38 | Reference to a finding this one contradicts. |
| `evidence_stratum` | 100.0 | Evidence stratum label. |
| `study_quality` | 100.0 | Stated study quality. |
| `study_type` | 63.16 | Study type as stated. |
| `n_participants` | 53.71 | Participant count as stated. |
| `comparator` | 63.16 | Comparator group. |
| `demographics` | 51.98 | Demographic detail. |
| `is_duplicate_or_preprint_note` | 1.66 | Note where the source may be a duplicate or preprint of another record. |
| `contradicts_or_extends` | 46.85 | Whether the finding contradicts or extends prior work. |
| `limitations_stated` | 49.16 | Limitations the source itself states. |
| `future_direction` | 53.87 | Future direction the source proposes. |
| `certainty_derived` | 100.0 | Hedge-detection over both `finding_text` and `provenance_quote`: both hedged -> hedged, neither -> confirmed, disagreement -> mixed. Never overwrites `certainty`. |
| `number_in_quote` | 54.34 | Is the value in `quantitative_detail`/`effect_size` actually present in the quote? ~9.9% of Tier-2 rows fail. **A genuine open question, not a bug to auto-fix.** |
| `pmid` | 100.0 | PubMed ID. **The paper key. Count papers with `pmid.nunique()`.** |
| `doi` | 96.99 | DOI. |
| `title` | 100.0 | Paper title. |
| `year` | 100.0 | Publication year. |
| `journal` | 99.87 | Journal. |
| `source_stream` | 100.0 | Which query stream retrieved the paper. |
| `theme` | 100.0 | Extraction theme. Package-specific vocabulary by construction — never compare across packages. |
| `fulltext_status` | 100.0 | `retrieved-fulltext` / `abstract-only` / `blocked`. **Drives yield ~3x and determines verifiability.** Always stratify on this. |
| `contact_note` | 14.63 | Contact note. |
| `fetch_note` | 100.0 | HTTP status or exception from the fetch, so `blocked` is diagnosable rather than terminal. |
| `chunk_id` | 100.0 | Extraction chunk. Maps to the tier via `chunk_order.parquet`. |
| `query_contamination` | 100.0 | stage-1 draw: a wrong-sense regex fired on title/abstract (flag, not exclusion) |
| `screen_final_verdict` | 100.0 | stage-3 screen verdict (in_scope | uncertain); off_topic papers were excluded before extraction |
| `screen_verdict_uncertain` | 100.0 | stage-3 screen: reconcile() returned uncertain -> extracted and flagged |
| `sample_stratum` | 0.0 | dry-run sampling stratum the paper was drawn into |
| `hgnc_id` | 13.91 | HGNC gene identifier. |
| `ensembl_id` | 13.81 | Ensembl gene identifier. |
| `gene_resolved` | 100.0 | Whether the gene symbol resolved against an authority. |
| `gene_id_source` | 100.0 | Provenance of the gene identifier. |
| `entity_ontology_id_resolved` | 15.2 | Resolved ontology identifier. **Meaning differs by tier: authority-derived in Tiers 3-4, model-emitted in Tiers 1-2.** Always read alongside `ontology_id_verification`. |
| `entity_ontology_label` | 15.2 | The authority's own label for the identifier. Comparing this to `entity` is how identifier correctness was measured offline. |
| `ontology_id_source` | 100.0 | **TRAP: inverted semantics across tiers.** `unresolved_nulled` means identifier KEPT in Tier 2 and identifier NULLED in Tiers 3-4. Provenance only — never a filter. See COMPARABILITY.md. |
| `hpo_ids_verified` | 45.57 | **Authority-verified HPO identifiers, semicolon-delimited.** Multi-value because 43.2% of mappable symptom strings resolve to more than one HPO term. |
| `hpo_labels_verified` | 45.57 | Labels for the verified HPO identifiers. |
| `hpo_id_source` | 100.0 | Provenance of the HPO identifiers. |
| `hpo_id_count` | 100.0 | Count of semicolon-delimited HPO identifiers. Invariant-checked against the string. |
| `tier` | 100.0 | Numeric tier. **Partially null — use `tier_source` instead.** |
| `unit_file` | 100.0 | see SCHEMA.md |
| `unit_vid` | 100.0 | see SCHEMA.md |
| `entity_ontology_id_resolved_worker` | 2.66 | see SCHEMA.md |
| `entity_ontology_label_worker` | 2.66 | see SCHEMA.md |
| `ontology_id_source_worker` | 100.0 | see SCHEMA.md |
| `entity_ontology_id_minting_ontology` | 15.2 | see SCHEMA.md |
| `hpo_ids_verified_worker` | 45.57 | see SCHEMA.md |
| `hpo_labels_verified_worker` | 45.57 | see SCHEMA.md |
| `hpo_id_source_worker` | 100.0 | see SCHEMA.md |
| `hpo_id_count_worker` | 100.0 | see SCHEMA.md |
| `hpo_id_minting_ontology` | 45.57 | see SCHEMA.md |
| `tier_source` | 100.0 | harmonization: `t<tier>` from config.tier.number |
| `cap_entity_kind` | 100.0 | harmonization: table carries `entity_kind` |
| `cap_hpo_verified` | 100.0 | harmonization: table carries `hpo_ids_verified` |
| `cap_number_in_quote` | 100.0 | harmonization: table carries `number_in_quote` |
| `cap_quote_source_retained` | 100.0 | harmonization: source text retained for every row |
| `ontology_id_verification` | 100.0 | harmonization: `authority_verified` iff `entity_ontology_id_resolved` non-null, else `no_id` |
| `ontology_id_match_mode` | 15.2 | harmonization: exact | synonym, parsed from `ontology_id_source` |
| `ontology_id_prefix` | 15.2 | harmonization: prefix of `entity_ontology_id_resolved` |
| `hpo_verification` | 100.0 | harmonization: no_hpo | authority_verified_exact | authority_verified_synonym from `hpo_id_source` |

## `condition` buckets (written before extraction)

Semantics: **population**. Version: eds dry-run v1 (2026-09-30); EDS_hypermobility inherited from CONDITION_BUCKET_DEFINITIONS.md v1 and split.

- **`hEDS`** — Hypermobile Ehlers-Danlos syndrome as the studied population: cohorts diagnosed under the 2017 hEDS criteria, and pre-2017 cohorts the paper labels "EDS hypermobility type" / "EDS type III". Includes hEDS comorbid with POTS, MCAS or GI dysmotility when hEDS is the specific subject. Nearest exclusions: hypermobility spectrum disorder goes to `HSD`; a cohort pooling hEDS and HSD without separate reporting goes to `multiple`; "joint hypermobility syndrome" cohorts without an EDS label go to `hypermobility_unspecified`.
- **`HSD`** — Hypermobility spectrum disorder (generalized, historical, localized or peripheral HSD, 2017 framework) as the studied population. Nearest exclusions: hEDS goes to `hEDS`; a pooled "hEDS/HSD" cohort goes to `multiple`; Beighton-assessed hypermobility without an HSD diagnosis goes to `hypermobility_unspecified`.
- **`EDS_other_type`** — Any 2017-classification EDS type OTHER than hypermobile, as the studied population, with the type named in `eds_type`: classical (cEDS), classical-like (clEDS), cardiac-valvular (cvEDS), vascular (vEDS), arthrochalasia (aEDS), dermatosparaxis (dEDS), kyphoscoliotic (kEDS), brittle cornea syndrome (BCS), spondylodysplastic (spEDS), musculocontractural (mcEDS), myopathic (mEDS), periodontal (pEDS), or a newer molecularly defined type. Includes single-family and case reports of a named type. Nearest exclusions: hypermobile EDS goes to `hEDS`; a cohort of mixed or unstated EDS type goes to `EDS_unspecified_or_mixed`.
- **`EDS_unspecified_or_mixed`** — An Ehlers-Danlos cohort whose type is not stated, or that pools several EDS types without reporting them separately (e.g. registry, survey or claims-code "EDS" cohorts). Nearest exclusions: a single named type goes to `hEDS` or `EDS_other_type`; a cohort that ALSO includes HSD or GJH as a separately described group goes to `multiple`.
- **`hypermobility_unspecified`** — Generalized or joint hypermobility assessed by Beighton score or an equivalent instrument WITHOUT an EDS or HSD diagnosis, including pre-2017 "joint hypermobility syndrome" (JHS/BJHS) cohorts not labelled EDS, and hypermobility studied as a trait in athletes, dancers or general-population samples. Inherited from CONDITION_BUCKET_DEFINITIONS.md v1 (EDS_hypermobility: "generalized joint hypermobility assessed by Beighton score WITHOUT an EDS diagnosis"). Nearest exclusion: orthopaedic single-joint laxity/instability with no hypermobility-as-condition question is `general` (and usually topical drift).
- **`multiple`** — Several listed conditions are subjects of the paper and reported as distinct groups — the typical case is a pooled or side-by-side hEDS and HSD cohort, or an EDS-types comparison that includes hEDS.
- **`general`** — The correct answer when no listed condition is the paper's subject: connective-tissue or collagen biology without an EDS/HSD cohort, other heritable connective-tissue disorders (Marfan, Loeys-Dietz, osteogenesis imperfecta) studied alone, and comparison groups. A catch-all by design; "too vague" is not a defect.