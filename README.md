# Personal AI App Factory — Artifact Bus

This repository is a **small transfer point** between a blind implementation agent and the evaluator.

## Purpose

Store only the minimum files needed to evaluate the current Factory run:

- `brief.md`
- `run_meta.json`
- `assumptions.md` when present
- `app/` static deliverable

Never store evaluator internals, hidden tests, Golden/Mutant apps, secrets, personal data, build caches, logs, screenshots, archives, or dependency folders.

## Size / performance policy

- `app/`: target **<= 250 KB**, hard maximum **1 MB**
- maximum **20 files** in `app/`
- no binary assets unless a future Brief explicitly requires them
- no `node_modules/`, build output, package caches, ZIP/TAR archives, screenshots, videos, or raw test reports
- no `package.json` or `package-lock.json` inside `app/`
- no Git LFS for V0
- keep evaluation logs outside this repository
- keep long-term history as small text records (hashes, verdicts, metadata), not duplicate app bundles

The goal is to minimize repository growth without weakening the app or evaluator. Size optimization must never remove functionality required by the Brief.

See `ARTIFACT_POLICY.md` for the full contract.
