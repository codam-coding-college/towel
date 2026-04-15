---
name: shellcheck
description: Lint and enforce Bash script quality and safety
license: MIT
compatibility: opencode
metadata:
  audience: developers
  workflow: code-quality
---

# ShellCheck Skill

This skill provides instructions for linting and enforcing Bash script quality and safety in the Towel project.

## What I do

- Run `shellcheck -x` on all modified or newly created Bash scripts (like `install`, `system/**/*.sh`, `user/**/*.sh`).
- Enforce strict Bash practices, particularly concerning variable quoting (SC2086) and return value masking (SC2155).
- Ensure that any bypassed rules use an inline directive accompanied by a clear justification (e.g., `# shellcheck disable=SC2086 # Justification: ...`).
- Provide actionable feedback based on ShellCheck's output and wiki links to resolve syntax and semantic issues.
- Recommend running `shfmt -w .` alongside `shellcheck` to maintain formatting guidelines.

## When to use me

Use this skill whenever you are editing, creating, or reviewing Bash shell scripts within the Towel repository. 

Always run me before finalizing any code modification task to ensure the scripts remain fault-tolerant and adhere to the project's quality standards. Ask clarifying questions if a ShellCheck warning seems to conflict with the intended logic of a script before arbitrarily disabling the rule.
