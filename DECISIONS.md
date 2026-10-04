# Factory V0 Decisions

- Formal runs start from `factory-baseline` at commit `52e4e3c7c4099a3b70d7b23181680ee5b1924796`.
- The baseline contains only README.md and .gitignore.
- run_meta.json is Controller-owned and is placed before implementation.
- The Controller computes the authoritative prompt SHA-256 from the exact UTF-8 bytes sent, with no newline or Unicode normalization.
- **V0 fixed implementation prompt**: 8,995 bytes, SHA-256 `30c9be27e3e64853a149f7dde9ae69f546a90d1becc218e48822871034730828`. This prompt is immutable for the formal Codex V0 series.
- Ambiguity: choose the simplest interpretation consistent with all stated clauses, record it in assumptions.md, and complete implementation.
- True contradiction: record it and stop for specification review.
- A platform-created PR is allowed but must not be merged for evaluation. Evaluation is pinned to the implementation head commit SHA.
- The BuildBrief README requirement is implemented as `app/README.md`. Factory report.md is Controller/Evaluator-owned.
- Planned Codex series settings: fresh isolated cloud task, Internet OFF, **no setup script**, no package downloads, and local commands/tests allowed inside the prepared workspace. The exact model and reasoning level must be visibly selectable and recorded before a run; otherwise the run is PRECHECK_FAILED.
- Use only included ChatGPT plan allowance. Do not purchase credits or enable paid continuation without explicit user approval.
- Record observed Codex usage/consumption after each run when the product exposes it.
- Repository-size reduction must not reduce application performance, acceptance behavior, UX, testability, or maintainability.
- app/ hard limits remain 20 files and 1 MB. 250 KB is a non-binding target only.
- Run isolation by branch reset is practical V0 isolation, not guaranteed historical erasure.
