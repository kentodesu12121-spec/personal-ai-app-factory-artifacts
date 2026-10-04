# Artifact Policy

## 1. Repository role

This repository is an Artifact Bus only. It is not the evaluator repository and not a package/cache store.

## 2. Allowed run payload

At repository root:
- `brief.md`
- `run_meta.json`
- `assumptions.md` (optional)
- `app/`

Inside `app/`:
- `index.html` is required
- allowed extensions: `.html .js .css .md .json .svg`
- max 20 files
- hard max total size: 1 MB
- V0 target total size: <= 250 KB when the Brief can be satisfied without quality loss
- symlinks prohibited
- build step prohibited
- `package.json` and `package-lock.json` prohibited

## 3. Never commit

- evaluator source
- hidden cases
- reference implementation
- Golden/Mutant apps
- secrets, tokens, credentials
- personal/company data
- `node_modules/`
- build/dist/cache directories
- Playwright browser downloads
- screenshots/video
- raw test logs/traces
- ZIP/TAR/GZ archives
- APK/IPA/binaries
- generated dependency lockfiles not required by the artifact contract

## 4. Performance rule

Repository-size reduction must not change app behavior, acceptance criteria, testability, or mobile UX.

Do not minify or combine files merely to save a few kilobytes if doing so reduces auditability or makes failures harder to diagnose. Prefer simple source files.

## 5. History rule

For completed runs, retain compact text metadata such as:
- run ID
- implementation agent
- prompt SHA-256
- brief SHA-256
- artifact SHA-256
- verdict
- timestamp

Do not duplicate the same app bundle just for archival purposes.

## 6. Evaluation artifacts

Evaluator logs, traces, screenshots, browser downloads, temporary servers, and failure diagnostics stay in the evaluator environment and are not committed here.

## 7. Future scaling

If repository history becomes materially large, compact/archive strategy must preserve the text audit ledger and current artifact while avoiding unnecessary binary history. Do not trade runtime performance or reproducibility for storage savings.
