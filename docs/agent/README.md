# Agent Memory Docs

This directory stores durable context for future agents: active risks, open follow-ups, and notable implementation decisions.

## Load Order
- Tier 0 (always): `docs/agent/README.md`, `docs/agent/index.md`
- Tier 1 (relevant): `docs/agent/warnings.md`, `docs/agent/todo.md`, latest handoffs, active ADRs
- Tier 2 (on demand): `docs/agent/archive/**`

## Lifecycle States
- `active`: current guidance
- `superseded`: replaced by newer guidance
- `resolved`: closed/completed guidance
- `archived`: historical reference only

## Naming
- Handoffs: `docs/agent/handoffs/YYYY-MM-DD-short-slug.md`
- Decisions: `docs/agent/decisions/ADR-XXXX-short-title.md`
- Keep active docs concise and archive stale history instead of deleting it.
