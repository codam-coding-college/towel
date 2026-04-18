# Agent Memory

This directory stores durable context for future agents.

## Context Loading Order
- Tier 0 (always): `docs/agent/README.md`, `docs/agent/index.md`
- Tier 1 (task-dependent): `docs/agent/warnings.md`, `docs/agent/todo.md`, recent files in `docs/agent/handoffs/`, relevant active ADRs in `docs/agent/decisions/`
- Tier 2 (on demand): `docs/agent/archive/**` and entries marked `superseded`, `resolved`, or `archived`

## Lifecycle States
- `active`: current and context-worthy
- `superseded`: replaced by newer guidance
- `resolved`: completed/closed
- `archived`: cold history

## Naming
- Handoffs: `YYYY-MM-DD-short-slug.md`
- Decisions: `ADR-XXXX-short-title.md`
