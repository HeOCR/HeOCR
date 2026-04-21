# Repository Agent Instructions

## Scope
- Keep this file static and procedural.
- Put evolving roadmap, release planning, and release sequencing in the docs, not here.

## Repository posture
- This repository stores versioned release trees exported by `hocrgen`.
- Public release trees live under `releases/<version>/`.
- Keep release payloads portable and repository-relative.
- Do not add machine-local absolute filesystem paths to committed manifests or docs.

## Planned PR notation
- If a PR implements a roadmap-tracked item or any other planned milestone item with notation such as `B5b4`, use `<notation>: <sentence-case summary>` in the PR title.
- Planned PR descriptions must include a top-level `## Planning notation` section naming the notation, parent milestone, and plan source.
- If work belongs to a planned milestone but no notation exists yet, define that notation in the planning docs before opening the PR.
- Planned PR notation applies to PR metadata, not branch names.

## Before merging
- Treat release or PR work as incomplete until a non-draft GitHub PR with a detailed description is open.
- Ensure the PR is labeled appropriately and assigned to a GitHub milestone before handing off.
- Verify that only the intended `releases/<version>/` tree and related repo guidance changes are included in the diff.
