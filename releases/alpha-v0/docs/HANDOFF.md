# Alpha Handoff

- Version: `alpha-v0`
- Target repo checkout: `HeOCR`
- Target release dir: `releases/alpha-v0/`
- Release profile: `profile_open_v1`
- hocrgen commit: `230e199198d195d9178c35c45d8446ed6d42762d`

## Export Summary
- Exported items: 3
- Exported real items: 2
- Exported synthetic items: 1
- Review-required audit items: 4
- Blocked audit items: 0

## Included Sources
- `nli_any_use_permitted`
- `pinkas_open`
- `project_synthetic`

## PR Checklist
- Confirm only `release_ready` items are present under `data/`.
- Confirm review-required and blocked items exist only as audit manifests under `manifests/`.
- Inspect `docs/DATASET_CARD.md`, `docs/RELEASE_NOTES.md`, and `docs/PROVENANCE.md` in the target tree.
- Open the HeOCR handoff PR against the target repository with this release tree under `releases/<version>/`.
