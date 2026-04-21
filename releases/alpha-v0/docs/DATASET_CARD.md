# HeOCR alpha-v0

## Scope
This alpha release is a narrow public subset exported from `hocrgen` using `profile_open_v1`.
It contains 3 release-ready items across the configured public splits.

## Included Sources
- `nli_any_use_permitted`
- `pinkas_open`
- `project_synthetic`

## Excluded From Public Payload
- Review-required items: 4
- Blocked items: 0

## Rights Posture
- Only release-ready items from the conservative public profile are included under `data/`.
- Review-required and blocked items are exported only as audit manifests.

## Split Counts
- `train`: 3

## Known Limitations
- This is an alpha release, not a full corpus snapshot.
- Kaggle and Hugging Face publication are intentionally deferred.
- Audit manifests are included for transparency, but only the release-ready subset is packaged as dataset payload.
