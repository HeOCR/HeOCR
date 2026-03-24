# HeOCR Concrete Acquisition Plan

## 1. Purpose of this document

This document tightens the earlier HeOCR design into a **concrete acquisition plan** for building the dataset with `hocrgen`.

It focuses on:

- exact candidate sources
- whether each source belongs in the **default public release** or only in an **extended/manual-review track**
- concrete inclusion criteria
- a practical composite licensing model
- a recommended folder layout and item schema for building and publishing the dataset

This plan is intentionally conservative. The default public release should include only content that is sufficiently clear for a public dataset release.

---

## 2. Release model

HeOCR should be managed as **two operational release profiles**:

### 2.1 `heocr-open` (default public release)
This is the main public dataset. It should contain only:

- project-owned synthetic data
- clearly open / public-domain / permissively licensed upstream data
- items whose rights metadata is explicit enough to support a public release

This should be the dataset published by default to Hugging Face and mirrored to the `HeOCR` GitHub dataset repo.

### 2.2 `heocr-review` (internal staging / candidate pool)
This is **not** the public release. It is a candidate pool for:

- manually reviewed archive material
- sources with item-level ambiguity
- sources with restrictions that may permit some use but are not sufficiently open for the main dataset
- items waiting on legal/policy review

This can live as manifests and review artifacts, but not as the default public downloadable dataset.

This split is important because some attractive sources are useful for discovery and review but should not automatically flow into a public “open” dataset.

---

## 3. Candidate sources

## 3.1 Tier A — include in `heocr-open` by default

These are the strongest candidates for the public dataset.

### A1. NLI items explicitly marked **“Any Use Permitted”**
**Source family:** National Library of Israel catalog / manuscripts / archives items  
**Role in HeOCR:** core real-scan source, especially for handwritten material  
**Why it fits:** item pages explicitly state “Any Use Permitted,” say the item may be copied and used for any purpose, and for the examples reviewed also state the item is part of the public domain in Israel.  
**Recommended use:** primary real-scan source for HeOCR.  
**Expected contribution:** mainly handwritten material; some printed material.

#### Inclusion criteria
Include an item only if all of the following hold:

1. The item page is publicly accessible without login.
2. The item page explicitly exposes a rights statement equivalent to:
   - `Any Use Permitted`
   - and/or explicit text permitting use for any purpose.
3. The item exposes downloadable or fetchable image assets of sufficient quality.
4. The item passes privacy/sensitivity checks.
5. The item is document-like and in scope for OCR/HTR.

#### Additional preferred filters
Prefer items that are:

- predominantly Hebrew
- handwritten modern Hebrew
- page-like scans rather than decorative isolated objects
- likely to include realistic OCR challenges

#### Recommended sub-collections / item families
Use NLI as multiple acquisition channels inside one source family:

- **NLI archives items** with `Any Use Permitted`
- **NLI Hebrew manuscripts / Ktiv item pages** with `Any Use Permitted`
- **NLI catalog items** where the rights block explicitly allows unrestricted use

#### Notes
This should be the **main default acquisition source** for real scans.

---

### A2. Pinkas dataset
**Source family:** Zenodo open dataset  
**Role in HeOCR:** small historical handwritten component  
**Why it fits:** public dataset with a clear open license (CC BY 4.0 on the reviewed record).  
**Recommended use:** fixed imported historical subset; not a crawler source.  
**Expected contribution:** small, high-value historical handwritten pages.

#### Inclusion criteria
- Import the dataset as an upstream dataset source, preserving provenance.
- Keep it as a separate source family in metadata.
- Do not relabel it as modern; keep it clearly historical.

#### Notes
This should remain a **small historical complement**, not a major share of HeOCR.

---

### A3. BiblIA / Medieval Hebrew manuscripts open set
**Source family:** Zenodo open dataset(s) related to BiblIA  
**Role in HeOCR:** small historical handwritten component with transcription/layout value  
**Why it fits:** reviewed Zenodo records describe an open annotated medieval Hebrew manuscript dataset; relevant records show CC BY-SA 4.0.  
**Recommended use:** imported historical subset, clearly separated in metadata.  
**Expected contribution:** small but valuable older handwritten Hebrew component.

#### Inclusion criteria
- Only ingest files actually distributed under the open dataset release.
- Preserve exact source split and license metadata.
- Keep as historical subset only.

#### Important caution
The reviewed description indicates the overall BiblIA release references both BnF and Vatican material, and in some contexts not all images are bundled equally. `hocrgen` should only ingest the files actually present in the open release package and should not attempt to scrape external linked images unless their rights and packaging are independently verified.

---

### A4. Project-owned synthetic data
**Source family:** generated by `hocrgen`  
**Role in HeOCR:** controlled augmentation and coverage expansion  
**Why it fits:** cleanest legal footing if all fonts/assets/texts are properly licensed  
**Recommended use:** first-class subset in the public dataset  
**Expected contribution:** 15–25% of the dataset, bounded by release policy

#### Inclusion criteria
Only generate public-release synthetic samples if:

- all fonts/assets are tracked and allowed
- text sources are project-authored, public-domain, or permissively licensed
- the generation recipe is reproducible
- the sample is marked synthetic in metadata

#### Recommended synthetic subtypes
- printed Hebrew
- handwritten-look Hebrew
- printed + handwritten mixed pages
- Hebrew with small English fragments
- forms, notes, letters, tables, stamps, annotations

---

## 3.2 Tier B — useful, but **exclude from `heocr-open` by default**

These sources can be useful for discovery or future expansion, but should not enter the default public dataset without a more specific policy decision.

### B1. Historical Jewish Press / JPress
**Source family:** NLI Historical Jewish Press  
**Role in HeOCR:** potentially valuable modern printed Hebrew scans  
**Why not default-open:** the reviewed terms of use require credit and indicate that accessibility of some collections depends on collaborations with rights holders and collection owners. This makes it a poor fit for a blanket “open dataset” ingestion rule.  
**Recommended use:** do not include in `heocr-open` by default.

#### Operational recommendation
Treat JPress as:

- `manual_review_only`, or
- a future optional add-on release with collection-level allowlisting

#### If ever used
Only include items when:

- a specific title/collection is separately verified as acceptable
- the item-level or collection-level rights are documented in a way compatible with that release
- the release is clearly labeled as distinct from `heocr-open`

---

### B2. Israel State Archives — selected subsets
**Source family:** Israel State Archives  
**Role in HeOCR:** potentially valuable modern handwritten and printed government document scans  
**Why not default-open:** the reviewed terms of use allow use of unmarked documents for any purpose including commercial use, but also forbid creating derivative works or secondary permits. That makes this source materially less open than typical open-data/open-content components and potentially incompatible with an “open-source dataset” framing.  
**Recommended use:** keep out of `heocr-open` unless a narrower safe subset and release policy are defined.

#### Operational recommendation
Treat ISA as:

- `manual_review_only`
- candidate pool for later specialized releases
- or entirely excluded from the first public release

#### If ever used
Use only if all of the following are true:

1. the item is unmarked and clearly covered by the permissive portion of the terms;
2. the planned dataset handling does not violate the derivative-work restriction;
3. attribution requirements are satisfied;
4. the HeOCR release labeling clearly distinguishes this subset from genuinely open/public-domain subsets.

#### Practical recommendation
For the initial HeOCR public release, **do not include ISA scans**. Keep ISA support in `hocrgen` behind a non-default feature flag and a manual review workflow.

---

## 3.3 Tier C — optional future additions

These are not needed for the initial public release, but are viable future candidates if separately verified.

### C1. Additional permissively licensed Hebrew OCR/HTR datasets
Add only if they satisfy all of:

- clear upstream license
- downloadable image assets
- Hebrew relevance
- compatibility with public redistribution

### C2. Institution-specific whitelists
If a library/archive exposes item-level rights pages equivalent to clear public-domain or unrestricted-use terms, it can be added through a source adapter plus a source-policy entry.

---

## 4. Recommended initial source mix

For the first public release of HeOCR, use this target:

- **55–65%** NLI `Any Use Permitted` real scans
  - mostly handwritten modern Hebrew
  - some printed modern Hebrew
- **15–25%** synthetic data
- **5–10%** Pinkas
- **5–10%** BiblIA / related open medieval Hebrew manuscript subsets

This keeps the dataset focused on modern handwritten Hebrew while still adding a bounded historical component and a meaningful synthetic component.

---

## 5. Concrete inclusion criteria

## 5.1 Item-level hard gates
An item may enter `heocr-open` only if all of these are true:

1. **Public access**  
   The item is publicly accessible without authentication.

2. **Rights clarity**  
   The source/item is either:
   - explicit public domain / unrestricted use,
   - under a recognized permissive content license,
   - or project-owned synthetic data.

3. **Redistribution feasibility**  
   The project can lawfully redistribute the image in the public release under the item’s documented terms.

4. **Image suitability**  
   The asset is an actual scanned image or equivalent raster document image suitable for OCR/HTR.

5. **Scope fit**  
   The content is relevant to HeOCR: document pages, forms, notes, letters, manuscripts, mixed printed/handwritten pages, or similar.

6. **Privacy/sensitivity pass**  
   The item does not obviously contain disallowed modern personal/sensitive material, or has passed a review rule allowing it.

7. **Quality minimum**  
   The image is decodable and above the minimum quality threshold.

---

## 5.2 Preferred-content ranking
Among eligible items, prioritize:

1. modern handwritten Hebrew
2. mixed handwritten + printed Hebrew
3. modern printed Hebrew documents
4. Hebrew with some English mixed in
5. historical handwritten Hebrew

---

## 5.3 Exclusion rules
Exclude items that are:

- not actually document scans
- too small / too low-resolution
- duplicates or near-duplicates already represented
- dominated by non-Hebrew content
- rights-ambiguous
- clearly sensitive in a way inappropriate for a public dataset
- inaccessible without unstable session logic
- PDFs with no useful page imagery extractable

---

## 6. Licensing model for HeOCR

## 6.1 Recommended dataset-level legal framing

HeOCR should **not** pretend the whole dataset has one single uniform content license.

Instead, it should be published as a **composite dataset** with:

- **project code license** for `hocrgen`
- **metadata/manifests license** for HeOCR metadata
- **per-item content rights metadata** for actual images

This is the safest and most truthful model.

---

## 6.2 Recommended top-level licensing structure

### A. `hocrgen` code
Use a standard permissive software license, such as:

- **MIT** or
- **Apache-2.0**

Recommendation: **Apache-2.0** if you want slightly clearer patent/process posture; **MIT** if you want maximum simplicity.

### B. HeOCR metadata/manifests
Recommended license:

- **CC BY 4.0**

This covers:
- manifests
- dataset cards
- split files
- QA reports
- documentation authored by the project

### C. HeOCR image assets
Use **per-item rights metadata**, not one fake global license.

Each item should have:

- `license_normalized`
- `license_raw`
- `rights_classification`
- `attribution_text`
- `redistribution_status`

---

## 6.3 Recommended normalized content-license taxonomy

Each item in HeOCR should map to one of a small controlled vocabulary values.

### `PD-IL`
Use for items explicitly identified as public domain in Israel / unrestricted in Israel.

Typical use:
- NLI items explicitly stating public domain in Israel and “Any Use Permitted”

### `CC-BY-4.0`
Use for imported datasets like Pinkas when the upstream record is CC BY 4.0.

### `CC-BY-SA-4.0`
Use for imported open datasets like BiblIA subsets where the upstream release is CC BY-SA 4.0.

### `PROJECT-SYNTHETIC`
Use for synthetic data authored/generated by the project and released under the project’s chosen terms.

Recommended release terms for this subset:
- **CC BY 4.0** for maximum reuse, or
- **CC0** if you want the synthetic subset to be maximally frictionless.

Recommendation: **CC BY 4.0** unless you specifically want zero-attribution synthetic data.

### `RESTRICTED-NONOPEN`
Use only in `heocr-review`, not in `heocr-open`.

Typical use:
- manually staged sources like ISA or ambiguous archive content

This classification should prevent accidental publication into the default public release.

---

## 6.4 Rights-classification field

In addition to `license_normalized`, keep a simpler policy field:

- `open`
- `open_with_attribution`
- `sharealike`
- `restricted_review_only`
- `blocked`

Suggested mapping:

- `PD-IL` -> `open`
- `CC-BY-4.0` -> `open_with_attribution`
- `CC-BY-SA-4.0` -> `sharealike`
- `PROJECT-SYNTHETIC` -> `open_with_attribution` (or `open` if CC0)
- `RESTRICTED-NONOPEN` -> `restricted_review_only`

---

## 6.5 Recommendation for public release messaging

The public dataset card should say something close to:

> HeOCR is a composite dataset. Its metadata and manifests are openly licensed by the project, while image assets are distributed with per-item provenance and rights metadata. The public `heocr-open` release includes only items that the project classifies as open/public-domain/permissively licensed or project-owned synthetic data.

This is much safer than claiming the entire dataset is simply “open source” under one monolithic license.

---

## 7. Recommended acquisition profiles in `hocrgen`

`hocrgen` should expose explicit profiles, so releases are policy-driven.

## 7.1 `profile_open_v1`
Public release profile.

Allowed sources:
- `nli_any_use_permitted`
- `pinkas_open`
- `biblia_open`
- `project_synthetic`

Blocked:
- `jpress`
- `state_archives`
- any source without explicit open status

## 7.2 `profile_review_v1`
Internal candidate profile.

Allowed source classes:
- everything in `profile_open_v1`
- plus `jpress`
- plus `state_archives_selected`
- plus manually curated candidates

Publication target:
- review manifests only
- no automatic public asset publication

## 7.3 `profile_open_plus_v1`
Future optional profile if some non-default sources become clearly safe.

---

## 8. Exact source-policy entries to start with

A practical initial `sources.yaml` should look conceptually like this:

```yaml
sources:
  - id: nli_any_use_permitted
    name: National Library of Israel - Any Use Permitted items
    status: allowed
    default_public_release: true
    fetcher: nli
    allowed_content_types:
      - handwritten_modern
      - printed_modern
      - handwritten_historical
    rights_match:
      mode: exact_phrase_or_structured_field
      any_of:
        - "Any Use Permitted"
    normalized_license: PD-IL
    rights_classification: open
    requires_manual_review: false

  - id: pinkas_open
    name: Pinkas dataset
    status: allowed
    default_public_release: true
    fetcher: static_upstream_dataset
    allowed_content_types:
      - handwritten_historical
    normalized_license: CC-BY-4.0
    rights_classification: open_with_attribution
    requires_manual_review: false

  - id: biblia_open
    name: BiblIA / open medieval Hebrew manuscripts
    status: allowed
    default_public_release: true
    fetcher: static_upstream_dataset
    allowed_content_types:
      - handwritten_historical
    normalized_license: CC-BY-SA-4.0
    rights_classification: sharealike
    requires_manual_review: false

  - id: project_synthetic
    name: Project synthetic generator
    status: allowed
    default_public_release: true
    fetcher: internal
    allowed_content_types:
      - synthetic_printed
      - synthetic_handwritten_style
      - synthetic_mixed
    normalized_license: PROJECT-SYNTHETIC
    rights_classification: open_with_attribution
    requires_manual_review: false

  - id: jpress
    name: Historical Jewish Press
    status: manual_review_only
    default_public_release: false
    fetcher: jpress
    normalized_license: RESTRICTED-NONOPEN
    rights_classification: restricted_review_only
    requires_manual_review: true

  - id: state_archives_selected
    name: Israel State Archives selected subset
    status: manual_review_only
    default_public_release: false
    fetcher: state_archives
    normalized_license: RESTRICTED-NONOPEN
    rights_classification: restricted_review_only
    requires_manual_review: true
```

---

## 9. Acquisition strategy by source

## 9.1 NLI `Any Use Permitted`
This should be the main recurring crawler source.

### Strategy
- crawl/search candidate item pages
- parse rights block
- accept only exact matches to the allowed rights pattern
- fetch image links
- run privacy and quality filters
- add to open candidate pool

### Desired heuristics
Prefer items likely to be:

- modern
- handwritten
- Hebrew-heavy
- document-like

### Operational note
Because NLI contains many object types, the crawler should include document-shape and content-type heuristics to avoid pulling irrelevant visual material.

---

## 9.2 Pinkas
Use as a fixed imported source.

### Strategy
- vendor/import upstream metadata
- verify checksums
- map to HeOCR schema
- keep as historical subset

---

## 9.3 BiblIA
Use as a fixed imported source.

### Strategy
- ingest only the files present in the open release package
- preserve upstream annotations where useful
- avoid scraping external image links unless separately approved

---

## 9.4 Synthetic generation
This should run continuously under versioned recipes.

### Strategy
- maintain tracked font/asset manifest
- run recipe-based generation
- generate metadata-rich synthetic pages
- enforce proportion caps at release time

### Hard rule
Synthetic must never exceed the configured cap for the public release.

---

## 9.5 JPress
Keep as a non-default future option.

### Strategy
- support metadata discovery only
- no public-release ingestion path by default
- allow manual allowlisting of specific collections only if policy changes

---

## 9.6 Israel State Archives
Keep behind a review-only wall.

### Strategy
- support carefully curated acquisition manifests only
- do not auto-publish into `heocr-open`
- if used later, publish under a distinct subset/profile with explicit caveats

---

## 10. Recommended folder layout for building the dataset

Below is a recommended repository/build layout spanning both `hocrgen` and the generated HeOCR artifact structure.

## 10.1 `hocrgen` repository layout

```text
hocrgen/
  README.md
  pyproject.toml
  src/hocrgen/
    cli.py
    config/
      profiles/
        profile_open_v1.yaml
        profile_review_v1.yaml
      sources.yaml
      licenses.yaml
      quality_thresholds.yaml
      privacy_rules.yaml
    fetchers/
      nli.py
      pinkas.py
      biblia.py
      jpress.py
      state_archives.py
      synthetic.py
    parsers/
      rights.py
      metadata.py
      image_links.py
    pipeline/
      discover.py
      acquire.py
      normalize.py
      classify.py
      dedupe.py
      split.py
      review.py
      package.py
      publish.py
    review/
      queue.py
      decisions.py
    schema/
      item.schema.json
      source.schema.json
      release.schema.json
    utils/
      hashing.py
      imaging.py
      logging.py
      text.py
  tests/
  docs/
    source-policy.md
    licensing.md
    release-profiles.md
  synthetic_assets/
    fonts/
      manifest.yaml
    templates/
    backgrounds/
    stamps/
  review_data/
    allowlists/
    blocklists/
    manual_decisions/
  .github/
    workflows/
      source_discovery.yml
      synthetic_generate.yml
      build_open_release.yml
      build_review_release.yml
      publish_hf.yml
      publish_github_dataset.yml
```

---

## 10.2 Build/output layout

```text
build/
  run_YYYYMMDD_HHMMSS/
    candidates/
      discovered_items.parquet
      fetched_metadata.parquet
    raw/
      nli/
      pinkas/
      biblia/
      synthetic/
    normalized/
      images/
      thumbnails/
    derived/
      phashes.parquet
      embeddings.parquet
      quality_metrics.parquet
    review/
      queue.parquet
      rejected.parquet
      approved.parquet
    release/
      heocr-open/
      heocr-review/
```

---

## 10.3 HeOCR release layout

```text
HeOCR/
  README.md
  DATASET_CARD.md
  CHANGELOG.md
  LICENSE_METADATA.md
  manifests/
    v0.1.0/
      items.parquet
      splits.parquet
      sources.parquet
      removals.parquet
      qa_report.json
      stats.json
  subsets/
    heocr-open/
      v0.1.0/
        train/
          images/
        validation/
          images/
        test/
          images/
        metadata/
          items.parquet
          splits.parquet
    benchmark/
      v0.1.0/
        images/
        metadata/
  previews/
  docs/
    sources.md
    licensing.md
    privacy.md
    schema.md
```

---

## 11. Recommended item schema

Each item should have enough metadata to support licensing, provenance, filtering, and future curation.

## 11.1 Core fields

```json
{
  "item_id": "heocr_nli_000000123",
  "subset": "real_handwritten_modern",
  "release_profile": "heocr-open",
  "split": "train",
  "image_path": "subsets/heocr-open/v0.1.0/train/images/heocr_nli_000000123.png",
  "thumbnail_path": "previews/heocr_nli_000000123.jpg",

  "source_id": "nli_any_use_permitted",
  "source_name": "National Library of Israel - Any Use Permitted items",
  "source_url": "https://...",
  "source_item_identifier": "NNL_...",
  "source_collection": "archives",
  "retrieved_at": "2026-03-24T00:00:00Z",

  "rights_label_raw": "Any Use Permitted",
  "license_raw": "Item page text as captured",
  "license_normalized": "PD-IL",
  "rights_classification": "open",
  "redistribution_status": "public_release_allowed",
  "attribution_text": "National Library of Israel, ...",

  "is_synthetic": false,
  "synthetic_recipe_id": null,

  "content_type": "handwritten",
  "content_mix": "hebrew_only",
  "content_period": "modern",
  "language_primary": "he",
  "language_secondary": [],
  "script": ["Hebrew"],

  "transcription": null,
  "layout_annotations": null,

  "width": 2480,
  "height": 3508,
  "sha256": "...",
  "phash": "...",

  "quality_score": 0.91,
  "privacy_flag": "clear",
  "manual_review_status": "auto_pass",
  "version_added": "v0.1.0",
  "version_removed": null
}
```

---

## 11.2 Recommended controlled vocabularies

### `content_type`
- `handwritten`
- `printed`
- `mixed`

### `content_period`
- `modern`
- `historical`

### `content_mix`
- `hebrew_only`
- `hebrew_with_english`
- `multilingual_other`

### `rights_classification`
- `open`
- `open_with_attribution`
- `sharealike`
- `restricted_review_only`
- `blocked`

### `redistribution_status`
- `public_release_allowed`
- `review_only`
- `blocked`

### `manual_review_status`
- `auto_pass`
- `manual_approved`
- `manual_rejected`
- `needs_review`

---

## 12. Split policy

## 12.1 Public splits
Publish:

- `train`
- `validation`
- `test`

## 12.2 Leakage prevention
Split by document family/source item where possible.

Do not place highly similar pages or different crops of the same page across different splits.

## 12.3 Historical subset handling
Historical sources can be included in the global splits, but also tag them clearly so users can exclude them if they want a modern-only benchmark.

---

## 13. Recommended GitHub Actions operating plan

## 13.1 Routine scheduled jobs
Run on schedule:

1. `discover_nli_candidates`
2. `generate_synthetic_candidates`
3. `build_open_release_dry_run`
4. `publish_open_release` (only on tagged/manual approval)
5. `build_review_pool`

## 13.2 Pull-request checks
On PRs to `hocrgen`:

- validate source registry
- validate rights-mapping rules
- validate synthetic asset manifest
- dry-run release stats
- ensure blocked/review-only sources do not leak into `profile_open_v1`

## 13.3 Publication gates
A public release should only publish if:

- all item licenses are normalized
- no `restricted_review_only` items are in `heocr-open`
- QA passes
- split integrity passes
- dataset card and changelog are generated

---

## 14. Concrete recommendation for v0.1.0

The first public release should be intentionally small and clean.

## Include
- NLI `Any Use Permitted` items only
- Pinkas
- BiblIA open release files
- project synthetic subset

## Exclude
- JPress
- Israel State Archives
- any ambiguous source
- any source requiring bespoke legal interpretation for redistribution

## Suggested v0.1.0 target composition
- 500–2,500 NLI real pages
- full imported Pinkas subset
- full imported open BiblIA subset actually bundled in the open release
- 200–800 synthetic pages

This is enough to establish the project cleanly without overreaching.

---

## 15. Bottom-line recommendations

1. **Make NLI `Any Use Permitted` the core source.**
2. **Treat Pinkas and BiblIA as bounded historical complements.**
3. **Keep ISA and JPress out of the default public release.**
4. **Model HeOCR as a composite dataset with per-item rights metadata.**
5. **Publish only the `heocr-open` profile by default.**
6. **Use `heocr-review` as a staging pool for anything less clear.**
7. **Keep the first release small, clean, and easy to defend legally.**

---

## 16. Practical summary table

| Source | Default public release? | Main role | Normalized license class | Notes |
|---|---:|---|---|---|
| NLI `Any Use Permitted` items | Yes | Main real-scan source | `PD-IL` | Primary source for modern handwritten Hebrew |
| Pinkas | Yes | Historical handwritten complement | `CC-BY-4.0` | Small fixed imported source |
| BiblIA open release | Yes | Historical handwritten complement | `CC-BY-SA-4.0` | Ingest only packaged open-release files |
| Project synthetic | Yes | Controlled augmentation | `PROJECT-SYNTHETIC` | Recommended public synthetic subset |
| JPress | No | Potential printed Hebrew source | `RESTRICTED-NONOPEN` | Keep review-only unless collection-specific clearance exists |
| Israel State Archives | No | Potential modern archive source | `RESTRICTED-NONOPEN` | Keep out of default public release |

---

## 17. Final policy position

The cleanest and strongest public version of HeOCR is:

> a composite public dataset centered on NLI items explicitly marked “Any Use Permitted,” supplemented by clearly open historical Hebrew OCR datasets and a project-owned synthetic subset, with all image assets carrying per-item provenance and normalized rights metadata.

That is the recommended acquisition posture for the initial public HeOCR roadmap.
