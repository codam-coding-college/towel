---
status: active
context_priority: high
component: runtime/cli
created_at: 2026-04-18
updated_at: 2026-04-18
superseded_by: none
resolved_by: none
---

# Handoff: Command Executables Refactor

## Task Summary
- Replaced runtime library-file command imports with executable-per-command dispatch.
- Kept `user/local/share/towel/bin/towel` as the single entrypoint and made it invoke `towel-*` executables.
- Consolidated shared runtime/export helpers into `user/local/share/towel/bin/towel-common`.
- Removed obsolete `user/local/share/towel/lib/*` command implementation files.

## Files Touched
- `user/local/share/towel/bin/towel`
- `user/local/share/towel/bin/towel-common`
- `user/local/share/towel/bin/towel-help`
- `user/local/share/towel/bin/towel-create`
- `user/local/share/towel/bin/towel-enter`
- `user/local/share/towel/bin/towel-exec`
- `user/local/share/towel/bin/towel-install`
- `user/local/share/towel/bin/towel-export`
- `user/local/share/towel/bin/towel-remove`
- `user/local/share/towel/lib/run` (deleted)
- `user/local/share/towel/lib/install` (deleted)
- `user/local/share/towel/lib/export` (deleted)
- `user/local/share/towel/lib/help` (deleted)
- `user/local/share/towel/lib/opts` (deleted)
- `user/local/share/towel/lib/clean` (deleted)
- `AGENTS.md`
- `docs/agent/index.md`
- `docs/agent/warnings.md`
- `docs/agent/todo.md`

## Behavior and Design Impact
- Command execution model is now explicit process dispatch instead of in-process function sourcing.
- Existing visible command behavior is preserved, including default `enter` behavior and install/export semantics.
- Shared logic remains centralized for consistency, but command boundaries are now executable-level.

## Validation Performed
- `bash -n install user/local/share/towel/bin/towel user/local/share/towel/bin/towel-help user/local/share/towel/bin/towel-common user/local/share/towel/bin/towel-create user/local/share/towel/bin/towel-enter user/local/share/towel/bin/towel-exec user/local/share/towel/bin/towel-install user/local/share/towel/bin/towel-export user/local/share/towel/bin/towel-remove`
- `shellcheck -x install user/local/share/towel/bin/towel user/local/share/towel/bin/towel-help user/local/share/towel/bin/towel-common user/local/share/towel/bin/towel-create user/local/share/towel/bin/towel-enter user/local/share/towel/bin/towel-exec user/local/share/towel/bin/towel-install user/local/share/towel/bin/towel-export user/local/share/towel/bin/towel-remove`
- Notes: expected dynamic-source `SC1091` infos for `towel-common` references and a benign `SC2034` warning for shared `TOWEL_INI` usage pattern.

## Risks and Caveats
- Dynamic sourcing in standalone executables relies on `TOWEL_BIN_DIR` path resolution; keep invocation environment assumptions stable.
- Existing warning `WARN-0001` still applies to desktop `Exec=` rewriting behavior.

## Linked Warning and TODO
- Warning: `WARN-0001` in `docs/agent/warnings.md`
- TODO: `TODO-0001` in `docs/agent/todo.md`

## Next Recommended Step
- Add a small integration smoke test script for command dispatch paths (`create`, `enter`, `exec`, `install`, `export`, `remove`) to reduce regression risk in future refactors.
