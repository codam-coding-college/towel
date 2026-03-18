# Towel Project Agentic Guidelines (AGENTS.md)

Welcome, Agent. This document defines the operational parameters, code style, architecture, and testing procedures for the **Towel** repository. Towel is a Distrobox setup project tailored for codam clusters, written predominantly in Bash.

As an AI agent operating in this repository, you must adhere strictly to these rules to maintain the integrity, security, and performance of the system setup scripts.

---

## 1. Project Context & Architecture

- **Purpose**: Automates the setup of Distrobox environments.
- **Structure**:
  - `install`: The primary entry point script for deployment.
  - `system/`: Contains system-level configuration files and scripts (e.g., `/etc` overlays).
  - `user/`: Contains user-level configurations, local binaries, and dotfiles.
- **Philosophy**: Lightweight, reproducible, and fault-tolerant system provisioning. Assume operations are run in potentially restricted environments.

---

## 2. Build, Lint, and Test Commands

Since this is a shell-script-based project, there is no traditional compilation step. However, rigorous linting and formatting are mandatory.

### Linting
We use **ShellCheck** to identify syntax issues and potential bugs.
- **Lint all scripts**: 
  ```bash
  shellcheck -x install system/**/*.sh user/**/*.sh
  ```
- **Ignore specific rules**: If a ShellCheck rule must be bypassed, use an inline directive with a justification:
  `# shellcheck disable=SC2086 # Justification: Intentionally splitting on spaces.`

### Formatting
We use **shfmt** to maintain consistent indentation and layout.
- **Check formatting**:
  ```bash
  shfmt -d -w .
  ```
- **Apply formatting**:
  ```bash
  shfmt -w .
  ```

### Testing
We use **Bats** (Bash Automated Testing System) for unit and integration testing.
- **Run all tests**:
  ```bash
  bats tests/
  ```
- **Run a single test file**:
  ```bash
  bats tests/test_specific_feature.bats
  ```
- **Run a specific test case within a file**:
  ```bash
  bats -f "Name of the specific test case" tests/test_specific_feature.bats
  ```

---

## 3. Code Style Guidelines

### 3.1. Language & Execution
- **Shebang**: Always use `#!/usr/bin/env bash` for execution portability. Do not use `#!/bin/sh` or `#!/bin/bash` directly unless strictly required by a hardcoded system path.
- **Version**: Target Bash 4.0+.

### 3.2. Formatting & Indentation
- **Indentation**: Use **tabs** for indentation (as seen in the core `install` script). Do not use spaces.
- **Line Length**: Soft wrap at 80 characters, hard wrap at 120 characters where possible. Break long commands with backslashes `\`.
- **Functions**: Define functions using the `function name () { ... }` syntax or `name() { ... }`. Be consistent with the existing codebase (`function cleanup () {`).

### 3.3. Naming Conventions
- **Variables (Local)**: `snake_case`. Always declare local variables inside functions using `local var_name="value"`.
- **Variables (Global/Environment)**: `UPPER_CASE_SNAKE_CASE` (e.g., `TOWEL_DATA`, `TOWEL_BIN`).
- **Functions**: `snake_case` (e.g., `setup_distrobox`, `parse_args`).
- **Constants**: `readonly UPPER_CASE="value"`.

### 3.4. Types & Declarations
- Bash is untyped, but you must use `declare` to enforce constraints where applicable.
- **Arrays**: `declare -a my_array=()`
- **Associative Arrays**: `declare -A my_dict=([key]="value")` (Used heavily for colors/configs in the `install` script).
- **Integers**: `declare -i my_num=0`

### 3.5. Error Handling & Safety
- **Strict Mode**: Every script must start with the following safety block:
  ```bash
  set -e          # Exit immediately if a pipeline returns a non-zero status
  set -E          # Inherit the ERR trap
  set -u          # Treat unset variables as an error
  set -o pipefail # Return value of a pipeline is the value of the last command to exit with a non-zero status
  ```
- **Cleanup Traps**: Use `trap` to ensure resources are cleaned up regardless of script success or failure.
  ```bash
  trap cleanup EXIT INT TERM
  ```
- **Custom Error Logging**: Use the provided `fatal` function for critical failures:
  ```bash
  if [[ ! -d "$REQUIRED_DIR" ]]; then
      fatal "Directory $REQUIRED_DIR not found. Aborting."
      exit 1
  fi
  ```

### 3.6. Imports & Includes
- Use `source` to include other scripts. Ensure the path is relative to the script directory using `dirname`:
  ```bash
  SCRIPT_DIR=$(cd "$(dirname "${BASH_SOURCE[0]}")" &>/dev/null && pwd)
  source "${SCRIPT_DIR}/system/helper.sh"
  ```

### 3.7. Variables & Quoting
- **Always quote variables**: Use `"$VAR"` instead of `$VAR` to prevent word splitting and globbing issues.
- **Command Substitution**: Use `$(command)` instead of backticks `` `command` ``.
- **Test Brackets**: Prefer the Bash keyword `[[ ... ]]` over `[ ... ]` or `test`. `[[ ]]` provides safer string comparison and pattern matching without word splitting.

### 3.8. Output & Interaction
- **Standard Out vs Standard Error**: 
  - Send normal output to `stdout` (`echo "Info..."`).
  - Send errors, warnings, and prompts to `stderr` (`echo "Error..." >&2`).
- **Colors**: Use the globally declared associative array for colors (e.g., `${color[red]}`). Always reset colors using `${color[reset]}` at the end of output strings.

### 3.9. Security Practices
- **Never hardcode secrets** (tokens, passwords). Read them from secure prompts or environment variables.
- **File Permissions**: When generating files (especially in `system/` and `user/`), explicitly set permissions using `chmod`.
- **Path Resolution**: Never trust `$PATH` blindly for critical system commands. Use absolute paths where possible, or explicitly set a safe `$PATH` at the top of the script.

---
**Agent Self-Correction Notice**: 
Before finalizing any modification, verify:
1. Did I quote all my variables?
2. Are my paths absolute or dynamically resolved relative to the script?
3. Did I run shellcheck on my changes?
4. Are my logs correctly routed to stdout/stderr?
