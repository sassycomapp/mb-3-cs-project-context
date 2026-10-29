---
title: "judgement-trail"
doc-id: "mb-117d5961"
updated: "2026-10-29"
---
# Judgement Trail
Flagged issues, developer decisions, and approved deviations, extracted per task.

## 2026-08-14 — WP10 Graphify + GBrain MCP verification

**Issue flagged:** Graphify MCP tools (`graph_stats`, `get_node`, `get_neighbors`, `shortest_path`, `query_graph`) are not present in this session's agent tool list, even though the server is configured (`opencode.json` → `mcp.graphify`, `type: local`, `enabled: true`), installed (`graphify 0.9.40`), and responds correctly when invoked directly over stdio (537 nodes / 434 edges; both path edges EXTRACTED).
**Agent action:** No configuration modified, nothing installed (per instruction). Verified the server side-by-side via a direct stdio MCP handshake to complete the read-only checks, and reported the tool-exposure absence as the key finding. Most likely cause: session not (re)loaded the project-level `mcp.graphify` config — a session restart is the candidate next step, deferred to Plan/developer.

## 2026-08-12 — Matt Pocock skills configuration

**Issue flagged:** Existing `## Agent skills` config targeted CLAUDE.md and claimed a Local markdown issue tracker + [[[mb-3-cs-project-context:docs-local/context|mb-3-cs (Mybizz Consulting Services)]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/CONTEXT.md)/`adr/` at repo root (40+/47 ADRs, `docs/adr-index.md`) — none of which matched the on-disk layout ([[[mb-3-cs-project-context:docs-local/context|mb-3-cs (Mybizz Consulting Services)]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/CONTEXT.md), `adr-local/` with 3 ADRs, `output-gstack/`, `ui/`).
**Developer decision:** Project uses AGENTS.md, not CLAUDE.md — `## Agent skills` block added to AGENTS.md; CLAUDE.md's block replaced with a pointer to AGENTS.md as the single source of truth. Issue tracker switched to GitHub Issues (`gh` CLI). Domain consumer rules corrected to the real layout.

**Issue flagged:** Stale `agents/` copies (doc-ids mb-3-cs-0008/0009) predated the move to `docs/agents/`.
**Developer decision:** Nothing gets deleted — moved to `obsolete/` via `git mv` (recorded as renames).

**Issue flagged:** [[[mb-3-cs-project-context:docs-local/context|mb-3-cs (Mybizz Consulting Services)]]](C:/dev/dev-mb-3-cs/mb-3-cs-project-context/docs-local/CONTEXT.md) claimed "47 Architecture Decision Records in `adr/`. Indexed at `docs/adr-index.md`" — neither exists on disk; docs-manager removed similar counts repo-wide on 2026-07-30 but CONTEXT.md kept the claim.
**Developer decision:** Corrected to "ADRs live in `adr-local/` (currently 3)". Flagged that the timezone-utc-storage-display-conversion ADR required by AGENTS.md's Timestamp Standard is not on disk — timestamps matched the established `date-created: YYYY-MM-DDTHHMMSS+0200` frontmatter convention.

## 2026-07-07 — dev-pdlf project registration

**Issue flagged:** Running `gbrain sync --repo /mnt/c/projects/dev-pdlf` without `--source` overwrote the `project-library` GBrain source repo path to point to dev-pdlf instead of `/mnt/c/projects/mb-3-cs/project-library`.
**Developer decision:** Resynced `project-library` with explicit `--source project-library` and correct repo path (restored to 328 pages). Created `dev-pdlf` source via `gbrain sources add` before syncing separately. Mitigation: always use `gbrain sources add <id>` first, then `gbrain sync --source <id>` for new project registration.

## 2026-07-06 — Docs reorganization consolidated

**Issue flagged:** Devlog/todo system status unresolved — handover questioned whether `C:\mybizz\devlog\` and `C:\mybizz\todo\` were still live.
**Developer decision:** Old system at `C:\mybizz\` does not exist on disk — superseded. Replacement established in AGENTS.md: `devlog-index.md` + `judgement-trail.md` + [[[pdlf:pdlf-library/docs-local/pdlf-todo|Active PDLF To-Do List]]](C:/pdlf/pdlf-library/docs-local/pdlf-todo.md) at project root. These files are actively maintained.

**Issue flagged:** [[[default:specifications-global/spec-security-architecture|Security & Secrets Architecture]]](C:/dev/project-library-global/specifications-global/spec-security-architecture.md) contained app-specific framing ("Mybizz CS," "this app's") but the content (RBAC role limits, token security, regulatory posture) applies Mybizz-wide.
**Developer decision:** Elevated to global. Moved to [[[default:specifications-global/spec-security-architecture|Security & Secrets Architecture]]](C:/dev/project-library-global/specifications-global/spec-security-architecture.md). Multi-tenant language corrected per [[[default:adr-global/adr-dependency-based-not-multi-tenant|Mybizz CS is a Dependency-Based Architecture, Not a Multi-Tenant Architecture]]](C:/dev/project-library-global/adr-global/adr-dependency-based-not-multi-tenant.md) ADR.

**Issue flagged:** HtmlTemplate ban (per [[[default:adr-global/adr-htmltemplate-use|HTMLTemplate Use]]](C:/dev/project-library-global/adr-global/adr-htmltemplate-use.md) ADR) contradicted by 10 documents still referencing custom HtmlTemplate for navigation/shells.
**Developer decision:** Resolved across all 10 documents. Layout shells now use NavigationRailLayout/NavigationDrawerLayout. [[[default:adr-global/adr-navigation-lambda-link-open-form|Navigation Standard: Lambda/Link/open_form]]](C:/dev/project-library-global/adr-global/adr-navigation-lambda-link-open-form.md) marked partially superseded (navigation pattern unchanged, layout shell decision superseded by [[[default:adr-global/adr-htmltemplate-use|HTMLTemplate Use]]](C:/dev/project-library-global/adr-global/adr-htmltemplate-use.md) ADR). Calendar-feature-spec.md requires full redesign — deferred.

**Issue flagged:** Vault system split between [[[default:specifications-global/spec-vault-system|The Vault System]]](C:/dev/project-library-global/specifications-global/spec-vault-system.md) and [[[default:specifications-global/spec-security|Security Specification]]](C:/dev/project-library-global/specifications-global/spec-security.md) — duplication between [[[default:specifications-global/spec-anvil-platform-standards|Anvil Platform Mechanics]]](C:/dev/project-library-global/specifications-global/spec-anvil-platform-standards.md) §10-11 and [[[default:specifications-global/spec-security|Security Specification]]](C:/dev/project-library-global/specifications-global/spec-security.md) §1-2.
**Developer decision:** Consolidated into single authoritative [[[default:specifications-global/spec-vault-system|The Vault System]]](C:/dev/project-library-global/specifications-global/spec-vault-system.md). Duplicate sections removed from both [[[default:specifications-global/spec-security|Security Specification]]](C:/dev/project-library-global/specifications-global/spec-security.md) and [[[default:specifications-global/spec-anvil-platform-standards|Anvil Platform Mechanics]]](C:/dev/project-library-global/specifications-global/spec-anvil-platform-standards.md). Original [[[default:specifications-global/spec-vault-system|The Vault System]]](C:/dev/project-library-global/specifications-global/spec-vault-system.md) deleted.

**Issue flagged:** ADR numbers used throughout project — all 40 ADRs dropped numeric prefixes.
**Developer decision:** Replaced all `ADR-NN` and `adr/NN-slug.md` references across 50+ files with slug names. Python script executed to ensure consistency.

**Issue flagged:** Template blanks in `termplates-checklists/` — full document copies, not blanks. Folder name had typo.
**Developer decision:** Folder renamed to `templates-checklists/`. 20 files emptied (zero-byte) and renamed with `tmp-` prefix. Naming convention: `tmp-` for templates, `chk-` for checklists.

**Issue flagged:** `(2)`-suffixed spec files — `spec-m3-design-standards (2).md` and `spec-anvil-platform-standards (2).md` lingered after handover said they were removed.
**Developer decision:** `(2)` versions confirmed as v1.2 superseding originals. Originals deleted, `(2)` suffixes dropped.

## 2026-07-06 — ADR reorganization

**Issue flagged:** Initial classification was wrong — treated many ADRs as local when the architecture, technology, and standards are shared across all Mybizz apps. Only the business context changes per app.
**Developer decision:** Reclassified: 39 global, 1 local. Key corrections: lead-capture, CRM, payment gateways, invoices, onboarding, free-trial-abandoned are all global (company-wide decisions). Tiers-model is global (Mybizz-centric). Schema-scope-reduction is the sole local ADR (CS-specific table set).
**Deletions:** 9 (multi-vertical — historical, no longer relevant), 12 (superseded build sequence), 13 (superseded by 16), 25 (onboarding finality, fully reversed by 26). ADR-37 kept as global despite partial supersession (still contains valid M3/CSS customization approach).

## 2026-07-06 — VS Code remnants cleanup

**Issue flagged:** `rm -rf` via WSL on `/mnt/c/Users/dev-p/AppData/Roaming/Code/` failed with "Permission denied" on 15 Git `askpass` scripts — Windows filesystem permission locks prevented deletion even after `chmod +w`.
**Developer decision:** Used `cmd.exe /c rmdir /s /q` — Windows-native deletion that bypasses the WSL/Windows permission boundary. Successful on first attempt.
**Scope note:** No `.code` directory found anywhere on the system; user can safely ignore that concern.

## 2026-07-06 — GStack + GBrain config optimization

**Issue flagged:** GBrain CLI commands (`sources list`, `sync`, `doctor`, `stats`) all failed silently — `broken-config` status.
**Root cause:** PGLite is single-writer. `gbrain serve` owned the exclusive database connection. CLI commands couldn't open the database.
**Developer decision:** Established the PGLite operational pattern: stop serve → run CLI commands → restart serve. Documented in gbrain-config-reference.md §7.

**Issue flagged:** GBrain at v0.42.44.0, PGLite engine. All workspace docs said v0.18.2, Supabase engine, OpenRouter proxy, local `embedding.ts` patch.
**Developer decision:** Updated 6 files to reflect current reality: `gbrain-install-plan.md`, `gbrain-repo-strategy.md`, Log, [[[mybizz-config-docs:gbrain/gbrain-reference|Reference: Commands, Mechanics, Incidents, Troubleshooting]]](C:/mybizz/mybizz-config-docs/gbrain/gbrain-reference.md), `gbrain-operating-guide.md`, `opencode-setup.md`. Removed stale references to OpenRouter patch and Supabase project ID.

**Issue flagged:** ZeroEntropy reranker failing (230 audit log entries over 3 weeks) — `ZEROENTROPY_API_KEY` not set.
**Developer decision:** API key provided by developer. Configured in `~/.bashrc` (persistent) and OpenCode MCP environment (for serve process). Reranker enabled via `gbrain config set search.reranker.enabled true`.

**Issue flagged:** 12 historical sync failures from `default` federated source — `createVersion failed: page not found`.
**Developer decision:** Auto-resolved. All 10 isolated sources synced successfully. Federation picks up current pages. Failures were from pre-migration path changes.

**Issue flagged:** 2 oversized pages (>50KB: `docs/mybizz-application-overview`, `docs/design`).
**Developer decision:** Flagged for future maintenance. Pages are embedded and searchable. Chunking would improve retrieval quality but requires re-indexing.

**Issue flagged:** `mb5pdlf/pdlf-state.md` had old pre-migration paths (`/mnt/c/_project-mb5pdlf/`).
**Developer decision:** Fixed to current paths (`/mnt/c/projects/mb5pdlf/`). Orphaned `~/.gstack/projects/unknown/` state removed.

## 2026-07-06 — OpenCode config optimization

**Issue flagged:** `mb-3-cs/AGENTS.md` directly violated global "Plan never writes" rule — granted Plan write access to `prompts/`, `docs/`, `tests/`.
**Developer decision:** Fixed. Plan write permissions removed. Build agent now covers all writes with explicit-approval gates on `prompts/`, `docs/`, `tests/`, `theme/`.

**Issue flagged:** Global `opencode.jsonc` had hardcoded model block (`xiaomi/mimo-v2.5-pro`). User wants model selected from UI picker.
**Developer decision:** Model block removed entirely. Provider block retained (OpenRouter API key needed for auth — env var approach previously unreliable).

**Issue flagged:** Global `external_directory` only covered `C:\mybizz\**` — didn't cover `C:\projects\**` or `C:\pdlf\**`.
**Developer decision:** Added both. All projects now accessible from any workspace without per-project config changes.

**Issue flagged:** 4 agent files missing task-completion reporting, devlog/judgement-trail awareness, and environment (Web UI only).
**Developer decision:** Added `## Task completion` and `## Environment` sections to all 4 agent files.

**Issue flagged:** 52 command files in `.opencode/commands/` — 3 duplicates, 2 broken headers, 5 formatting outliers.
**Developer decision:** Removed duplicates (`gstack-context-save.md`, `gstack-learn.md`, `gstack-upgrade.md`). Fixed headers ([[[pdlf:app-factory/phase-d-scaffold-and-assemble/d32-reference|Step 32 Physical Scaffold Build]]](C:/pdlf/app-factory/phase-d-scaffold-and-assemble/d32-reference.md), `review.md`). Standardized 5 outliers.

**Issue flagged:** mb-3-cs project library agent files (plan/build) now part of PDLF transition. All projects are PDLF projects.
**Developer decision:** Project configs (`opencode.json`) created for mb4ecom and mb5pdlf for proper OpenCode operation. PDLF-specific config handled in later pass.

**Issue flagged:** API key in plain text in `opencode.jsonc` and `opencode-10-config-files.md`.
**Developer decision:** Key redacted from workspace doc. Kept in config file (env var approach was unreliable — this is a known, deliberate trade-off).

**Issue flagged:** Three workspace reference docs had stale paths, Desktop bridge text, model references, and duplicated content blocks.
**Developer decision:** All three rewritten: `workspace-docmap.md`, `workspace-description.md`, `opencode-10-config-files.md`.

## 2026-07-05 — Repo migration

**Issue flagged:** GBrain CLI v0.42.44.0 argument parser rejects `--id` flag syntax. "Unknown flag" errors on positional values.
**Agent action:** Read CLI source to discover correct syntax: `gbrain sources add <id> --path <path>` (positional, not `--id` flag).

**Issue flagged:** GBrain daemon unstartable from OpenCode bash tool — `gbrain serve` is MCP-stdio-only and exits on stdin close.
**Agent action:** Noted as manual step. CLI operations (sync, embed, search, doctor) all work without daemon.

**Previously (2026-07-04):**

**Issue flagged:** pdlf-project-library path (`C:\pdlf\pdlf-project-library`) nested inside pdlf source (`C:\pdlf`) — gbrain blocks overlapping source paths.
**Developer decision:** Skip pdlf-project-library as a separate gbrain source. Content under that path will be included in the `pdlf` source.

**Issue flagged:** MCP `gbrain_sources_add` does not store `local_path` — sources created via MCP show null paths.
**Agent action:** Switched to gbrain CLI for source registration. Required stopping `gbrain serve` to free PGLite lock.

**Issue flagged:** Three repos (`pdlf`, `scratch`, `scratch-project-library`) not git-initialized. GBrain requires git repos.
**Agent action:** Initialized with `git init` and placeholder commits (allowed structural bootstrap).
