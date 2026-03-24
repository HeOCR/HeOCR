# HeOCR Dataset Design & Specification

## 1. Overview

**HeOCR** is a public, versioned dataset of Hebrew document images intended for OCR, HTR, document understanding, and multimodal model evaluation, with emphasis on:

- **modern Hebrew handwritten document scans**
- **some modern printed Hebrew material**
- **a small portion of older handwritten Hebrew**
- **a synthetic component** combining printed and handwritten-looking Hebrew, with some English mixed in

The dataset is generated, expanded, curated, and published by an open-source toolchain named **hocrgen**.

The system is designed to:

- continuously and transparently expand the dataset from public/permissively usable sources
- enforce source-level and item-level licensing constraints
- maintain reproducibility and provenance
- publish every dataset revision to both:
  - **Hugging Face**
  - a separate public **GitHub dataset repository**
- run most ongoing maintenance and expansion flows on **GitHub Actions**

---

## 2. Primary Goals

1. Build a **usable open dataset** for Hebrew OCR/HTR and document VLM work.
2. Focus on **modern Hebrew document scans**, especially handwritten.
3. Keep the dataset **legally conservative**:
   - only ingest material that is clearly permitted
   - preserve provenance and licensing metadata for every item
4. Make the dataset **incrementally expandable** and **fully reproducible**.
5. Support both:
   - **raw image-based training/evaluation**
   - future addition of **transcriptions**, **layout labels**, and other annotations
6. Ensure the project can be run with low operational overhead, primarily from GitHub-hosted automation.

---

## 3. Non-Goals

At least initially, HeOCR is **not** intended to:

- solve full transcription for all documents automatically
- guarantee perfect OCR/HTR labels for every item
- ingest content with ambiguous or insufficient usage rights
- scrape entire source platforms indiscriminately
- serve as a general-purpose archive mirror of source institutions

The initial product is a **dataset generation and curation system**, not a full scholarly archive.

---

## 4. Project Structure

The initiative consists of two public repositories:

### 4.1 `hocrgen`
The open-source generation, curation, and publishing tool.

Responsibilities:

- source crawling/scraping
- rights filtering
- metadata extraction
- download orchestration
- deduplication
- dataset manifest generation
- synthetic sample generation
- validation and QA
- packaging
- versioning
- publishing to Hugging Face
- syncing to the dataset GitHub repo

### 4.2 `HeOCR`
The public dataset repository.

Responsibilities:

- store dataset manifests and metadata
- optionally store lightweight assets or sharded index files
- track released dataset versions
- expose changelogs
- mirror published dataset structure
- act as a canonical public record of dataset contents per version

Depending on storage constraints, full binary images may either:
- live directly in the repo via git-lfs if manageable, or
- be stored primarily on Hugging Face, while the GitHub repo stores manifests, metadata, release notes, and smaller representative subsets.

The default assumption should be:

- **Hugging Face** is the primary dataset hosting target
- **GitHub** is the transparency / code / manifest / release index target

---

## 5. Dataset Scope

## 5.1 Main content categories

HeOCR should contain the following top-level content families:

1. **Modern handwritten Hebrew scans**  
   Main emphasis. Real scanned handwritten documents in modern Hebrew.

2. **Modern printed Hebrew scans**  
   Smaller but meaningful component. Printed pages/forms/notices/articles/documents in modern Hebrew.

3. **Older handwritten Hebrew scans**  
   Small portion only. Included mainly for diversity and robustness, not as the main identity of the dataset.

4. **Synthetic documents**  
   Generated documents that simulate realistic Hebrew document images, including:
   - printed-only
   - handwritten-style only
   - mixed printed + handwritten
   - Hebrew + small English mixtures

---

## 5.2 Intended use cases

HeOCR should support:

- OCR training and evaluation
- Hebrew HTR experiments
- document image understanding
- layout-aware OCR pipelines
- synthetic-to-real transfer research
- multimodal pretraining/evaluation
- benchmarking Hebrew-capable OCR systems
- fine-tuning VLMs on Hebrew document interpretation tasks

---

## 5.3 Out-of-scope or limited-scope content

The following should be minimized or excluded unless explicitly approved:

- low-resolution web thumbnails
- born-digital PDFs without scan imagery
- content whose rights are unclear
- documents containing highly sensitive personal information
- content requiring user authentication or paywalled access
- content with anti-scraping terms incompatible with the project
- materials where only OCR text is available but not the underlying scan

---

## 6. Content Mix Targets

These are target proportions, not hard guarantees.

### Initial target composition

- **55–70%** modern handwritten Hebrew scans
- **10–20%** modern printed Hebrew scans
- **5–10%** older handwritten Hebrew scans
- **15–25%** synthetic generated documents

The exact ratios may vary by release, but the project should bias strongly toward **modern handwritten Hebrew**.

### Language mix target

Most documents should be:

- predominantly Hebrew

Some should include:

- Hebrew with small portions of English
- numerals, dates, signatures, headers, stamps, marginal notes, or form fields

Synthetic data should deliberately introduce realistic bilingual fragments.

---

## 7. Source Policy

## 7.1 General principle

Every source must be explicitly classified before ingestion.

Each source receives a policy status:

- `allowed`
- `allowed_with_item_filtering`
- `manual_review_only`
- `blocked`

No source may be scraped into production without a documented source policy entry.

---

## 7.2 Example source classes

### A. Strongly allowed sources
Sources that clearly expose public-domain or permissively reusable items.

Examples may include:

- institutional collections where items are explicitly marked **“Any Use Permitted”**
- public-domain repositories with downloadable scans and permissive reuse signals
- open datasets under recognized open licenses
- project-owned synthetic generation

### B. Allowed with item filtering
Sources where only a subset of items are acceptable.

Examples:

- archives where some items are public/permitted and others are restricted
- collections with item-level rights fields
- sources requiring conservative selectors such as:
  - rights label == permitted
  - year range
  - collection whitelist
  - manually curated item IDs

### C. Manual review only
Sources that may be useful, but where automatic ingestion is too risky.

Examples:

- collections with ambiguous rights statements
- sources with unstable metadata
- collections where documents may include sensitive personal data
- institutions with unclear terms for bulk downloading

### D. Blocked
Sources incompatible with the project.

Examples:

- explicit anti-scraping restrictions incompatible with intended use
- content lacking sufficient rights clarity
- sources known to include protected modern copyrighted content without clear permission

---

## 7.3 Source registry

`hocrgen` should maintain a machine-readable source registry, for example:

```yaml
sources:
  - id: nli_any_use_permitted
    name: National Library of Israel - Any Use Permitted items
    status: allowed_with_item_filtering
    content_types:
      - handwritten_modern
      - printed_modern
    fetcher: nli
    rights_strategy: require_item_rights_match
    allowed_rights_values:
      - "Any Use Permitted"
    manual_review_required: false

  - id: israel_state_archives_selected
    name: Israel State Archives - curated safe subset
    status: manual_review_only
    content_types:
      - handwritten_modern
      - printed_modern
    fetcher: state_archives
    rights_strategy: curated_id_allowlist
    manual_review_required: true

  - id: pinkas_open
    name: Pinkas dataset
    status: allowed
    content_types:
      - handwritten_historical
    fetcher: direct_download
    manual_review_required: false

  - id: synthetic_generator
    name: Project synthetic generator
    status: allowed
    content_types:
      - synthetic
    fetcher: internal
    manual_review_required: false
```

This registry is the policy backbone of the project.

---

## 8. Licensing and Compliance

## 8.1 Core legal posture

HeOCR should take a **strictly conservative** approach:

- keep source-item provenance for every sample
- store source rights metadata as captured at acquisition time
- only ingest content meeting source policy rules
- allow manual removal of items when rights concerns arise
- support tombstoning/removal in future versions

---

## 8.2 Item-level provenance requirements

Every dataset item should store, at minimum:

- `item_id`
- `source_id`
- `source_name`
- `source_url`
- `source_item_identifier`
- `retrieved_at`
- `rights_label_raw`
- `rights_classification`
- `license_normalized`
- `manual_review_status`
- `sha256`
- `version_added`
- `version_removed` (nullable)

---

## 8.3 Dataset-level license

HeOCR should use a dataset-level framing such as:

- code in `hocrgen`: standard open-source software license
- dataset metadata/manifests: open license
- data assets: distributed **subject to per-item rights metadata**

The published dataset should clearly state that the dataset is a **curated aggregation of items with per-item provenance and rights metadata**, rather than implying a single uniform legal status for all images unless that is actually true.

If a subset is fully project-owned synthetic data, that subset can be given a clean uniform license.

---

## 8.4 Removal and corrections policy

The project must document a process for:

- rights takedown requests
- metadata corrections
- item removal
- source blocking
- release retraction or patch releases when needed

Every release changelog should indicate:

- items added
- items removed
- reason categories for removals

---

## 9. Data Model

## 9.1 Core unit

The fundamental unit is a **document image sample**.

A sample may represent:

- a page scan
- a cropped region
- a line image
- a synthetic page
- a synthetic crop

The initial default should be **page-level samples**, with optional derived views later.

---

## 9.2 Recommended schema

Each item should include fields such as:

```json
{
  "item_id": "heocr_nli_000012345",
  "subset": "real_handwritten_modern",
  "image_path": "images/train/heocr_nli_000012345.png",
  "thumbnail_path": "thumbnails/train/heocr_nli_000012345.jpg",
  "source_id": "nli_any_use_permitted",
  "source_name": "National Library of Israel - Any Use Permitted items",
  "source_url": "https://...",
  "source_item_identifier": "NNL_...",
  "retrieved_at": "2026-03-24T00:00:00Z",
  "document_date_estimate": "1954",
  "document_period": "modern",
  "language_primary": "he",
  "language_secondary": ["en"],
  "script": ["Hebrew", "Latin"],
  "content_type": "handwritten",
  "content_period": "modern",
  "is_synthetic": false,
  "synthetic_recipe_id": null,
  "transcription": null,
  "ocr_text_source": null,
  "layout_annotations": null,
  "rights_label_raw": "Any Use Permitted",
  "rights_classification": "permitted",
  "license_normalized": "item_specific_permitted",
  "manual_review_status": "auto_pass",
  "contains_personal_data_risk": "unknown",
  "quality_score": 0.88,
  "width": 2480,
  "height": 3508,
  "sha256": "..."
}
```

---

## 9.3 Required classification dimensions

Every sample should be classified across at least these dimensions:

- `real` vs `synthetic`
- `handwritten` vs `printed` vs `mixed`
- `modern` vs `historical`
- `hebrew_only` vs `hebrew_mixed_english`
- `page` vs `region` vs `line`
- `train` / `validation` / `test`
- rights review state

---

## 9.4 Optional future annotation fields

Future versions may include:

- transcription
- line segmentation
- region bounding boxes
- handwriting/print ratio estimate
- page rotation/skew metadata
- document genre
- writer count estimate
- document language proportions
- stamp/signature presence
- quality tags
- OCR baseline outputs

---

## 10. Dataset Splits

## 10.1 High-level split strategy

HeOCR should publish at least:

- `train`
- `validation`
- `test`

The split process must avoid leakage across highly similar samples.

---

## 10.2 Leakage prevention rules

Where possible, split by document family rather than purely by page.

Do not place near-duplicates across splits.

The split logic should consider:

- source item ID
- document bundle / archival unit
- duplicate clustering
- synthetic generation seed families
- crop lineage

---

## 10.3 Benchmark subset

A smaller, stable benchmark subset should eventually be created:

- fixed versioned evaluation set
- stricter manual review
- higher metadata quality
- stable across minor releases when possible

This benchmark subset can become the recommended evaluation set for Hebrew OCR systems.

---

## 11. Synthetic Data Component

## 11.1 Role of synthetic data

Synthetic data is a first-class but bounded component of HeOCR.

It should be used to:

- expand script/style diversity
- add controlled examples of mixed print + handwriting
- introduce English fragments
- create layout/task diversity
- support weakly supervised pretraining

Synthetic data must never dominate the identity of the dataset.

---

## 11.2 Synthetic document types

The generator should support:

1. **printed Hebrew documents**
2. **handwritten-look Hebrew documents**
3. **mixed printed + handwritten documents**
4. **Hebrew + English mixed documents**
5. **forms, letters, notes, notebooks, labels, memos, invoices, tables, annotations**
6. **scanned-looking degradations**
7. **marginal notes, signatures, corrections, stamps, highlighted text**

---

## 11.3 Fonts and assets

Only freely usable assets may be used.

Assets must be tracked in a manifest with:

- asset name
- license
- source URL
- checksum
- allowed usage scope

The generator should distinguish:

- clean printed fonts
- handwriting-like fonts
- decorative or low-utility fonts to avoid
- Latin fonts for mixed-language components

---

## 11.4 Synthetic generation metadata

Every synthetic sample should record:

- generator version
- template ID
- seed
- fonts used
- text sources used
- language mix
- degradation recipe
- whether handwriting-like rendering was applied
- whether layered composition was used

---

## 11.5 Text sources for synthetic docs

Synthetic text sources must also be open/permitted.

Recommended categories:

- public-domain or permissively licensed Hebrew text
- project-authored template text
- generated text authored specifically for the project
- small English inserts from safe open sources or project-authored text

The project should avoid creating synthetic samples from copyrighted text unless clearly licensed.

---

## 12. Data Acquisition Pipeline

## 12.1 Pipeline stages

`hocrgen` should implement a staged pipeline:

1. **Discover candidates**
2. **Fetch metadata**
3. **Apply rights filters**
4. **Apply collection/item allowlists/blocklists**
5. **Download assets**
6. **Normalize and validate files**
7. **Deduplicate**
8. **Classify**
9. **Optional manual review**
10. **Assign splits**
11. **Package dataset**
12. **Publish**
13. **Emit reports and changelog**

---

## 12.2 Discovery

Discovery may use:

- collection APIs
- HTML scraping where permitted
- static curated ID lists
- manually maintained allowlists
- upstream open dataset downloads
- internal synthetic generation jobs

Discovery should be incremental and resumable.

---

## 12.3 Rights filtering

Rights filtering is mandatory before download-to-dataset promotion.

Examples of rules:

- require exact rights label match
- require source collection allowlist
- require item age/date constraints
- require manual approval for flagged sources
- reject items with missing or ambiguous rights metadata

---

## 12.4 Download and normalization

Downloaded files should be normalized into standard formats where appropriate:

- preserve original file if possible
- derive normalized PNG/JPEG/TIFF as needed
- compute checksums
- extract dimensions and format info
- generate thumbnails
- store raw acquisition metadata

---

## 12.5 Deduplication

The system should detect and manage:

- exact duplicates
- near-duplicate scans
- resized or recompressed duplicates
- crop/full-page lineage duplicates
- synthetic regeneration duplicates

Recommended signals:

- sha256
- perceptual hash
- image embeddings
- source item identifiers
- OCR text similarity, if available

---

## 12.6 Classification

Samples should be classified automatically where possible into:

- handwritten / printed / mixed
- modern / historical
- Hebrew-only / mixed-language
- quality tier
- likely page genre

Low-confidence classifications may be sent to manual review.

---

## 13. Manual Review Workflow

## 13.1 Purpose

Manual review should exist for:

- borderline rights cases
- poor-quality images
- obviously misclassified items
- privacy-sensitive content
- duplicates not confidently resolved automatically

---

## 13.2 Review outputs

Each reviewed item may receive statuses like:

- `approved`
- `rejected`
- `needs_legal_review`
- `needs_privacy_review`
- `needs_source_policy_update`

Manual review decisions should be stored in versioned review files.

---

## 13.3 Human effort minimization

Because the project is intended to run mostly on GitHub Actions, manual review should be:

- sampled
- targeted
- lightweight
- triggered by confidence thresholds or source policy rules

The system should optimize for **minimal but meaningful human intervention**.

---

## 14. Privacy and Sensitive Content

Even permissively reusable scans may contain personal or sensitive information.

HeOCR should define a conservative privacy policy for modern documents.

Potential controls:

- source restrictions for highly sensitive categories
- date windows to reduce modern personal exposure risk
- regex/OCR-based flagging for IDs, phone numbers, addresses
- manual review for flagged documents
- exclusion of clearly sensitive modern personal records

A rights-permitted item is not automatically appropriate for inclusion.

---

## 15. Quality Assurance

## 15.1 QA categories

The pipeline should validate:

- image decodability
- minimum resolution
- metadata completeness
- rights metadata presence
- duplicate status
- split integrity
- synthetic asset licensing
- publication consistency

---

## 15.2 Sample-level quality metrics

Possible quality metrics:

- blur estimate
- contrast estimate
- skew angle
- foreground density
- OCRability heuristic
- script coverage heuristic
- handwriting likelihood score

---

## 15.3 Release-level QA checks

Before release, the system should verify:

- counts by source
- counts by content type
- counts by rights classification
- counts by split
- duplicate rate
- removed items list
- added items list
- dataset card generation
- manifest consistency with stored assets

---

## 16. Versioning Strategy

## 16.1 Dataset versions

HeOCR should use semantic-style dataset versioning, for example:

- `v0.1.0`
- `v0.2.0`
- `v1.0.0`

Suggested semantics:

- **major**: breaking schema/split changes or substantial re-curation
- **minor**: new data added, non-breaking metadata expansion
- **patch**: metadata fixes, removals, corrections without significant scope change

---

## 16.2 Release artifacts

Each release should publish:

- dataset manifest
- changelog
- dataset card
- per-source summary
- split summary
- removal log
- quality report
- reproducibility metadata
- generation tool version used

---

## 16.3 Reproducibility

Every release should be reproducible, or at least auditable, through:

- pinned source registry
- pinned scraping logic version
- pinned synthetic generator version
- release manifest with checksums
- Git commit SHA of `hocrgen`

---

## 17. Publishing Targets

## 17.1 Hugging Face

Hugging Face is the primary dataset distribution target.

The published dataset should include:

- dataset card
- version info
- splits
- metadata schema
- source and rights documentation
- known limitations
- intended uses and restrictions

If asset volume is large, the dataset may be sharded.

---

## 17.2 GitHub dataset repo

The `HeOCR` GitHub repo should include:

- release notes
- dataset manifests
- changelog
- source policy documentation
- schema docs
- sample thumbnails/examples
- scripts or references for downloading the current release
- optional lightweight benchmark subset or metadata-only mirror

The repo should avoid becoming unwieldy if the full binary dataset becomes too large.

---

## 17.3 Synchronization guarantees

A dataset release should be considered complete only if:

- Hugging Face publication succeeds
- GitHub dataset repo is updated
- release metadata matches across both targets

Partial publication should be treated as failed until reconciled.

---

## 18. GitHub Actions Operating Model

## 18.1 Intended automation posture

Most routine maintenance and expansion should run on GitHub Actions.

Expected GitHub Actions responsibilities:

- scheduled discovery jobs
- metadata refresh
- candidate validation
- synthetic generation jobs
- QA jobs
- release packaging
- publication orchestration
- dry-run reports on pull requests

---

## 18.2 Constraints

Because GitHub Actions has storage/runtime/network constraints, the design should support:

- incremental jobs
- resumable pipelines
- partitioned crawls
- sharded work units
- artifact-based handoff between workflow stages
- optional external storage/cache if needed

Heavier full rebuilds may still occasionally require a local or cloud-maintainer run, but the default path should remain GitHub-first.

---

## 18.3 Suggested workflow categories

`hocrgen` should eventually expose workflows such as:

- `source-discovery.yml`
- `candidate-review-report.yml`
- `synthetic-generate.yml`
- `dataset-build.yml`
- `dataset-publish.yml`
- `dataset-verify.yml`
- `source-policy-check.yml`

---

## 18.4 PR-driven governance

Changes to the following should be reviewable in pull requests:

- source registry
- allowlists/blocklists
- rights filter rules
- synthetic assets
- schema changes
- release notes
- benchmark subset definitions

This keeps the dataset governance transparent.

---

## 19. Suggested Storage Layout

A possible logical structure:

```text
HeOCR/
  README.md
  DATASET_CARD.md
  CHANGELOG.md
  schemas/
    item.schema.json
    source_policy.schema.json
  manifests/
    v0.1.0/
      items.parquet
      sources.parquet
      splits.parquet
      removals.parquet
      qa_report.json
  samples/
    preview/
  benchmark/
    v0.1.0/
      items.parquet
  docs/
    source-policy.md
    privacy-policy.md
    release-process.md
```

A possible build/output structure from `hocrgen`:

```text
build/
  raw/
  normalized/
  thumbnails/
  manifests/
  reports/
  synthetic/
  review_queues/
  release_bundle/
```

---

## 20. Dataset Card Requirements

Each release should include a dataset card covering:

- what HeOCR is
- intended users
- composition by source/type
- real vs synthetic breakdown
- modern vs historical breakdown
- licensing structure
- per-item rights metadata explanation
- privacy limitations
- known biases
- recommended evaluation subsets
- release history
- citation instructions

---

## 21. Risks and Mitigations

## 21.1 Rights ambiguity risk
**Risk:** a source or item is less reusable than initially believed.  
**Mitigation:** source registry, item-level rights capture, conservative filters, removals process.

## 21.2 Privacy risk
**Risk:** modern documents may contain personal information.  
**Mitigation:** source constraints, sensitive-content heuristics, manual review, removal pipeline.

## 21.3 Dataset drift
**Risk:** dataset loses focus and becomes mostly synthetic or historical.  
**Mitigation:** enforce composition targets and release-level ratio checks.

## 21.4 Operational brittleness
**Risk:** scraping workflows break as source sites change.  
**Mitigation:** source-specific adapters, robust tests, fallback curated allowlists, modular fetchers.

## 21.5 Storage bloat
**Risk:** GitHub repo becomes too large.  
**Mitigation:** use Hugging Face as primary binary host; keep GitHub metadata-first.

## 21.6 Quality inconsistency
**Risk:** scanned document quality varies widely.  
**Mitigation:** QA metrics, stratified subsets, quality metadata, benchmark subset curation.

---

## 22. Success Criteria

HeOCR is succeeding if, over time, it achieves most of the following:

- a legally conservative, publicly documented acquisition process
- a growing corpus of modern handwritten Hebrew scans
- a useful but bounded printed Hebrew component
- a transparent synthetic augmentation component
- reproducible versioned releases
- robust per-item provenance
- easy public consumption through Hugging Face
- community trust in its governance and licensing posture
- practical value for OCR/HTR/VLM experimentation

---

## 23. Recommended Initial Milestones

## Milestone 1: Foundation
- create `hocrgen` and `HeOCR` repos
- define schema
- define source registry format
- implement source policy system
- implement synthetic generation MVP
- implement manifest generation
- publish a tiny pilot release

## Milestone 2: Real-source ingestion MVP
- implement one strongly allowed modern-source adapter
- add one historical open dataset
- add deduplication
- add QA reports
- publish first mixed-source release

## Milestone 3: Governance and safety
- add privacy flagging
- add manual review queue format
- add removal workflow
- add richer dataset card generation

## Milestone 4: Expansion automation
- add scheduled GitHub Actions expansion jobs
- add dataset diff reports
- add split stabilization
- sync releases to Hugging Face and GitHub automatically

## Milestone 5: Benchmarking and annotations
- create stable benchmark subset
- add optional transcription fields
- add layout/line annotations for a subset
- publish baselines and evaluation scripts

---

## 24. Naming

- **Dataset name:** `HeOCR`
- **Generation/update tool:** `hocrgen`

Recommended taglines:

- **HeOCR**: Open Hebrew Document OCR Dataset
- **hocrgen**: Reproducible Generator and Publisher for HeOCR

---

## 25. Summary

HeOCR should be a **public, versioned, legally conservative Hebrew document dataset** centered on **modern handwritten Hebrew scans**, supported by some printed material, a small historical component, and a carefully controlled synthetic component.

The core architectural principle is:

**policy-driven acquisition + per-item provenance + reproducible versioned publishing**

The core operational principle is:

**GitHub-first automation, Hugging Face-first dataset hosting**

The core product principle is:

**maximize usefulness while staying conservative about rights, privacy, and provenance**
