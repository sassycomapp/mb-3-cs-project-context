---
title: "domain"
doc-id: "mb-18292576"
updated: "2026-10-29"
---
# Domain Docs

How the engineering skills should consume this repo's domain documentation when exploring the codebase.

## Two-repo architecture

This application spans two repos:

- **`mb-3-cs-project-library`** — planning, documentation, ADRs, wireframes, design artefacts.
- **`mb-3-cs`** — live application code (Python/Anvil). Built on Anvil.works.

Both repos apply to the same application. When a skill needs implementation context, read from `mb-3-cs`. When it needs architectural decisions or design context, read from this repo.

## Before exploring, read these

- **[[[mb-3-cs-project-context:docs-local/context|mb-3-cs (Mybizz Consulting Services)]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/CONTEXT.md)** — domain glossary and key concepts (create lazily via `/domain-modeling` when needed).
- **`adr-local/`** — Architecture Decision Records for this project.

If any of these files don't exist, **proceed silently**. The `/domain-modeling` skill creates them lazily when terms or decisions actually get resolved.

## File structure

```
/
├── docs-local/               ← Project documentation, incl. CONTEXT.md (glossary)
├── adr-local/                ← Architecture Decision Records
├── docs/
│   └── agents/               ← Agent configuration (issue tracker, triage labels, domain)
├── output-gstack/            ← GStack-produced reports
├── output-opencode/          ← Task completion reports
├── output-matt-skills/       ← Matt Pocock skill outputs
├── pdlf/                     ← PDLF ledger and workflow state
├── ui/                       ← Screen/mockup references
├── register-local/           ← Project registry
├── policy-local/             ← Project policies
├── prompts-local/            ← Project prompts
├── docs-standard-local/      ← Standard process docs (SRS, PRD, BRD, …)
├── standard-operating-procedures-local/ ← Project SOPs
├── obsolete/                 ← Superseded documents (developer purges only)
├── .scratch/                 ← Working scratch (issue tracker history)
├── .out-of-scope/            ← Rejected issues
└── wip/                      ← Work in progress
```

## Use the glossary's vocabulary

When your output names a domain concept (in an issue title, a refactor proposal, a hypothesis, a test name), use the term as defined in [[[mb-3-cs-project-context:docs-local/context|mb-3-cs (Mybizz Consulting Services)]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/CONTEXT.md). Don't drift to synonyms the glossary explicitly avoids.

If the concept you need isn't in the glossary yet, that's a signal — either you're inventing language the project doesn't use (reconsider) or there's a real gap (note it for `/domain-modeling`).

## Flag ADR conflicts

If your output contradicts an existing ADR, surface it explicitly rather than silently overriding:

> _Contradicts the [[[default:adr-global/adr-client-instance-architecture|Client Instance Architecture]]](C:/dev/project-library-global/adr-global/adr-client-instance-architecture.md) ADR (client instance architecture) — but worth reopening because…_
