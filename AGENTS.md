# AGENTS.md

High-signal notes for OpenCode agents working in this repo.

## Repo shape and entrypoints
- This repo is a Bash-based Distrobox setup; there is no build system, no CI workflow, and no test suite in-tree.
- `install` is the installer entrypoint: it copies `user/local/share/towel/*` into `$HOME/.local/share/towel`, copies `user/config/containers` into `$HOME/.config`, and symlinks `$HOME/.local/bin/towel`.
- Runtime CLI entrypoint is `user/local/share/towel/bin/towel`; command implementations are in `user/local/share/towel/lib/run` and help text is in `user/local/share/towel/lib/help`.
- `system/etc/**` contains container/distrobox config payloads (not executed directly by scripts in this repo).

## Behavior quirks worth preserving
- `install` intentionally hardens shell behavior by enabling builtin commands and unaliasing names (`enable` + `unalias`), and uses absolute paths like `/usr/bin/cp`; keep this style in installer changes.
- `install` appends PATH export to shell rc only when `$HOME/.local/bin` is missing; preserve the literal single-quoted export line.
- `bin/towel` always calls `towel_enter` after handler dispatch (`"${cmd_handlers[enter]}" "$@"` at loop end). Changing command flow here alters user-visible behavior.
- `lib/opts` defines a parser that is currently not used by `bin/towel`; avoid editing it unless you also rewire the entrypoint.

## Verification commands (focused)
- Lint executable scripts directly (files do not use `.sh` extensions):
  - `shellcheck -x install user/local/share/towel/bin/towel user/local/share/towel/lib/*`
- Format only shell scripts, not the whole repo:
  - `shfmt -w install user/local/share/towel/bin/towel user/local/share/towel/lib/*`

## Style conventions observed in code
- Tabs are used for indentation in shell scripts.
- Existing files mix `#!/usr/bin/env bash` and `#!/bin/bash`; prefer `#!/usr/bin/env bash` for new/updated executable scripts unless matching an existing file is required.
- Quote variable expansions consistently; scripts rely on host/container boundary checks via `CONTAINER_ID` and should keep stderr messaging for misuse paths.
