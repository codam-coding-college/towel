---
status: active
context_priority: high
component: runtime/export
created_at: 2026-04-18
updated_at: 2026-04-18
superseded_by: none
resolved_by: none
---

# Handoff: Install/Export Refactor and Native Wrappers

## Task Summary
- Split monolithic runtime logic into focused modules: container runtime, install flow, export flow.
- Changed install UX to explicit host export confirmation with `-y|--yes` override.
- Replaced `distrobox-export` wrapper-patching with towel-owned wrapper generation.
- Switched wrapper storage to a managed towel export store with host symlinks for deterministic cleanup.
- Implemented previously missing `towel export <pkg...>` command handler.

## Files Touched
- `user/local/share/towel/bin/towel`
- `user/local/share/towel/lib/run`
- `user/local/share/towel/lib/install`
- `user/local/share/towel/lib/export`
- `user/local/share/towel/lib/help`
- `AGENTS.md`
- `docs/agent/index.md`

## Behavior and Design Impact
- `towel install <pkg...>` now prompts once: `Export installed apps/binaries to host now? [y/N]`.
- `towel install -y|--yes <pkg...>` assumes export.
- Non-interactive `towel install` without `--yes` exits with user-error semantics.
- Export logic runs inside towel container context via existing host/container trampoline.
- Bin wrappers: canonical files are created in `~/.local/share/towel/exports/bin/<cmd>`, symlinked to `~/.local/bin/<cmd>`, execute `towel exec <cmd> "$@"`, and can self-remove (link + target) after failure prompt.
- Desktop wrappers: canonical files are created in `~/.local/share/towel/exports/applications/towel-*.desktop`, symlinked to `~/.local/share/applications/towel-*.desktop`, with rewritten `Exec=`/`TryExec`; desktop db refreshed.
- Cleanup now removes towel-managed symlinks/targets by path ownership under the export store instead of marker/content grep.

## Validation Performed
- `bash -n user/local/share/towel/bin/towel user/local/share/towel/lib/run user/local/share/towel/lib/install user/local/share/towel/lib/export user/local/share/towel/lib/help`
- `shellcheck -x install user/local/share/towel/bin/towel user/local/share/towel/lib/*`
- Notes: only pre-existing dynamic-source `SC1091` infos and existing `SC2034` in `lib/opts`.

## Risks and Caveats
- Desktop `Exec=` rewriting preserves existing command tail but remains string-based; unusual desktop files may need future hardening.
- Export discovery still depends on `dnf repoquery -l` and only considers `/usr/bin/*` and `/usr/share/applications/*.desktop`.
- Host dynamic-linking exports for container toolchains are intentionally deferred.

## Linked Warning and TODO
- Warning: `WARN-0001` in `docs/agent/warnings.md`
- TODO: `TODO-0001` in `docs/agent/todo.md`

## Next Recommended Step
- If clang toolchain sharing becomes priority, prototype `towel export-lib` with profile-scoped env activation and strict guardrails (no global linker config changes).
