# HeOCR

Public dataset repository for versioned HeOCR release trees exported by `hocrgen`.

## Release layout

- Versioned public releases live under `releases/<version>/`.
- Each release should contain `data/`, `manifests/`, and `docs/` as exported by `hocrgen`.
- Public release trees must stay portable and must not leak machine-local absolute paths.

## Planned PR workflow

- If a PR implements a roadmap-tracked item or any other planned milestone item, use the title format `<notation>: <sentence-case summary>`.
- Planned PR bodies should include a top-level `## Planning notation` section that names the notation, parent milestone, and plan source.
- If work belongs to a planned milestone but no notation exists yet, define the notation in the planning docs before opening the PR.
