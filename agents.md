---
title: "AGENTS"
doc-id: "mb-7532e863"
updated: "2026-10-01"
---
# AGENTS.md — mb-3-cs-project-library

> Inherits `~/.config/opencode/AGENTS.md` in full. This file may only
> tighten those rules for this specific repo, never loosen them.

## Project Purpose

`mb-3-cs-project-library` — the planning, documentation, and design
corpus for the `mb-3-cs` (Mybizz Consulting Services) Anvil application.
The upstream planning authority for that app; `mb-3-cs` implements what
this repo specifies.

## Definitions

- **This repo** — the docs/planning repo, paired with the code repo
  `mb-3-cs`.
- **Code repo** — `mb-3-cs`, read-only context from here; never write
  into it from this repo.

## Precedence Order

User instruction → global `AGENTS.md` → this file's hard rules → ADRs
→ project docs → skill defaults.

## Hard Rules

- **The build agent in this repo writes only here** — never into
  `mb-3-cs` (the code repo). The code repo has its own, separate
  build agent for that.
- **The plan agent never writes anywhere, in any repo, under any
  circumstance.**
- **Mandatory UTC+2 timestamp headers** on any touched file, per the
  Timestamp Standard below.

## Workspace Structure

| Folder | Contents |
|---|---|
| `docs-local/` | Project documentation, including [[[mb-3-cs-project-context:docs-local/design\|DESIGN]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/DESIGN.md) |
| `adr-local/` | Architecture decision records specific to this project |
| `wireframes/` | Wireframe and visual references |
| `output-gstack/` | GStack pipeline outputs (CEO/ENG/design review reports) |
| `output-opencode/` | Task completion reports |

## Artifact Governance

- GStack session outputs (reports, task lists, handoff notes) →
  `output-gstack/`.
- Promoted durable documentation → `docs-local/`.
- Architecture decisions → `adr-local/`.
- Wireframes and visual references → `wireframes/`.
- Never save transient session outputs into `docs-local/` or
  `adr-local/`.

## Source of Truth

This repo is the upstream planning authority for `mb-3-cs`. Decisions
made here govern what gets implemented in the code repo, not the
reverse.

## Task Completion Reporting

Every task produces a completion report saved to `output-opencode/`.
Update `devlog-index.md` and `judgement-trail.md` here. Ask the two
housekeeping questions (commit/push, GBrain sync) separately at every
task completion.

## Timestamp Standard

Every date or timestamp generated for a build artefact must follow the
format and conversion method defined in the
[[[default:adr-global/adr-timezone-utc-storage-display-conversion|Client Timezone: IANA String, UTC Storage, Display-Time Conversion]]](C:/dev/project-library-global/adr-global/adr-timezone-utc-storage-display-conversion.md) ADR. Read that document
before generating any such timestamp. If anything about the required
method is unclear, stop and ask the developer.

## Environment

- Web UI only: `opencode web` from WSL. No Desktop app, no Desktop
  bridge.
- Workspace root: `/mnt/c/dev/dev-mb-3-cs/mb-3-cs-project-library/`.
- Code repo (read-only context): `/mnt/c/dev/dev-mb-3-cs/mb-3-cs/`.

## Agent skills

### Issue tracker

GitHub Issues via the `gh` CLI — issues and PRDs live in this repo's GitHub Issues. See `docs/agents/issue-tracker.md`.

### Triage labels

Five canonical labels: `needs-triage`, `needs-info`, `ready-for-agent`, `ready-for-human`, `wontfix` (defaults). See `docs/agents/triage-labels.md`.

### Domain docs

Single-context — [[[mb-3-cs-project-context:docs-local/context|mb-3-cs (Mybizz Consulting Services)]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/CONTEXT.md) + `adr-local/` for architectural decisions. Two-repo architecture: this repo holds planning/docs, `mb-3-cs` holds app code. See `docs/agents/domain.md`.

---

## Anvil Agent References (skills available via ~/.agents/skills/)

Anvil skills (`server-code`, `client-forms`, `client-modules`, `data-models`, `anvil-yaml`) are installed and available. Source checkout: `/mnt/c/mybizz/anvil-agent-references/`.

Resource paths, per the source repo's own AGENTS.md:
- Anvil client stubs: `/mnt/c/mybizz/anvil-agent-references/stubs/client/`
- Anvil server stubs: `/mnt/c/mybizz/anvil-agent-references/stubs/server/`
- Anvil type references: `/mnt/c/mybizz/anvil-agent-references/reference/types/`
- Anvil agent docs: `/mnt/c/mybizz/anvil-agent-references/docs/`
